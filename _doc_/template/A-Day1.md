
# 2024年暑期初级班-Day1课程答疑

> Update: `2024/7/15 22:30`

### Q1. 请问什么时候使用全局暂元什么时候用局部暂用？在使用的时候有什么区别
答：在Stata中,全局和局部宏(在Stata中称为“暂元”)是两种用于存储文本的方法。它们的主要区别在于作用域和生命周期。
- 局部宏(Local Macros)的作用域仅限于定义它们的程序或命令块。一旦程序或命令块结束,局部宏就会消失。这使得局部宏非常适合在程序中临时存储和操作信息。例如，你可能会在一个循环中使用局部宏来存储每次迭代的结果。
- 全局宏(Global Macros)的作用域则是整个Stata会话。一旦你定义了一个全局宏,它就会一直存在,直到你明确地删除它,或者你结束了Stata会话。
global是在开始设定的，local更多地在命令块或者循环中短暂定义。
总的来说,需要在整个Stata会话中访问某个值时使用全局宏,需要临时存储某个值时使用局部宏。

(回答人：连玉君&李萌)

### Q2.上课时老师提到数据框frames是什么

答：Stata 中数据框 **frames** 是一个由行和列组成的表格，每一列代表一个变量，每一行代表一个观察值。每个变量可以有不同的数据类型，例如数值型、字符型、日期型等。数据框让我们可以看到、管理和操作数据，比如进行数据的筛选、排序、合并、计算等操作。在 Stata 中，我们可以使用命令如 `merge`、`append`、`sort` 等来对数据框内容进行填补、排序等。

相关推文如下：
- [陈卓然](https://www.lianxh.cn/search.html?s=陈卓然), 2022, [Stata数据处理：数据框使用教程](https://www.lianxh.cn/details/976.html), 连享会 No.976.
- [徐嘉树](https://www.lianxh.cn/search.html?s=徐嘉树), 2021, [Stata：如何同时对多个数据框操作-frame](https://www.lianxh.cn/details/755.html), 连享会 No.755.
- , 2024, [Stata绘图：地图神器geoplot](https://www.lianxh.cn/details/1383.html), 连享会 No.1383.
- [郭盼亭](https://www.lianxh.cn/search.html?s=郭盼亭), 2023, [Stata：数据框纵向追加-append-fframeappend](https://www.lianxh.cn/details/1231.html), 连享会 No.1231.
- [张弛](https://www.lianxh.cn/search.html?s=张弛), 2024, [Stata 绘图：用 `geoplot` 绘制中国地图](https://www.lianxh.cn/details/1401.html), 连享会 No.1401.

  (回答人：连玉君&陈荟文)

### Q3.能不能解释一下3.2.3执行命令后的单值结果中课件440行-479行里面的字母都是什么意思

```stata
*----------------------------- 
*-3.2.3  执行命令后的单值结果

  sysuse "auto.dta", clear
  sum price , d
  return list
    dis r(mean)
	
  tabstat price wei len mpg turn foreign,   ///
    stat(mean sd p50 min max) format(%7.2f) c(s) 
                                                         /*
 variable |    mean        sd       p50       min       max
----------+------------------------------------------------
    price | 6165.26   2949.50   5006.50   3291.00  15906.00
          |                   ....                         
  foreign |    0.30      0.46      0.00      0.00      1.00
-----------------------------------------------------------
                                                          */

  reg price wei len mpg foreign 
  ereturn list
/*
scalars:
            e(N) =  74
         e(df_m) =  4
         e(df_r) =  69
            e(F) =  21.00608903000541
           e(r2) =  .54909138813248
          e(rss) =  286356456.2102971
         e(r2_a) =  .5229517584590007
           e(ll) =  -666.2427169040275
macros:
      e(cmdline) : "regress price wei len mpg foreign"
        e(title) : "Linear regression"
matrices:
            e(b) :  1 x 5
            e(V) :  5 x 5
functions:
            e(sample)   
*/  
  dis _b[wei]    //变量的估计系数
  dis _b[_cons]  //常数项
  dis e(N)       //样本数
  dis e(r2)      //R2  
  esttab, s(r2 r2_a F N) b(%4.2f) nogap
/*
  ----------------------------
                        (1)   
                      price   
  ----------------------------
  weight               5.72***
                     (5.63)   
  				   ....     
  _cons             5515.58   
                     (1.05)   
  ----------------------------
  r2                   0.55   
  r2_a                 0.52   
  F                   21.01   
  N                   74.00   
  ----------------------------
*/  
```

答： `ereturn list` 的含义与 `e(*)` 类对象解释

#### `ereturn list` 的含义

`ereturn list` 命令用于列出最近一次回归或估计命令执行后，存储在 `e()` 类返回值中的所有对象。这些对象包括标量、矩阵、宏和函数等，用于存储模型的各种统计结果。

#### `e(*)` 类对象的含义

> **常见单值**   

- `e(N)`：样本数，模型中使用的观测样本数量。
- `e(df_m)`：模型的自由度，自变量的数量。
- `e(df_r)`：残差的自由度，样本数减去自变量数量减一。
- `e(F)`：F 统计量，用于检验回归模型总体显著性。
- `e(r2)`：R²（决定系数），反映自变量对因变量解释的比例。
- `e(rss)`：残差平方和，用于衡量回归模型的拟合误差。
- `e(r2_a)`：调整后的 R²，考虑了自变量数量对模型拟合优度的影响。
- `e(ll)`：对数似然值，用于衡量模型的拟合程度。

> **矩阵**

- `e(b)`：回归系数矩阵，是一个 1 x k 的矩阵，其中 k 是回归模型中的变量数量。该矩阵存储了模型中各个自变量的回归系数，包括常数项（截距）。

  例如，如果回归模型是：
$$
\text{price} = \beta_0 + \beta_1 \times \text{weight} + \beta_2 \times \text{length} + \beta_3 \times \text{mpg} + \beta_4 \times \text{foreign} + \epsilon
$$

- `e(b)` 矩阵的内容将是：
$$
\begin{bmatrix} \beta_0 & \beta_1 & \beta_2 & \beta_3 & \beta_4 \end{bmatrix}
$$

- `e(V)`：回归系数的协方差矩阵，是一个 k x k 的矩阵，其中 k 是回归模型中的变量数量。该矩阵存储了各个回归系数之间的协方差和方差信息。对角线上的元素是各个回归系数的方差，非对角线上的元素是回归系数之间的协方差。

  例如，`e(V)` 矩阵可能如下：

$$\begin{bmatrix}
  \text{Var}(\beta_0) & \text{Cov}(\beta_0, \beta_1) & \cdots & \text{Cov}(\beta_0, \beta_4) \\
  \text{Cov}(\beta_1, \beta_0) & \text{Var}(\beta_1) & \cdots & \text{Cov}(\beta_1, \beta_4) \\
  \vdots & \vdots & \ddots & \vdots \\
  \text{Cov}(\beta_4, \beta_0) & \text{Cov}(\beta_4, \beta_1) & \cdots & \text{Var}(\beta_4)
  \end{bmatrix}
$$

> **函数**
- `e(sample)`：样本指示函数，用于标识用于回归分析的观测样本，返回一个二进制向量，向量的每个元素对应数据集中相应观测值的使用情况。若观测值被包含在分析中，则相应位置为 1，否则为 0。这个指示函数有助于在回归分析后识别和操作用于分析的特定观测样本。

通过这些 `e(*)` 类对象，您可以提取和使用模型的详细统计结果进行进一步分析和报告。

  (回答人：孙雅妮)

### Q4.请问为什么cd这条命令会标红

![2024初级day1q4](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/2024%E5%88%9D%E7%BA%A7%E7%8F%AD%E7%AD%94%E7%96%91%E5%9B%BE%E7%89%871.jpg)

答：确认 **D:/Stata17/ado\persona1//PX A 2024b/A3_program/data** 这个路径是否真的存在于你的系统中。注意路径中的 // 可能是不必要的，应该只有一个斜杠。
检查每个子目录是否都存在，特别是包含空格和特殊字符的目录。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/2024%E5%88%9D%E7%BA%A7%E7%8F%AD%E7%AD%94%E7%96%91%E5%9B%BE%E7%89%872.png)
- 解决方法一：建立文件夹，不要直接解压文件在personal中。
  ![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/2024%E5%88%9D%E7%BA%A7%E7%8F%AD%E7%AD%94%E7%96%91%E5%9B%BE%E7%89%873.png)
- 解决方法二：删除/符号
  ![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/2024%E5%88%9D%E7%BA%A7%E7%8F%AD%E7%AD%94%E7%96%91%E5%9B%BE%E7%89%874.png)
  
(回答人：连玉君&金顶)

### Q5.请问这个cap是什么意思
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/2024%E5%88%9D%E7%BA%A7%E7%8F%AD%E7%AD%94%E7%96%91%E5%9B%BE%E7%89%875.jpg)
答：如果命令有问题的话不报错且继续运行。参见 `help capture` 或 [**[P]** capture](https://www.stata.com/manuals/pcapture.pdf)。

在 Stata 中，每次执行命令后，系统会将返回代码存储在 `_rc` 系统变量中。若命令成功执行，`_rc` 的值为 `0`；若命令执行出错，`_rc` 会存储相应的错误代码。

`capture` 命令的作用是在执行命令时，即使命令出错，Stata 也不会报错并中断程序，而是继续执行后续代码。同时，`capture` 会将错误代码存储在 `_rc` 中。

在编写复杂的 Stata 程序时，`capture` 和 `_rc` 常用于处理错误信息。比如，在有些场景下，用户预先知道某条命令可能会出错，如删除不存在的变量，可以使用 `capture` 忽略错误，并根据 `_rc` 的值酌情做出处理。
例如：

```stata
capture des lnprice // 确认该变量是否存在
if _rc != 0{        // 如果不存在 lnprice 变量，则生成一个
	gen lnprice = ln(price)
}
```

又如，可以用如下代码判断一个变量是否存在，并酌情显示提示信息：
```stata
capture drop age
if _rc != 0 {
    display "Variable 'age' does not exist. Error code: " _rc
} else {
    display "Variable 'age' was successfully dropped."
}
```

`capture` 命令和 `_rc` 系统变量是 Stata 中处理错误和控制程序流的重要工具。通过使用 `capture` 命令可以忽略执行命令时出现的错误，而 `_rc` 则提供了检查命令执行结果的手段，使用户能够编写更为稳健和灵活的 Stata 程序。

### 参考文献

- Newson, R. B. (2017). Stata Tip 127: Use Capture Noisily Groups. The Stata Journal, 17(2), 511–514. [Link](https://journals.sagepub.com/doi/10.1177/1536867X1701700215), [PDF](https://journals.sagepub.com/doi/pdf/10.1177/1536867X1701700215), [Google](<https://scholar.google.com/scholar?q=Stata Tip 127: Use Capture Noisily Groups>).
 
  (回答人：林枫)

### Q6.请问3.2.2存放数值命令里面的exp(2)是什么意思
答：`exp(2)` 表示自然对数的底数 e 的平方值

  (回答人：李沣航)

### Q7. substr是什么意思呢
答：scalar s2 = substr(s1,1,5)：声明一个标量 s2，并赋值为 s1 的子字符串，从第1个字符开始，长度为5。substr(s, p, l) 将给出字符串 s 的起始位置为p、长度为l的子字符串。当l为负数时，“起始位置”将从后数起。
- [李辉旸](https://www.lianxh.cn/search.html?s=李辉旸), 2023, [Stata中的各种函数一览](https://www.lianxh.cn/details/1147.html), 连享会 No.1147.
- [连玉君](https://www.lianxh.cn/search.html?s=连玉君), 2023, [字符函数ustrregexm()：文字变量匹配中的英文字母大小写问题](https://www.lianxh.cn/details/1283.html), 连享会 No.1283.
- [陈勇吏](https://www.lianxh.cn/search.html?s=陈勇吏), 2020, [七条建议：用Stata处理文字变量和字符变量](https://www.lianxh.cn/details/279.html), 连享会 No.279.
- 连玉君笔记，[Stata 中的字符函数](https://gitee.com/arlionn/stata/wikis/%E5%88%9D%E7%BA%A7%E7%AC%94%E8%AE%B0/Stata%E4%B8%AD%E7%9A%84%E5%AD%97%E7%AC%A6%E5%87%BD%E6%95%B0.md)

  (回答人：李沣航&陈荟文)

### Q8. 请问这个 close* 是什么意思

```stata
  *-Q: _N 与 _n 有何区别?
  *-应用：计算收盘价的一阶滞后项和股票收益率
	sysuse "sp500", clear
    generate close_lag = close[_n-1]
	gen ret = (close-close[_n-1])/close[_n-1]
    list date close* ret in 1/3, clean
/*
            date     close   close_lag          ret  
  1.   02jan2001   1283.27           .            .  
  2.   03jan2001   1347.56     1283.27    .05009861  
  3.   04jan2001   1333.34   1347.5601   -.01055247 
*/
```

答：通配符：在处理文件名、变量名或其他字符串时，* 可以用作通配符，表示匹配任意字符。
- `list var*`  就会包含 **var1**, **var2**, **varmake** 等。
- `close*` 表示所有以 **close** 开头的变量.
- `list date close* ret in 1/3, clean` 这个命令会列出 date、所有以 close 开头的变量，以及 ret 变量的第 1 到第 3 行的数据，并以干净(即不带边框和多余间距)的格式显示。

详情参见：
- [**[]** varlist](https://www.stata.com/manuals/u11.pdf#u11.4varnameandvarlists) 
- 连玉君, 2022, [Stata101：人手一份的入门指南](https://www.lianxh.cn/details/1065.html), 连享会 No.1065.

  (回答人：马朝阳&颜国强)

### Q9.请问这个 lp, lc, lw 是什么意思

```stata
 *-4.2.5.5 附加线属性的独立性
   
   sysuse sp500, clear
   line open date, yline(1100,lp(shortdash_dot) lc(blue*0.6))  ///
                   yline(1313,lw(*2.5) lc(green*0.4))          ///
                   xline(15242,lw(*2) lc(pink*0.4) lp(longdash))
```
答：
- `lp`：这是线条模式 (Line Pattern，`lpattern`) 的缩写。例如，
  - `lp(shortdash_dot)` 表示线条模式为短划线和点的组合；
- `lc`：这是线条颜色 (Line Color，`lcolor`) 的缩写。例如，
  - `lc(blue*0.6)` 表示线条颜色为「淡蓝色」，其色彩浓度为标准蓝色的 0.6 倍。
  - `lc(blue*0.6%70)` 则进一步为「淡蓝色」赋予了 70% 的透明度。
- `lw`：这是线条宽度 (Line Width，`lwidth`) 的缩写。例如，
  - `lw(*2.5)` 表示线条宽度为默认宽度的 2.5 倍。

详情参见帮助文件：
- [**[G-3]** added_line_options](https://www.stata.com/manuals/g-3added_line_options.pdf) 
- 线条的宽度：[**[G-4]** linewidthstyle](https://www.stata.com/manuals/g-4linewidthstyle.pdf) 
- 线条的颜色：[**[G-4]** colorstyle](https://www.stata.com/manuals/g-4colorstyle.pdf) 
- 线条的类型：[**[G-4]** linepatternstyle](https://www.stata.com/manuals/g-4linepatternstyle.pdf) 

>  (回答人：李萌)

### Q10. 地图命令在哪里使用
答：你可以用 `lianxh 地图` 命令查看包含「地图」关键词的推文。

- [万莉](https://www.lianxh.cn/search.html?s=万莉), 2021, [Stata绘图：世行可视化案例-条形图-密度函数图-地图-断点回归图-散点图](https://www.lianxh.cn/details/560.html), 连享会 No.560.
- [万莉](https://www.lianxh.cn/search.html?s=万莉), 2020, [Stata：空间计量之用-spmap-绘制地图.md](https://www.lianxh.cn/details/401.html), 连享会 No.401.
- [冷萱](https://www.lianxh.cn/search.html?s=冷萱), 2021, [Stata：我和她离多远？基于百度地图API的地理距离计算](https://www.lianxh.cn/details/509.html), 连享会 No.509.
- [张弛](https://www.lianxh.cn/search.html?s=张弛), 2024, [Stata 绘图：用 `geoplot` 绘制中国地图](https://www.lianxh.cn/details/1401.html), 连享会 No.1401.
- [彭显耿](https://www.lianxh.cn/search.html?s=彭显耿), 2020, [GIS地图制作栅格计算器的应用](https://www.lianxh.cn/details/475.html), 连享会 No.475.
- , 2024, [Stata绘图：地图神器geoplot](https://www.lianxh.cn/details/1383.html), 连享会 No.1383.
- [王卓](https://www.lianxh.cn/search.html?s=王卓), 2022, [Python：绘制动态地图-pyecharts](https://www.lianxh.cn/details/922.html), 连享会 No.922.
- [肖蕊](https://www.lianxh.cn/search.html?s=肖蕊), 2022, [Stata：地图绘制命令介绍-maptile](https://www.lianxh.cn/details/925.html), 连享会 No.925.
- [陈卓然](https://www.lianxh.cn/search.html?s=陈卓然), 2022, [Stata绘图：绘制二维地图和中国地图-geo2xy](https://www.lianxh.cn/details/984.html), 连享会 No.984.


  (回答人：连玉君&高君)