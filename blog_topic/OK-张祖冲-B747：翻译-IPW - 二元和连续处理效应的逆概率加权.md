# B747：翻译-IPW - 二元和连续处理效应的逆概率加权

> 作者：张祖冲（江苏大学）  
> 邮箱：zzchong1228@163.com

本文基于 Andrew Heiss 撰写的题为 [Generating inverse probability weights for both binary and continuous treatments](https://www.andrewheiss.com/blog/2020/12/01/ipw-binary-continuous/) 的内容，主要介绍了如何为二元和连续处理变量生成逆概率权重（IPW），并使用 R 语言中的不同包（如 `ipw` 和 `WeightIt`）来实现这一目标。

## 1. **背景介绍**

- 文章主要围绕因果推断中的逆概率加权（IPW）方法展开。IPW 是一种用于处理观察数据中混杂因素的技术，通过调整权重来模拟随机实验的效果。
    
- 作者提到，虽然 IPW 在二元处理变量（如是否使用某种项目）中应用广泛，但在处理连续变量（如某种干预的程度）时，计算 IPW 的过程更为复杂。

## 2. 准备工作

首先，需要安装并加载必要的 R 包。如果未安装这些包，需要先运行以下命令：

```R
install.packages("tidyverse")
install.packages("ggdag")
install.packages("truncnorm")
install.packages("ipw")
install.packages("WeightIt")
```

然后加载这些包：

```R
library(tidyverse)   # 用于数据处理
library(broom)       # 整理成数据框格式  
library(scales)      # 用于绘图
library(ggdag)       # 同于可视化 
library(dagitty)     # 用于分析 DAGs 
library(truncnorm)   # 用于处理截断正态分布
library(ipw)         # 用于计算逆概率权重
library(WeightIt)    # 用于估计倾向得分权重
```

## 3. Binary treatments
### 3.1 Example data

这段代码的目的是**定义一个有向无环图 (DAG)**，**准备并可视化 DAG**。

```R
# 定义一个 DAG，并将其赋值给变量 mosquito_dag
mosquito_dag <- dagify(mal ~ net + inc + hlth,
                       net ~ inc + hlth,
                       hlth ~ inc,
                       coords = list(x = c(mal = 4, net = 1, inc = 2, hlth = 3),
                                     y = c(mal = 1, net = 1, inc = 2, hlth = 2)),
                       exposure = "net",
                       outcome = "mal")

ggdag_status(mosquito_dag) +
  guides(color = "none") +
  theme_dag()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250514091418815.png)

这段代码的**核心目的**是**模拟生成一个数据集**，这个数据集包含了关于个体收入、健康状况、是否使用蚊帐以及患疟疾风险的信息。

数据生成过程包括以下变量：

- **疟疾风险**：0-100 的范围，使用 Beta 分布。
- **蚊帐使用**：二元变量（0/1），基于收入和健康状况的概率生成。
- **收入**：正态分布，均值为 500，标准差为 75。
- **健康状况**：0-100 的范围，使用 Beta 分布

```R
# 让随机过程保持一致
set.seed(1234)

# 模拟 1138 个人
n_people <- 1138

net_data <- tibble(
  # 创建一个 ID 列
  id = 1:n_people,
  # 生成收入变量：正态分布，均值 500，标准差 75
  income = rnorm(n_people, mean = 500, sd = 75)
) %>% 
  # 生成健康变量：beta 分布，大致在 70 左右
  mutate(health_base = rbeta(n_people, shape1 = 7, shape2 = 4) * 100,
         # 健康状况会因收入每增加 1 美元而提高 0.02
         health_income_effect = income * 0.02,
         # 计算最终健康得分并加入一些噪音
         health = health_base + health_income_effect + rnorm(n_people, mean = 0, sd = 3),
         # 重新调整范围，使其不超过 100
         health = rescale(health, to = c(min(health), 100))) %>% 
  # 基于收入、健康和随机噪音生成蚊帐变量
  mutate(net_score = (0.5 * income) + (1.5 * health) + rnorm(n_people, mean = 0, sd = 15),
         # 将蚊帐得分缩放到 0.05 到 0.95 之间，以创建一个使用蚊帐的概率
         net_probability = rescale(net_score, to = c(0.05, 0.95)),
         # 使用该概率随机生成一个 0/1 变量
         net = rbinom(n_people, 1, net_probability)) %>% 
  # 最后，基于收入、健康、蚊帐使用和随机噪音生成疟疾风险变量
  mutate(malaria_risk_base = rbeta(n_people, shape1 = 4, shape2 = 5) * 100,
         # 使用蚊帐时风险降低 10。但因为我们会重新调整数值范围，
         # 所以这里需要把效应设置得更大，才能在调整后达到 -10 得效果
         # 随着健康和收入的提高，风险也会降低。调整这些数字，直到产生合理的系数
         malaria_effect = (-30 * net) + (-1.9 * health) + (-0.1 * income),
         # 计算最终的疟疾风险得分并加入一些噪音
         malaria_risk = malaria_risk_base + malaria_effect + rnorm(n_people, 0, sd = 3),
         # 重新调整范围，使其不低于 0
         malaria_risk = rescale(malaria_risk, to = c(5, 70))) %>% 
  select(-c(health_base, health_income_effect, net_score, net_probability, 
            malaria_risk_base, malaria_effect))

head(net_data)
## # A tibble: 6 × 5
##      id income health   net malaria_risk
##   <int>  <dbl>  <dbl> <int>        <dbl>
## 1     1   409.   63.1     0         45.1
## 2     2   521.   83.5     1         23.4
## 3     3   581.   73.0     0         36.5
## 4     4   324.   60.6     0         58.7
## 5     5   532.   73.4     1         32.7
## 6     6   538.   42.6     0         52.5
```

这段代码发现 `grant` 和 `malaria_risk` 之间存在统计上显著的负相关。但是，代码的作者通过注释明确指出，这很可能只是一个相关性，并不能代表因果关系，因为模型可能忽略了重要的混杂因素。这个系数 -0.417 仅仅描述了在 `grant_data` 中，在不考虑其他任何因素的情况下，`grant` 和 `malaria_risk` 之间的线性关联模式。
### 3.2 IPW manually, binary treatment

```R
# 错误的 “相关性不是因果关系” 效应
model_grant_naive <- lm(malaria_risk ~ grant, data = grant_data)
tidy(model_grant_naive)
## # A tibble: 2 × 5
##   term        estimate std.error statistic   p.value
##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
## 1 (Intercept)   44.2      1.35       32.8  2.56e-178
## 2 grant         -0.417    0.0615     -6.78 1.69e- 11
```

`adjustmentSets(mosquito_dag)` 的输出 `## { hlth, inc }` 是一个基于所定义的因果图的**行动指南**。说明，为了得到对蚊帐使用 (`net`) 与疟疾风险 (`mal`) 之间因果关系的无偏估计，必须在你的统计分析中考虑并调整收入 (`inc`) 和健康状况 (`hlth`) 这两个变量。忽略它们会导致混杂偏误，使得观察到的关联不等于真实的因果效应。

```R
adjustmentSets(mosquito_dag)
## { hlth, inc }
```

通过逻辑回归模型预测蚊帐使用的倾向得分（propensity score），然后根据公式计算权重：

$$\frac{\mathrm{Treatment}}{\text{Propensity}}+\frac{1-\mathrm{Treatment}}{1-\text{Propensity}}$$

这段代码的核心目的是**计算逆概率权重 (Inverse Probability Weights, IPW)**，以估计处理效应（在这里是蚊帐使用的效应）的方法。

```R
# 使用 Logit 模型预测蚊帐的使用
model_predict_net <- glm(net ~ income + health,
                         family = binomial(link = "logit"),
                         data = net_data)

# 生成倾向得分和 IPW
net_data_ipw <- augment_columns(model_predict_net, net_data,
                                type.predict = "response") %>% 
  rename(propensity = .fitted) %>% 
  mutate(ipw = (net / propensity) + ((1 - net) / (1 - propensity)))

net_data_ipw %>% 
  select(id, income, health, net, malaria_risk, propensity, ipw) %>% 
  head()
## # A tibble: 6 × 7
##      id income health   net malaria_risk propensity   ipw
##   <int>  <dbl>  <dbl> <int>        <dbl>      <dbl> <dbl>
## 1     1   409.   63.1     0         45.1      0.380  1.61
## 2     2   521.   83.5     1         23.4      0.628  1.59
## 3     3   581.   73.0     0         36.5      0.659  2.93
## 4     4   324.   60.6     0         58.7      0.266  1.36
## 5     5   532.   73.4     1         32.7      0.597  1.68
## 6     6   538.   42.6     0         52.5      0.459  1.85
```

`model_net_ipw` 通过使用逆概率权重，提供了一个对蚊帐使用 (`net`) 降低疟疾风险 (`malaria_risk`) 的因果效应的估计。现在得到的 IPW 调整后的估计值是 -10.5,**这非常接近设定的真实效应 -10！**

```R
model_net_ipw <- lm(malaria_risk ~ net, data = net_data_ipw, weights = ipw)
tidy(model_net_ipw)
## # A tibble: 2 × 5
##   term        estimate std.error statistic  p.value
##   <chr>          <dbl>     <dbl>     <dbl>    <dbl>
## 1 (Intercept)     40.4     0.409      98.8 0       
## 2 net            -10.5     0.578     -18.3 1.83e-65
```

### 3.3 IPW with the **ipw** package, binary treatment

这段代码的目的是使用 `ipw` 包中的 `ipwpoint()` 函数来计算逆概率权重 (IPW)，并将其与之前手动计算的 IPW 进行比较。

```R
# ipwpoint() 函数不能处理 tibble 类型的数据！强制将 net_data 转换为 data.frame 类型
weights_ipwpoint <- ipwpoint(
  exposure = net,
  family = "binomial",  # The treatment is binary
  link = "logit",
  denominator = ~ income + health,
  data = as.data.frame(net_data)
)

# 他们是一样的！
head(weights_ipwpoint$ipw.weights)
## [1] 1.61 1.59 2.93 1.36 1.68 1.85
head(net_data_ipw$ipw)
## [1] 1.61 1.59 2.93 1.36 1.68 1.85
```

这段代码首先将通过 `ipwpoint()` 函数计算出的逆概率权重添加回数据框中，然后使用这些权重拟合了一个线性回归模型来估计蚊帐使用对疟疾风险的影响。其输出结果与之前使用手动计算的 IPW 拟合的模型完全相同，这符合预期，因为两种方法计算出的权重本身就是相同的。

```R
net_data_ipwpoint <- net_data %>% 
  mutate(ipw = weights_ipwpoint$ipw.weights)

model_net_ipwpoint <- lm(malaria_risk ~ net, 
                         data = net_data_ipwpoint, weights = ipw)
tidy(model_net_ipwpoint)
## # A tibble: 2 × 5
##   term        estimate std.error statistic  p.value
##   <chr>          <dbl>     <dbl>     <dbl>    <dbl>
## 1 (Intercept)     40.4     0.409      98.8 0       
## 2 net            -10.5     0.578     -18.3 1.83e-65
```

这段代码的目的是使用 `WeightIt` 包中的 `weightit()` 函数来计算逆概率权重 (IPW)，并将其与之前两种方法（手动计算和使用 `ipw` 包的 `ipwpoint()`）计算得到的 IPW 进行比较。

### 3.4 IPW with the **WeightIt** package, binary treatment

```R
weights_weightit <- weightit(net ~ income + health,  # 用混杂因素建模蚊帐使用
                             data = net_data, 
                             estimand = "ATE",  # 计算平均处理效应
                             method = "ps")  # 用倾向得分构建权重
weights_weightit
## A weightit object
##  - method: "glm" (propensity score weighting with GLM)
##  - number of obs.: 1138
##  - sampling weights: none
##  - treatment: 2-category
##  - estimand: ATE
##  - covariates: income, health

# See even more details here
# summary(weights_weightit)

# Same as the other methods!
head(weights_weightit$weights)
## [1] 1.61 1.59 2.93 1.36 1.68 1.85
```

这段代码的目的是使用由 `WeightIt` 包的 `weightit()` 函数计算得到的逆概率权重 (IPW) 来拟合一个加权线性模型，并查看其结果，这与对 `ipwpoint()` 计算的权重所做的操作类似。

```R
net_data_weightit <- net_data %>% 
  mutate(ipw = weights_weightit$weights)

model_net_weightit <- lm(malaria_risk ~ net, 
                         data = net_data_weightit, weights = ipw)
tidy(model_net_weightit)
## # A tibble: 2 × 5
##   term        estimate std.error statistic  p.value
##   <chr>          <dbl>     <dbl>     <dbl>    <dbl>
## 1 (Intercept)     40.4     0.409      98.8 0       
## 2 net            -10.5     0.578     -18.3 1.83e-65
```

## 4. Continuous treatments
### 4.1 Example data

这段代码显示一个图形，清晰地展示了 `grant`、`mal`、`inc` 和 `hlth` 之间的假定因果关系。

```R
grant_dag <- dagify(mal ~ grant + inc + hlth,
                    grant ~ inc + hlth,
                    hlth ~ inc,
                    coords = list(x = c(mal = 4, grant = 1, inc = 2, hlth = 3),
                                  y = c(mal = 1, grant = 1, inc = 2, hlth = 2)),
                    exposure = "grant",
                    outcome = "mal")

ggdag_status(grant_dag) +
  guides(color = "none") +
  theme_dag()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250514103953765.png)

这个新的模拟数据集 `grant_data` 将用于后续分析，特别是研究 `grant`（一个连续的处理变量）对 `malaria_risk` 的因果效应，同时需要控制混杂因素 `income` 和 `health`。

```R
# 让随机过程保持一致
set.seed(1234)

# 模拟 1504 个人
n_people <- 1504

grant_data <- tibble(
  # 创建一个 ID 列（不是必须的，但有的话更好）
  id = 1:n_people,
  # 生成收入变量：正态分布，均值 500，标准差 75
  income = rnorm(n_people, mean = 500, sd = 75)
) %>%
  # 生成健康变量：beta 分布，大致在 70 左右
  mutate(health_base = rbeta(n_people, shape1 = 7, shape2 = 4) * 100,
         # 健康状况会因收入每增加 1 美元而提高 0.02
         health_income_effect = income * 0.02,
         # 计算最终健康得分并加入一些噪音
         health = health_base + health_income_effect + rnorm(n_people, mean = 0, sd = 3),
         # 重新调整范围，使其不超过 100
         health = rescale(health, to = c(min(health), 100))) %>% 
  # 生成补助变量
  mutate(grant_base = rtruncnorm(n_people, mean = 18, sd = 10, a = 5, b = 40),
         # 收入较低的人补助较高；健康状况较差的人补助较高
         grant_effect = (income * -0.25) + (health * -0.5),
         # 计算最终补助金额 + 噪音 + 重新将其缩放回指定位置
         grant = grant_base + grant_effect + rnorm(n_people, mean = 0, sd = 8),
         grant = round(rescale(grant, to = c(5, 40)), 0)) %>% 
  # 最后，基于收入、健康、补助金额和随机噪音生成疟疾风险变量
  mutate(malaria_risk_base = rbeta(n_people, shape1 = 4, shape2 = 5) * 100,
         # 随着补助金的增加，风险降低。调整了这些数字，直到他们产生合理的系数
         malaria_effect = (-40 * grant) + (-25 * health) + (-0.05 * income),
         # 计算最终的疟疾风险得分并加入一些噪音
         malaria_risk = malaria_risk_base + malaria_effect + rnorm(n_people, 0, sd = 3),
         # 重新调整范围，使其不低于 0 （实际是 5 到 70）
         malaria_risk = rescale(malaria_risk, to = c(5, 70))) %>% 
  select(-c(health_base, health_income_effect, grant_base, grant_effect, 
            malaria_risk_base, malaria_effect))

head(grant_data)
## # A tibble: 6 × 5
##      id income health grant malaria_risk
##   <int>  <dbl>  <dbl> <dbl>        <dbl>
## 1     1   409.   70.2    27         26.3
## 2     2   521.   75.3    18         33.3
## 3     3   581.   62.6    20         42.3
## 4     4   324.   83.9    28         11.8
## 5     5   532.   74.3    20         31.8
## 6     6   538.   75.9    15         36.2
```

### 4.2 IPW manually, continuous treatment

这段代码的目的是拟合一个“天真”的线性回归模型，来观察 `grant`（补助金额）与 `malaria_risk`（疟疾风险）之间的简单关联，而**不考虑任何潜在的混杂因素**。

```R
# 错误的 “相关性不是因果关系” 效应
model_grant_naive <- lm(malaria_risk ~ grant, data = grant_data)
tidy(model_grant_naive)
## # A tibble: 2 × 5
##   term        estimate std.error statistic   p.value
##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
## 1 (Intercept)   44.2      1.35       32.8  2.56e-178
## 2 grant         -0.417    0.0615     -6.78 1.69e- 11
```

`adjustmentSets(grant_dag)` 这行代码以及它的输出 `## { hlth, inc }` 是基于之前定义的 `grant_dag` 进行的因果推断分析

```R
adjustmentSets(grant_dag)
## { hlth, inc }
```

当使用二元处理时，计算了每个观测值的倾向得分，然后使用以下公式生成逆概率权重：
$$\frac{\mathrm{Treatment}}{\text{Propensity}}+\frac{1-\mathrm{Treatment}}{1-\text{Propensity}}$$

但是，不能使用连续治疗变量来做到这一点，因为实际上并没有倾向评分。

$$\mathrm{IPW}=\frac{f_X(X;\mu_1,\sigma_1^2)}{f_{X|C}(X|C=c;\mu_2,\sigma_2^2)}$$

符号解释:

- $X$: 连续的暴露或治疗变量（比如每天吸烟量、药物剂量等）。
- $C$: 混杂变量（比如年龄、性别等可能影响治疗分配的因素）。
- $f(\cdot)$: 概率密度函数，用于描述变量的分布规律（均值为 $\mu$，方差为 $\sigma^2$）。
- 分子 $f_X(X;\mu_1,\sigma_1^2)$:
单纯治疗变量 $X$ 本身的概率分布（比如假设所有人的治疗剂量服从正态分布）。
（理论上可以用 1 代替分子，但直接用 $X$ 的分布能稳定权重，避免极端值问题。）
分母 $f_{X|C}(X|C=c;\mu_2,\sigma_2^2)$:混杂变量 $C$ 影响下治疗变量 $X$ 的条件概率分布。

```R
# 分子是仅关于处理变量的概率分布
# 将为其使用正态分布（因此使用 dnorm()）。给 dnorm() 输入每个人的补助金额、一个简单的 grant ~ 1 模型的预测值，以及该模型残差的标准差。
model_num <- lm(grant ~ 1, data = grant_data)
num <- dnorm(grant_data$grant,
             predict(model_num),
             sd(model_num$residuals))

# 分母是由混杂因素解释的处理变量的概率分布。
# 将再次为其使用正态分布。
# 将给 dnorm() 输入补助金额、一个包含混杂因素的模型的预测值，
# 以及该模型残差的标准差。
model_den <- lm(grant ~ health + income, data = grant_data)
den <- dnorm(grant_data$grant,
             predict(model_den),
             sd(model_den$residuals))

# 最后，通过构建分数来制作实际的 IPW 权重
grant_data_ipw <- grant_data %>% 
  mutate(ipw = num / den)

head(grant_data_ipw)
## # A tibble: 6 × 6
##      id income health grant malaria_risk   ipw
##   <int>  <dbl>  <dbl> <dbl>        <dbl> <dbl>
## 1     1   409.   70.2    27         26.3 0.288
## 2     2   521.   75.3    18         33.3 0.492
## 3     3   581.   62.6    20         42.3 0.687
## 4     4   324.   83.9    28         11.8 0.177
## 5     5   532.   74.3    20         31.8 0.499
## 6     6   538.   75.9    15         36.2 0.748
```

这段代码在前一步为连续处理变量 `grant` 计算了稳定化逆概率权重 (SIPW) 之后，利用这些权重来拟合一个加权线性模型，以估计 `grant` 对 `malaria_risk` 的因果效应。

```R
model_grant_ipw <- lm(malaria_risk ~ grant, data = grant_data_ipw, weights = ipw)
tidy(model_grant_ipw)
## # A tibble: 2 × 5
##   term        estimate std.error statistic   p.value
##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
## 1 (Intercept)    58.4     1.79        32.6 1.95e-176
## 2 grant          -1.11    0.0806     -13.7 1.45e- 40
```

### 4.3 IPW with the **ipw** package, continuous treatment

这段代码的目的是使用 `ipw` 包中的 `ipwpoint()` 函数来为**连续处理变量 `grant`** 计算稳定化逆概率权重 (SIPW)，并将其与我们之前手动计算的 SIPW 进行比较。

```R
weights_continuous_ipwpoint <- ipwpoint(
  exposure = grant,
  family = "gaussian",
  numerator = ~ 1,
  denominator = ~ health + income,
  data = as.data.frame(grant_data)
)

# 值相同！
head(grant_data_ipw$ipw)
## [1] 0.288 0.492 0.687 0.177 0.499 0.748
head(weights_continuous_ipwpoint$ipw.weights)
## [1] 0.288 0.492 0.687 0.177 0.499 0.748
```

将这些权重放入数据集中，并使用运行模型。

```R
grant_data_ipwpoint <- grant_data %>% 
  mutate(ipw = weights_continuous_ipwpoint$ipw.weights)

model_grant_ipwpoint <- lm(malaria_risk ~ grant, 
                           data = grant_data_ipwpoint, weights = ipw)
tidy(model_grant_ipwpoint)
## # A tibble: 2 × 5
##   term        estimate std.error statistic   p.value
##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
## 1 (Intercept)    58.4     1.79        32.6 1.95e-176
## 2 grant          -1.11    0.0806     -13.7 1.45e- 40
```

### IPW with the **WeightIt** package, continuous treatment

```R
weights_weightit <- weightit(grant ~ income + health,  # Model grant amount with confounders
                             data = grant_data, 
                             stabilize = TRUE)
weights_weightit
## A weightit object
##  - method: "glm" (propensity score weighting with GLM)
##  - number of obs.: 1504
##  - sampling weights: none
##  - treatment: continuous
##  - covariates: income, health

# See even more details here
# summary(weights_weightit)

# Not the same as the other methods :(
head(weights_weightit$weights)
## [1] 0.580 0.990 1.384 0.356 1.005 1.506
```

这段代码的目的是比较之前手动计算的、以及通过 `ipw` 包的 `ipwpoint()` 计算的稳定化逆概率权重 (SIPW) 与通过 `WeightIt` 包的 `weightit()` 函数计算的权重。

```R
# 手动计算的权重
head(grant_data_ipw$ipw)
## [1] 0.288 0.492 0.687 0.177 0.499 0.748

# weightit()计算的权重除以 2
head(weights_weightit$weights) / 2
## [1] 0.290 0.495 0.692 0.178 0.502 0.753
```

这段代码的目的是使用 `weights_weightit$weights` 来拟合一个加权线性模型，以估计 `grant` 对 `malaria_risk` 的因果效应。

```R
grant_data_weightit <- grant_data %>% 
  mutate(ipw = weights_weightit$weights)

model_grant_weightit <- lm(malaria_risk ~ grant, 
                           data = grant_data_weightit, weights = ipw)
tidy(model_grant_weightit)
## # A tibble: 2 × 5
##   term        estimate std.error statistic   p.value
##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
## 1 (Intercept)    58.4     1.79        32.6 1.95e-176
## 2 grant          -1.11    0.0806     -13.7 1.45e- 40
```
