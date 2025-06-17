# B806：Stata：pretrends-更严谨的平行趋势检验
&emsp;

> **作者：** 赵文琦 (浙江大学)        
> **E-mail:**  <wenqi.zhao@zju.edu.cn>   

> **Source:** 本推文主要参考了以下论文，特此致谢！
>- [stata-pretrends](https://github.com/mcaceresb/stata-pretrends)
>- Roth, Jonathan. "Pretest with Caution: Event-Study Estimates after Testing for Parallel Trends." American Economic Review: Insights 4, no. 3 (2022): 305-22. [-PDF-](https://jonathandroth.github.io/assets/files/roth_pretrends_testing.pdf)
>- He, Guojun, and Shaoda Wang. 2017. "Do College Graduates Serving as Village Officials Help Rural China?" American Economic Journal: Applied Economics 9 (4): 186–215.[-PDF-](https://pubs.aeaweb.org/doi/pdfplus/10.1257/app.20160079)
>- [郭楚玉，2022，DID-倍分法：事前趋势检验的局限性和诊断](https://www.lianxh.cn/details/1103.html)


- Title：Stata：pretrends-更严谨的平行趋势检验
- keywords：事前趋势检验，倍分法，双重差分法，Pre-trend Test



## 1. 引言：为什么需要事前趋势检验 (Pre-trend Test) 的功效分析？

在双重差分 (DID) 研究中，平行趋势假设是我们得出因果推断的基础。通常，我们通过检验处理前的系数是否显著，来判断平行趋势假设是否成立。但这种做法存在一个问题：**如果事前趋势真的存在，我们的检验有多大把握能发现它？**

Roth (2022) 提出，事前趋势检验应具备足够的功效 (power) 。换言之，如果平行趋势假设确实被违背，我们的检验应该能以较高概率检测出来。 `pretrends` 命令正是基于这一思想，帮助研究者评估事前趋势检验的功效，并可视化潜在的趋势违背。

## 2. pretrends命令的理论基础
识别真实、有效政策效应的困难是数据缺失。在任何时间，研究对象可能处于两种潜在结果中的任何一种，但不可能同时处于两种状态。因此我们需要控制组，作为处理组的反事实状态 (无法观测到、数据缺失) 的替代。一个“良好的”控制组需要和处理组同质，即两者越像越好。Pre-trend test 就是检验处理组和控制组是否“相像”的一个检验。

通常来说，我们希望事前趋势的系数都显著的不异于 0，从而得出结论：因变量的事前趋势在处理组和控制组之间是很相像的，因此控制组是“良好的”。然而，Roth (2022) 这篇文章指出，现有的 pre-trend test 有两个问题。

- 第一个问题是检验本身的低功效性 (low power) ，即事前平行趋势不满足时，pre-trend test 能检测出事前平行趋势不满足的概率很低。针对该问题，`pretrends` 命令可通过计算事前功效来评估事前趋势检验的可靠性，即平行趋势假设需要被违背到何种程度，才能使我们以特定概率检测到它。这类似于RCT中的最小可检测效应 (MDE)。

- 第二个问题是样本的选择性偏误。作者认为能通过平行趋势检验的样本，只是整个 DGP (data generating process) 里的某一类样本 (selected sample) 。Selected sample 会对政策估计系数造成进一步的统计偏误 (statistical distortion)。针对该问题，`pretrends` 命令可计算在“未发现显著事前趋势”条件下系数的期望值。此外，该命令还可以通过贝叶斯因子 (Bayes Factor) 评估，得出如果未发现显著事前趋势，我们应该在多大程度上对平行趋势成立有信心。

接下来，本文将通过实例说明上述两个问题。

>**Note：** 如果你担心违背平行趋势假设，也可考虑 [HonestDiD](https://github.com/mcaceresb/stata-honestdid/?tab=readme-ov-file#example-usage----medicaid-expansions)提供的敏感性分析方法。HonestDiD不依赖事前检验显著性，而是假设处理后平行趋势违背相对于处理前不会“太大”，构建考虑了这种不确定性的置信区间。

## 2.1 低功效性 (low power)

首先，是事前趋势检验本身的低功效性 (low power) 问题。我们假设三种事前处理组和控制组之间的相对趋势：分别是平行事前趋势、线性事前趋势和二次函数事前趋势。

## 2.1.1 平行事前趋势 (No Pre-trend)

如下图所示，我们认为绿色圆点代表了真实的 pre-trend 情况，即处理组和对照组的 Y 变量拥有相同的事前平行趋势。Pre-trend test 的结果显示 P-value = 0.81。我们无法拒绝原假设： 黑色三角 (回归估计系数) 等于绿色圆点。这是理想状况，事后期 (0-3 期) 的系数反映了真实的政策处理效应。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig03.png)

## 2.1.2 线性事前趋势 (Linear Pre-trend)

如下图所示，我们认为红色圆点代表了真实的 pre-trend 情况，即处理组和控制组的 Y 变量的事前相对趋势满足线性关系。Pre-trend test 的原假设为黑色三角 (回归估计系数) 等于红色圆点。作者有意让这个 pre-trend test 的 P-value 也等于 0.81，即在这种情况下，我们同样无法拒绝原假设。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig04.png)

根据事前趋势的三个红色圆点可以外推出一条直线。在这种情况下，事后期 (0-3 期) 的系数**高估**了政策处理效应。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig05.png)

## 2.1.3 二次函数事前趋势 (Quadratic Pre-trend)

如下图所示，此时蓝色圆点代表了真实的 pre-trend 情况，即处理组和控制组的 Y 变量的事前相对趋势满足二次函数关系。Pre-trend test 的原假设为黑色三角 (回归估计系数) 等于蓝色圆点。P-value 同样等于 0.81，即在这种情况下，我们仍无法拒绝原假设。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig06.png)

根据事前趋势的三个蓝色圆点可以外推出一条抛物线。在这种情况下，事后期 (0-3 期) 的系数**低估**了政策处理效应。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig07.png)

上述三种 pre-trend 情境告诉我们，在进行 Pre-trend test 时，我们无法拒绝平行事前趋势，但同样也无法拒绝线性事前趋势或二次函数事前趋势——而后两者都会导致事后期的估计系数偏误。这就是事前趋势检验本身的低功效性 (low power) 的体现。

>**Note：** 当事前期的期数较少时，更容易出现事前趋势检验低功效性问题。

## 2.2 样本的选择性偏误

Pre-trend test 存在的第二个问题是“看似”**通过**前期趋势检验所导致的额外统计偏差。作者认为，能通过平行趋势检验的样本是整个 DGP 里面某一类特殊的样本 (selected sample)， 这会进一步导致偏误。

现在我们举一个简单的三期 DID 模型的例子。假设有三期 t∈{−1,0,1} ，处理组在 t=0 和 t=1 期受处理。假设没有处理效应。处理组相对控制组的变化趋势是一个线性关系，系数为 δ 。

下图展示了当 δ=3 时，处理组和控制组 Y 变量的相对变化趋势。注意，因为假设没有处理效应，控制组 Y 变量的整体均值为 0，而处理组 Y 变量随时间变化，是一条斜率为 3 的线段。通过模拟，我们可以从这个总体中抽取出很多种样本结果，以灰色线表示。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig08.png)

在这些随机抽样中，蓝色高亮线段是那些能通过 Pre-trend test 检验的样本。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig09.png)

可以发现，那些在事前期比较平缓 (即事前期系数更接近于0) 的样本更容易通过检验。这就是通过事前趋势检验而产生的额外统计偏差，它会导致无法估计出真实的、有效的政策处理效应。

## 3. pretrends命令使用说明
## 3.1 命令安装

推荐从GitHub直接安装最新版本：

```stata
local github https://raw.githubusercontent.com
net install pretrends, from(`github'/mcaceresb/stata-pretrends/main) replace
```

也可以下载后本地安装：

```stata
*先下载：https://github.com/mcaceresb/stata-pretrends
cap noi net uninstall pretrends // 如已安装，先卸载
net install pretrends, from(`c(pwd)'/stata-pretrends-main) // 从当前工作目录安装
```

使用 `pretrends` 前，你需要：

- 已完成一个事件研究回归，并能提取系数向量和协方差矩阵
- 确定系数对应的相对时间周期
- 安装 `coefplot` 包 (用于可视化，可选)

>**Note：** `pretrends` 可处理任何渐近正态估计器的事件研究结果，包括Callaway and Sant'Anna (2020)和Sun and Abraham (2020)的方法，只要系数和协方差矩阵保存在 `e(b)` 和 `e(V)` 中。

## 3.2 语法格式

`pretrends` 命令有两个主要的子命令，下面详细介绍其语法和选项。

## 3.2.1 计算特定功效下的事前趋势斜率

```stata
pretrends power <power_level> [, pre(varlist) post(varlist)]
```

**核心选项：**

- `<power_level>`：所需功效水平 (0-1之间) ，如 `0.8` 表示80%功效
- `pre(varlist)`：指定事件研究中事前处理系数的位置或变量列表
- `post(varlist)`：指定事件研究中后置处理系数的位置或变量列表

**返回结果：**

- `r(slope)`：计算出的线性事前趋势斜率 (正值，表示幅度)
- `r(Power)`：指定的功效水平

## 3.2.2 假设趋势下的功效分析与可视化

```stata
pretrends [, numpre(integer) b(matrix_name) v(matrix_name) slope(num) delta(matrix_name) time(numlist) ref(num) nocoefplot coefplot]
```

**核心选项：**

- `numpre(integer)`：事前处理系数数量
- `b(matrix_name)`：包含事件研究系数的矩阵名称
- `v(matrix_name)`：包含系数协方差矩阵的名称

**假设趋势选项：**

- `slope(num)`：假设线性事前趋势的斜率，如 `slope(0.052)`
- `delta(matrix_name)`：包含任意非线性假设趋势的矩阵

**时间周期指定选项：**

- `time(numlist)`：系数对应的相对时间周期列表，如 `time(-4(1)3)`
- `ref(num)`：基准期，如 `ref(-1)`

**可视化选项：**

- `coefplot`： (默认) 绘制事件研究图并叠加假设趋势
- `nocoefplot`：跳过可视化

**返回结果：**

- `r(Power)`：在假设的事前趋势下，我们发现显著事前趋势的概率。数值越高表明在假设的趋势下，我们更有可能发现显著的事前系数。
- `r(BF)`：贝叶斯因子，代表在假设的事前趋势下相对于平行趋势下通过事前趋势检验的概率之比。贝叶斯因子越小，在观察到事前趋势不显著 (即通过事前趋势检验) 时，我们就越倾向于支持平行趋势 (相对于假设趋势) 成立。
- `r(LR)`：似然比，代表在假设趋势下观察到的系数的似然值与在平行趋势下观察到的系数的似然值之比。如果这个值很小，那么在数据中观察到的事件研究系数在平行趋势下出现的可能性要远大于在假设趋势下出现的可能性。
- `r(results)`：用于制作事件图的数据。请注意 `meanAfterPretesting` 这一列，它也被绘制出来了。这一列的基本思路在于，如果我们仅在未发现显著的事前趋势这一条件下分析事件研究，那么我们分析的只是数据的一个选定子集。 `meanAfterPretesting` 这一列告诉我们，如果实际上的事前趋势就是研究人员所假设的趋势，那么在未发现显著事前趋势这一条件下，我们预期的系数值。

## 4. Stata 实操
## 4.1 一个例子

在中国农村治理体系中，由于信息不对称和监管成本高，上级政府往往难以精准识别贫困人口。虽然村干部更了解本地情况，但地方精英俘获 (elite capture) 问题又可能导致扶贫资源被非贫困户占用。面对这一两难困境，大学生村官 (CGVOs) 计划为解决基层扶贫难题提供了一个独特视角。He and Wang (2017) 的研究采用双重差分法 (DID)，考察了大学生村官计划能否提高贫困户识别与帮扶效率，其平行趋势检验方程如下：

$$y_{it} = \sum_{\substack{k \geq -4,\\ k \ne -1}}^{k=3} D_{it}^k \cdot \delta_k + \mu_i + \rho_t + \varepsilon_{it}$$

其中， $$y_{it}$$ 为村庄 $$v$$ 在 $$t$$ 年的结果变量，当其含义为登记贫困户数量时，估计结果绘制如下：

![](https://github.com/mcaceresb/stata-pretrends/raw/main/doc/HeAndWang.png)

下面通过该估计结果进行实例演示。

## 4.2 事件研究

首先载入原始数据：

```stata
use "https://media.githubusercontent.com/media/mcaceresb/stata-pretrends/main/data/workfile_AEJ.dta", clear
```

接着，运行 He and Wang (2017) 的回归模型：

```stata
reghdfe l_poor_reg_rate Lead_D4_plus Lead_D3 Lead_D2 D0 Lag_D1 Lag_D2 Lag_D3_plus, absorb(v_id year) cluster(v_id) dof(none)
```

可以得到如下结果：

```stata
             |               Robust
l_poor_reg~e |      Coef.   Std. Err.      t    P>|t|     [95% Conf. Interval]
-------------+----------------------------------------------------------------
Lead_D4_plus |   .0667032   .0943746     0.71   0.480    -.1191533    .2525596
     Lead_D3 |  -.0077018   .0770514    -0.10   0.920    -.1594428    .1440392
     Lead_D2 |  -.0307691   .0551237    -0.56   0.577    -.1393268    .0777887
          D0 |   .0840307   .0626478     1.34   0.181    -.0393445    .2074059
      Lag_D1 |   .2424418   .0898103     2.70   0.007     .0655741    .4193096
      Lag_D2 |    .219879   .0887783     2.48   0.014     .0450438    .3947142
 Lag_D3_plus |   .1910925   .0989365     1.93   0.055    -.0037478    .3859329
       _cons |   1.478639   .0811732    18.22   0.000      1.31878    1.638497
```

## 4.3 计算特定功效 (power) 下的线性趋势斜率

计算50%功效下的线性事前趋势斜率:

```stata
pretrends power 0.5, pre(1/3) post(4/7)
```

得到结果：

```stata
Slope for 50% power =  .0520259
```

这表明，如果存在斜率为0.052的线性事前趋势，我们仅有50%的概率能检测到显著的事前趋势！换言之，即使真实存在这种趋势，我们大约有一半的情况下会错误地接受平行趋势假设。

## 4.4 可视化假设趋势并分析功效

接上例，假设存在斜率约为 0.0520259 的线性事前趋势:

```stata
* 提取回归系数和协方差矩阵
matrix sigma = e(V)
matrix beta  = e(b)
* 筛选出事件研究相关系数 (前7个)
matrix beta  = beta[., 1..7]
matrix sigma = sigma[1..7, 1..7]
* 可视化分析
pretrends, numpre(3) b(beta) v(sigma) slope(`r(slope)')
```

得到结果：

![](https://github.com/mcaceresb/stata-pretrends/blob/main/doc/plot50.png)

生成的图表显示了假设趋势 (红线) 与估计系数 (蓝点) 的对比，以及在“通过事前检验”条件下的期望系数 (蓝虚线)。

此外，也可以通过 `return list` 命令查看详细计算结果，得到：

```stata
scalars:
                 r(LR) =  .1062107867920915
              r(Bayes) =  .5699360246383278
              r(Power) =  .4991930498887394
              r(slope) =  .052

macros:
   r(PreTrendsResults) : "PreTrendsResults"

matrices:
            r(results) :  8 x 6
              r(delta) :  1 x 8
```

还可以用 `matlist r(results)` 命令和 `matlist r(delta)` 命令查看矩阵，得到：

```stata
.	matlist	r(results)		

             |         t    betahat         lb         ub  deltatrue  meanAft~g 
-------------+------------------------------------------------------------------
          r1 |        -4   .0667032  -.1182677    .251674      -.156  -.0922333 
          r2 |        -3  -.0077018  -.1587197   .1433162      -.104   -.055506 
          r3 |        -2  -.0307691  -.1388096   .0772715      -.052  -.0278831 
          r4 |        -1          0          0          0          0          0 
          r5 |         0   .0840307  -.0387567    .206818       .052    .064827 
          r6 |         1   .2424418   .0664168   .4184668       .104   .1207074 
          r7 |         2    .219879   .0458768   .3938812       .156   .1692721 
          r8 |         3   .1910925  -.0028194   .3850045       .208   .2242819 


.	matlist	r(delta)

             |        c1         c2         c3         c4         c5         c6         c7         c8 
-------------+----------------------------------------------------------------------------------------
          r1 |     -.156      -.104      -.052          0       .052       .104       .156       .208 
```

除了线性趋势外，我们还可以分析任意形式的非线性趋势。这里以二次函数事前趋势为例：

```stata
* 定义二次函数事前趋势
mata st_matrix("deltaquad", 0.024 * ((-4::3) :- (-1)):^2)

* 分析非线性趋势
pretrends, time(-4(1)3) ref(-1) deltatrue(deltaquad) coefplot
```
得到结果：

![](https://github.com/mcaceresb/stata-pretrends/blob/main/doc/plotQuad.png)

生成的图表同样显示了假设趋势 (红线) 与估计系数 (蓝点) 的对比，以及在“通过事前检验”条件下的期望系数 (蓝虚线)。

还可以查看详细结果：

```stata
. return list

scalars:
                 r(LR) =  .4332635420562245
              r(Bayes) =  .3841447062350145
              r(Power) =  .662449239187808
              r(slope) =  .

macros:
   r(PreTrendsResults) : "PreTrendsResults"

matrices:
            r(results) :  8 x 6
              r(delta) :  1 x 8

. matlist r(results)

             |         t    betahat         lb         ub  deltatrue  meanAft~g 
-------------+------------------------------------------------------------------
          r1 |        -4   .0667032  -.1182677    .251674       .216   .1184861 
          r2 |        -3  -.0077018  -.1587197   .1433162       .096    .040358 
          r3 |        -2  -.0307691  -.1388096   .0772715       .024   .0040393 
          r4 |        -1          0          0          0          0          0 
          r5 |         0   .0840307  -.0387567    .206818       .024   .0093079 
          r6 |         1   .2424418   .0664168   .4184668       .096    .072993 
          r7 |         2    .219879   .0458768   .3938812       .216   .2004382 
          r8 |         3   .1910925  -.0028194   .3850045       .384   .3617779 

. matlist r(delta)

             |        c1         c2         c3         c4         c5         c6         c7         c8 
-------------+----------------------------------------------------------------------------------------
          r1 |      .216       .096       .024          0       .024       .096       .216       .384 
```

## 5. 结语

`pretrends` 命令为评估DID研究中事前趋势检验的可靠性提供了实用工具。不显著的事前趋势不等于不存在，因此在处理事前趋势检验的结果时要更加谨慎，也可考虑与不依赖事前趋势检验显著性的 [HonestDiD]事前趋势检验https://github.com/mcaceresb/stata-honestdid/?tab=readme-ov-file#example-usage----medicaid-expansions) 等敏感性分析方法结合使用。
