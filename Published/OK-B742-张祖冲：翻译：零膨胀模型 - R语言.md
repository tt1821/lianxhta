
> 作者：张祖冲（江苏大学）  
> 邮箱：zzchong1228@163.com

本篇推文翻译自[A guide to modeling proportions with Bayesian beta and zero-inflated beta regression models | Andrew Heiss – Andrew Heiss](https://www.andrewheiss.com/blog/2021/11/08/beta-regression-guide/#explore-the-data)
核心目标是指导读者如何使用贝叶斯方法中的贝塔回归（Beta regression）和零膨胀贝塔回归（Zero-inflated Beta regression）来建模比例数据（proportional data）。可以通过这篇文章学习到：

- 比例数据建模的挑战和解决方案。
- 贝塔回归和零膨胀贝塔回归的基本原理和实现方法。
- 如何使用贝叶斯方法进行模型估计和解释。
- 如何通过可视化展示模型结果和不确定性。

# 准备工作

首先，需要安装并加载必要的 R 包。如果未安装这些包，需要先运行以下命令：

```R
install.packages("brms")
install.packages("tidybayes")
install.packages("broom.mixed")
install.packages("vdemdata")
install.packages("patchwork")
install.packages("betareg")
install.packages("extraDistr")
install.packages("gghalves")
install.packages("ggbeeswarm")
install.packages("marginaleffects")
install.packages("modelsummary")
install.packages("broom.mixed")
install.packages("vdemdata")
```

其中 `broom.mixed` 与 `vdemdata` 安装困难的同学可以先下载 Rtools ，**使 R 环境具备从源代码编译 R 包的能力。**

**步骤一**：安装 Rtools

1. 访问 Rtools 的官方下载页面： [https://cran.rstudio.com/bin/windows/Rtools/](https://cran.rstudio.com/bin/windows/Rtools/)
2. 在页面上，会看到不同 R 版本对应的 Rtools 安装程序。
        - **R 4.4.x** 通常对应 **Rtools44**。
        - R 4.3.x 通常对应 Rtools43。
        - R 4.2.x 通常对应 Rtools42。
        - R 4.0.x 和 R 4.1.x 通常对应 Rtools40。
3. 点击对应的链接下载 Rtools 安装程序。

（查看 R 版本的方法为：`R.version.string`）

**步骤二**：安装 Rtools
- 在安装过程中，会有一个步骤询问是否将 Rtools 的路径添加到系统环境变量 PATH 中。**务必勾选这个选项！** 这是最关键的一步。如果 Rtools 没有被添加到 PATH，R 将无法找到编译工具（如 `make.exe`）。

**步骤三**：**重启 R 和 RStudi**

- 为了让 R 和 RStudio 能够识别到新添加的系统环境变量。

**步骤四**：验证 Rtools 安装 (在 R 控制台中)

```R
Sys.which("make")
```

如果安装或配置成功，会看到一个路径：

```R
# 对于 Rtools44 
# "C:\\rtools44\\usr\\bin\\make.exe" 
# 或者对于 Rtools40 
# "C:\\rtools40\\usr\\bin\\make.exe"
```

接下来进行安装 `future` ：

```R
install.packages("future")
library(broom.mixed)
```

而 `vdemdata` 的安装使用手动安装更为便捷：

1. 在浏览器中访问 `vdemdata` 的 GitHub 页面：[https://github.com/vdeminstitute/vdemdata](https://github.com/vdeminstitute/vdemdata)
2. 点击绿色的 "Code" 按钮，然后选择 "Download ZIP"。

```R
# 假设你解压到了 C:/temp/vdemdata-main
remotes::install_local("C:/temp/vdemdata-main")
```

接下来，加载这些包，并完成基础工作：

```R
library(tidyverse)        # 用于数据科学
library(brms)             # 用于贝叶斯回归建模
library(tidybayes)        
library(broom)            # 将模型对象的结果转换为整洁的数据框格式
library(broom.mixed)      # Convert brms model objects to data frames
library(vdemdata)         # 提供一些项目数据
library(betareg)          # 用于拟合 Beta 回归模型
library(extraDistr)       # 提供了一些额外的概率分布函数
library(ggdist)           # 可视化分布
library(gghalves)         # 
library(ggbeeswarm)       # 
library(ggrepel)          # 在 ggplot2 图中自动调整标签位置
library(patchwork)        # 将多个 ggplot 对象组合成一个复杂的图形
library(scales)           # 提供了格式化数字的函数
library(marginaleffects)  # 用于计算和可视化回归模型的边际效应
library(modelsummary)     # 创建回归结果摘要表

set.seed(1234)  # 保证分析可复现

# 定义模型拟合优度统计量 (Goodness-of-Fit Stats)
gof_stuff <- tribble(
  ~raw, ~clean, ~fmt,
  "nobs", "N", 0,
  "r.squared", "R²", 3
)

# 自定义 ggplot 主题 (Custom ggplot Theme)
theme_clean <- function() {
  theme_minimal(base_family = "Barlow Semi Condensed") +
    theme(panel.grid.minor = element_blank(),
          plot.title = element_text(family = "BarlowSemiCondensed-Bold"),
          axis.title = element_text(family = "BarlowSemiCondensed-Medium"),
          strip.text = element_text(family = "BarlowSemiCondensed-Bold",
                                    size = rel(1), hjust = 0),
          strip.background = element_rect(fill = "grey80", color = NA))
}

# 更新几何对象的默认设置 (Update Geom Defaults)
update_geom_defaults("label_repel", list(family = "Barlow Semi Condensed"))

# 将事物格式化为百分比点
label_pp <- label_number(accuracy = 1, scale = 100, 
                         suffix = " pp.", style_negative = "minus")
label_pp_tiny <- label_number(accuracy = 0.01, scale = 100, 
                              suffix = " pp.", style_negative = "minus")
```

这段代码首先对 V-Dem 数据进行了预处理，得到了一个包含 2010-2019 年相关变量的干净数据集 `vdem_clean`。然后，基于 `vdem_clean`，进一步提取了 2015 年的数据，并通过添加微小噪声的方式，创建了一个用于标记该年民主化程度极值的辅助列，形成了 `vdem_2015` 数据集。

```R
# 创建 vdem_clean 数据框
vdem_clean <- vdem %>% 
  select(country_name, country_text_id, year, region = e_regionpol_6C,
         polyarchy = v2x_polyarchy, corruption = v2x_corr, 
         civil_liberties = v2x_civlib, prop_fem = v2lgfemleg, v2lgqugen) %>% 
  filter(year >= 2010, year < 2020) %>% 
  drop_na(v2lgqugen, prop_fem) %>% 
  mutate(quota = v2lgqugen > 0,
         prop_fem = prop_fem / 100,
         polyarchy = polyarchy * 100)

# 创建 vdem_2015 数据框
vdem_2015 <- vdem_clean %>% 
  filter(year == 2015) %>% 
  mutate(polyarchy_noise = polyarchy + rnorm(n(), 0, sd = 0.01)) %>% 
  mutate(highlight = polyarchy_noise == max(polyarchy_noise) | 
           polyarchy_noise == min(polyarchy_noise)) %>% 
  select(-polyarchy_noise)
```

## Explore the data

这段代码使用 `ggplot2` 及其扩展包创建两个图表，并通过 `patchwork` 包将它们并排显示。

```R
quota_halves <- ggplot(vdem_2015, aes(x = quota, y = prop_fem)) +
  # 左侧：半散点图
  geom_half_point(aes(color = quota), 
                  transformation = position_quasirandom(width = 0.1),
                  side = "l", size = 0.5, alpha = 0.5) +
  # 右侧：半箱线图
  geom_half_boxplot(aes(fill = quota), side = "r") + 
  # Y 轴标签格式化为百分比
  scale_y_continuous(labels = label_percent()) +
  # 填充色和点颜色使用 viridis 版
  scale_fill_viridis_d(option = "plasma", end = 0.8) +
  scale_color_viridis_d(option = "plasma", end = 0.8) +
  # 移除颜色图例
  guides(color = "none", fill = "none") +
  # 设置坐标轴标签
  labs(x = "Quota", y = "Proportion of women in parliament") +
  theme_clean()

quota_densities <- ggplot(vdem_2015, aes(x = prop_fem, fill = quota)) +
  # 添加密度图层
  geom_density(alpha = 0.6) +
  # x 轴标签格式化为百分比
  scale_x_continuous(labels = label_percent()) +
  # 填充色使用 viridis 色板
  scale_fill_viridis_d(option = "plasma", end = 0.8) +
  # 设置坐标轴和图例标签
  labs(x = "Proportion of women in parliament", y = "Density", fill = "Quota") +
  theme_clean() +
  # 将图例放在底部
  theme(legend.position = "bottom")

quota_halves | quota_densities
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515142205905.png)

这段代码创建一个散点图，展示 2015 年各国民主化程度 (`polyarchy`) 与议会中女性议员比例 (`prop_fem`) 之间的关系，并高亮显示民主化程度最高和最低的国家。

```R
ggplot(vdem_2015, aes(x = polyarchy, y = prop_fem)) +
  # 添加散点图层
  geom_point(aes(color = highlight), size = 1) +
  # 添加线性回归拟合线
  geom_smooth(method = "lm") +
  # 为高亮的点添加国家名称标签
  geom_label_repel(data = filter(vdem_2015, highlight == TRUE), 
                   aes(label = country_name),
                   seed = 1234) +
  # Y 轴标签格式化为百分比
  scale_y_continuous(labels = label_percent()) +
  # 自定义点的颜色，并移除图例
  scale_color_manual(values = c("grey30", "#FF4136"), guide = "none") +
  # 设置坐标轴标签
  labs(x = "Polyarchy (democracy)", y = "Proportion of women in parliament") +
  # 应用自定义主题
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515144604326.png)

## 1. Linear probability models

这段 R 代码使用 `lm()` 函数拟合了三个**线性概率模型 (Linear Probability Models, LPMs)**。

```R
# 线性概率模型
# 因变量是 prop_fem (女性议员比例)，自变量是 quota (是否有性别配额) 和 polyarchy (民主化程度)。数据来源是 vdem_2015
model_ols1 <- lm(prop_fem ~ quota,
                 data = vdem_2015)
# 试图探究民主化程度 (polyarchy) 与议会中女性议员的比例 (prop_fem) 之间的线性关系
model_ols2 <- lm(prop_fem ~ polyarchy,
                 data = vdem_2015)
# 试图同时探究性别配额和民主化程度对议会中女性议员比例的线性影响，并且控制了彼此的影响
model_ols3 <- lm(prop_fem ~ quota + polyarchy,
                 data = vdem_2015)
```

生成表格。

```R
modelsummary(list(model_ols1, model_ols2, model_ols3),
             gof_map = gof_stuff)
```

|           | (1)     | (2)     | (3)     |
| --------- | ------- | ------- | ------- |
| Intercept | 0.195   | 0.155   | 0.132   |
|           | (0.012) | (0.020) | (0.022) |
| quotaTRUE | 0.048   | —       | 0.050   |
|           | (0.018) |         | (0.018) |
| polyarchy | —       | 0.001   | 0.001   |
|           |         | (0.000) | (0.000) |
| **N**     | 172     | 172     | 172     |
| **R²**    | 0.039   | 0.059   | 0.103   |

## 2. Fractional logistic regression

这段代码是对女性议员比例（`prop_fem`）进行 **准二项式回归（Quasi-Binomial Regression）**。

```R
# 仅考察性别配额的影响
model_frac_logit1 <- glm(prop_fem ~ quota, 
                         data = vdem_2015, 
                         family = quasibinomial())

# 仅考察民主程度的影响
model_frac_logit2 <- glm(prop_fem ~ polyarchy, 
                         data = vdem_2015, 
                         family = quasibinomial())

# 联合考察性别配额和民主程度的影响
model_frac_logit3 <- glm(prop_fem ~ quota + polyarchy, 
                         data = vdem_2015, 
                         family = quasibinomial())
```

这段代码将统计结果合并为一张 **回归结果汇总表**。

```R
modelsummary(list(model_frac_logit1, model_frac_logit2, model_frac_logit3),
             gof_map = gof_stuff)
```

|           | (1)     | (2)     | (3)     |
| --------- | ------- | ------- | ------- |
| Intercept | -1.418  | -1.662  | -1.813  |
|           | (0.072) | (0.128) | (0.139) |
| quotaTRUE | 0.281   | —       | 0.300   |
|           | (0.107) |         | (0.106) |
| polyarchy | —       | 0.007   | 0.007   |
|           |         | (0.002) | (0.002) |
| **N**     | 172     | 172     | 172     |

**研究性别配额（quota）和民主程度（polyarchy）对女性议员比例（prop_fem）的影响**，并通过多种统计方法和可视化手段，回答核心问题。

```R
# 仅包含截距项的准二项式回归模型
model_frac_logit0 <- glm(prop_fem ~ 1, 
                         data = vdem_2015, 
                         family = quasibinomial())
# 截距项估计值为 1.29
tidy(model_frac_logit0)
## # A tibble: 1 × 5
##   term        estimate std.error statistic  p.value
##   <chr>          <dbl>     <dbl>     <dbl>    <dbl>
## 1 (Intercept)    -1.29    0.0540     -24.0 1.24e-56

logit0_intercept <- model_frac_logit0 %>% 
  tidy() %>% 
  filter(term == "(Intercept)") %>% 
  pull(estimate)

# The intercept in logits. Who even knows what this means.
logit0_intercept
## [1] -1.29
```

将其转化为概率。

```R
# 将截距项（代表平均对数几率）转换回了原始的比例尺度。
plogis(logit0_intercept)
## [1] 0.215
```

直接计算了2015年数据中女性议员比例的样本均值。

```R
mean(vdem_2015$prop_fem)
## [1] 0.215
```

从 `model_frac_logit1` 模型中提取了截距项和 `quotaTRUE` 的系数。

```R
# 从 model_frac_logit1 模型中提取截距项的估计值
logit1_intercept <- model_frac_logit1 %>% 
  tidy() %>% 
  filter(term == "(Intercept)") %>% 
  pull(estimate)

# 从 model_frac_logit1 模型中提取 quotaTRUE 的估计值
logit1_quota <- model_frac_logit1 %>% 
  tidy() %>% 
  filter(term == "quotaTRUE") %>% 
  pull(estimate)

# 错误的配额边际效应计算方式
plogis(logit1_quota)
## [1] 0.568
```

行代码的目的是计算 **在 `model_frac_logit1` 模型下，性别配额 (`quota`) 对女性议员比例 (`prop_fem`) 的平均边际效应 (Average Marginal Effect, AME)**。

```R
plogis(logit1_intercept + logit1_quota) - plogis(logit1_intercept)
## [1] 0.0469
```

`data_to_predict` 数据框代表了两种**假设情景**：

1. 一个国家**没有**性别配额 (`quota = FALSE`)，并且其民主指数 (`polyarchy`) 处于样本中的平均水平 (53.2)。
2. 一个国家**有**性别配额 (`quota = TRUE`)，并且其民主指数 (`polyarchy`) 处于样本中的平均水平 (53.2)。

```R
# 计算模型 3 中除配额外所有变量的典型值
data_to_predict <- datagrid(model = model_frac_logit3, 
                            quota = c(FALSE, TRUE))
data_to_predict
##   prop_fem polyarchy quota
## 1    0.215      53.2 FALSE
## 2    0.215      53.2  TRUE
```

这段代码的目的是：
- **使用已经拟合好的 `model_frac_logit3` 模型，为两种特定的情景（有配额 vs. 无配额，同时保持民主指数为平均水平）生成女性议员比例的预测值。**
- **量化并可视化在这两种情景下，性别配额对女性议员比例的预测影响，并给出这种影响的置信区间。**

```R
# 将这个新数据集代入模型中
frac_logit_pred <- predictions(model_frac_logit3, newdata = data_to_predict)
frac_logit_pred
## 
##  Estimate Pr(>|z|) 2.5 % 97.5 % polyarchy quota
##     0.192   <0.001 0.171  0.215      53.2 FALSE
##     0.242   <0.001 0.215  0.271      53.2  TRUE
## 
## Columns: rowid, estimate, p.value, conf.low, conf.high, prop_fem, polyarchy, quota

ggplot(frac_logit_pred, aes(x = quota, y = estimate)) +
  geom_pointrange(aes(ymin = conf.low, ymax = conf.high)) +
  scale_y_continuous(labels = label_percent(accuracy = 1)) +
  labs(x = "Quota", y = "Predicted proportion of women in parliament") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515155612750.png)

这段代码的目的是可视化在控制了（或固定了）性别配额因素后（通常是固定在“无配额”状态），民主程度 (`polyarchy`) 与女性议员比例之间的预测关系。

```R
# 为 polyarchy 从 0 到 100 的一系列值生成预测
frac_logit_pred <- predictions(model_frac_logit3, 
                               newdata = datagrid(polyarchy = seq(0, 100, by = 1)))

ggplot(frac_logit_pred, aes(x = polyarchy, y = estimate)) + 
  geom_ribbon(aes(ymin = conf.low, ymax = conf.high), 
              alpha = 0.4, fill = "#480B6A") +
  geom_line(linewidth = 1, color = "#480B6A") +
  scale_y_continuous(labels = label_percent()) +
  labs(x = "Polyarchy (democracy)", 
       y = "Predicted proportion of women in parliament") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515155923808.png)



```R
# 计算平均边际效应 (Average Marginal Slopes)
avg_slopes(model_frac_logit3, variables = "polyarchy")
## 
##       Term Estimate Std. Error    z Pr(>|z|)   2.5 %  97.5 %
##  polyarchy  0.00119   0.000349 3.42   <0.001 0.00051 0.00188
## 
## Columns: term, estimate, std.error, statistic, p.value, conf.low, conf.high

# 特定 polyarchy 值下的预测
predictions(model_frac_logit3,
            newdata = datagrid(polyarchy = c(56, 66)))
## 
##  Estimate Pr(>|z|) 2.5 % 97.5 % quota polyarchy
##     0.196   <0.001 0.174  0.219 FALSE        56
##     0.207   <0.001 0.184  0.232 FALSE        66
## 
## Columns: rowid, estimate, p.value, conf.low, conf.high, prop_fem, quota, polyarchy
```

这段代码**使用 `modelsummary` 包创建一个包含三个不同模型/结果的汇总表格，以便于比较它们对女性议员比例的预测因素（性别配额和民主指数）的估计结果。

注：原文的使用的是 `marginaleffects()`，但笔者复现的时候不断报错： `没有"marginaleffects"这个函数`,最后采用 `avg_slopes()` 予以解决。


```R
modelsummary(list("OLS" = model_ols3,
                  "Fractional logit" = model_frac_logit3,
                  "Fractional logit<br>(marginal effects)" = avg_slopes(model_frac_logit3)),
             gof_map = gof_stuff,
             fmt = 4,
             escape = FALSE)
```

|                 | OLS             | Fractional logit | Fractional logit (marginal effects) |
| --------------- | --------------- | ---------------- | ----------------------------------- |
| **(Intercept)** | 0.1317 (0.0215) | -1.8127 (0.1387) |                                     |
| **quotaTRUE**   | 0.0505 (0.0176) | 0.3004 (0.1057)  |                                     |
| **polyarchy**   | 0.0012 (0.0003) | 0.0071 (0.0021)  | 0.0012 (0.0003)                     |
| **quota**       |                 |                  | 0.0510 (0.0181)                     |
| **N**           | 172             | 172              | 172                                 |
| **R²**          | 0.103           |                  |                                     |

## Interlude: The beta distribution and distributional regression

这段代码是**在同一个图上绘制两个不同参数的正态分布 (Normal distribution) 的概率密度函数 (Probability Density Function, PDF) 曲线，并使用不同的颜色和图例来区分它们。**

```R
ggplot(data = tibble(x = seq(-10, 20)), aes(x = x)) +
  geom_function(fun = dnorm, args = list(mean = 5, sd = 4),
                aes(color = "Normal(mean = 5, sd = 4)"), linewidth = 1) +
  geom_function(fun = dnorm, args = list(mean = 10, sd = 2),
                aes(color = "Normal(mean = 10, sd = 2)"), linewidth = 1) +
  scale_color_viridis_d(option = "plasma", end = 0.8, name = "") +
  theme_clean() +
  theme(legend.position = "bottom")
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515161811174.png)

### Beta distributions and shape parameters

Beta 分布是用来处理百分比和比例的，因为它只在 0 到 1 之间，不包括 0 和 1 本身，形状灵活，通过两个参数（`shape1`/`a`和`shape2`/`b`）可呈现对称、偏态、U型等多种形态。**`a`（正确数）与`b`（错误数）**：类比于“成功次数”和“失败次数”。例如，考试得6分（正确）和4分（错误）对应`a=6`、`b=4`，其比例计算为 `6/(6+4)=60%`。

$$\frac{\text{Number correct}}{\text{Number correct}+\text{Number incorrect}}$$
如果成绩是 6 分，可以写成：
$$\frac{6}{6+4}=\frac{6}{10}$$
Beta 分布通过参数 `a` 和 `b` 灵活描述比例数据，其参数直观对应实际场景中的“成功-失败”次数，是处理 0-1 区间数据的理想选择。
$$\frac{a}{a+b}$$

```R
ggplot() +
  geom_function(fun = dbeta, args = list(shape1 = 6, shape2 = 4),
                aes(color = "Beta(shape1 = 6, shape2 = 4)"),
                size = 1) +
  scale_color_viridis_d(option = "plasma", name = "") +
  theme_clean() +
  theme(legend.position = "bottom")
## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515162124489.png)

Beta 分布的魔力（也是最令人困惑的部分）在于，只需更改形状参数即可获得各种曲线。

```R
ggplot() +
  geom_function(fun = dbeta, args = list(shape1 = 6, shape2 = 4),
                aes(color = "Beta(shape1 = 6, shape2 = 4)"),
                size = 1) +
  geom_function(fun = dbeta, args = list(shape1 = 60, shape2 = 40),
                aes(color = "Beta(shape1 = 60, shape2 = 40)"),
                size = 1) +
  geom_function(fun = dbeta, args = list(shape1 = 9, shape2 = 1),
                aes(color = "Beta(shape1 = 9, shape2 = 1)"),
                size = 1) +
  geom_function(fun = dbeta, args = list(shape1 = 2, shape2 = 11),
                aes(color = "Beta(shape1 = 2, shape2 = 11)"),
                size = 1) +
  scale_color_viridis_d(option = "plasma", end = 0.8, name = "",
                        guide = guide_legend(nrow = 2)) +
  theme_clean() +
  theme(legend.position = "bottom")
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515163344315.png)

### Mean and precision instead of shapes

传统 Beta 分布使用形状参数 `a`（shape1）和 `b`（shape2），但这两个参数缺乏直观性（如难以直接反映分布的均值或方差）。本节提出一种更易理解的参数化方法：用 **均值（μ）** 和 **精度（φ）** 替代 `a` 和 `b`，并推导两者的数学关系。

这些形状和 $μ$ 和 $φ$ 参数在数学上是相关的，并且可以互换。从形式上讲，这两个形状可以使用 $μ$ 和 $φ$ 这样：
$$\begin{aligned}&\mathrm{shape1~}(a)=\mu\times\phi\\&\mathrm{shape2~}(b)=(1-\mu)\times\phi\end{aligned}
$$
基于此，我们可以做一些代数来弄清楚如何写 $μ$ 和 $φ$ 用一个 $a$ 和 $b$ :

$$\begin{aligned}[\mathrm{get~}\phi\mathrm{~alone}]&[\text{solve for }\mu]&[\text{solve for }\phi]\\\mu\times\phi=a&b=(1-\mu)\times\phi&a=\mu\times\phi\\\phi=\frac{a}{\mu}&b=(1-\mu)\times\frac{a}{\mu}&a=\left(\frac{a}{a+b}\right)\times\phi\\b=\frac{a}{\mu}-\frac{a\mu}{\mu}&a=\frac{a\phi}{a+b}\\b=\frac{a}{\mu}-a&(a+b)a=a\phi\\a+b=\frac{a}{\mu}&a^2+ba=a\phi\\&\phi=a+b\\\mu=\frac{a}{a+b}\end{aligned}$$
因此，可以在这两个参数化之间进行转换：
$$\begin{array}{ccccccc}\mathrm{Shape~1:}&&a=\mu\phi&&\mathrm{Mean:}&&\mu=\frac{a}{a+b}\\\mathrm{Shape~2:}&&b=(1-\mu)\phi&&\text{Precision:}&&\phi=a+b\end{array}$$

制作几个小函数来在它们之间切换:

```R
shapes_to_muphi <- function(shape1, shape2) {
  mu <- shape1 / (shape1 + shape2)
  phi <- shape1 + shape2
  return(list(mu = mu, phi = phi))
}

muphi_to_shapes <- function(mu, phi) {
  shape1 <- mu * phi
  shape2 <- (1 - mu) * phi
  return(list(shape1 = shape1, shape2 = shape2))
}
```

使用`shape1` `shape2` $μ$ 和 $φ$ 相反：

```R
shapes_to_muphi(6, 4)
## $mu
## [1] 0.6
## 
## $phi
## [1] 10
```

`shape1` `shape2`

```R
shapes_to_muphi(60, 40)
## $mu
## [1] 0.6
## 
## $phi
## [1] 100
```

这段代码是为了**直观地比较和对比 Beta 分布的两种常见参数化方法及其对分布形状的影响。**

```R
beta_shapes <- ggplot() +
  geom_function(fun = dbeta, args = list(shape1 = 6, shape2 = 4),
                aes(color = "dbeta(shape1 = 6, shape2 = 4)"),
                size = 1) +
  geom_function(fun = dbeta, args = list(shape1 = 60, shape2 = 40),
                aes(color = "dbeta(shape1 = 60, shape2 = 40)"),
                size = 1) +
  geom_function(fun = dbeta, args = list(shape1 = 9, shape2 = 1),
                aes(color = "dbeta(shape1 = 9, shape2 = 1)"),
                size = 1) +
  geom_function(fun = dbeta, args = list(shape1 = 2, shape2 = 11),
                aes(color = "dbeta(shape1 = 2, shape2 = 11)"),
                size = 1) +
  scale_color_viridis_d(option = "plasma", end = 0.8, name = "",
                        guide = guide_legend(ncol = 1)) +
  labs(title = "Shape-based beta distributions") +
  theme_clean() +
  theme(legend.position = "bottom")

beta_mu_phi <- ggplot() +
  geom_function(fun = dprop, args = list(mean = 0.6, size = 10),
                aes(color = "dprop(mean = 0.6, size = 10)"),
                size = 1) +
  geom_function(fun = dprop, args = list(mean = 0.6, size = 100),
                aes(color = "dprop(mean = 0.6, size = 100)"),
                size = 1) +
  geom_function(fun = dprop, args = list(mean = 0.9, size = 10),
                aes(color = "dprop(mean = 0.9, size = 10)"),
                size = 1) +
  geom_function(fun = dprop, args = list(mean = 0.154, size = 13),
                aes(color = "dprop(mean = 0.154, size = 13)"),
                size = 1) +
  scale_color_viridis_d(option = "plasma", end = 0.8, name = "",
                        guide = guide_legend(ncol = 1)) +
  labs(title = "Mean- and precision-based beta distributions") +
  theme_clean() +
  theme(legend.position = "bottom")

beta_shapes | beta_mu_phi
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515164732771.png)

## 3a: Beta regression

现在可以探究 Beta 回归如何让我们对 0 到 1 之间的结果进行建模。

这个错误明确地告诉我们，在 `vdem_2015` 数据框的 `prop_fem` 列中，**至少存在一个值为 0 或 1 (或者可能两者都有) 的观测**。

```R
model_beta <- betareg(prop_fem ~ quota | quota, 
                      data = vdem_2015, 
                      link = "logit")
## Error in betareg(prop_fem ~ quota | quota, data = vdem_2015, link = "logit"): invalid dependent variable, all observations must be in (0, 1)
```

现在可以做一些技巧，把 0.001 加到所有 0 上，这样它们实际上就不是 0。这是作弊，但目前还好:)

```R
vdem_2015_fake0 <- vdem_2015 %>% 
  mutate(prop_fem = ifelse(prop_fem == 0, 0.001, prop_fem))

model_beta <- betareg(prop_fem ~ quota | quota, 
                      data = vdem_2015_fake0, 
                      link = "logit")
tidy(model_beta)
## # A tibble: 4 × 6
##   component term        estimate std.error statistic  p.value
##   <chr>     <chr>          <dbl>     <dbl>     <dbl>    <dbl>
## 1 mean      (Intercept)   -1.44     0.0861    -16.7  7.80e-63
## 2 mean      quotaTRUE      0.296    0.115       2.58 9.79e- 3
## 3 precision (Intercept)    2.04     0.140      14.6  2.42e-48
## 4 precision quotaTRUE      0.440    0.214       2.06 3.93e- 2
```

### Interpreting coefficients

现在有两组系数，每个参数（均值和精度）一组。平均值的参数是在 logit 尺度上测量的，就像之前的 logistic 回归一样，可以通过使用系数和截距并将其拼凑在一起来计算具有配额的边际效应。

```R
beta_mu_intercept <- model_beta %>% 
  tidy() %>% 
  filter(component == "mean", term == "(Intercept)") %>% 
  pull(estimate)

beta_mu_quota <- model_beta %>% 
  tidy() %>% 
  filter(component == "mean", term == "quotaTRUE") %>% 
  pull(estimate)

plogis(beta_mu_intercept + beta_mu_quota) - plogis(beta_mu_intercept)
## [1] 0.0501
```

### Working with the precision parameter

这段代码的目的是**创建一个图表，将实际观测数据中按有无配额 (`quota`) 分组的女性议员比例 (`prop_fem`) 的经验密度分布，与从 Beta 回归模型 (`model_beta`) 中得到的有无配额情况下的理论 Beta 分布的概率密度函数 (PDF) 进行比较。**

```R
# 提取精读模型系数
beta_phi_intercept <- model_beta %>% 
  tidy() %>% 
  filter(component == "precision", term == "(Intercept)") %>% 
  pull(estimate)

beta_phi_quota <- model_beta %>% 
  tidy() %>% 
  filter(component == "precision", term == "quotaTRUE") %>% 
  pull(estimate)

# 创建图例标题字符串
no_quota_title <- paste0("dprop(mean = plogis(", round(beta_mu_intercept, 2),
                         "), size = exp(", round(beta_phi_intercept, 2), "))")

quota_title <- paste0("dprop(mean = plogis(", round(beta_mu_intercept, 2),
                      " + ", round(beta_mu_quota, 2), 
                      "), size = exp(", round(beta_phi_intercept, 2),
                      " + ", round(beta_phi_quota, 2), "))")

# ggplot 绘图部分
ggplot(data = tibble(x = 0:1), aes(x = x)) +
  geom_density(data = vdem_2015_fake0, 
               aes(x = prop_fem, fill = quota), alpha = 0.5, color = NA) +
  stat_function(fun = dprop, size = 1,
                args = list(size = exp(beta_phi_intercept), 
                            mean = plogis(beta_mu_intercept)),
                aes(color = no_quota_title)) +
  stat_function(fun = dprop, size = 1,
                args = list(size = exp(beta_phi_intercept + beta_phi_quota), 
                            mean = plogis(beta_mu_intercept + beta_mu_quota)),
                aes(color = quota_title)) +
  scale_x_continuous(labels = label_percent()) +
  scale_fill_viridis_d(option = "plasma", end = 0.8, name = "Quota",
                       guide = guide_legend(ncol = 1, order = 1)) +
  scale_color_viridis_d(option = "plasma", end = 0.8, direction = -1, name = "",
                        guide = guide_legend(reverse = TRUE, ncol = 1, order = 2)) +
  labs(x = "Proportion of women in parliament") +
  theme_clean() +
  theme(legend.position = "bottom")
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515184647054.png)

如果精度确实不随 `quota` 变化，那么这个模型可能更优（更简约）。如果精度确实随 `quota` 变化，那么允许精度也建模的 `model_beta` 会更合适。

```R
model_beta_no_phi <- betareg(prop_fem ~ quota, 
                             data = vdem_2015_fake0, 
                             link = "logit")
tidy(model_beta_no_phi)
## # A tibble: 3 × 6
##   component term        estimate std.error statistic  p.value
##   <chr>     <chr>          <dbl>     <dbl>     <dbl>    <dbl>
## 1 mean      (Intercept)   -1.48     0.0785    -18.8  3.70e-79
## 2 mean      quotaTRUE      0.370    0.111       3.33 8.76e- 4
## 3 precision (phi)          9.12     0.962       9.48 2.62e-21
```

这段代码的目的是**创建一个图表，将实际观测数据中按有无配额 (`quota`) 分组的女性议员比例 (`prop_fem`) 的经验密度分布，与基于 `model_beta_no_phi` 模型的常数精度参数和（可能来自其他模型的）均值参数所定义的理论 Beta 分布的概率密度函数 (PDF) 进行比较。**

```R
# 提取常数精度参数
beta_phi <- model_beta_no_phi %>% 
  tidy() %>% 
  filter(component == "precision") %>% 
  pull(estimate)

# 创建图例标题字符串
no_quota_title <- paste0("dprop(mean = plogis(", round(beta_mu_intercept, 2),
                         "), size = ", round(beta_phi, 2), ")")

quota_title <- paste0("dprop(mean = plogis(", round(beta_mu_intercept, 2),
                      " + ", round(beta_mu_quota, 2), 
                      "), size = ", round(beta_phi, 2), ")")

ggplot(data = tibble(x = 0:1), aes(x = x)) +
  geom_density(data = vdem_2015_fake0, 
               aes(x = prop_fem, fill = quota), alpha = 0.5, color = NA) +
  stat_function(fun = dprop, size = 1,
                args = list(size = beta_phi, 
                            mean = plogis(beta_mu_intercept)),
                aes(color = no_quota_title)) +
  stat_function(fun = dprop, size = 1,
                args = list(size = beta_phi, 
                            mean = plogis(beta_mu_intercept + beta_mu_quota)),
                aes(color = quota_title)) +
  # 坐标轴格式化、颜色标度、标签和主题设置
  scale_x_continuous(labels = label_percent()) +
  scale_fill_viridis_d(option = "plasma", end = 0.8, name = "Quota",
                       guide = guide_legend(ncol = 1, order = 1)) +
  scale_color_viridis_d(option = "plasma", end = 0.8, direction = -1, name = "",
                        guide = guide_legend(reverse = TRUE, ncol = 1, order = 2)) +
  labs(x = "Proportion of women in parliament") +
  theme_clean() +
  theme(legend.position = "bottom")
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250515192256495.png)

### Average marginal effects

```R
avg_slopes(model_beta,
           newdata = datagrid(quota = c(FALSE, TRUE)))
## 
##   Term     Contrast Estimate Std. Error   z Pr(>|z|)  2.5 % 97.5 %
##  quota TRUE - FALSE   0.0501     0.0192 2.6  0.00926 0.0124 0.0878
## 
## Columns: term, contrast, estimate, std.error, statistic, p.value, conf.low, conf.high
```

## 3b: Beta regression, Bayesian style

这段代码的目的是**使用贝叶斯方法拟合一个 Beta 回归模型，来探究性别配额 (`quota`) 对女性议员比例 (`prop_fem`) 的影响。与传统的频率派 Beta 回归不同，这个模型不仅估计了 `quota` 对 `prop_fem` 均值的影响，还同时估计了 `quota` 对 Beta 分布精度参数 `phi` (φ) 的影响。**

---

注:
```
install.packages("cmdstanr", repos = c("https://mc-stan.org/r-packages/", getOption("repos")))
library(cmdstanr)
check_cmdstan_toolchain()
install_cmdstan()
# 如果在命令行工具中无法连接到Github，可以自行下载后指定本地安装路径
install_cmdstan(release_file = "您的本地文件路径/cmdstan-版本号.tar.gz")
```

---


```R
# 一个 brmsfit 对象，被赋值给 model_beta_bayes
model_beta_bayes <- brm(
  bf(prop_fem ~ quota,
     phi ~ quota),
  data = vdem_2015_fake0,
  family = Beta(),
  chains = 4, iter = 2000, warmup = 1000,
  cores = 4, seed = 1234, 
  # Use the cmdstanr backend for Stan because it's faster and more modern than
  # the default rstan You need to install the cmdstanr package first
  # (https://mc-stan.org/cmdstanr/) and then run cmdstanr::install_cmdstan() to
  # install cmdstan on your computer.
  backend = "cmdstanr",
  file = "model_beta_bayes"  # Save this so it doesn't have to always rerun
)
```

 与 `betareg()` 中 `model_beta` 的结果基本相同。

```R
tidy(model_beta_bayes, effects = "fixed")
## # A tibble: 4 × 7
##   effect component term            estimate std.error conf.low conf.high
##   <chr>  <chr>     <chr>              <dbl>     <dbl>    <dbl>     <dbl>
## 1 fixed  cond      (Intercept)       -1.44     0.0864  -1.61      -1.27 
## 2 fixed  cond      phi_(Intercept)    2.02     0.140    1.74       2.29 
## 3 fixed  cond      quotaTRUE          0.296    0.114    0.0713     0.518
## 4 fixed  cond      phi_quotaTRUE      0.436    0.211    0.0159     0.837
```

### Working with the posterior

第一部分是posterior_beta的创建，接着绘制后验分布可视化图形。

```R
# 提取贝叶斯 β 回归模型的后验样本，并整理为长格式
posterior_beta <- model_beta_bayes %>% 
  # 使用正则表达式提取所有以"b_"开头的变量（固定效应）
  gather_draws(`b_.*`, regex = TRUE) %>% 
  # 创建新列：component区分均值/精度参数，intercept标记截距项
  mutate(component = ifelse(str_detect(.variable, "phi_"), "Precision", "Mean"),
         intercept = str_detect(.variable, "Intercept"))

# 绘制后验分布可视化图形
ggplot(posterior_beta, aes(x = .value, y = fct_rev(.variable), fill = component)) +
  geom_vline(xintercept = 0) +
  stat_halfeye(aes(slab_alpha = intercept), 
               .width = c(0.8, 0.95), point_interval = "median_hdi") +
  scale_fill_viridis_d(option = "viridis", end = 0.6) +
  scale_slab_alpha_discrete(range = c(1, 0.4)) +
  guides(fill = "none", slab_alpha = "none") +
  labs(x = "Coefficient", y = "Variable",
       caption = "80% and 95% credible intervals shown in black") +
  facet_wrap(vars(component), ncol = 1, scales = "free_y") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250528143756579.png)

处理贝叶斯模型的后验结果。

```R
# 提取贝叶斯 β 回归模型的后验样本并处理参数
model_beta_bayes %>% 
  spread_draws(`b_.*`, regex = TRUE) %>% 
  mutate(across(starts_with("b_phi"), ~exp(.))) %>%
  mutate(across((!starts_with(".") & !starts_with("b_phi")), ~plogis(.))) %>%
  gather_variables() %>% 
  median_hdi()
## # A tibble: 4 × 7
##   .variable       .value .lower .upper .width .point .interval
##   <chr>            <dbl>  <dbl>  <dbl>  <dbl> <chr>  <chr>    
## 1 b_Intercept      0.192  0.167  0.219   0.95 median hdi      
## 2 b_phi_Intercept  7.57   5.66   9.80    0.95 median hdi      
## 3 b_phi_quotaTRUE  1.55   0.997  2.27    0.95 median hdi      
## 4 b_quotaTRUE      0.573  0.522  0.630   0.95 median hdi
```

### Posterior average marginal effects

#### Binary predictor

计算贝叶斯beta回归模型中分类变量`quota`的平均对比效应。

```R
model_beta_bayes %>% 
  avg_comparisons(variables = "quota")
## 
##   Term     Contrast Estimate  2.5 % 97.5 %
##  quota TRUE - FALSE     0.05 0.0121 0.0875
## 
## Columns: term, contrast, estimate, conf.low, conf.high
```

直接对比存在/不存在性别配额时女性议员比例的差异分布。

```R
# Plug a dataset where quota is FALSE and TRUE into the model
beta_bayes_pred <- model_beta_bayes %>% 
  epred_draws(newdata = tibble(quota = c(FALSE, TRUE)))

ggplot(beta_bayes_pred, aes(x = .epred, y = quota, fill = quota)) +
  stat_halfeye(.width = c(0.8, 0.95), point_interval = "median_hdi") +
  scale_x_continuous(labels = label_percent()) +
  scale_fill_viridis_d(option = "plasma", end = 0.8) +
  guides(fill = "none") +
  labs(x = "Predicted proportion of women in parliament", y = NULL,
       caption = "80% and 95% credible intervals shown in black") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601141351467.png)

可视化贝叶斯beta回归模型中`quota`变量的平均边际效应后验分布。

```R
beta_bayes_pred_diff <- beta_bayes_pred %>% 
  compare_levels(variable = .epred, by = quota)

beta_bayes_pred_diff %>% median_hdi()
## # A tibble: 1 × 7
##   quota        .epred .lower .upper .width .point .interval
##   <chr>         <dbl>  <dbl>  <dbl>  <dbl> <chr>  <chr>    
## 1 TRUE - FALSE 0.0500 0.0136 0.0889   0.95 median hdi

ggplot(beta_bayes_pred_diff, aes(x = .epred)) +
  stat_halfeye(.width = c(0.8, 0.95), point_interval = "median_hdi",
               fill = "#bc3032") +
  scale_x_continuous(labels = label_pp) +
  labs(x = "Average marginal effect of having a gender-based\nquota on the proportion of women in parliament", 
       y = NULL, caption = "80% and 95% credible intervals shown in black") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601142034417.png)

#### Continuous predictor

构建一个贝叶斯beta回归模型，同时建模因变量的均值($μ$)和精度($φ$)。

```R
model_beta_bayes_1 <- brm(
  bf(prop_fem ~ quota + polyarchy,  # 建模均值参数 μ：女性议员比例与配额和民主程度的线性组合
     phi ~ quota + polyarchy),      # 建模精度参数 φ：相同变量解释分布的离散程度
  data = vdem_2015_fake0,           # 使用人工处理过 0 值的数据集（beta 分布不支持0/1）
  family = Beta(),                  # 指定 beta 分布作为响应变量分布
  init = 0,                         # 强制所有参数初始值为 0，避免 Stan 给 phi 生成负值导致采样失败
  chains = 4,                       # 运行 4 条独立 MCMC 链
  iter = 2000,                      # 每条链总迭代 2000 次
  warmup = 1000,                    # 前 1000 次作为预热期（ burn-in ）丢弃
  cores = 4,                        # 使用 4 个 CPU 核心并行计算
  seed = 1234,                      # 设定随机种子保证结果可重复
  backend = "cmdstanr",             # 使用更高效的 cmdstanr 接口替代默认 rstan
  file = "model_beta_bayes_1"       # 将模型结果保存到文件避免重复计算
)

```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601155950494.png)

计算并显示在贝叶斯 Beta 回归模型 `model_beta_bayes_1` 中，预测变量 `polyarchy` 对响应变量（女性议员比例 `prop_fem_transformed`）的平均边际效应。

```R
avg_slopes(model_beta_bayes_1, variables = "polyarchy")
## 
##       Term Estimate    2.5 %  97.5 %
##  polyarchy  0.00126 0.000502 0.00196
## 
## Columns: term, estimate, conf.low, conf.high
```

显示了在 `polyarchy` 的不同水平上，其对女性议员比例的边际贡献是不同的。

```R
slopes(model_beta_bayes_1, 
       variables = "polyarchy",
       newdata = datagrid(polyarchy = c(20, 50, 80)))
## 
##       Term Estimate    2.5 %  97.5 % quota polyarchy
##  polyarchy 0.000991 0.000438 0.00140 FALSE        20
##  polyarchy 0.001153 0.000464 0.00178 FALSE        50
##  polyarchy 0.001314 0.000489 0.00219 FALSE        80
## 
## Columns: rowid, term, estimate, conf.low, conf.high, predicted, predicted_hi, predicted_lo, tmp_idx, prop_fem, quota, polyarchy
```

通过可视化后验分布，详细地展示和比较在 `polyarchy` 的不同水平（20, 50, 80）上，`polyarchy` 对女性议员比例的边际效应及其不确定性。这比仅仅查看点估计和汇总的置信区间提供了更丰富的信息。

```R
ame_beta_bayes_1 <- model_beta_bayes_1 %>% 
  slopes(variables = "polyarchy",
         newdata = datagrid(polyarchy = c(20, 50, 80))) %>% 
  posterior_draws()

ggplot(ame_beta_bayes_1, aes(x = draw, fill = factor(polyarchy))) +
  geom_vline(xintercept = 0) +
  stat_halfeye(.width = c(0.8, 0.95), point_interval = "median_hdi",
               slab_alpha = 0.75) +
  scale_x_continuous(labels = label_pp_tiny) +
  scale_fill_viridis_d(option = "viridis", end = 0.6) +
  labs(x = "Average marginal effect of polyarchy (democracy)", 
       y = "Density", fill = "Polyarchy",
       caption = "80% and 95% credible intervals shown in black") +
  theme_clean() +
  theme(legend.position = "bottom")
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601160407154.png)

## 4: Zero-inflated beta regression, Bayesian style

### A new parameter for modeling the zero process

检查并统计在 `vdem_2015` 数据框中，`prop_fem`（女性议员比例）列的数值等于0的观测数量及其所占的比例。

```R
vdem_2015 %>% # 使用 vdem_2015 数据框
  count(prop_fem == 0) %>% # 对 prop_fem 列是否等于 0 这个条件进行计数
                           # 这会生成一个新列，其值为 TRUE (如果 prop_fem == 0) 或 FALSE (如果 prop_fem != 0)
                           # count() 函数会统计 TRUE 和 FALSE 各有多少个，并将结果存储在名为 'n' 的列中
  mutate(prop = n / sum(n)) # 添加一个新列 'prop'，计算每个组 (TRUE 和 FALSE) 的占比
                            # n 是当前组的计数，sum(n) 是总计数

## 输出结果:
##   prop_fem == 0   n   prop    # 列名: prop_fem == 0 (条件判断的结果), n (计数), prop (占比)
## 1         FALSE 169 0.9826    # 当 prop_fem 不等于 0 时 (FALSE)，有 169 条记录，占比约为 0.9826 (或 98.26%)
## 2          TRUE   3 0.0174    # 当 prop_fem 等于 0 时 (TRUE)，有 3 条记录，占比约为 0.0174 (或 1.74%)

```

拟合一个贝叶斯零膨胀 Beta 回归模型 (`zero_inflated_beta`)。

```R
model_beta_zi_int_only <- brm( # 将 brms 模型拟合结果赋值给变量 model_beta_zi_int_only
  bf(prop_fem ~ quota,       # 定义模型公式的第一部分 (Beta 分布的均值 mu): prop_fem (响应变量) 由 quota (预测变量) 预测
     phi ~ quota,            # 定义模型公式的第二部分 (Beta 分布的精度参数 phi): phi 也由 quota 预测
     zi ~ 1),                # 定义模型公式的第三部分 (零膨胀概率 zi): zi 只包含一个截距项 (即不依赖于任何预测变量，是一个常数概率)
  data = vdem_clean,         # 指定使用 vdem_clean 数据框进行模型拟合
  family = zero_inflated_beta(), # 指定模型的分布族为零膨胀 Beta 分布 (Zero-Inflated Beta)
                                 # 这适用于响应变量是比例数据，并且可能包含过多的精确的0值的情况
  chains = 4, iter = 2000, warmup = 1000, # MCMC 设置: 4条链，每条链迭代2000次，其中1000次为预热/丢弃
  cores = 4, seed = 1234,    # 使用4个CPU核心并行计算，并设置随机种子以保证结果可复现
  backend = "cmdstanr",      # 指定使用 cmdstanr 作为 Stan 的后端接口
  file = "model_beta_zi_int_only" # 将编译好的模型和采样结果保存到名为 "model_beta_zi_int_only" 的相关文件中
                                  # 这样下次运行时如果模型未改变，可以跳过编译过程
)

```

```R
tidy(model_beta_zi_int_only, effects = "fixed")
## # A tibble: 5 × 7
##   effect component term            estimate std.error conf.low conf.high
##   <chr>  <chr>     <chr>              <dbl>     <dbl>    <dbl>     <dbl>
## 1 fixed  cond      (Intercept)       -1.42     0.0229   -1.47     -1.38 
## 2 fixed  cond      phi_(Intercept)    2.37     0.0437    2.29      2.46 
## 3 fixed  zi        (Intercept)       -4.04     0.186    -4.43     -3.70 
## 4 fixed  cond      quotaTRUE          0.303    0.0331    0.238     0.369
## 5 fixed  cond      phi_quotaTRUE      0.237    0.0677    0.105     0.368
```

这段代码的目的是量化模型估计的“额外”产生0的概率。首先提取出零膨胀部分的截距（在logit尺度上），然后通过逆logit变换将其转换为一个更易于解释的概率值。输出结果 `0.0172` 表明，根据这个模型，大约有 1.72% 的概率观测到的 `prop_fem` 值为0是由于零膨胀过程产生的，而不是来自 Beta 分布本身。

```R
zi_intercept <- tidy(model_beta_zi_int_only, effects = "fixed") %>% 
  filter(component == "zi", term == "(Intercept)") %>% 
  pull(estimate)

# Logit scale intercept
zi_intercept
## b_zi_Intercept 
##          -4.04

# Transformed to a probability/proportion
plogis(zi_intercept)
## b_zi_Intercept 
##         0.0172
```

这段代码的目的是通过后验预测的可视化，来理解零膨胀 Beta 回归模型在不同 `quota` 条件下对女性议员比例的预测行为，特别是区分和展示模型预测的精确0值（来自零膨胀机制）与来自 Beta 分布的连续比例值。

```R
# 步骤1: 从模型中生成后验预测抽样，并进行一些转换
beta_zi_pred_int <- model_beta_zi_int_only %>% # 使用已拟合的零膨胀 Beta 回归模型 model_beta_zi_int_only
  predicted_draws(newdata = tibble(quota = c(FALSE, TRUE))) %>% # 生成后验预测抽样。
                                                                # newdata 指定了在 quota 为 FALSE 和 TRUE 两种情况下进行预测。
                                                                # predicted_draws 会为每个 MCMC 抽样和 newdata 中的每一行生成一个预测值 (.prediction)。
  mutate(is_zero = .prediction == 0,                         # 添加一个新列 is_zero，如果预测值 .prediction 精确为0，则为 TRUE，否则为 FALSE。
         .prediction = ifelse(is_zero, .prediction - 0.01, .prediction)) # 修改 .prediction 列：
                                                                         # 如果 is_zero 为 TRUE (即原始预测为0)，则将 .prediction 设为 -0.01。
                                                                         # 这样做是为了在后续的直方图中将精确的0值与非常接近0的非零值区分开，
                                                                         # 通过将0值移动到一个略微为负的位置，使其在直方图中形成一个独立的条形。
                                                                         # 如果 is_zero 为 FALSE，则 .prediction 保持不变。

# 步骤2: 使用 ggplot2 可视化后验预测的分布
ggplot(beta_zi_pred_int, aes(x = .prediction)) + # 设置绘图数据和 aesthetics: x 轴为 (可能被修改过的) 后验预测值 .prediction
  geom_histogram(aes(fill = is_zero), binwidth = 0.025, # 绘制直方图:
                                                        # fill 根据 is_zero (是否为原始的0预测) 进行填充颜色区分
                 boundary = 0, color = "white") +      # binwidth 设置条柱的宽度，boundary 设置条柱的边界从0开始，color 设置条柱边框颜色
  geom_vline(xintercept = 0) + # 在 x=0 处添加一条垂直线作为参考
  scale_x_continuous(labels = label_percent()) + # 格式化 x 轴标签 (假设 label_percent 是一个将数值转为百分比显示的函数)
  scale_fill_viridis_d(option = "plasma", end = 0.5, # 为填充颜色设置 viridis (plasma) 调色板
                       guide = guide_legend(reverse = TRUE)) + # 设置图例，并反转图例项的顺序
  labs(x = "Predicted proportion of women in parliament", # 设置 x 轴标签
       y = "Count", # 设置 y 轴标签
       fill = "Is zero?") + # 设置图例标题
  facet_wrap(vars(quota), ncol = 2, # 根据 quota 变量的值进行分面 (创建子图)，每行显示2个子图
             labeller = labeller(quota = c(`TRUE` = "Quota", # 自定义分面标签的显示文本
                                           `FALSE` = "No Quota"))) +
  theme_clean() + # 应用自定义的 ggplot 主题 (假设 theme_clean 已定义)
  theme(legend.position = "bottom") # 将图例放置在底部

```
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601161639828.png)

拟合一个贝叶斯零膨胀 Beta 回归模型 (`zero_inflated_beta`)，其中**所有三个主要参数**（Beta 分布的均值 `mu`、Beta 分布的精度 `phi`、以及零膨胀概率 `zi`）都允许受到预测变量 `quota` 的影响。

```R
model_beta_zi <- brm(
  bf(prop_fem ~ quota,
     phi ~ quota,
     zi ~ quota),
  data = vdem_clean,
  family = zero_inflated_beta(),
  chains = 4, iter = 2000, warmup = 1000,
  cores = 4, seed = 1234,
  backend = "cmdstanr",
  file = "model_beta_zi"
)

tidy(model_beta_zi, effects = "fixed")
## # A tibble: 6 × 7
##   effect component term            estimate std.error conf.low conf.high
##   <chr>  <chr>     <chr>              <dbl>     <dbl>    <dbl>     <dbl>
## 1 fixed  cond      (Intercept)       -1.42     0.0236   -1.47     -1.38 
## 2 fixed  cond      phi_(Intercept)    2.37     0.0433    2.29      2.45 
## 3 fixed  zi        (Intercept)       -3.53     0.192    -3.93     -3.18 
## 4 fixed  cond      quotaTRUE          0.304    0.0334    0.239     0.368
## 5 fixed  cond      phi_quotaTRUE      0.236    0.0696    0.101     0.371
## 6 fixed  zi        quotaTRUE         -5.57     2.64    -12.2      -2.21

zi_intercept <- tidy(model_beta_zi, effects = "fixed") %>% 
  filter(component == "zi", term == "(Intercept)") %>% 
  pull(estimate)

zi_quota <- tidy(model_beta_zi, effects = "fixed") %>% 
  filter(component == "zi", term == "quotaTRUE") %>% 
  pull(estimate)

plogis(zi_intercept + zi_quota) - plogis(zi_intercept)
## b_zi_Intercept 
##        -0.0283
```

这段代码通过提取和分析零膨胀 Beta 回归模型中零膨胀概率 (`zi`) 的后验预测，量化了 `quota` 对数据中产生额外0值概率的影响。结果表明，在这个模型中，设置配额显著降低了观测到女性议员比例为零的可能性。

```R
pred_beta_zi <- model_beta_zi %>% 
  epred_draws(newdata = expand_grid(quota = c(FALSE, TRUE)),
              dpar = "zi")

# We now have columns for the overall prediction (.epred) and for the
# zero-inflation probability (zi)
head(pred_beta_zi)
## # A tibble: 6 × 7
## # Groups:   quota, .row [1]
##   quota  .row .chain .iteration .draw .epred     zi
##   <lgl> <int>  <int>      <int> <int>  <dbl>  <dbl>
## 1 FALSE     1     NA         NA     1  0.186 0.0358
## 2 FALSE     1     NA         NA     2  0.188 0.0247
## 3 FALSE     1     NA         NA     3  0.187 0.0388
## 4 FALSE     1     NA         NA     4  0.187 0.0388
## 5 FALSE     1     NA         NA     5  0.191 0.0274
## 6 FALSE     1     NA         NA     6  0.186 0.0162

# Look at the average zero-inflation probability across quota
pred_beta_zi %>%
  group_by(quota) %>% 
  median_hdci(zi)
## # A tibble: 2 × 7
##   quota       zi   .lower  .upper .width .point .interval
##   <lgl>    <dbl>    <dbl>   <dbl>  <dbl> <chr>  <chr>    
## 1 FALSE 0.0286   1.91e- 2 0.0396    0.95 median hdci     
## 2 TRUE  0.000220 3.91e-11 0.00233   0.95 median hdci

pred_beta_zi %>% 
  compare_levels(variable = zi, by = quota) %>% 
  median_hdci()
## # A tibble: 1 × 7
##   quota             zi  .lower  .upper .width .point .interval
##   <chr>          <dbl>   <dbl>   <dbl>  <dbl> <chr>  <chr>    
## 1 TRUE - FALSE -0.0281 -0.0389 -0.0179   0.95 median hdci
```

这段代码通过计算和可视化，清晰地展示了“实施性别配额”这一政策对“一个国家议会中女性议员比例由于结构性因素（零膨胀机制）而为零的概率”的平均影响。

```R
mfx_quota_zi <- pred_beta_zi %>% 
  compare_levels(variable = zi, by = quota)

ggplot(mfx_quota_zi, aes(x = zi)) +
  stat_halfeye(.width = c(0.8, 0.95), point_interval = "median_hdi",
               fill = "#fb9e07") +
  scale_x_continuous(labels = label_pp) +
  labs(x = "Average marginal effect of having a gender-based quota\non the proportion of countries with zero women in parliament", 
       y = NULL, caption = "80% and 95% credible intervals shown in black") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601162818972.png)

这段代码的目的是通过后验预测的可视化，来展示和比较在 `model_beta_zi`（一个允许配额影响零膨胀概率的模型）下，有配额和无配额条件对女性议员比例预测分布的全面影响。

```R
beta_zi_pred <- model_beta_zi %>% 
  predicted_draws(newdata = tibble(quota = c(FALSE, TRUE))) %>% 
  mutate(is_zero = .prediction == 0,
         .prediction = ifelse(is_zero, .prediction - 0.01, .prediction))

ggplot(beta_zi_pred, aes(x = .prediction)) +
  geom_histogram(aes(fill = is_zero), binwidth = 0.025, 
                 boundary = 0, color = "white") +
  geom_vline(xintercept = 0) +
  scale_x_continuous(labels = label_percent()) +
  scale_fill_viridis_d(option = "plasma", end = 0.5,
                       guide = guide_legend(reverse = TRUE)) +
  labs(x = "Predicted proportion of women in parliament", 
       y = "Count", fill = "Is zero?") +
  facet_wrap(vars(quota), ncol = 2,
             labeller = labeller(quota = c(`TRUE` = "Quota", 
                                           `FALSE` = "No Quota"))) +
  theme_clean() +
  theme(legend.position = "bottom")
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601163007848.png)

### Average marginal effects, incorporating the zero process

这段代码计算并可视化了在考虑了零膨胀的复杂模型 (`model_beta_zi`) 中，`quota` 对女性议员比例期望值的总体平均影响。结果表明，平均而言，实施配额会使女性议员的预期比例提高约5.75个百分点，并且这个效应是统计上可信的。

```R
ame_beta_zi <- model_beta_zi %>%
  avg_comparisons(variables = "quota") %>% 
  posterior_draws()

ame_beta_zi %>% median_hdi(draw)
## # A tibble: 1 × 6
##     draw .lower .upper .width .point .interval
##    <dbl>  <dbl>  <dbl>  <dbl> <chr>  <chr>    
## 1 0.0575 0.0466 0.0688   0.95 median hdi

ggplot(ame_beta_zi, aes(x = draw)) +
  stat_halfeye(.width = c(0.8, 0.95), point_interval = "median_hdi",
               fill = "#bc3032") +
  scale_x_continuous(labels = label_pp) +
  labs(x = "Average marginal effect of having a gender-based\nquota on the proportion of women in parliament", y = NULL,
       caption = "80% and 95% credible intervals shown in black") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601163313570.png)

### Special case #1: Zero-one-inflated beta regression

以下是围绕代码的零一膨胀beta回归（ZOIB）模型技术提炼：

== 核心应用场景 == 当响应变量同时存在：

- 大量0值（左截断）
- 大量1值（右截断）
- 及(0,1)区间连续值 （传统beta回归无法处理双截断情况）

== 模型结构分解 == ZOIB通过三个子模型联合建模：

1. 【极端值判断】逻辑回归：预测观测值是否属于{0,1}极端值（zoi参数）
2. 【极值类型判断】条件逻辑回归：当为极端值时，预测是0还是1（coi参数）
3. 【连续值建模】Beta回归：对非极端值的(0,1)连续值建模（mu参数 + phi精度参数）

== BRMS代码实现 ==

```R
brm(
  bf(
    outcome ~ covariates,   # 连续值的均值参数（logit尺度）
    phi ~ covariates,       # 连续值的精度参数（log尺度） 
    zoi ~ covariates,       # 极端值概率参数（logit尺度）
    coi ~ covariates        # 条件1值概率参数（logit尺度）
  ),
  data = df,
  family = zero_one_inflated_beta(),
  chains = 4, 
  iter = 2000
)

```

### Special case #2: One-inflated beta regression

这段代码是一个简单的数据转换步骤，用于计算并添加一个与现有比例变量 `prop_fem` 互补的比例变量 `prop_not_fem`。

```R
mutate(prop_not_fem = 1 - prop_fem)
```

## Super fancy detailed model with lots of moving parts, just for fun

### Set better priors

这段代码演示了logit函数与概率之间的转换关系，并可视化logistic函数的S型曲线。

```R
qlogis(c(0.01, 0.001, 0.0001))
## [1] -4.60 -6.91 -9.21
qlogis(c(0.99, 0.999, 0.9999))
## [1] 4.60 6.91 9.21

tibble(x = seq(-8, 8, by = 0.1)) %>% 
  mutate(y = plogis(x)) %>% 
  ggplot(aes(x = x, y = y)) +
  geom_line(size = 1) +
  labs(x = "Logit scale", y = "Probability scale") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601170217326.png)

这段代码包含数据预处理、贝叶斯模型设定、先验分布检查及正态分布可视化四个部分。

```R
vdem_clean <- vdem_clean %>% 
  # Scale polyarchy back down to 0-1 values to help Stan with modeling
  mutate(polyarchy = polyarchy / 100) %>% 
  # Make region and year factors instead of numbers
  mutate(region = factor(region),
         year = factor(year))

# Create the model formula
fancy_formula <- bf(
  # mu (mean) part
  prop_fem ~ quota + polyarchy + corruption + 
    civil_liberties + (1 | year) + (1 | region),
  # phi (precision) part
  phi ~ quota + (1 | year) + (1 | region),
  # alpha (zero-inflation) part
  zi ~ quota + polyarchy + (1 | year) + (1 | region)
)

get_prior(
  fancy_formula,
  data = vdem_clean,
  family = zero_inflated_beta()
)
##                 prior     class            coef  group resp dpar nlpar lb ub       source
##                (flat)         b                                                   default
##                (flat)         b civil_liberties                              (vectorized)
##                (flat)         b      corruption                              (vectorized)
##                (flat)         b       polyarchy                              (vectorized)
##                (flat)         b       quotaTRUE                              (vectorized)
##  student_t(3, 0, 2.5) Intercept                                                   default
##  student_t(3, 0, 2.5)        sd                                         0         default
##  student_t(3, 0, 2.5)        sd                 region                  0    (vectorized)
##  student_t(3, 0, 2.5)        sd       Intercept region                  0    (vectorized)
##  student_t(3, 0, 2.5)        sd                   year                  0    (vectorized)
##  student_t(3, 0, 2.5)        sd       Intercept   year                  0    (vectorized)
##                (flat)         b                              phi                  default
##                (flat)         b       quotaTRUE              phi             (vectorized)
##  student_t(3, 0, 2.5) Intercept                              phi                  default
##  student_t(3, 0, 2.5)        sd                              phi        0         default
##  student_t(3, 0, 2.5)        sd                 region       phi        0    (vectorized)
##  student_t(3, 0, 2.5)        sd       Intercept region       phi        0    (vectorized)
##  student_t(3, 0, 2.5)        sd                   year       phi        0    (vectorized)
##  student_t(3, 0, 2.5)        sd       Intercept   year       phi        0    (vectorized)
##                (flat)         b                               zi                  default
##                (flat)         b       polyarchy               zi             (vectorized)
##                (flat)         b       quotaTRUE               zi             (vectorized)
##        logistic(0, 1) Intercept                               zi                  default
##  student_t(3, 0, 2.5)        sd                               zi        0         default
##  student_t(3, 0, 2.5)        sd                 region        zi        0    (vectorized)
##  student_t(3, 0, 2.5)        sd       Intercept region        zi        0    (vectorized)
##  student_t(3, 0, 2.5)        sd                   year        zi        0    (vectorized)
##  student_t(3, 0, 2.5)        sd       Intercept   year        zi        0    (vectorized)

ggplot(data = tibble(x = -4:4), aes(x = x)) +
  geom_function(fun = dnorm, args = list(mean = 0, sd = 1), linewidth = 1) +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601170652190.png)

```R
priors <- c(set_prior("student_t(3, 0, 2.5)", class = "Intercept"),
            set_prior("normal(0, 1)", class = "b"))
```

### Run the model  运行模型

这段代码使用 **brms 包**拟合一个贝叶斯零膨胀 Beta 回归模型。

```R
fancy_model <- brm(
  fancy_formula,
  data = vdem_clean,
  family = zero_inflated_beta(),
  prior = priors,
  init = 0,
  control = list(adapt_delta = 0.97,
                 max_treedepth = 12),
  chains = 4, iter = 2000, warmup = 1000,
  cores = 4, seed = 1234, 
  threads = threading(2),
  backend = "cmdstanr",
  file = "fancy_model"
)
```

### Analyze and plot the results

```R
tidy(fancy_model)
## # A tibble: 16 × 8
##    effect   component group  term                estimate std.error  conf.low conf.high
##    <chr>    <chr>     <chr>  <chr>                  <dbl>     <dbl>     <dbl>     <dbl>
##  1 fixed    cond      <NA>   (Intercept)         -0.654      0.239   -1.12      -0.196 
##  2 fixed    cond      <NA>   phi_(Intercept)      2.83       0.274    2.26       3.34  
##  3 fixed    zi        <NA>   (Intercept)         -0.888      3.56    -6.55       6.92  
##  4 fixed    cond      <NA>   quotaTRUE            0.367      0.0294   0.310      0.424 
##  5 fixed    cond      <NA>   polyarchy           -0.104      0.152   -0.409      0.192 
##  6 fixed    cond      <NA>   corruption          -0.738      0.0721  -0.885     -0.599 
##  7 fixed    cond      <NA>   civil_liberties     -0.589      0.132   -0.843     -0.328 
##  8 fixed    cond      <NA>   phi_quotaTRUE       -0.0729     0.0763  -0.223      0.0770
##  9 fixed    zi        <NA>   quotaTRUE           -6.81       4.60   -18.9       -2.11  
## 10 fixed    zi        <NA>   polyarchy            0.00477    0.949   -1.88       1.85  
## 11 ran_pars cond      region sd__(Intercept)      0.483      0.227    0.232      1.05  
## 12 ran_pars cond      year   sd__(Intercept)      0.0817     0.0312   0.0382     0.153 
## 13 ran_pars cond      region sd__phi_(Intercept)  0.610      0.288    0.293      1.36  
## 14 ran_pars cond      year   sd__phi_(Intercept)  0.0367     0.0312   0.00121    0.117 
## 15 ran_pars zi        region sd__(Intercept)      8.47       6.11     1.91      24.9   
## 16 ran_pars zi        year   sd__(Intercept)      0.204      0.171    0.00770    0.653
```

性别配额政策对提高女性议员比例存在显著正向效应，后验分布集中在5.5%~7.5%区间，结果稳健可信。

```R
# 后验效应提取
ame_fancy_zi_quota <- fancy_model %>%
  avg_comparisons(variables = "quota") %>% 
  posterior_draws()

# 效应值统计汇总
ame_fancy_zi_quota %>% median_hdi(draw)
## # A tibble: 1 × 6
##     draw .lower .upper .width .point .interval
##    <dbl>  <dbl>  <dbl>  <dbl> <chr>  <chr>    
## 1 0.0653 0.0556 0.0749   0.95 median hdi

# 效应分布可视化
ggplot(ame_fancy_zi_quota, aes(x = draw)) +
  stat_halfeye(.width = c(0.8, 0.95), point_interval = "median_hdi",
               fill = "#bc3032") +
  scale_x_continuous(labels = label_pp) +
  labs(x = "Average marginal effect of having a gender-based\nquota on the proportion of women in parliament", y = NULL,
       caption = "80% and 95% credible intervals shown in black") +
  theme_clean()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601173107570.png)

```R
r_fancy <- ame_fancy_zi_quota %>% median_hdi(draw)
```

生成并可视化民主程度（polyarchy）与性别配额（quota）对女性议员比例的联合预测效应。

```R
ame_fancy_zi_polyarchy_quota <- fancy_model %>% 
  predictions(newdata = datagrid(quota = unique,
                                 polyarchy = seq(0, 1, by = 0.1))) %>% 
  posterior_draws() %>% 
  # Scale polyarchy back up for plotting
  mutate(polyarchy = polyarchy * 100)

# With tidybayes instead
# ame_fancy_zi_polyarchy_quota <- fancy_model %>%
#   epred_draws(newdata = datagrid(model = fancy_model, 
#                                  quota = unique, 
#                                  polyarchy = seq(0, 1, by = 0.1)))

ggplot(ame_fancy_zi_polyarchy_quota,
       aes(x = polyarchy, y = draw, color = quota, fill = quota)) +
  stat_lineribbon(aes(fill_ramp = stat(level))) +
  scale_y_continuous(labels = label_percent()) +
  scale_fill_viridis_d(option = "plasma", end = 0.8) +
  scale_color_viridis_d(option = "plasma", end = 0.8) +
  scale_fill_ramp_discrete(range = c(0.2, 0.7)) +
  facet_wrap(vars(quota), ncol = 2,
             labeller = labeller(quota = c(`TRUE` = "Quota",
                                           `FALSE` = "No Quota"))) +
  labs(x = "Polyarchy (democracy)",
       y = "Predicted proportion of women MPs",
       fill = "Quota", color = "Quota",
       fill_ramp = "Credible interval") +
  theme_clean() +
  theme(legend.position = "bottom")
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250601173451803.png)

## References[](https://www.andrewheiss.com/blog/2021/11/08/beta-regression-guide/#references-1)

Keele, Luke, Randolph T. Stevenson, and Felix Elwert. 2020. “The Causal Interpretation of Estimated Associations in Regression Models.” _Political Science Research and Methods_ 8 (1): 1–13. [https://doi.org/10.1017/psrm.2019.31](https://doi.org/10.1017/psrm.2019.31).

Kneib, Thomas, Alexander Silbersdorff, and Benjamin Säfken. 2021. “Rage Against the Mean—a Review of Distributional Regression Approaches.” _Econometrics and Statistics_. [https://doi.org/10.1016/j.ecosta.2021.07.006](https://doi.org/10.1016/j.ecosta.2021.07.006).

Tripp, Aili Mari, and Alice Kang. 2008. “The Global Impact of Quotas: On the Fast Track to Increased Female Legislative Representation.” _Comparative Political Studies_ 41 (3): 338–61. [https://doi.org/10.1177/0010414006297342](https://doi.org/10.1177/0010414006297342).
