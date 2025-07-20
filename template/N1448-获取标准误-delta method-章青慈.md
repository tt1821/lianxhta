
> **作者**：章青慈 (中央财经大学)     
> **邮箱**：<Quincy_zqc@163.com>    

&emsp;

> **编者按**：本文主要整理自以下内容，特此致谢！
> - Alex Gold, Nat Olin, Annie Wang. What is the Delta Method?. 2020. [-Link-](https://cran.r-project.org/web/packages/modmarg/vignettes/delta-method.html)
> - Perraillon, M.C., 2020. Lecture, Interpreting model results: Marginal and incremental effects. [-Link-](https://clas.ucdenver.edu/marcelo-perraillon/sites/default/files/attached-files/week_6_margins_perraillon.pdf)

&emsp; 

- **Title**: 获取标准误：Delta Method
- **Keywords**: Delta法, 标准误, 线性逼近, 泰勒展开, R语言, margins, GLM

&emsp; 


## 1. 引言

Delta method，又称为增量法或 Delta 法，是一种在概率统计、金融、经济学等领域广泛应用的概率分布逼近方法。该方法由 Cramér 在 20 世纪 40 年代奠定，可通过线性逼近来估计随机变量函数的概率分布，常用于计算复杂随机变量的方差、标准差等统计量，从而简化概率推断的计算过程。

Delta method 是一种半参数方法，它的基本思想是利用泰勒展开式，将一个复杂函数近似为线性函数，进而利用线性函数的性质进行估计。具体而言，假设有一个随机变量 $Y$ 是由另一个随机变量 $X$ 通过可导函数 $G(X)$ 得到的，即 $Y=G(X)$。Delta method 通过对 $G(X)$ 在 $X$ 的均值处进行泰勒展开 (常用一阶)，得到一个线性逼近的表达式，进而利用这个线性表达式来估计 $Y$ 的方差和标准差。

&emsp;

## 2. 理论背景

### 2.1 delta method 和泰勒展开

泰勒展开允许我们将可微函数 $G(x)$ 重新表述为 $G(x)$ 的导数 (的无限和) 的形式。更精确地说，一个在 $a$ 点处无限可微的函数 $G(x)$ 在 $a$ 点的值可以写成以下形式：

$$
G(x) = G(a) + \frac{G'(a)}{1!}(x - a) + \frac{G''(a)}{2!}(x-a)^2 + \frac{G'''(a)}{3!}(x-a)^3 + \dots
$$

在展开式中截取一定数量的项 (通常是两项) 之后停止，我们就可以得到一个 $G(x)$ 的近似，这一点在计算复杂函数的标准误时尤其有用。

在预测边际水平下，当回归模型具有链接函数 (link function) 时，预测水平 $P_m$ 在协变量 $X_1$ 处的列向量为：

$$
P_m(X_1 \beta) = \text{link}^{-1}(X_1 \beta)
$$

同一回归模型的预测效应 $P_e$ 是 $Xβ$ 的函数，其导数为：

$$
P_e(X_1 \beta) = \frac{d(\text{link}^{-1}(X_1 \beta))}{d(X \beta)}
$$

对于连续变量，这通常是一个实际导数 (即瞬时变化率)；对于离散变量 (分类变量)，这可能是在不同 $X$ 值下计算得到的 $P(Xβ)$ 之间的差，即一阶差分。

**使用泰勒展开进行近似**

利用泰勒展开，我们可以近似P，即随机变量Xβ在点Xβ附近的任意函数，形式如下：

$$
P(X \beta) = P(X_1 \beta) + \frac{d(P(X_1 \beta))}{d(X\beta)}(X\beta - X_1 \beta)
$$

**预测水平的泰勒展开**

对于预测水平，泰勒展开为：

$$
P_m(X \beta) = \text{link}^{-1}(X_1 \beta) + \frac{d(\text{link}^{-1}(X_1 \beta))}{d(X \beta)}(X\beta - X_1 \beta)
$$

注意这里的最后一项实际上为 0，但保留以展示泰勒展开的形式。实际应用中，我们会根据具体的 $X$ 值来计算。

**分类变量预测效应的泰勒展开**

对于分类变量的预测效应，我们试图估计在 $Pe(X_1​β−X_2​β)$ 处的效应，其泰勒展开为：

$$
\begin{aligned}
P_e(X \beta) &= \text{link}^{-1} (X_1 \beta - X_2 \beta) + 
\frac{d(\text{link}^{-1}(X_1 \beta - X_2 \beta))}{d(X \beta)}(X\beta - (X_1 \beta - X_2 \beta)) \\
% &= \text{link}^{-1} (X_1 \beta) - \text{link}^{-1} (X_2 \beta) + \frac{d(\text{link}^{-1})}{d(X \beta)}(X_1) - \frac{d(\text{link}^{-1})}{d(X \beta)}(X_2)
\end{aligned}
$$

这里，我们比较了两个不同 $X$ 值 ($X_1$ ​和 $X_2$​) 下的效应。

**连续变量预测效应的导数**

对于连续变量的预测效应，边际效应是一个导数，因此其泰勒展开涉及二阶导数甚至更高阶导数。

$$
\begin{aligned}
P_e(X_1 \beta) &= \frac{d(\text{link}^{-1}(X_1 \beta))}{d(X \beta)} + 
\frac{d^2(\text{link}^{-1}(X_1 \beta))}{d(X \beta)}\left(\frac{d(\text{link}^{-1}(X \beta))}{d(X \beta)} - \frac{d(\text{link}^{-1}(X_1 \beta))}{d(X \beta)}\right)
\end{aligned}
$$

### 2.2 delta method 的工作原理

在上述估计中，$P(Xβ)$ 的方差是未知的，但可以用 delta method 计算近似的方差。

$$
\text{Var}[P(X \beta)] = \text{Var}\left[P(X_1 \beta) + \frac{d(P(X_1 \beta))}{d(X\beta)}(X\beta - X_1 \beta)\right]
$$

由于 $X_1​β$ 是一个已知点，其方差为零，因此上式简化为：

$$
\text{Var}[P(X \beta)] = \text{Var}\left[\frac{d(P(X_1 \beta))}{d(X\beta)} \cdot X\beta\right]
$$

**方差计算**

上式中的第一项是雅可比矩阵，矩阵中的元素是预测水平 $i$ 关于回归变量 $j$ 的偏导数。并且，通过回归，我们直接获得 $Xβ$ 的方差，即方差-协方差矩阵 $V$。方差的计算公式如下：

$$
\text{Var}[P(X \beta)] = \left(\frac{d(P(X_1 \beta))}{d(X\beta)}\right)^T \ V \left(\frac{d(P(X_1 \beta))}{d(X\beta)}\right)
$$

**步骤总结**

1. 计算雅可比矩阵：首先，计算 $Xβ$ 的逆链接函数的雅可比矩阵 $J$。

2. 获取方差-协方差矩阵：从回归输出中获取方差-协方差矩阵 $V$，或以其他方式进行计算。

3. 矩阵相乘：将雅可比矩阵 $J$ 的转置、原始方差-协方差矩阵 $V$、雅可比矩阵 $J$ 三者依次相乘 (“三明治”乘法)，所得矩阵的元素即是所求方差。

&emsp;

## 3. Stata 实操

### 3.1 命令简介

`margins` 命令可以进行边际预测，以及边际效应计算，通过 `expression()` 选项可以用于任何函数形式。在本文中，我们主要介绍如何通过 `margins` 命令实现 delta method，计算并提取回归的标准误。

### 3.2 具体应用

#### 3.2.1 通过数值方法计算标准误

```stata
. use https://www.stata-press.com/data/r16/cattaneo2, clear
. gen sm_age = mbsmoke * mage
. qui reg bweight mbsmoke mage sm_age
. matrix list e(V)

symmetric e(V)[4,4]
            mbsmoke        mage      sm_age       _cons
mbsmoke   10733.267
   mage   71.343306   2.6610229
 sm_age  -403.72658  -2.6610229   15.868305
  _cons  -1997.5365  -71.343306   71.343306   1997.5365

* var(beta2)
. di e(V)[2,2]
2.6610229
* var (beta3)
. di e(V)[3,3]
15.868305
* cov(b2,b3)
. di e(V)[3,2]
-2.6610229
* 当mbsmoke=0时，边际效应的标准差是
. di sqrt(e(V)[2,2])
1.6312642
* 当mbsmoke=1时，边际效应的标准差是
. di sqrt(e(V)[2,2] + e(V)[3,3] + 2*e(V)[3,2])
3.6341825
```

#### 3.2.2 使用 `margins` 命令计算标准误

```stata
. qui reg bweight i.mbsmoke##c.mage
. margins, dydx(mage) at(mbsmoke=(0 1)) vsquish

Average marginal effects                                 Number of obs = 4,642
Model VCE: OLS
Expression: Linear prediction, predict()
dy/dx wrt:  mage
1._at: mbsmoke = 0
2._at: mbsmoke = 1
--------------------------------------------------------------------
       |           Delta-method                                 
       |     dy/dx   std. err.     t    P>|t|   [95% conf. interval]
-------+------------------------------------------------------------
 mage  |                                                            
   _at |                                                            
    1  |  11.36258   1.631264    6.97   0.000   8.164523    14.56063
    2  | -3.950895   3.634182   -1.09   0.277  -11.07562    3.173831
--------------------------------------------------------------------
```

使用 `margins` 命令计算出的 Delta-method std. err. 和上文计算结果一致。

#### 3.2.3 复现 delta method 计算标准误的原理

```stata
. qui reg bweight i.mbsmoke##c.mage
. margins mbsmoke, nofvlabel

Predictive margins                                 Number of obs = 4,642
Model VCE: OLS
Expression: Linear prediction, predict()
------------------------------------------------------------------------
         |          Delta-method
         |   Margin   std. err.      t    P>|t|     [95% conf. interval]
---------+--------------------------------------------------------------
 mbsmoke 
      0  | 3409.435   9.221561   369.72   0.000     3391.356    3427.514
      1  | 3132.374   19.85928   157.73   0.000      3093.44    3171.308
------------------------------------------------------------------------

* 提取雅可比矩阵并保存
. matrix list r(Jacobian)

r(Jacobian)[2,6]
                0b.           1.               0b.mbsmoke#   1.mbsmoke#        
           mbsmoke      mbsmoke         mage      co.mage       c.mage   _cons
0.mbsmoke        0            0    26.504524            0            0       1
1.mbsmoke        0            1    26.504524            0    26.504524       1

. matrix J = r(Jacobian)

* 保存回归的方差-协方差矩阵
. matrix list e(V)

symmetric e(V)[6,6]
                  0b.           1.               0b.mbsmoke#   1.mbsmoke#           
             mbsmoke      mbsmoke         mage      co.mage       c.mage      _cons
0b.mbsmoke         0
 1.mbsmoke         0    10733.267
      mage         0    71.343306    2.6610229
0b.mbsmoke#
   co.mage         0            0            0            0
1.mbsmoke#
    c.mage         0   -403.72658   -2.6610229            0    15.868305
     _cons         0   -1997.5365   -71.343306            0    71.343306  1997.5365

* 复现delta method计算的标准差 
. matrix Vrep = J*e(V)*J'
. matrix list Vrep

symmetric Vrep[2,2]
                   1.         1.
             mbsmoke    mbsmoke
0.mbsmoke  85.037195
1.mbsmoke  1.300e-12  394.39086

* 当mbsmoke=0时，边际效应的标准差是
. di sqrt(Vrep[1,1])
9.2215614
* 当mbsmoke=1时，边际效应的标准差是
. di sqrt(Vrep[2,2])
19.859277
```

需要注意的是，输入的 **mbsmoke** 变量取值有 2 种，回归输出的变量有 6 个，这种情况下雅可比矩阵应当是 6×2 矩阵，但 `margins` 命令返回的雅可比矩阵是 2×6 矩阵，实际上是一般雅可比矩阵的转置形式，所以此处计算方差的公式为 $\text{matrix Vrep} = J*e(V)*J'$。

&emsp;

## 4. R 语言实操

### 4.1 包简介

`modmarg` 包是基于 Stata 的 `margins` 命令，利用 delta method 进行计算的包。`modmarg` 通过 CRAN (Comprehensive R Archive Network) 直接提供，命令安装：

```r
install.packages("modmarg")
```

### 4.2 具体应用

#### 4.2.1 使用数据集 `margex` 进行逻辑回归分析

```r
library(modmarg)
data(margex)
lg <- glm(outcome ~ treatment * age, data = margex, family = 'binomial')
summary(lg)

Call:
glm(formula = outcome ~ treatment * age, family = "binomial", 
    data = margex)
Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-1.3200  -0.6033  -0.3212  -0.1580   2.9628  
Coefficients:
              Estimate Std. Error z value Pr(>|z|)    
(Intercept)   -7.03092    0.50247 -13.993   <2e-16 ***
treatment      1.35170    0.62208   2.173   0.0298 *  
age            0.11060    0.01069  10.347   <2e-16 ***
treatment:age -0.01046    0.01301  -0.804   0.4216    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
(Dispersion parameter for binomial family taken to be 1)
    Null deviance: 2732.1  on 2999  degrees of freedom
Residual deviance: 2169.4  on 2996  degrees of freedom
AIC: 2177.4
Number of Fisher Scoring iterations: 6
```

**treatment** 变量在 0 或 1 处的平均结果是什么？仅通过回归结果，我们不能直接解释系数，因为 **treatment** 的作用还受 **age** 的水平影响。

#### 4.2.2 计算边际效应

```r
# Extract the n x k matrix of data
x <- model.matrix(lg)
# Extract the coefficients from the model (a column vector with k entries)
beta <- matrix(lg$coefficients)
# CONTROL:
# Set treatment and treatment:age to 0 for all observations
x[, "treatment"] <- 0
x[, 'treatment:age'] <- x[, 'treatment'] * x[, 'age']
# Get linear predictors
pred_ctl <- x %*% beta
# Apply the inverse link function to get predicted probabilities
pp_ctl <- 1 / (1 + exp(-pred_ctl))
# Get the average predicted probability
mean_pp_ctl <- mean(pp_ctl)
# TREATMENT:
# Set treatment to 1 and treatment:age to age for all observations
x[, "treatment"] <- 1
x[, 'treatment:age'] <- x[, 'treatment'] * x[, 'age']
# Get linear predictors
pred_treat <- x %*% beta
# Apply the inverse link function to get predicted probabilities
pp_treat <- 1 / (1 + exp(-pred_treat))
# Get the average predicted probability
mean_pp_treat <- mean(pp_treat)
# RESULTS:
mean_pp_ctl
## [1] 0.1126685
mean_pp_treat
## [1] 0.208375
```

#### 4.2.3 应用 delta method 计算标准误

```r
# Get the data
x <- model.matrix(lg)
# CONTROL ERROR
# Apply the derivative of the inverse link function to the linear predictors
deriv <- as.vector(exp(-pred_ctl) / (1 + exp(-pred_ctl))^2)
# Set treatment to 0
x[, 'treatment'] <- 0
x[, 'treatment:age'] <- x[, 'treatment'] * x[, 'age']
# Complete the chain rule by matrix-multiplying the derivatives by the data,
# now we have the jacobian
j <- deriv %*% x / nrow(x)
# The variance of our estimate is the cross product of the jacobian and the model's
# variance-covariance matrix
variance <- j %*% vcov(lg) %*% t(j)
# The error is the square root of that
se_ctl <- sqrt(diag(variance))
# TREATMENT ERROR: same logic
deriv <- as.vector(exp(-pred_treat) / (1 + exp(-pred_treat))^2)
x <- model.matrix(lg)
x[, 'treatment'] <- 1
x[, 'treatment:age'] <- x[, 'treatment'] * x[, 'age']
j <- deriv %*% x / nrow(x)
variance <- j %*% vcov(lg) %*% t(j)
se_treat <- sqrt(diag(variance))
se_ctl
## [1] 0.009334252
se_treat
## [1] 0.009089667
```

#### 4.2.4 汇总计算结果

```r
result <- data.frame(
  Label = c("treatment = 0", "treatment = 1"),
  Margin = c(mean_pp_ctl, mean_pp_treat),
  Standard.Error = c(se_ctl, se_treat)
)

result
          Label    Margin Standard.Error
1 treatment = 0 0.1126685    0.009334252
2 treatment = 1 0.2083750    0.009089667
```

&emsp;

## 5. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh 边际效应, m`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

- 专题：[交乘项-调节-中介](https://www.lianxh.cn/blogs/21.html)
  - 刘聪聪, 陈点点, 2020, [Stata：interflex-交乘项该这么分析!](https://www.lianxh.cn/details/121.html), 连享会 No.121.
- 专题：[Probit-Logit](https://www.lianxh.cn/blogs/27.html)
  - 展一帆, 周依仿, 2021, [Logit-Probit：非线性模型中交互项的边际效应解读](https://www.lianxh.cn/details/616.html), 连享会 No.616.
- 专题：[分位数回归](https://www.lianxh.cn/blogs/48.html)
  - 曹钰潇, 2022, [Stata分位数回归I：理解边际效应和条件边际效应](https://www.lianxh.cn/details/857.html), 连享会 No.857.
- 专题：[回归分析](https://www.lianxh.cn/blogs/32.html)
  - 江鑫, 2021, [Stata：边际效应知多少？f_able命令（上）](https://www.lianxh.cn/details/689.html), 连享会 No.689.
  - 江鑫, 2021, [Stata：边际效应知多少？f_able命令（下）](https://www.lianxh.cn/details/690.html), 连享会 No.690.
  - 祁本章, 2021, [Logit-Probit中的交乘项及边际效应图示](https://www.lianxh.cn/details/575.html), 连享会 No.575.
  - 谭炜荣, 2024, [Stata：组间边际效应差异检验-gsem](https://www.lianxh.cn/details/1442.html), 连享会 No.1442.
- 专题：[Stata教程](https://www.lianxh.cn/blogs/17.html)
  - 连享会, 2021, [Stata-Python交互-5：边际效应三维立体图示](https://www.lianxh.cn/details/555.html), 连享会 No.555.
  - 连享会, 2020, [Stata: 手动计算和图示边际效应](https://www.lianxh.cn/details/226.html), 连享会 No.226.
  - 连玉君, 2020, [Stata：图示连续变量的连续边际效应](https://www.lianxh.cn/details/189.html), 连享会 No.189.
  - 连玉君, 杨柳, 2020, [Stata: 边际效应分析](https://www.lianxh.cn/details/64.html), 连享会 No.64.
  - 连玉君, 杨柳, 2020, [Stata：Logit模型一文读懂](https://www.lianxh.cn/details/170.html), 连享会 No.170.
- 专题：[面板数据](https://www.lianxh.cn/blogs/20.html)
  - 郭盼亭, 2022, [Stata：面板Logit的边际效应和处理效应估计-mfelogit](https://www.lianxh.cn/details/1127.html), 连享会 No.1127.
  - 陈希, 2022, [Stata：如何理解三个变量的交乘项？](https://www.lianxh.cn/details/1118.html), 连享会 No.1118.
  - 陈美琪, 2023, [Stata：展示OLS和GLM的交乘项(一)-icalc](https://www.lianxh.cn/details/1157.html), 连享会 No.1157.
  - 陈美琪, 2023, [Stata：展示OLS和GLM的交乘项(二)-icalc](https://www.lianxh.cn/details/1166.html), 连享会 No.1166.
- 专题：[专题课程](https://www.lianxh.cn/blogs/44.html)
  - 陈贤孟, 2022, [解读交互项及其边际效应](https://www.lianxh.cn/details/1075.html), 连享会 No.1075.
