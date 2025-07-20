
> **作者：** 刘潍嘉 (清华大学)    
> **邮箱：** <weijialiu99@163.com>    

> 受限于个人能力，文中难免有错误和疏漏之处，还请大家多多批评指正！

&emsp; 

- **Title**: 扎马步-常用概率分布函数详解：Stata篇
- **Keywords**: 概率分布, 离散分布, 连续分布, Stata, 数学性质, 概率密度函数, 累积分布函数, 随机变量, 伯努利分布, 二项分布, 几何分布, 泊松分布, 超几何分布, 正态分布, 指数分布, 均匀分布, t 分布, 卡方分布, F 分布

&emsp; 


## 0. 引言

概率分布是统计建模、推断和数据分析的基石，它们为描述随机现象和量化不确定性提供了数学框架。本文为15种关键的离散和连续概率分布提供了相关理论及 Stata 的软件实现概述。对于每种分布，将分别阐述其简介、常见用途、数学属性 (包括概率质量函数/概率密度函数 (PMF/PDF)、累积分布函数 (CDF)、矩母函数 (MGF)、特征函数 (CF)、期望值 ($E[X]$) 和方差 ($Var(X)$)，然后提供在 Stata 中实现的代码示例，并附带使用多个参数绘制PMF/PDF/CDF图形变化的代码。整体结构分为两部分：第一部分介绍离散分布，第二部分介绍连续分布。

## 1. 离散分布


离散随机变量是指其可能取值是有限个或可数无限多个的随机变量。离散分布通过概率质量函数 (Probability Mass Function, PMF) 来描述，PMF 给出了随机变量取每个特定值的概率。离散分布广泛应用于对计数结果 (如事件发生次数、成功次数等) 进行建模。

### 1.1 伯努利分布 (X ~ Bernoulli(p))

#### 1.1.1 简介与用途

伯努利分布是最简单的离散分布，用于模拟只有两个可能结果 (通常称为“成功”和“失败”，编码为 1 和 0) 的单次随机试验。参数 $p\ (0 \leq p \leq 1)$ 代表“成功”结果发生的概率。

**常见用途**：
- 模拟抛硬币 (正面/反面) 。
- 对调查问卷中的“是/否”回答进行建模。
- 表示某个特征的存在与否。
- 作为构建更复杂离散分布 (如二项分布、几何分布) 的基础模块。

#### 1.1.2 数学性质

**表 1.1：伯努利分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $p \in [0, 1]$              |
| 取值范围       | $k \in \{0, 1\}$           |
| PMF           | $P(X = k) = p^k (1 - p)^{1 - k}$|
| CDF           | $F(k) = \begin{cases} 0 & k < 0 \\ 1 - p & 0 \leq k < 1 \\ 1 & k \geq 1 \end{cases}$|
| 期望值 E[X]   | $p$                         |
| 方差 Var(X)   | $p(1 - p)$                  |
| 矩母函数 (MGF)| $M_X(t) = (1 - p) + p e^t$  |
| 特征函数 (CF) | $\phi_X(t) = (1 - p) + p e^{i t}$|

#### 1.1.3 软件实现 (Stata)

Stata 中没有直接的伯努利分布函数，但可以将其视为试验次数 n=1 的二项分布。

<details>
<summary>Stata 实现代码 (PMF, CDF, RVS (Random variables))</summary>

```stata
* 参数设置
local p = 0.7

* 计算 PMF (P(X=1) 和 P(X=0))
* 使用二项分布函数，其中试验次数 n=1
display "P(X=1) for p=`p': " binomialp(1, 1, `p')
display "P(X=0) for p=`p': " binomialp(1, 0, `p')

* 计算 CDF (P(X<=0))
display "P(X<=0) for p=`p': " binomial(1, 0, `p')

* 生成随机变量 (生成10个)
clear
set obs 10
gen bern_rv = rbinomial(1, `p')
list bern_rv
```
</details>

#### 1.1.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PMF 示例)</summary>

```stata
clear
set obs 2
gen k = _n - 1

* 计算不同 p 值的 PMF
gen pmf_p03 = binomialp(1, k, 0.3)
gen pmf_p05 = binomialp(1, k, 0.5)
gen pmf_p07 = binomialp(1, k, 0.7)

* 绘制条形图
twoway (bar pmf_p03 k, barwidth(0.2) fcolor(blue%50)) ///
       (bar pmf_p05 k, barwidth(0.2) fcolor(red%50)) ///
       (bar pmf_p07 k, barwidth(0.2) fcolor(green%50)), ///
       legend(order(1 "p=0.3" 2 "p=0.5" 3 "p=0.7") pos(6) ring(0)) ///
       title("Bernoulli Distribution PMF for different p values") ///
       xtitle("k (outcome)") ytitle("P(X = k)") ///
       xlabel(0 1)
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
clear
* 为绘制阶梯函数准备数据点
input float k float cdf_p03 float cdf_p05 float cdf_p07
-0.5 0   0   0
0    0   0   0
0    0.7 0.5 0.3
1    0.7 0.5 0.3
1    1   1   1
1.5  1   1   1
end

* 绘制阶梯图
twoway (line cdf_p03 k, connect(stairstep) lcolor(blue)) ///
       (line cdf_p05 k, connect(stairstep) lcolor(red)) ///
       (line cdf_p07 k, connect(stairstep) lcolor(green)), ///
       legend(order(1 "p=0.3" 2 "p=0.5" 3 "p=0.7") pos(5) ring(0)) ///
       title("Bernoulli Distribution CDF for different p values") ///
       xtitle("k") ytitle("F(k) = P(X <= k)") ///
       xlabel(-0.5 0 1 1.5) ylabel(0 0.5 1, angle(0))
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-1-1-4.png)

#### 1.1.5 联系与延伸

伯努利分布是理解其他几个重要离散分布的关键基础。二项分布描述了多次独立伯努利试验中的成功次数；几何分布描述了获得第一次成功所需的伯努利试验次数；负二项分布则描述了获得指定次数成功所需的伯努利试验次数。因此，这些更复杂分布的性质 (如期望、方差) 通常可以通过考虑其底层的独立伯努利试验来推导。

### 1.2 二项分布 (X ~ Binomial(n, p))

#### 1.2.1 简介与用途

二项分布用于描述在一系列固定的 $n$次独立同分布的伯努利试验中，“成功”结果出现的次数 $k$，其中每次试验成功的概率为 $p$。

**常见用途**：
- 质量控制：一批产品中次品的数量。
- 调查分析：同意某个观点的受访者人数。
- 市场研究：购买特定产品的客户数量。
- 任何涉及重复独立二元结果试验的场景。

#### 1.2.2 数学性质

**表 1.2：二项分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $n \in \{1, 2, \dots\}, p \in [0, 1]$|
| 取值范围       | $k \in \{0, 1, \dots, n\}$ |
| PMF           | $P(X = k) = \binom{n}{k} p^k (1 - p)^{n - k}$|
| CDF           | $F(k) = \sum_{i=0}^{k} \binom{n}{i} p^i (1 - p)^{n - i}$|
| 期望值 E[X]   | $np$                        |
| 方差 Var(X)   | $np(1 - p)$                 |
| 矩母函数 (MGF)| $M_X(t) = [(1 - p) + p e^t]^n$|
| 特征函数 (CF) | $\phi_X(t) = [(1 - p) + p e^{i t}]^n$|

**说明**：

- PMF 中的 $\binom{n}{k} = \frac{n!}{k!(n - k)!}$ 是二项式系数，代表从 $n$次试验中选出 $k$次成功的方式数。
- 期望和方差可以通过将二项变量视为 $n$个独立伯努利变量之和来推导：
  
   $E[X] = E\left[ \sum_{i=1}^n Y_i \right] = \sum E[Y_i] = \sum p = np$, $\text{Var}(X) = \text{Var}\left(\sum Y_i \right) = \sum \text{Var}(Y_i) = \sum p(1 - p) = np(1 - p)$ (其中 $Y_i \sim \text{Bernoulli}(p)$) 。

- MGF 也是基于独立伯努利变量之和的 MGF 是各 MGF 的乘积这一性质得出的：$M_X(t) = (M_Y(t))^n = [(1 - p) + p e^t]^n$。

#### 1.2.3 软件实现 (Stata)

<details>
<summary>Stata 实现代码 (PMF, CDF, RVS)</summary>

```stata
* 参数设置
local n = 10  // 试验次数
local p = 0.3  // 成功概率

* 计算 PMF (例: k=3)
display "P(X=3) for n=`n', p=`p': " binomialp(`n', 3, `p')

* 计算 CDF (例: k=4)
display "P(X<=4) for n=`n', p=`p': " binomial(`n', 4, `p')

* 生成随机变量 (生成5个)
clear
set obs 5
gen binom_rv = rbinomial(`n', `p')
list binom_rv
```
</details>

#### 1.2.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PMF 示例)</summary>

```stata
clear
local n = 20
set obs 21
gen k = _n - 1

* 计算不同 p 值的 PMF
gen pmf_p02 = binomialp(`n', k, 0.2)
gen pmf_p05 = binomialp(`n', k, 0.5)
gen pmf_p08 = binomialp(`n', k, 0.8)

* 绘制图形
twoway (line pmf_p02 k, sort connect(direct) msymbol(O)) ///
       (line pmf_p05 k, sort connect(direct) msymbol(S)) ///
       (line pmf_p08 k, sort connect(direct) msymbol(T)), ///
       legend(order(1 "p=0.2" 2 "p=0.5" 3 "p=0.8") pos(11) ring(0)) ///
       title("Binomial Distribution PMF for n=`n' and different p values") ///
       xtitle("k (Number of Successes)") ytitle("P(X = k)")
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
clear
local n = 20
set obs 21
gen k = _n - 1

* 计算不同 p 值的 CDF
gen cdf_p02 = binomial(`n', k, 0.2)
gen cdf_p05 = binomial(`n', k, 0.5)
gen cdf_p08 = binomial(`n', k, 0.8)

* 绘制图形
twoway (line cdf_p02 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_p05 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_p08 k, sort connect(stairstep) msymbol(i)), ///
       legend(order(1 "p=0.2" 2 "p=0.5" 3 "p=0.8") pos(5) ring(0)) ///
       title("Binomial Distribution CDF for n=`n' and different p values") ///
       xtitle("k (Number of Successes)") ytitle("F(k) = P(X <= k)")
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-1-2-4.png)

#### 1.2.5 联系与延伸

二项分布与其他分布有着重要的联系。当试验次数 $n$很大且成功概率 $p$适中时，根据中心极限定理，二项分布可以用正态分布 $N(np, np(1 - p))$来近似。通常当 $np \ge 5$ 且 $n(1-p) \ge 5$ 时，近似效果较好。这是因为二项变量是独立同分布伯努利变量的和，而中心极限定理保证了独立同分布变量之和 (或均值) 在 $n$趋于无穷时趋向正态分布。

另一方面，当 $n$很大，$p$很小，使得 $\lambda = np$的值适中时，二项分布可以用泊松分布 $\text{Poisson}(\lambda)$来近似。这可以通过考察二项 PMF 在 $n \to \infty, p \to 0, np = \lambda$条件下的极限来证明，极限结果恰好是泊松 PMF。这个近似在处理稀有事件的大量重复试验时非常有用。

此外，比较二项分布与泊松分布的方差和期望关系也很有启发。对于 $p < 1$的二项分布，方差 $\text{Var}(X) = np(1 - p)$严格小于其期望 $E[X] = np$。而泊松分布的方差等于期望 ($\text{Var}(X) = E[X] = \lambda$) 。这意味着，如果使用 $\lambda = np$的泊松分布来近似二项分布，二项分布本身表现出相对于泊松分布的“欠离散” (underdispersion) 。在对实际计数数据建模时，这是一个重要的诊断特征：如果观察数据的方差显著小于均值，可能暗示着一个类似二项分布的机制 (例如，存在一个固定的试验总数上限) ，而不是一个纯粹的泊松过程。

### 1.3 几何分布 (X ~ Geometric(p))

#### 1.3.1 简介与用途

几何分布用于模拟为了获得 **第一次** 成功所需要进行的独立伯努利试验的次数。需要注意的是，几何分布有两种常见的定义：
1. $X$代表获得第一次成功所需的总试验次数 ($k = 1, 2, 3, \dots$) 。
2. $X$代表在第一次成功 **之前** 经历的失败次数 ($k = 0, 1, 2, \dots$) 。

Stata 的相关函数（负二项分布）采用第二种定义。为与理论部分保持一致，我们采用第一种定义（试验次数），并在代码中进行相应转换。

**常见用途**：
- 模拟离散场景下的等待时间 (例如，直到成功的尝试次数) 。
- 部件可靠性：直到首次失效的循环次数。
- 其他关注的是第一次成功所需的尝试次数，而不是总尝试次数的问题。

#### 1.3.2 数学性质

**表 1.3：几何分布性质总结 (X = 试验次数)**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $p \in (0, 1]$              |
| 取值范围       | $k \in \{1, 2, 3, \dots\}$ |
| PMF           | $P(X = k) = (1 - p)^{k - 1} p$|
| CDF           | $F(k) = 1 - (1 - p)^k$      |
| 期望值 E[X]   | $\frac{1}{p}$               |
| 方差 Var(X)   | $\frac{1 - p}{p^2}$        |
| 矩母函数 (MGF)| $M_X(t) = \frac{p e^t}{1 - (1 - p) e^t}$, for $t < -\ln(1 - p)$|
| 特征函数 (CF) | $\phi_X(t) = \frac{p e^{i t}}{1 - (1 - p) e^{i t}}$|

**说明**：
- PMF 表示前 $k - 1$次试验失败 (每次概率 $1 - p$) ，第 $k$次试验成功 (概率 $p$) 。
- CDF 可以通过计算 PMF 的累加和 (一个等比数列求和) 得到。
- 几何分布具有无记忆性 (Memoryless Property)：$P(X > n + k \mid X > n) = P(X > k)$。这意味着，已知已经进行了 $n$次试验且未成功，还需要进行至少 $k$次试验才能成功的条件概率，等于从一开始就需要至少 $k$次试验才能成功的概率。过去的失败不影响未来的等待时间。

#### 1.3.3 软件实现 (Stata)

Stata 没有直接的几何分布函数，但几何分布是负二项分布（成功次数 r=1）的特例。Stata 的 `nbinomialp(r, k, p)` 中的 `k` 是指失败次数。因此，对于试验次数为 `x` 的几何分布，其失败次数为 `x-1`。

<details>
<summary>Stata 实现代码 (PMF, CDF, RVS)</summary>

```stata
* 参数设置
local p = 0.2  // 成功概率

* 计算 PMF (例: 试验次数 x=5，即失败次数 k=4)
* 使用负二项分布函数，其中 r=1, 失败次数 k=4
display "P(X=5 trials) for p=`p': " nbinomialp(1, 5-1, `p')

* 计算 CDF (例: 试验次数 x=5, P(X<=5), 即失败次数 k<=4)
display "P(X<=5 trials) for p=`p': " nbinomial(1, 5-1, `p')

* 生成随机变量 (生成10个试验次数)
clear
set obs 10
* rnbinomial(1, `p') 生成失败次数，加 1 得到试验次数
gen geom_rv = rnbinomial(1, `p') + 1
list geom_rv
```
</details>

#### 1.3.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PMF 示例)</summary>

```stata
clear
set obs 20
gen k = _n // k 是试验次数

* 计算不同 p 值的 PMF (失败次数 = k-1)
gen pmf_p01 = nbinomialp(1, k-1, 0.1)
gen pmf_p03 = nbinomialp(1, k-1, 0.3)
gen pmf_p05 = nbinomialp(1, k-1, 0.5)

* 绘制图形
twoway (line pmf_p01 k, sort connect(direct) msymbol(O)) ///
       (line pmf_p03 k, sort connect(direct) msymbol(S)) ///
       (line pmf_p05 k, sort connect(direct) msymbol(T)), ///
       legend(order(1 "p=0.1" 2 "p=0.3" 3 "p=0.5") pos(1) ring(0)) ///
       title("Geometric Distribution PMF for different p values") ///
       xtitle("k (Number of Trials until first success)") ytitle("P(X = k)")
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
clear
set obs 20
gen k = _n // k 是试验次数

* 计算不同 p 值的 CDF (失败次数 = k-1)
gen cdf_p01 = nbinomial(1, k-1, 0.1)
gen cdf_p03 = nbinomial(1, k-1, 0.3)
gen cdf_p05 = nbinomial(1, k-1, 0.5)

* 绘制图形
twoway (line cdf_p01 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_p03 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_p05 k, sort connect(stairstep) msymbol(i)), ///
       legend(order(1 "p=0.1" 2 "p=0.3" 3 "p=0.5") pos(5) ring(0)) ///
       title("Geometric Distribution CDF for different p values") ///
       xtitle("k (Number of Trials until first success)") ytitle("F(k) = P(X <= k)")
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-1-3-4.png)

#### 1.3.5 联系与延伸

几何分布是连续情形下指数分布的离散对应。两者都用于模拟等待时间 (直到第一个事件发生) ，并且都具有标志性的无记忆性。这种概念上的联系对于在应用中 (如可靠性、排队) 选择使用离散时间模型还是连续时间模型至关重要。

同时，几何分布是负二项分布的一个特例。负二项分布计算的是获得 $r$次成功所需的试验次数，而几何分布计算的是获得第 1 次成功所需的试验次数。在负二项分布的各种公式 (PMF、MGF 等) 中，令 $r = 1$，即可得到相应的几何分布公式。这表明几何分布属于更广泛的等待时间分布族。

### 1.4 负二项 (帕斯卡) 分布 (X ~ Pascal(m, p) or NB(r, p))

#### 1.4.1 简介与用途

负二项分布 (帕斯卡分布) 用于模拟在一系列独立同分布的伯努利试验中，为了达到预定次数 $r$(或 $m$) 的成功，所需要的试验总次数，或者在达到 $r$次成功 **之前** 所经历的失败次数。与几何分布类似，其定义需要明确：随机变量 $X$究竟是计数总试验次数，还是计数失败次数。
Stata 的 `nbinomial` 系列函数定义随机变量 $X$是在获得 $r$次成功 **之前** 所经历的失败次数 $k$。

**常见用途**：
- 作为几何分布的推广 (等待 $r$次成功) 。
- 对“过度离散” (overdispersed) 的计数数据进行建模，即数据的方差大于均值的情况，这在生物学、流行病学、经济学等领域很常见。
- 事故统计、消费者购买行为等模型。

#### 1.4.2 数学性质

**表 1.4：负二项分布性质总结 (X = 失败次数 k, r = 成功次数)**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $r \in \{1, 2, \dots\}, p \in (0, 1]$|
| 取值范围       | $k \in \{0, 1, 2, \dots\}$ |
| PMF           | $P(X = k) = \binom{k + r - 1}{k} p^r (1 - p)^k$或者 $\binom{k+r-1}{r-1} p^r (1-p)^k$|
| CDF           | $F(k) = \sum_{i=0}^{k} \binom{i + r - 1}{i} p^r (1 - p)^i$(通常无简单闭式) |
| 期望值 E[X]   | $\frac{r (1 - p)}{p}$       |
| 方差 Var(X)   | $\frac{r (1 - p)}{p^2}$     |
| 矩母函数 (MGF)| $M_X(t) = \left(\frac{p}{1 - (1 - p) e^t} \right)^r$, for $t < -\ln(1 - p)$|
| 特征函数 (CF) | $\phi_X(t) = \left(\frac{p}{1 - (1 - p) e^{i t}} \right)^r$|

**说明**：
- PMF 的含义是：在总共 $k + r$次试验中，最后一次必须是成功 (概率 $p$) ，而在之前的 $k + r - 1$次试验中，恰好有 $r - 1$次成功和 $k$次失败。从 $k + r - 1$次中选择 $k$次失败位置的方式数为 $\binom{k + r - 1}{k}$。
- 当 $r = 1$时，PMF 变为几何分布 (失败次数) 。
- 期望和方差可以通过将负二项变量看作 $r$个独立几何变量 (计数失败次数) 之和来推导。

#### 1.4.3 软件实现 (Stata)

<details>
<summary>Stata 实现代码 (PMF, CDF, RVS)</summary>

```stata
* 参数设置
local r = 5  // 成功次数
local p = 0.6  // 成功概率

* 计算 PMF (例: k=3 次失败)
display "P(X=3 failures) for r=`r', p=`p': " nbinomialp(`r', 3, `p')

* 计算 CDF (例: k=4 次失败, P(X<=4))
display "P(X<=4 failures) for r=`r', p=`p': " nbinomial(`r', 4, `p')

* 生成随机变量 (生成10个失败次数)
clear
set obs 10
gen nbinom_rv = rnbinomial(`r', `p')
list nbinom_rv
```
</details>

#### 1.4.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PMF 示例)</summary>

```stata
clear
local r = 5
set obs 31
gen k = _n - 1

* 计算不同 p 值的 PMF
gen pmf_p02 = nbinomialp(`r', k, 0.2)
gen pmf_p05 = nbinomialp(`r', k, 0.5)
gen pmf_p08 = nbinomialp(`r', k, 0.8)

* 绘制图形
twoway (line pmf_p02 k, sort connect(direct) msymbol(O)) ///
       (line pmf_p05 k, sort connect(direct) msymbol(S)) ///
       (line pmf_p08 k, sort connect(direct) msymbol(T)), ///
       legend(order(1 "p=0.2" 2 "p=0.5" 3 "p=0.8") pos(1) ring(0)) ///
       title("Negative Binomial Distribution PMF for r=`r' and different p values") ///
       xtitle("k (Number of Failures before r-th success)") ytitle("P(X = k)")
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
clear
local r = 5
set obs 31
gen k = _n - 1

* 计算不同 p 值的 CDF
gen cdf_p02 = nbinomial(`r', k, 0.2)
gen cdf_p05 = nbinomial(`r', k, 0.5)
gen cdf_p08 = nbinomial(`r', k, 0.8)

* 绘制图形
twoway (line cdf_p02 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_p05 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_p08 k, sort connect(stairstep) msymbol(i)), ///
       legend(order(1 "p=0.2" 2 "p=0.5" 3 "p=0.8") pos(5) ring(0)) ///
       title("Negative Binomial Distribution CDF for r=`r' and different p values") ///
       xtitle("k (Number of Failures before r-th success)") ytitle("F(k) = P(X <= k)")
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-1-4-4.png)

#### 1.4.5 联系与延伸

负二项分布可以通过泊松分布与伽马分布的混合得到。如果 $X$的条件分布是 $\text{Poisson}(\lambda)$，而 $\lambda$服从 $\text{Gamma}(r, \frac{p}{1-p})$，则 $X$的边际分布是 $\text{NB}(r, p)$。这解释了其在过度离散数据建模中的应用。

此外，负二项分布是 $r$个独立几何分布变量 (计数失败次数) 之和，提供了一个直观的构造方式。

### 1.5 超几何分布 (X ~ Hypergeometric(N, K, n))

#### 1.5.1 简介与用途

超几何分布描述了从一个包含 $N$个元素 (其中有 $K$个“成功”元素和 $N - K$个“失败”元素) 的有限总体中，进行不放回抽样，抽取 $n$个元素时，抽到的“成功”元素的数量 $X$。

**常见用途**：
- 质量控制：从一批产品中抽检 $n$件，其中次品 (成功) 的数量。
- 生态学研究：标记重捕法估计种群数量。
- 遗传学：抽取等位基因。
- 纸牌游戏：发牌时某种牌的数量。

**与二项分布的关键区别**：超几何分布的试验是不独立的，因为每次抽取都改变了总体中剩余元素的构成比例。

#### 1.5.2 数学性质

**表 1.5：超几何分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $N$(总体大小), $K$(总体中成功数, $0 \leq K \leq N$), $n$(样本大小, $0 \leq n \leq N$) |
| 取值范围       | $x \in \{\max(0, n - N + K), \dots, \min(n, K)\}$|
| PMF           | $P(X = x) = \frac{\binom{K}{x} \binom{N - K}{n - x}}{\binom{N}{n}}$|
| CDF           | $F(x) = \sum_{i=\max(0, n - N + K)}^{x} \frac{\binom{K}{i} \binom{N - K}{n - i}}{\binom{N}{n}}$|
| 期望值 E[X]   | $n \frac{K}{N}$             |
| 方差 Var(X)   | $n \frac{K}{N} \left(1 - \frac{K}{N}\right) \frac{N - n}{N - 1}$|
| 矩母函数 (MGF)| 无简单闭式解                     |
| 特征函数 (CF) | 无简单闭式解                     |

**说明**：
- PMF 的逻辑是：从 $K$个成功元素中选出 $x$个的方式数 $\binom{K}{x}$，乘以从 $N - K$个失败元素中选出 $n - x$个的方式数 $\binom{N - K}{n - x}$，再除以从 $N$个总元素中选出 $n$个的所有可能方式数 $\binom{N}{n}$。
- 期望值的形式 $n \frac{K}{N}$与二项分布的期望 $np$类似，其中 $p = \frac{K}{N}$是第一次抽取时成功概率。
- 方差包含有限总体修正因子 (FPC) $\frac{N - n}{N - 1}$，比二项分布方差小，反映不放回抽样的信息增益。

#### 1.5.3 软件实现 (Stata)

Stata 的函数 `hypergeometricp(N, K, n, x)` 和 `hypergeometric(N, K, n, x)` 的参数与理论定义完全一致。

<details>
<summary>Stata 实现代码 (PMF, CDF, RVS)</summary>

```stata
* 参数设置
local N_total = 50     // 总体大小
local K_success = 10   // 总体中成功元素个数
local n_drawn = 8      // 抽取样本大小

* 计算 PMF (例: x=2 个成功)
display "P(X=2) for N=`N_total', K=`K_success', n=`n_drawn': " hypergeometricp(`N_total', `K_success', `n_drawn', 2)

* 计算 CDF (例: x=3 个成功, P(X<=3))
display "P(X<=3) for N=`N_total', K=`K_success', n=`n_drawn': " hypergeometric(`N_total', `K_success', `n_drawn', 3)

* 生成随机变量 (生成10个)
clear
set obs 10
gen hyper_rv = rhypergeometric(`N_total', `K_success', `n_drawn')
list hyper_rv
```
</details>

#### 1.5.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PMF 示例)</summary>

```stata
clear
local N_total = 50
local n_drawn = 10
set obs 11
gen x = _n - 1

* 变化的参数: 总体中成功元素的个数 K
gen pmf_K5 = hypergeometricp(`N_total', 5, `n_drawn', x)
gen pmf_K15 = hypergeometricp(`N_total', 15, `n_drawn', x)
gen pmf_K25 = hypergeometricp(`N_total', 25, `n_drawn', x)

* 绘制图形
twoway (line pmf_K5 x, sort connect(direct) msymbol(O)) ///
       (line pmf_K15 x, sort connect(direct) msymbol(S)) ///
       (line pmf_K25 x, sort connect(direct) msymbol(T)), ///
       legend(order(1 "K=5" 2 "K=15" 3 "K=25") pos(1) ring(0)) ///
       title("Hypergeometric Distribution PMF (N=`N_total', N_drawn=`n_drawn')") ///
       xtitle("x (Number of Successes in Sample)") ytitle("P(X = x)")
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
clear
local N_total = 50
local n_drawn = 10
set obs 11
gen x = _n - 1

* 变化的参数: 总体中成功元素的个数 K
gen cdf_K5 = hypergeometric(`N_total', 5, `n_drawn', x)
gen cdf_K15 = hypergeometric(`N_total', 15, `n_drawn', x)
gen cdf_K25 = hypergeometric(`N_total', 25, `n_drawn', x)

* 绘制图形
twoway (line cdf_K5 x, sort connect(stairstep) msymbol(i)) ///
       (line cdf_K15 x, sort connect(stairstep) msymbol(i)) ///
       (line cdf_K25 x, sort connect(stairstep) msymbol(i)), ///
       legend(order(1 "K=5" 2 "K=15" 3 "K=25") pos(5) ring(0)) ///
       title("Hypergeometric Distribution CDF (N=`N_total', N_drawn=`n_drawn')") ///
       xtitle("x (Number of Successes in Sample)") ytitle("F(x) = P(X <= x)")
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-1-5-4.png)

#### 1.5.5 联系与延伸

当 $N \gg n$(总体大小远大于样本大小时)，超几何分布近似于 $\text{Binomial}(n, p = \frac{K}{N})$，因为不放回抽样的影响变得很小。这在大型总体抽样中常用于简化计算。

### 1.6 泊松分布 (X ~ Poisson(λ))

#### 1.6.1 简介与用途

泊松分布用于模拟固定区间内随机独立事件的数量，参数 $\lambda > 0$是平均发生次数。

**常见用途**：
- 呼叫中心：单位时间内电话数量。
- 生物学：DNA 突变数量。
- 物理学：放射性衰变次数。

#### 1.6.2 数学性质

**表 1.6：泊松分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $\lambda > 0$               |
| 取值范围       | $k \in \{0, 1, 2, \dots\}$ |
| PMF           | $P(X = k) = \frac{e^{-\lambda} \lambda^k}{k!}$|
| CDF           | $F(k) = \sum_{i=0}^{k} \frac{e^{-\lambda} \lambda^i}{i!}$|
| 期望值 E[X]   | $\lambda$                   |
| 方差 Var(X)   | $\lambda$                   |
| 矩母函数 (MGF)| $M_X(t) = \exp(\lambda (e^t - 1))$|
| 特征函数 (CF) | $\phi_X(t) = \exp(\lambda (e^{i t} - 1))$|

**说明**：
- 期望等于方差 (等离散性) 。
- 可加性：独立泊松变量之和仍为泊松分布。如果 $X_1 \sim \text{Poisson}(\lambda_1)$ 和 $X_2 \sim \text{Poisson}(\lambda_2)$ 是独立的，则 $X_1 + X_2 \sim \text{Poisson}(\lambda_1 + \lambda_2)$。

#### 1.6.3 软件实现 (Stata)

Stata 函数使用 `m` 作为参数 $\lambda$。

<details>
<summary>Stata 实现代码 (PMF, CDF, RVS)</summary>

```stata
* 参数设置
local lambda = 3.5

* 计算 PMF (例: k=4)
display "P(X=4) for lambda=`lambda': " poissonp(`lambda', 4)

* 计算 CDF (例: k=5, P(X<=5))
display "P(X<=5) for lambda=`lambda': " poisson(`lambda', 5)

* 生成随机变量 (生成10个)
clear
set obs 10
gen poisson_rv = rpoisson(`lambda')
list poisson_rv
```
</details>

#### 1.6.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PMF 示例)</summary>

```stata
clear
set obs 26
gen k = _n - 1

* 不同的 lambda 参数
gen pmf_L1 = poissonp(1, k)
gen pmf_L4 = poissonp(4, k)
gen pmf_L10 = poissonp(10, k)

* 绘制图形
twoway (line pmf_L1 k, sort connect(direct) msymbol(O)) ///
       (line pmf_L4 k, sort connect(direct) msymbol(S)) ///
       (line pmf_L10 k, sort connect(direct) msymbol(T)), ///
       legend(order(1 "λ=1.0" 2 "λ=4.0" 3 "λ=10.0") pos(1) ring(0)) ///
       title("Poisson Distribution PMF for different λ values") ///
       xtitle("k (Number of Events)") ytitle("P(X = k)")
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
clear
set obs 26
gen k = _n - 1

* 不同的 lambda 参数
gen cdf_L1 = poisson(1, k)
gen cdf_L4 = poisson(4, k)
gen cdf_L10 = poisson(10, k)

* 绘制图形
twoway (line cdf_L1 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_L4 k, sort connect(stairstep) msymbol(i)) ///
       (line cdf_L10 k, sort connect(stairstep) msymbol(i)), ///
       legend(order(1 "λ=1.0" 2 "λ=4.0" 3 "λ=10.0") pos(5) ring(0)) ///
       title("Poisson Distribution CDF for different λ values") ///
       xtitle("k (Number of Events)") ytitle("F(k) = P(X <= k)")
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-1-6-4.png)

#### 1.6.5 联系与延伸

泊松分布是二项分布在 $n \to \infty, p \to 0, np = \lambda$条件下的极限，适用于描述在连续时间或空间内稀有事件的发生次数。当 $\lambda$较大时 (通常 $\lambda > 20$或 $\lambda > 10$作为一个粗略的指导)，泊松分布可以用正态分布 $N(\lambda, \lambda)$来近似。如果观察到的计数数据的方差显著大于或小于其均值，则可能需要考虑其他分布，如负二项分布 (用于过度离散，方差大于均值) 或广义泊松分布。

&emsp;

## 2. 连续分布


连续随机变量的取值覆盖一个区间或多个区间的并集。它们通过概率密度函数 (Probability Density Function, PDF) $f(x)$来描述。对于连续随机变量 $X$，其在任意单点 $x_0$ 的概率 $P(X=x_0)=0$。概率是通过 PDF 在某个区间上的积分来定义的：$P(a \leq X \leq b) = \int_{a}^{b} f(x) \, dx$。

### 2.1 指数分布 (X ~ Exponential(λ))

#### 2.1.1 简介与用途

指数分布用于模拟独立随机事件发生的时间间隔。如果事件的发生遵循泊松过程 (即单位时间内事件发生的平均次数 $\lambda$是恒定的)，那么两次连续事件之间的时间间隔服从参数为 $\lambda$(速率参数) 的指数分布。有时也用尺度参数 $\beta = 1/\lambda$来参数化。

**常见用途**：
- 可靠性工程：设备或部件的无故障运行时间。
- 排队论：顾客到达服务台的时间间隔，或服务时间的模型。
- 放射性衰变：放射性粒子衰变所需的时间。

#### 2.1.2 数学性质

**表 2.1：指数分布性质总结 (参数为速率 $\lambda$)**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $\lambda > 0$(速率参数)     |
|               | $\beta = 1/\lambda > 0$(尺度参数) |
| 取值范围       | $x \in [0, \infty)$        |
| PDF           | $f(x; \lambda) = \lambda e^{-\lambda x}$for $x \ge 0$|
|               | $f(x; \beta) = \frac{1}{\beta} e^{-x/\beta}$for $x \ge 0$|
| CDF           | $F(x; \lambda) = 1 - e^{-\lambda x}$for $x \ge 0$|
|               | $F(x; \beta) = 1 - e^{-x/\beta}$for $x \ge 0$|
| 期望值 E[X]   | $\frac{1}{\lambda}$(或 $\beta$)          |
| 方差 Var(X)   | $\frac{1}{\lambda^2}$(或 $\beta^2$)       |
| 矩母函数 (MGF)| $M_X(t) = \frac{\lambda}{\lambda - t}$, for $t < \lambda$|
| 特征函数 (CF) | $\phi_X(t) = \frac{\lambda}{\lambda - i t}$|

**说明**：
- 无记忆性：$P(X > s + t \mid X > s) = P(X > t)$for $s, t \ge 0$。这意味着，如果一个设备已经无故障运行了 $s$小时，它还能至少再无故障运行 $t$小时的概率，与一个全新设备能至少无故障运行 $t$小时的概率相同。

#### 2.1.3 软件实现 (Stata)

Stata 的 `exponentialden()` 和 `exponential()` 函数使用尺度参数 `b`，它等于 $1/\lambda$。

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local lambda = 0.5
local scale_b = 1/`lambda'

* 计算 PDF (例: x=2)
display "PDF at x=2 for lambda=`lambda': " exponentialden(`scale_b', 2)

* 计算 CDF (例: x=3)
display "CDF at x=3 for lambda=`lambda': " exponential(`scale_b', 3)

* 生成随机变量 (生成10个)
clear
set obs 10
gen exp_rv = rexponential(`scale_b')
list exp_rv
```
</details>

#### 2.1.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = 0.5 * exp(-0.5*x), range(0 5) lcolor(blue) lpattern(solid))
(function y = 1.0 * exp(-1.0*x), range(0 5) lcolor(red) lpattern(dash))
(function y = 2.0 * exp(-2.0*x), range(0 5) lcolor(green) lpattern(dot)),
legend(order(1 "λ=0.5" 2 "λ=1.0" 3 "λ=2.0") pos(1) ring(0))
title("Exponential Distribution PDF for different λ values")
xtitle("x") ytitle("f(x)");
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = 1 - exp(-0.5*x), range(0 5) lcolor(blue) lpattern(solid))
(function y = 1 - exp(-1.0*x), range(0 5) lcolor(red) lpattern(dash))
(function y = 1 - exp(-2.0*x), range(0 5) lcolor(green) lpattern(dot)),
legend(order(1 "λ=0.5" 2 "λ=1.0" 3 "λ=2.0") pos(5) ring(0))
title("Exponential Distribution CDF for different λ values")
xtitle("x") ytitle("F(x)");
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-1-4.png)

#### 2.1.5 联系与延伸

指数分布是几何分布的连续对应物，两者都具有无记忆性。指数分布是伽马分布 $\text{Gamma}(\alpha, \lambda)$在形状参数 $\alpha = 1$时的特例 (即 $\text{Exponential}(\lambda) \equiv \text{Gamma}(1, \lambda)$)。它也是韦伯分布 (Weibull distribution) 在形状参数为1时的特例。多个独立同分布的指数随机变量之和服从爱尔朗分布或伽马分布。

### 2.2 拉普拉斯 (双指数) 分布 (X ~ Laplace(μ, b))

#### 2.2.1 简介与用途

拉普拉斯分布，也称为双指数分布，是一种连续概率分布，以其相对于均值的对称“尖峰”形状和比正态分布更重的尾部（即具有更高的峰度）而闻名。它由位置参数 $\mu$(决定分布的中心) 和尺度参数 $b > 0$(决定分布的离散程度或宽度) 来定义。

**常见用途**：
- 建模具有重尾特征的数据，例如金融市场中的某些资产回报率。
- 在稳健统计中，拉普拉斯分布与最小绝对偏差 (LAD) 回归相关，正如正态分布与最小二乘回归相关一样。LAD 回归对异常值不如最小二乘敏感。
- 信号处理和图像压缩中的差分信号建模。

#### 2.2.2 数学性质

**表 2.2：拉普拉斯分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $\mu \in (-\infty, \infty)$(位置参数), $b > 0$(尺度参数) |
| 取值范围       | $x \in (-\infty, \infty)$  |
| PDF           | $f(x; \mu, b) = \frac{1}{2b} \exp\left(-\frac{|x - \mu|}{b}\right)$|
| CDF           | $F(x; \mu, b) = \begin{cases} \frac{1}{2} \exp\left(\frac{x - \mu}{b}\right) & \text{if } x < \mu \\ 1 - \frac{1}{2} \exp\left(-\frac{x - \mu}{b}\right) & \text{if } x \geq \mu \end{cases}$|
| 期望值 E[X]   | $\mu$                       |
| 方差 Var(X)   | $2b^2$                     |
| 矩母函数 (MGF)| $M_X(t) = \frac{e^{\mu t}}{1 - b^2 t^2}$, for $|t| < \frac{1}{b}$|
| 特征函数 (CF) | $\phi_X(t) = \frac{e^{i \mu t}}{1 + b^2 t^2}$|

#### 2.2.3 软件实现 (Stata)

Stata 核心功能中没有直接提供拉普拉斯分布函数，需要手动计算或使用社区贡献的包（如 `ssc install extrand` 中的 `rlaplace`）。

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local mu = 0
local b = 1

* 计算 PDF (例: x=1.5)，手动计算
display "PDF at x=1.5 for mu=`mu', b=`b': " (1/(2*`b'))*exp(-abs(1.5 - `mu')/`b')

* 计算 CDF (例: x=1.5)，手动计算
* 因为 x >= mu, 使用第二个公式
display "CDF at x=1.5 for mu=`mu', b=`b': " 1 - (1/2)*exp(-(1.5 - `mu')/`b')

* 生成随机变量 (生成10个)
clear
set obs 10
gen lap_rv = rlaplace(`mu', `b')
list lap_rv
```
</details>

#### 2.2.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = (1/(2*0.5))*exp(-abs(x-0)/0.5), range(-5 5) lcolor(blue) lpattern(solid))
(function y = (1/(2*1.0))*exp(-abs(x-0)/1.0), range(-5 5) lcolor(red) lpattern(dash))
(function y = (1/(2*2.0))*exp(-abs(x-0)/2.0), range(-5 5) lcolor(green) lpattern(dot)),
legend(order(1 "b=0.5" 2 "b=1.0" 3 "b=2.0") pos(1) ring(0))
title("Laplace Distribution PDF for μ=0 and different b values")
xtitle("x") ytitle("f(x)");
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = cond(x<0, 0.5*exp(x/0.5), 1-0.5*exp(-x/0.5)), range(-5 5) lcolor(blue) lpattern(solid))
(function y = cond(x<0, 0.5*exp(x/1.0), 1-0.5*exp(-x/1.0)), range(-5 5) lcolor(red) lpattern(dash))
(function y = cond(x<0, 0.5*exp(x/2.0), 1-0.5*exp(-x/2.0)), range(-5 5) lcolor(green) lpattern(dot)),
legend(order(1 "b=0.5" 2 "b=1.0" 3 "b=2.0") pos(5) ring(0))
title("Laplace Distribution CDF for μ=0 and different b values")
xtitle("x") ytitle("F(x)");
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-2-4.png)

#### 2.2.5 联系与延伸

拉普拉斯分布可以看作是两个独立同分布的指数随机变量之差（经过适当的移位和缩放）。具体来说，如果 $X_1 \sim \text{Exponential}(\lambda)$ 和 $X_2 \sim \text{Exponential}(\lambda)$ 是独立的，那么 $Y = X_1 - X_2$ 服从参数为 $\mu=0$和 $b=1/\lambda$的拉普拉斯分布。拉普拉斯分布的最大似然估计 (MLE) 对于位置参数 $\mu$是样本中位数，而对于尺度参数 $b$是样本绝对偏差的平均值 ($\frac{1}{N}\sum|x_i - \text{median}|$)。

### 2.3 正态 (高斯) 分布 (X ~ N(μ, σ²))

#### 2.3.1 简介与用途

正态分布，也称高斯分布，是统计学中最重要和最广泛应用的连续概率分布之一。它由两个参数定义：均值 $\mu$，表示分布的中心位置；方差 $\sigma^2$(或标准差 $\sigma$)，表示分布的离散程度或宽度。其概率密度函数曲线呈对称的钟形。

**常见用途**：
- 模拟许多自然界和社会科学中的现象，如身高、体重、测量误差等。
- 中心极限定理的核心：大量独立随机变量之和（或均值）的分布在满足一定条件下趋向于正态分布，无论原始变量的分布是什么。
- 许多统计检验和推断方法（如 t 检验、ANOVA、线性回归中的误差项假设）的基础。

#### 2.3.2 数学性质

**表 2.3：正态分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $\mu \in (-\infty, \infty)$(均值), $\sigma^2 > 0$(方差) |
| 取值范围       | $x \in (-\infty, \infty)$  |
| PDF           | $f(x; \mu, \sigma^2) = \frac{1}{\sigma \sqrt{2\pi}} \exp\left(-\frac{(x - \mu)^2}{2\sigma^2}\right)$|
| CDF           | $F(x; \mu, \sigma^2) = \Phi\left(\frac{x - \mu}{\sigma}\right)$, 其中 $\Phi(z)$是标准正态分布 N(0,1) 的CDF |
| 期望值 E[X]   | $\mu$                       |
| 方差 Var(X)   | $\sigma^2$                 |
| 矩母函数 (MGF)| $M_X(t) = \exp\left(\mu t + \frac{\sigma^2 t^2}{2}\right)$|
| 特征函数 (CF) | $\phi_X(t) = \exp\left(i\mu t - \frac{\sigma^2 t^2}{2}\right)$|

**说明**：
- 标准正态分布：当 $\mu=0$且 $\sigma^2=1$(即 $\sigma=1$) 时，称为标准正态分布，通常用 $Z$表示。其 PDF 为 $\phi(z) = \frac{1}{\sqrt{2\pi}} e^{-z^2/2}$。
- 线性组合：独立正态随机变量的线性组合仍然服从正态分布。

#### 2.3.3 软件实现 (Stata)

Stata 的 CDF 函数 `normal()` 需要输入标准化值 Z = (x - μ) / σ。

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local mu = 10      // 均值 mu
local sigma = 2    // 标准差 sigma

* 计算 PDF (例: x=11)
display "PDF at x=11 for mu=`mu', sigma=`sigma': " normalden(11, `mu', `sigma')

* 计算 CDF (例: x=12)
display "CDF at x=12 for mu=`mu', sigma=`sigma': " normal((12 - `mu')/`sigma')

* 生成随机变量 (生成5个)
clear
set obs 5
gen norm_rv = rnormal(`mu', `sigma')
list norm_rv
```
</details>

#### 2.3.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
// Case 1: Varying sigma, fixed mu
twoway
(function y = normalden(x, 0, 0.5), range(-7 7) lcolor(blue) lpattern(solid))
(function y = normalden(x, 0, 1.0), range(-7 7) lcolor(red) lpattern(dash))
(function y = normalden(x, 0, 2.0), range(-7 7) lcolor(green) lpattern(dot)),
name(p1, replace) title("Normal PDF (μ=0, varying σ)")
legend(order(1 "σ=0.5" 2 "σ=1.0" 3 "σ=2.0"))
xtitle("x") ytitle("f(x)");

// Case 2: Varying mu, fixed sigma
twoway
(function y = normalden(x, -2, 1), range(-7 7) lcolor(blue) lpattern(solid))
(function y = normalden(x, 0, 1), range(-7 7) lcolor(red) lpattern(dash))
(function y = normalden(x, 2, 1), range(-7 7) lcolor(green) lpattern(dot)),
name(p2, replace) title("Normal PDF (σ=1, varying μ)")
legend(order(1 "μ=-2.0" 2 "μ=0.0" 3 "μ=2.0"))
xtitle("x") ytitle("f(x)");

graph combine p1 p2;
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
// Case 1: Varying sigma, fixed mu
twoway
(function y = normal((x-0)/0.5), range(-7 7) lcolor(blue) lpattern(solid))
(function y = normal((x-0)/1.0), range(-7 7) lcolor(red) lpattern(dash))
(function y = normal((x-0)/2.0), range(-7 7) lcolor(green) lpattern(dot)),
name(c1, replace) title("Normal CDF (μ=0, varying σ)")
legend(order(1 "σ=0.5" 2 "σ=1.0" 3 "σ=2.0"))
xtitle("x") ytitle("F(x)");

// Case 2: Varying mu, fixed sigma
twoway
(function y = normal((x-(-2))/1), range(-7 7) lcolor(blue) lpattern(solid))
(function y = normal((x-0)/1), range(-7 7) lcolor(red) lpattern(dash))
(function y = normal((x-2)/1), range(-7 7) lcolor(green) lpattern(dot)),
name(c2, replace) title("Normal CDF (σ=1, varying μ)")
legend(order(1 "μ=-2.0" 2 "μ=0.0" 3 "μ=2.0"))
xtitle("x") ytitle("F(x)");

graph combine c1 c2;
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-3-4.png)

#### 2.3.5 联系与延伸

中心极限定理 (Central Limit Theorem，CLT) 是统计理论中的一个基石。CLT 指出，从任何具有有限均值和有限方差的总体中抽取足够大的独立随机样本时，样本均值的分布将近似于正态分布，无论原始总体的分布形状如何。通常样本大小 $n \ge 30$被认为“足够大”，但具体取决于原始分布的偏度和峰度。这个定理的重要性在于，它允许我们对总体参数（如均值）进行统计推断，即使我们对总体的具体分布知之甚少，这使得正态分布在假设检验和置信区间构建中扮演核心角色。

### 2.4 Beta 分布 (X ~ Beta(a, b))

#### 2.4.1 简介与用途

Beta 分布是一种定义在区间 $[0, 1]$ 上的连续概率分布族，由两个正的形状参数 $a$(alpha) 和 $b$(beta) 控制。由于其取值范围限定在0到1之间，Beta 分布特别适用于对比例、百分比或概率等量进行建模。

**常见用途**：
- 对服从二项分布的事件的成功概率 $p$进行建模，特别是在贝叶斯统计中，Beta 分布是伯努利分布和二项分布成功概率 $p$的共轭先验分布。
- 项目管理（如 PERT 技术）中，用于描述任务完成时间的随机性（经过适当的缩放和平移后）。
- 建模任何取值范围在0和1之间的随机变量，如人口中某特征的比例、机器的效率等。
- 根据 $a$ 和 $b$ 的不同取值，Beta 分布可以呈现出多种形状（如U形、钟形、偏斜、均匀分布等）。

#### 2.4.2 数学性质

**表 2.4：Beta 分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $a > 0, b > 0$(形状参数)      |
| 取值范围       | $x \in [0, 1]$             |
| PDF           | $f(x; a, b) = \frac{x^{a-1} (1-x)^{b-1}}{B(a,b)}$, 其中 $B(a,b) = \frac{\Gamma(a)\Gamma(b)}{\Gamma(a+b)}$是 Beta 函数 |
| CDF           | $F(x; a, b) = I_x(a, b)$, 其中 $I_x(a,b)$是正则化不完全 Beta 函数 |
| 期望值 E[X]   | $\frac{a}{a + b}$           |
| 方差 Var(X)   | $\frac{ab}{(a + b)^2 (a + b + 1)}$|
| 众数 Mode(X)  | $\frac{a-1}{a+b-2}$for $a>1, b>1$. 若 $a<1, b<1$ 则为U形，众数为0和1. 若 $a=1, b=1$ 则为均匀分布. 其他情况需单独讨论. |

#### 2.4.3 软件实现 (Stata)

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local a = 2
local b = 5

* 计算 PDF (例: x=0.3)
display "PDF at x=0.3 for a=`a', b=`b': " betaden(`a', `b', 0.3)

* 计算 CDF (例: x=0.4)
display "CDF at x=0.4 for a=`a', b=`b': " ibeta(`a', `b', 0.4)

* 生成随机变量 (生成10个)
clear
set obs 10
gen beta_rv = rbeta(`a', `b')
list beta_rv
```
</details>

#### 2.4.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = betaden(0.5, 0.5, x), range(0.001 0.999))
(function y = betaden(5, 1, x), range(0.001 0.999))
(function y = betaden(1, 3, x), range(0.001 0.999))
(function y = betaden(2, 2, x), range(0.001 0.999))
(function y = betaden(2, 5, x), range(0.001 0.999))
(function y = betaden(1, 1, x), range(0.001 0.999)),
legend(pos(1) ring(0) col(1) order(1 "a=0.5,b=0.5" 2 "a=5,b=1" 3 "a=1,b=3" 4 "a=2,b=2" 5 "a=2,b=5" 6 "a=1,b=1"))
title("Beta Distribution PDF for different a and b values")
xtitle("x") ytitle("f(x)");
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = ibeta(0.5, 0.5, x), range(0 1))
(function y = ibeta(5, 1, x), range(0 1))
(function y = ibeta(1, 3, x), range(0 1))
(function y = ibeta(2, 2, x), range(0 1))
(function y = ibeta(2, 5, x), range(0 1))
(function y = ibeta(1, 1, x), range(0 1)),
legend(pos(5) ring(0) col(1) order(1 "a=0.5,b=0.5" 2 "a=5,b=1" 3 "a=1,b=3" 4 "a=2,b=2" 5 "a=2,b=5" 6 "a=1,b=1"))
title("Beta Distribution CDF for different a and b values")
xtitle("x") ytitle("F(x)");
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-4-4.png)

#### 2.4.5 联系与延伸

Beta 分布在贝叶斯推断中作为伯努利/二项分布成功概率的共轭先验分布非常有用。如果先验信念用 Beta(a,b) 表达，观察到 k 次成功和 n-k 次失败后，后验分布仍然是 Beta 分布，具体为 Beta(a+k, b+n-k)。Beta 分布与F分布和t分布也有关系。如果 $X \sim \text{Gamma}(\alpha_1, \theta)$和 $Y \sim \text{Gamma}(\alpha_2, \theta)$是独立的，则 $\frac{X}{X+Y} \sim \text{Beta}(\alpha_1, \alpha_2)$。当 $a=1, b=1$ 时，Beta(1,1) 就是 [0,1] 上的均匀分布。

### 2.5 卡方分布 (X ~ χ²(n))

#### 2.5.1 简介与用途

卡方分布（Chi-squared distribution），记作 $\chi^2(n)$，是由 $n$个独立的标准正态随机变量的平方和构成的分布。参数 $n$称为自由度（degrees of freedom），它必须是一个正整数。卡方分布是伽马分布的一个特例。

**常见用途**：
- **拟合优度检验**：例如，皮尔逊卡方检验，用于检验观察频数与期望频数之间是否存在显著差异。
- **列联表中的独立性检验**：检验两个分类变量是否独立。
- **方差推断**：对正态总体的方差进行置信区间的估计和假设检验。
- 在构建其他统计分布（如 F 分布和 t 分布）时作为组成部分。

#### 2.5.2 数学性质

**表 2.5：卡方分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $n \in \{1, 2, \dots\}$(自由度) |
| 取值范围       | $x \in [0, \infty)$        |
| PDF           | $f(x; n) = \frac{1}{2^{n/2} \Gamma(n/2)} x^{n/2 - 1} e^{-x/2}$, for $x \ge 0$|
| CDF           | $F(x; n) = P\left(\frac{n}{2}, \frac{x}{2}\right)$, 其中 $P(k,y) = \frac{\gamma(k,y)}{\Gamma(k)}$是正则化低阶不完全伽马函数 |
| 期望值 E[X]   | $n$                         |
| 方差 Var(X)   | $2n$                        |
| 矩母函数 (MGF)| $M_X(t) = (1 - 2t)^{-n/2}$, for $t < 1/2$|
| 特征函数 (CF) | $\phi_X(t) = (1 - 2it)^{-n/2}$|

**说明**：
- 可加性：如果 $X_1 \sim \chi^2(n_1)$和 $X_2 \sim \chi^2(n_2)$是独立的，则 $X_1 + X_2 \sim \chi^2(n_1 + n_2)$。
- 当自由度 $n$较大时，卡方分布 $\chi^2(n)$可以用正态分布 $N(n, 2n)$来近似。

#### 2.5.3 软件实现 (Stata)

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local df = 5  // 自由度 df

* 计算 PDF (例: x=4.5)
display "PDF at x=4.5 for df=`df': " chi2den(`df', 4.5)

* 计算 CDF (例: x=6.0, P(X<=6.0))
display "CDF at x=6.0 for df=`df': " chi2(`df', 6.0)

* 生成随机变量 (生成10个)
clear
set obs 10
gen chi2_rv = rchi2(`df')
list chi2_rv
```
</details>

#### 2.5.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = chi2den(1, x), range(0.01 20))
(function y = chi2den(2, x), range(0.01 20))
(function y = chi2den(3, x), range(0.01 20))
(function y = chi2den(5, x), range(0.01 20))
(function y = chi2den(10, x), range(0.01 20)),
legend(pos(1) ring(0) col(1) order(1 "df=1" 2 "df=2" 3 "df=3" 4 "df=5" 5 "df=10"))
title("Chi-squared Distribution PDF")
subtitle("for different degrees of freedom (df)")
xtitle("x") ytitle("f(x)") yscale(range(0 0.5));
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = chi2(1, x), range(0 20))
(function y = chi2(2, x), range(0 20))
(function y = chi2(3, x), range(0 20))
(function y = chi2(5, x), range(0 20))
(function y = chi2(10, x), range(0 20)),
legend(pos(5) ring(0) col(1) order(1 "df=1" 2 "df=2" 3 "df=3" 4 "df=5" 5 "df=10"))
title("Chi-squared Distribution CDF")
subtitle("for different degrees of freedom (df)")
xtitle("x") ytitle("F(x)");
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-5-4.png)

#### 2.5.5 联系与延伸

卡方分布是伽马分布 $\text{Gamma}(\alpha, \beta)$的一个特例：$\chi^2(n) \sim \text{Gamma}(\alpha = n/2, \beta = 1/2)$(如果伽马分布的第二个参数是速率参数) 或 $\text{Gamma}(\alpha = n/2, \theta = 2)$(如果伽马分布的第二个参数是尺度参数)。它在抽样理论中非常重要，特别是在涉及样本方差的推断时。例如，若从正态总体 $N(\mu, \sigma^2)$ 中抽取大小为 $m$ 的随机样本，其样本方差为 $S^2$，则统计量 $\frac{(m-1)S^2}{\sigma^2}$服从自由度为 $m-1$ 的卡方分布。

### 2.6 t 分布 (X ~ T(n))

#### 2.6.1 简介与用途

t 分布（Student's t-distribution）是一种形状与正态分布相似但尾部更重（即更易出现极端值）的连续概率分布。它由一个参数定义：自由度 $n$(degrees of freedom, df)，通常是一个正整数。当样本量较小且总体标准差未知时，t 分布用于估计正态总体的均值。

**常见用途**：
- **单样本 t 检验**：检验单个总体的均值是否等于某个特定值。
- **双样本 t 检验**：检验两个独立或配对总体的均值是否相等。
- **置信区间构建**：为总体均值构建置信区间。
- **回归分析**：检验回归系数的显著性。

#### 2.6.2 数学性质

**表 2.6：t 分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $n \in \{1, 2, \dots\}$(自由度) |
| 取值范围       | $x \in (-\infty, \infty)$  |
| PDF           | $f(x; n) = \frac{\Gamma\left(\frac{n+1}{2}\right)}{\sqrt{n\pi} \Gamma\left(\frac{n}{2}\right)} \left(1 + \frac{x^2}{n}\right)^{-\frac{n+1}{2}}$|
| CDF           | 没有简单的闭式解，通常通过数值方法或正则化不完全 Beta 函数计算。 |
| 期望值 E[X]   | $0$(for $n > 1$)        |
| 方差 Var(X)   | $\frac{n}{n - 2}$(for $n > 2$) |
| 众数 Mode(X)  | $0$|

**说明**：
- 当自由度 $n \to \infty$时，t 分布趋近于标准正态分布 N(0,1)。
- 对于较小的 $n$，t 分布的尾部比正态分布更厚，这意味着它赋予远离均值的值更高的概率。
- t 分布是对称的，以0为中心。

#### 2.6.3 软件实现 (Stata)

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local df = 10  // 自由度 df

* 计算 PDF (例: x=1.5)
display "PDF at x=1.5 for df=`df': " tden(`df', 1.5)

* 计算 CDF (例: x=2.0, P(X<=2.0))
display "CDF at x=2.0 for df=`df': " t(`df', 2.0)

* 生成随机变量 (生成5个)
clear
set obs 5
gen t_rv = rt(`df')
list t_rv
```
</details>

#### 2.6.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = tden(1, x), range(-5 5))
(function y = tden(2, x), range(-5 5))
(function y = tden(5, x), range(-5 5))
(function y = tden(30, x), range(-5 5))
(function y = normalden(x, 0, 1), range(-5 5) lpattern(dash) lcolor(black)),
legend(pos(1) ring(0) col(1) order(1 "t(df=1)" 2 "t(df=2)" 3 "t(df=5)" 4 "t(df=30)" 5 "Normal(0,1)"))
title("t-Distribution PDF for different degrees of freedom (df)")
xtitle("x") ytitle("f(x)") yscale(range(0 0.45));
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = t(1, x), range(-5 5))
(function y = t(2, x), range(-5 5))
(function y = t(5, x), range(-5 5))
(function y = t(30, x), range(-5 5))
(function y = normal(x), range(-5 5) lpattern(dash) lcolor(black)),
legend(pos(5) ring(0) col(1) order(1 "t(df=1)" 2 "t(df=2)" 3 "t(df=5)" 4 "t(df=30)" 5 "Normal(0,1)"))
title("t-Distribution CDF for different degrees of freedom (df)")
xtitle("x") ytitle("F(x)");
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-6-4.png)

#### 2.6.5 联系与延伸

t 分布可以通过一个标准正态随机变量 $Z \sim N(0,1)$和一个独立于 $Z$的卡方随机变量 $V \sim \chi^2(n)$来构造。具体地，随机变量 $T = \frac{Z}{\sqrt{V/n}}$服从自由度为 $n$的 t 分布。在实际应用中，这通常出现在当从正态总体 $N(\mu, \sigma^2)$中抽取小样本（$n_{sample} < 30$）时，用样本均值 $\bar{X}$和样本标准差 $S$来构建统计量 $\frac{\bar{X} - \mu}{S/\sqrt{n_{sample}}}$，该统计量服从自由度为 $n_{sample}-1$的 t 分布。

### 2.7 伽马分布 (X ~ Gamma(α, λ))

#### 2.7.1 简介与用途

伽马分布是一种非常灵活的连续概率分布，定义在正实数轴上。它由两个参数控制：形状参数 $\alpha > 0$(alpha, or k) 和速率参数 $\lambda > 0$(lambda, or $\beta$)。有时，伽马分布也用形状参数 $\alpha$和尺度参数 $\theta = 1/\lambda$来参数化。

**常见用途**：
- 模拟等待时间，例如，直到第 $\alpha$个独立泊松事件发生的总时间。
- 在可靠性工程中，模拟部件或系统的寿命。
- 在保险和金融领域，用于建模索赔金额或损失大小。
- 在气象学中，用于建模降雨量。
- 作为贝叶斯统计中一些参数（如泊松分布的均值 $\lambda$，或正态分布的精度即方差的倒数）的共轭先验分布。

#### 2.7.2 数学性质 (使用形状 $\alpha$和速率 $\lambda$)

**表 2.7：伽马分布性质总结**

| 性质          | 数学表达式 (速率参数 $\lambda$)                        | 数学表达式 (尺度参数 $\theta = 1/\lambda$) |
|---------------|---------------------------------------------------------|----------------------------------------------------------|
| 参数          | $\alpha > 0$(形状), $\lambda > 0$(速率)       | $\alpha > 0$(形状), $\theta > 0$(尺度)        |
| 取值范围       | $x \in [0, \infty)$                                | $x \in [0, \infty)$                                 |
| PDF           | $f(x; \alpha, \lambda) = \frac{\lambda^\alpha}{\Gamma(\alpha)} x^{\alpha - 1} e^{-\lambda x}$, for $x \ge 0$| $f(x; \alpha, \theta) = \frac{1}{\Gamma(\alpha)\theta^\alpha} x^{\alpha - 1} e^{-x/\theta}$, for $x \ge 0$|
| CDF           | $F(x; \alpha, \lambda) = P(\alpha, \lambda x) = \frac{\gamma(\alpha, \lambda x)}{\Gamma(\alpha)}$ (正则化低阶不完全伽马函数) | $F(x; \alpha, \theta) = P(\alpha, x/\theta) = \frac{\gamma(\alpha, x/\theta)}{\Gamma(\alpha)}$|
| 期望值 E[X]   | $\frac{\alpha}{\lambda}$                            | $\alpha\theta$                                      |
| 方差 Var(X)   | $\frac{\alpha}{\lambda^2}$                          | $\alpha\theta^2$                                    |
| 众数 Mode(X)  | $\frac{\alpha-1}{\lambda}$for $\alpha \ge 1$. 0 if $0 < \alpha < 1$. | $(\alpha-1)\theta$for $\alpha \ge 1$. 0 if $0 < \alpha < 1$. |
| MGF           | $M_X(t) = \left(\frac{\lambda}{\lambda - t}\right)^\alpha$, for $t < \lambda$| $M_X(t) = (1 - \theta t)^{-\alpha}$, for $t < 1/\theta$|

**说明**：
- Stata 的 `gammaden(a,b,g,x)` 和 `rgamma(a,b)` 使用形状参数 `a` ($\alpha$) 和**尺度**参数 `b` ($\theta$)。
- Stata 的 `gammap(a,x)` 计算的是**标准**伽马分布（尺度为1）的 CDF。要计算任意尺度 `b` 的 CDF，需使用 `gammap(a, x/b)`。
- $\Gamma(\alpha) = \int_0^\infty z^{\alpha-1}e^{-z}dz$是伽马函数。
- $\gamma(\alpha, x) = \int_0^x t^{\alpha-1}e^{-t}dt$是低阶不完全伽马函数。
- 可加性：如果 $X_i \sim \text{Gamma}(\alpha_i, \lambda)$是独立的 (i=1,2)，则 $X_1+X_2 \sim \text{Gamma}(\alpha_1+\alpha_2, \lambda)$(速率参数相同)。

#### 2.7.3 软件实现 (Stata)

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local alpha = 3    // 形状参数 a (alpha)
local lambda = 0.5 // 速率参数 lambda
local scale = 1/`lambda' // 尺度参数 b (theta)

* 计算 PDF (例: x=5)，使用形状 a 和尺度 b
display "PDF at x=5 for alpha=`alpha', scale=`scale': " gammaden(`alpha', `scale', 0, 5)

* 计算 CDF (例: x=7)，使用 gammap(a, x/b)
display "CDF at x=7 for alpha=`alpha', scale=`scale': " gammap(`alpha', 7/`scale')

* 生成随机变量 (生成10个)，使用形状 a 和尺度 b
clear
set obs 10
gen gamma_rv = rgamma(`alpha', `scale')
list gamma_rv
```
</details>

#### 2.7.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
// Case 1: Varying shape (alpha), fixed scale
twoway
(function y = gammaden(1, 1, 0, x), range(0.01 20))
(function y = gammaden(2, 1, 0, x), range(0.01 20))
(function y = gammaden(3, 1, 0, x), range(0.01 20))
(function y = gammaden(5, 1, 0, x), range(0.01 20)),
name(p1, replace) title("Gamma PDF (scale=1, varying α)")
legend(pos(1) ring(0) col(1) order(1 "α=1.0" 2 "α=2.0" 3 "α=3.0" 4 "α=5.0"))
xtitle("x") ytitle("f(x)");

// Case 2: Fixed shape (alpha), varying scale
twoway
(function y = gammaden(2, 1/0.5, 0, x), range(0.01 20)) // lambda=0.5 -> scale=2
(function y = gammaden(2, 1/1.0, 0, x), range(0.01 20)) // lambda=1.0 -> scale=1
(function y = gammaden(2, 1/2.0, 0, x), range(0.01 20)), // lambda=2.0 -> scale=0.5
name(p2, replace) title("Gamma PDF (α=2, varying λ)")
legend(pos(1) ring(0) col(1) order(1 "λ=0.5 (scale=2.0)" 2 "λ=1.0 (scale=1.0)" 3 "λ=2.0 (scale=0.5)"))
xtitle("x") ytitle("f(x)");

graph combine p1 p2;
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
// Case 1: Varying shape (alpha), fixed scale
twoway
(function y = gammap(1, x/1), range(0 20))
(function y = gammap(2, x/1), range(0 20))
(function y = gammap(3, x/1), range(0 20))
(function y = gammap(5, x/1), range(0 20)),
name(c1, replace) title("Gamma CDF (scale=1, varying α)")
legend(pos(5) ring(0) col(1) order(1 "α=1.0" 2 "α=2.0" 3 "α=3.0" 4 "α=5.0"))
xtitle("x") ytitle("F(x)");

// Case 2: Fixed shape (alpha), varying scale
twoway
(function y = gammap(2, x/(1/0.5)), range(0 20)) // lambda=0.5 -> scale=2
(function y = gammap(2, x/(1/1.0)), range(0 20)) // lambda=1.0 -> scale=1
(function y = gammap(2, x/(1/2.0)), range(0 20)), // lambda=2.0 -> scale=0.5
name(c2, replace) title("Gamma CDF (α=2, varying λ)")
legend(pos(5) ring(0) col(1) order(1 "λ=0.5 (scale=2.0)" 2 "λ=1.0 (scale=1.0)" 3 "λ=2.0 (scale=0.5)"))
xtitle("x") ytitle("F(x)");

graph combine c1 c2;
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-7-4.png)

#### 2.7.5 联系与延伸

伽马分布是一个非常通用的分布族，它包含了其他几个重要的分布作为特例：
- **指数分布**：当形状参数 $\alpha = 1$时，伽马分布 $\text{Gamma}(1, \lambda)$就是速率为 $\lambda$的指数分布 $\text{Exponential}(\lambda)$。
- **爱尔朗分布**：当形状参数 $\alpha$是一个正整数（通常记为 $k$）时，伽马分布 $\text{Gamma}(k, \lambda)$就是爱尔朗分布 $\text{Erlang}(k, \lambda)$。它表示 $k$个独立的、速率均为 $\lambda$的指数随机变量之和的分布。
- **卡方分布**：当形状参数 $\alpha = n/2$(其中 $n$是自由度) 且速率参数 $\lambda = 1/2$(或者说尺度参数 $\theta = 2$) 时，伽马分布 $\text{Gamma}(n/2, 1/2)$就是自由度为 $n$的卡方分布 $\chi^2(n)$。

形状参数 $\alpha$通常可以解释为“事件发生的数量”，而速率参数 $\lambda$(或尺度参数 $\theta$) 则与事件发生的快慢或平均间隔有关。

### 2.8 爱尔朗分布 (X ~ Erlang(k, λ))

#### 2.8.1 简介与用途

爱尔朗分布是伽马分布的一个特例，其形状参数 $k$(对应伽马分布中的 $\alpha$) 必须是一个正整数。速率参数 $\lambda$(或尺度参数 $\theta = 1/\lambda$) 与伽马分布中的定义相同。爱尔朗分布描述了 $k$个独立的、具有相同速率 $\lambda$的指数随机事件的总等待时间。

**常见用途**：
- **排队论**：模拟需要经过 $k$个独立同分布指数服务阶段的总服务时间，或者模拟第 $k$个顾客到达的时间（如果顾客到达遵循泊松过程）。
- **可靠性工程**：模拟一个由 $k$个部件串联组成的系统，当一个部件失效后立即被同类部件替换，直到第 $k$个部件失效的总时间（假设每个部件寿命服从指数分布）。
- 电话工程（由 A.K. Erlang 首次提出用于建模电话呼叫）。

#### 2.8.2 数学性质 (使用形状 $k$和速率 $\lambda$)

**表 2.8：爱尔朗分布性质总结**

| 性质          | 数学表达式 (速率参数 $\lambda$)                        | 数学表达式 (尺度参数 $\theta = 1/\lambda$) |
|---------------|---------------------------------------------------------|----------------------------------------------------------|
| 参数          | $k \in \{1, 2, \dots\}$(形状), $\lambda > 0$(速率) | $k \in \{1, 2, \dots\}$(形状), $\theta > 0$(尺度) |
| 取值范围       | $x \in [0, \infty)$                                | $x \in [0, \infty)$                                 |
| PDF           | $f(x; k, \lambda) = \frac{\lambda^k x^{k-1} e^{-\lambda x}}{(k-1)!}$, for $x \ge 0$| $f(x; k, \theta) = \frac{x^{k-1} e^{-x/\theta}}{\theta^k (k-1)!}$, for $x \ge 0$|
| CDF           | $F(x; k, \lambda) = 1 - \sum_{j=0}^{k-1} \frac{e^{-\lambda x} (\lambda x)^j}{j!}$ (与泊松分布CDF相关) | $F(x; k, \theta) = 1 - \sum_{j=0}^{k-1} \frac{e^{-x/\theta} (x/\theta)^j}{j!}$|
| 期望值 E[X]   | $\frac{k}{\lambda}$                            | $k\theta$                                      |
| 方差 Var(X)   | $\frac{k}{\lambda^2}$                          | $k\theta^2$                                    |
| 众数 Mode(X)  | $\frac{k-1}{\lambda}$for $k \ge 1$. | $(k-1)\theta$for $k \ge 1$. |
| MGF           | $M_X(t) = \left(\frac{\lambda}{\lambda - t}\right)^k$, for $t < \lambda$| $M_X(t) = (1 - \theta t)^{-k}$, for $t < 1/\theta$|

**说明**：
- 由于 $k$是整数，伽马函数 $\Gamma(k) = (k-1)!$。
- CDF 的形式表明，如果 $X \sim \text{Erlang}(k, \lambda)$，则 $P(X \le x)$等于一个泊松随机变量 $Y \sim \text{Poisson}(\lambda x)$取值至少为 $k$的概率，即 $P(X \le x) = P(Y \ge k)$。

#### 2.8.3 软件实现 (Stata)

爱尔朗分布是伽马分布的特例，因此直接使用伽马分布的函数，确保形状参数为整数即可。

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local k = 4       // 形状参数 k (整数)
local lambda = 0.8 // 速率参数 lambda
local scale = 1/`lambda' // 尺度参数

* 计算 PDF (例: x=5)
display "Erlang PDF at x=5 for k=`k', scale=`scale': " gammaden(`k', `scale', 0, 5)

* 计算 CDF (例: x=6)
display "Erlang CDF at x=6 for k=`k', scale=`scale': " gammap(`k', 6/`scale')

* 生成随机变量 (生成10个)
clear
set obs 10
gen erlang_rv = rgamma(`k', `scale')
list erlang_rv
```
</details>

#### 2.8.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
// Case 1: Varying shape (k), fixed scale
twoway
(function y = gammaden(1, 1, 0, x), range(0.01 15))
(function y = gammaden(2, 1, 0, x), range(0.01 15))
(function y = gammaden(5, 1, 0, x), range(0.01 15))
(function y = gammaden(10, 1, 0, x), range(0.01 15)),
name(p1, replace) title("Erlang PDF (λ=1, varying k)")
legend(pos(1) ring(0) col(1) order(1 "k=1" 2 "k=2" 3 "k=5" 4 "k=10"))
xtitle("x") ytitle("f(x)");

// Case 2: Fixed shape (k), varying rate (lambda)
twoway
(function y = gammaden(3, 1/0.5, 0, x), range(0.01 15)) // scale=2
(function y = gammaden(3, 1/1.0, 0, x), range(0.01 15)) // scale=1
(function y = gammaden(3, 1/2.0, 0, x), range(0.01 15)), // scale=0.5
name(p2, replace) title("Erlang PDF (k=3, varying λ)")
legend(pos(1) ring(0) col(1) order(1 "λ=0.5" 2 "λ=1.0" 3 "λ=2.0"))
xtitle("x") ytitle("f(x)");

graph combine p1 p2;
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
// Case 1: Varying shape (k), fixed scale
twoway
(function y = gammap(1, x/1), range(0 15))
(function y = gammap(2, x/1), range(0 15))
(function y = gammap(5, x/1), range(0 15))
(function y = gammap(10, x/1), range(0 15)),
name(c1, replace) title("Erlang CDF (λ=1, varying k)")
legend(pos(5) ring(0) col(1) order(1 "k=1" 2 "k=2" 3 "k=5" 4 "k=10"))
xtitle("x") ytitle("F(x)");

// Case 2: Fixed shape (k), varying rate (lambda)
twoway
(function y = gammap(3, x/(1/0.5)), range(0 15)) // scale=2
(function y = gammap(3, x/(1/1.0)), range(0 15)) // scale=1
(function y = gammap(3, x/(1/2.0)), range(0 15)), // scale=0.5
name(c2, replace) title("Erlang CDF (k=3, varying λ)")
legend(pos(5) ring(0) col(1) order(1 "λ=0.5" 2 "λ=1.0" 3 "λ=2.0"))
xtitle("x") ytitle("F(x)");

graph combine c1 c2;
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-8-4.png)

#### 2.8.5 联系与延伸

爱尔朗分布是伽马分布中形状参数为整数的特例。当 $k=1$ 时，爱尔朗分布 $\text{Erlang}(1, \lambda)$简化为指数分布 $\text{Exponential}(\lambda)$。随着 $k$ 的增加，爱尔朗分布的形状从指数分布的单调递减变为具有明显峰值的钟形，并逐渐趋向对称。当 $k$ 很大时，根据中心极限定理（因为爱尔朗变量是 $k$ 个独立同分布指数变量之和），爱尔朗分布可以用正态分布 $N(k/\lambda, k/\lambda^2)$来近似。爱尔朗分布的 CDF 与泊松分布的 PMF/CDF 之间存在密切关系，这在排队论中有重要应用。

### 2.9 均匀分布 (X ~ Uniform(a, b))

#### 2.9.1 简介与用途

均匀分布（Uniform distribution），也称矩形分布，是一种简单的概率分布，其中所有等长度的子区间内的结果具有相同的概率。它由两个参数定义：下界 $a$和上界 $b$，其中 $a < b$。随机变量 $X$在区间 $[a, b]$内取任何值的概率密度是恒定的，而在该区间之外为零。

**常见用途**：
- **随机数生成**：计算机生成其他分布的随机数时，通常以标准均匀分布 $\text{Uniform}(0,1)$作为基础（例如通过逆变换法）。
- **无信息先验**：在贝叶斯统计中，当对某个参数的先验知识非常有限时，有时会使用均匀分布作为无信息或“平坦”先验（尽管这在某些情况下可能不合适或导致不当后验）。
- 模拟某些物理过程，例如一个圆盘上随机一点到圆心的距离的平方（经过变换后）。
- 对称的舍入误差建模。

#### 2.9.2 数学性质

**表 2.9：均匀分布性质总结**

| 性质          | 数学表达式                        |
|---------------|----------------------------------|
| 参数          | $a, b \in (-\infty, \infty)$with $b > a$|
| 取值范围       | $x \in [a, b]$             |
| PDF           | $f(x; a, b) = \begin{cases} \frac{1}{b - a} & \text{for } a \le x \le b \\ 0 & \text{otherwise} \end{cases}$|
| CDF           | $F(x; a, b) = \begin{cases} 0 & \text{for } x < a \\ \frac{x - a}{b - a} & \text{for } a \le x \le b \\ 1 & \text{for } x > b \end{cases}$|
| 期望值 E[X]   | $\frac{a + b}{2}$           |
| 方差 Var(X)   | $\frac{(b - a)^2}{12}$      |
| 矩母函数 (MGF)| $M_X(t) = \frac{e^{tb} - e^{ta}}{t(b - a)}$for $t \neq 0$, $M_X(0) = 1$|
| 特征函数 (CF) | $\phi_X(t) = \frac{e^{itb} - e^{ita}}{it(b - a)}$for $t \neq 0$, $\phi_X(0) = 1$|

#### 2.9.3 软件实现 (Stata)

Stata 没有直接的函数用于计算 U(a, b) 的 PDF 或 CDF，需要手动实现。`runiform()` 生成 U(0,1) 随机数。

<details>
<summary>Stata 实现代码 (PDF, CDF, RVS)</summary>

```stata
* 参数设置
local a = 2
local b = 8

* 计算 PDF (例: x=5)
display "PDF at x=5 for U(`a', `b'): " 1/(`b' - `a')
* PDF at x=1 (区间外，为0)
display "PDF at x=1 for U(`a', `b'): " 0

* 计算 CDF (例: x=6)
display "CDF at x=6 for U(`a', `b'): " (6 - `a')/(`b' - `a')

* 生成随机变量 (生成10个)
clear
set obs 10
gen unif_rv = `a' + (`b' - `a') * runiform()
list unif_rv
```
</details>

#### 2.9.4 分布函数绘图 (Stata)

<details>
<summary>Stata 绘图代码 (PDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = 1/(1-0), range(0 1))
(function y = 1/(1-(-1)), range(-1 1))
(function y = 1/(5-2), range(2 5)),
legend(pos(1) ring(0) order(1 "U(0,1)" 2 "U(-1,1)" 3 "U(2,5)"))
title("Uniform Distribution PDF for different (a, b) ranges")
xtitle("x") ytitle("f(x)") yscale(range(0 1.1)) xscale(range(-2 6));
#delimit cr
```
</details>

<details>
<summary>Stata 绘图代码 (CDF 示例)</summary>

```stata
#delimit ;
twoway
(function y = cond(x<0, 0, cond(x>1, 1, (x-0)/(1-0))), range(-2 6))
(function y = cond(x<-1, 0, cond(x>1, 1, (x-(-1))/(1-(-1)))), range(-2 6))
(function y = cond(x<2, 0, cond(x>5, 1, (x-2)/(5-2))), range(-2 6)),
legend(pos(5) ring(0) order(1 "U(0,1)" 2 "U(-1,1)" 3 "U(2,5)"))
title("Uniform Distribution CDF for different (a, b) ranges")
xtitle("x") ytitle("F(x)");
#delimit cr
```
</details>

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%9F%BA%E7%A1%80%E6%A6%82%E7%8E%87%E5%88%86%E5%B8%83%E8%AF%A6%E8%A7%A3%EF%BC%9A%E7%90%86%E8%AE%BA%E4%B8%8E%20Stata%20%E5%AE%9E%E7%8E%B0-Stata-2-9-4.png)

#### 2.9.5 联系与延伸

标准均匀分布 $\text{Uniform}(0,1)$是许多随机数生成算法的核心。逆变换法 (Inverse Transform Sampling) 是一种重要的技术，它利用均匀分布的 CDF 来从任何其他概率分布生成随机数，前提是该分布的 CDF 的逆函数已知或易于计算。在给定区间上，均匀分布是具有最大熵的连续分布，这意味着在仅知道变量取值范围的情况下，均匀分布是最不带有额外假设或信息的选择。


## 参考资料

- Pishro-Nik, H. (2014). *Introduction to Probability, Statistics, and Random Processes*. Kappa Research LLC. Available at: [https://www.probabilitycourse.com](https://www.probabilitycourse.com).
- Casella, G., & Berger, R. L. (2002). *Statistical Inference*. Duxbury Press.
- Hogg, R. V., McKean, J., & Craig, A. T. (2013). *Introduction to Mathematical Statistics* (7th ed.). Pearson.
- StataCorp. (2023). *Stata Base Reference Manual*. Stata Press. [https://www.stata.com/manuals/](https://www.stata.com/bookstore/users-guide/)
- Mitchell, M. N. (2012). *A Visual Guide to Stata Graphics* (3rd ed.). Stata Press. [https://www.stata.com/bookstore/visual-guide-to-stata-graphics/](https://www.stata.com/bookstore/visual-guide-to-stata-graphics/)


&emsp;

## 4. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh 统计证据 独立与不相关 分布 经济显著性 如何检查 , md nocat`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 
  
  - 侯新烁, 连玉君, 2020, [快速呈现常用分布临界值表](https://www.lianxh.cn/details/362.html), 连享会 No.362.
  - 修博文, 2024, [你的研究是可信的吗？本文将教你如何检查](https://www.lianxh.cn/details/1342.html), 连享会 No.1342.
  - 刘潍嘉, 2024, [偏态分布数据的回归模型选择](https://www.lianxh.cn/details/1405.html), 连享会 No.1405.
  - 吕卓阳, 2021, [Stata 论文复现：Temperature Shocks and Economic Growth](https://www.lianxh.cn/details/730.html), 连享会 No.730.
  - 崔颖, 2020, [Stata：用负二项分布预测蚊子存活率](https://www.lianxh.cn/details/399.html), 连享会 No.399.
  - 汪京, 2024, [multihistogram-多变量直方图](https://www.lianxh.cn/details/1457.html), 连享会 No.1457.
  - 王恩泽, 2020, [reldist-相对分布：分布差异分析和检验](https://www.lianxh.cn/details/441.html), 连享会 No.441.
  - 罗丹, 2025, [不要“拍脑袋”建模：如何有效连接统计证据与理论解释](https://www.lianxh.cn/details/1590.html), 连享会 No.1590.
  - 苗妙, 2022, [抛弃p值？经济显著性与统计显著性](https://www.lianxh.cn/details/990.html), 连享会 No.990.
  - 连玉君, 2022, [Stata绘图：唯美的函数图-自定义水平附加线和竖直附加线](https://www.lianxh.cn/details/1064.html), 连享会 No.1064.
  - 陈勇吏, 2020, [Stata程序：Monte-Carlo-模拟之产生符合特定分布的随机数](https://www.lianxh.cn/details/343.html), 连享会 No.343.
  - 陈卓然, 2022, [Stata：多元正态t截断分布的命令](https://www.lianxh.cn/details/883.html), 连享会 No.883.
  - 陈卓然, 2022, [Stata：局部回归分布估计量-lpdensity](https://www.lianxh.cn/details/850.html), 连享会 No.850.
  - 陈卓然, 2022, [Stata：自回归分布滞后模型简介 (ARDL)](https://www.lianxh.cn/details/999.html), 连享会 No.999.
  - 陈治中, 2020, [品头论足-distcomp：组间分布差异检验](https://www.lianxh.cn/details/488.html), 连享会 No.488.
  - 雷诺, 2023, [因果推断：傻傻分不清-独立与不相关等价吗？](https://www.lianxh.cn/details/1274.html), 连享会 No.1274.
