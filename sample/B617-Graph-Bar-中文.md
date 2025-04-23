> 这是 ChatGPT 自动翻译的版本，请务必修改一下，以确保术语、关键词的翻译正确；同时，保证行文的风格是中国人写的「人话」。


<br>
<br>


--- - --

# 高级柱状图在 Stata 中的应用（二）：可视化离散变量之间的关系

> **作者：** (xx大学)    
> **邮箱：** <xxxx>      
> **来源：** John V. Kane. 2024. 高级柱状图在 Stata 中的应用（二）：可视化离散变量之间的关系. The Stata Gallery 博客. [-链接-](https://medium.com/the-stata-gallery/advanced-bar-graphs-in-stata-part-2-visualizing-relationships-between-discrete-variables-7afb8cb5aaf2)

这篇指南介绍了如何制作柱状图，以更有效地传达离散变量之间的双变量关系。它展示了如何使用 Stata 的 -graph bar- 命令以及用户编写的 `catplot` 外部命令来实现这一目的。文中提供了代码以改进关键美学元素，并介绍了一种巧妙的技巧来在柱状图中添加“总计”类别。

## 1. 主要内容

### 1.1 引言

在上一篇推文 [Stata 绘图：高级柱状图（一）- 均值和置信区间](https://www.lianxh.cn/details/1379.html) 中，我讲解了如何创建带有置信区间的均值柱状图。这些图表在展示一个离散变量与一个连续变量之间的关系时非常有用。

然而，柱状图的另一种更为常见的用途是展示 *类别的百分比* 而不是均值。当我们想要显示一个 *离散* 变量的变化或两个离散变量之间的关系时，我们会使用这种方法。

尽管这种图表很常见，但有效传达两个离散变量之间的关系可能会很棘手。例如，柱中的百分比可能不是我们真正想要比较的百分比。此外，从代码的角度来看，还有很多需要注意的图表选项，这些选项并不总是很直观。

本指南的目的是介绍如何使我们的柱状图尽可能具有信息性，显示我们真正想要比较的内容，并使用关键选项来改善美观性。还将讨论用户编写的 `catplot` 外部命令以及创建“总计”类别的一种巧妙技术。

### 1.2 准备工作

在开始之前，有几件事需要注意：

1. 图表将使用 **stcolor** 模板（Stata v.18 中的新功能）或 **schemepack** 模板，详见如下推文：
    - 袁子晴, 2021, [史上最牛 Stata 绘图模板-schemepack：酷似 R 中的 ggplot2](https://www.lianxh.cn/details/819.html), 连享会 No.819.

安装方法如下：

```Stata
ssc install schemepack, replace
```

然后通过以下命令探索所有新模板：

```Stata
graph query, schemes
```

> 注意：所有图表使用 “AbelPro-Regular” 字体，可在[这里](https://www.cufonfonts.com/font/abel-pro)下载。有关安装/使用 Stata 中非本机字体的详细信息，请参见[此处](https://twitter.com/UptonOrwell/status/1605056863858565120)。

### 1.3 简单开始

让我们从导入 lbw.dta 数据集开始（该数据集包含母亲和新生儿体重的数据）：

```Stata
webuse lbw.dta, replace
```

为了本指南的目的，让我们创建一个四类别变量以表示每位母亲的年龄类别（这些大致分为四分位数）。(注意：我在[这里](https://medium.com/the-stata-gallery/cleaning-data-in-stata-c9a98c8fda74)介绍了 Stata 中的数据清理命令以及非常有用的 -fre- 外部命令)。

```Stata
gen agecats=1 if inrange(age, 14, 19)
replace agecats=2 if inrange(age, 20, 23)
replace agecats=3 if inrange(age, 24, 26)
replace agecats=4 if inrange(age, 27, 45)

fre agecats

label define agecats 1 "14-19" 2 "20-23" 3 "24-26" 4 "27-45", replace
label values agecats agecats

tab agecats
```

创建一个简单的年龄类别变量计数柱状图的语法如下：

```Stata
graph bar (count), over(agecats)
```

这段代码给我们提供了以下图表（注意 y 轴显示的是频率而不是百分比）：

![简单的年龄类别变量计数柱状图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*ZxUNJMlxs_SAsbCCUYDkkg.png)

当然，我们可以（也应该）添加各种选项。我在以下代码中对它们进行了注释，但特别注意两个选项：

1. **gap(#)** 选项，它实际上是 **over(varname)** 选项中的一个子选项。这个选项控制每个变量条之间的空间量。将“#”的值降低到 1 以下会 *减少* 条之间的距离，增加到 1 以上会增加距离（相对于默认距离）。这是一个需要注意的关键美学特征。
2. **b1title( )** 选项，它在 x 轴上放置一个标题。发现这个选项存在花了我很长时间。（**xtitle( )** 命令不适用于柱状图；令人困惑的是，Stata 会坚持认为“xaxis1 不存在”。）

```Stata
graph bar (count),  /// 基本的计数柱状图命令
  over(agecats, gap(.9)) /// 识别“over”变量；指定条之间的距离（此处）
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// 格式化条标签
  ylab(, glcolor(gs15) glstyle(solid)) /// 添加浅灰色实线水平线
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// 格式化条的外观
  scheme(white_jet) /// 指定模板
  b1title(Age Group) /// x 轴标题
  ytitle(Frequency) /// y 轴标题
  graphregion(margin(vsmall)) /// 图表与外边缘之间的小边距
  xsize(6.5) ysize(4.5) // 指定图表尺寸（英寸）
```

这会产生如下图表：

![带有附加选项的计数柱状图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*EfCniWTjZKBSWTVjHC3oUA.png)

频率/计数是有用的，但迟早（尤其是对于更大的数据集），你可能会想查看百分比。

只需在第一行代码中删除 **(count)** 规范即可实现这一点。我还更改了 y 轴的标题以表示显示的是百分比，而不是频率。

```Stata
graph bar,  /// 基本的百分比柱状图命令
  over(agecats, gap(.9)) /// 识别“over”变量；指定条之间的距离（此处）
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// 格式化条标签
  ylab(, glcolor(gs15) glstyle(solid)) /// 添加浅灰色实线水平线
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// 格式化条的外观
  scheme(white_jet) /// 指定模板
  b1title(Age Group) /// x 轴标题
  ytitle(Percent of Sample) /// y 轴标题
  graphregion(margin(vsmall)) /// 图表与外边缘之间的小边距
  xsize(6.5) ysize(4.5) // 指定图表尺寸（英寸）
```

我们得到如下图表：

![百分比柱状图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*RaiZD3y92POuk6b4BaNnDw.png)

## 2. 有效传达双变量关系：一些不太好的选择

假设我们想展示母亲的年龄与她们在怀孕期间是否吸烟（是或否）之间的关系。获得一个交叉表非常简单：

![交叉表](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*KePRYsUv34pTvwPl2Tmzjw.png)

这里我们指定了列百分比，使我们能够回答以下问题：*在每个年龄组内，吸烟和不吸烟的母亲各占百分比是多少？* 如果我们想要寻找这些变量之间的

 *关系*，我们希望看到随着从一个列移动到另一个列，吸烟和不吸烟的百分比有很大的不同（这表明知道母亲的年龄可以在一定程度上预测她是否吸烟）。

实际上，每列的百分比确实有一些变化（例如，14-19 岁的母亲中约有 45% 吸烟，而 24-26 岁的母亲中仅有 30.6% 吸烟），尽管无论如何，卡方检验表明这两者之间没有显著的统计关系（*p*=.60）。

但对于我们的目的，真正的问题是：如何最有效地用图表传达这一模式？

在第一次尝试中，我们将指定两个 **over( )** 变量。在这种情况下，第一个变量确定出现在 x 轴上的次要值（即，紧靠轴线下方），而第二个 **over( )** 变量确定出现在 x 轴上的主要值（即，更靠下方）。简而言之，每个主要值将包含所有次要值。

让我们试试看！

```Stata
graph bar, over(smoke, gap(.6)) /// 识别次要 over 变量；注意这控制条之间的间距
  over(agecats, gap(1)) /// 识别主要 over 变量
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// 格式化条标签
  ylab(, glcolor(gs15) glstyle(solid)) /// 添加浅灰色实线水平线
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// 格式化条的外观
  scheme(white_jet) /// 指定模板
  b1title(Age Group) /// x 轴标题
  ytitle(Percent of Sample) /// y 轴标题
  graphregion(margin(vsmall)) /// 图表与外边缘之间的小边距
  xsize(6.5) ysize(4.5) // 指定图表尺寸（英寸）
```

我们得到如下图表：

![带有两个 **over( )** 变量的柱状图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*hFHWcGqCfE8_TiUxKF20qg.png)

乍一看，这似乎达到了我们的目的：它展示了不同年龄类别中不吸烟者和吸烟者的百分比。

然而，如果你仔细观察，你会发现一个不太合适的地方：*百分比是基于总样本的，而不是每个年龄类别的*。换句话说，所有条的百分比总和为 100，而不是每个年龄类别的总和为 100。

这意味着，我们***不能***说，例如，20-23 岁的母亲中有 18% 是不吸烟的。

这个图表实际上让我们很难知道任何年龄组中的母亲吸烟和不吸烟的百分比。

问题是，百分比是基于总样本的，而不是我们上面的交叉表中的每个年龄类别的总观察数。这种微妙的区别会造成很多困惑。

另一个（虽然不太重要）问题是 x 轴上的文本太多，可能会让读者更难处理。

一个简单的解决方法是添加 **asyvars** 选项。这将为 **over( )** 变量的每个类别生成不同的颜色。我们还可以使用 **bar(#, ...)** 选项来指定每个颜色的外观（“1”表示第一个类别，“2”表示第二个类别，等等）：

```Stata
graph bar, over(smoke, gap(.6)) /// 注意这控制烟类别之间的间距
  over(agecats, gap(1)) /// 控制年龄类别之间的间距
  asyvars /// 为第一个“over()”变量的每个类别创建图例
  legend(pos(3)) /// 将图例放置在3点钟位置
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// 格式化条标签
  ylab(, glcolor(gs15) glstyle(solid)) /// 添加浅灰色实线水平线
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// 格式化第一个烟类别的外观
  bar(2, fcolor(blue%85) fintensity(inten100) lcolor(midblue) lwidth(vthin)) /// 格式化第二个烟类别的外观
  scheme(white_jet) /// 指定模板
  b1title(Age Group) /// x 轴标题
  ytitle(Percent of Sample) /// y 轴标题
  graphregion(margin(vsmall)) /// 图表与外边缘之间的小边距
  xsize(6.5) ysize(4.5) // 指定图表尺寸（英寸）
```

我们得到如下图表：

![带有 **asyvars** 选项的双变量关系柱状图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*Lh3DTVuIegSbUoTuS3Xe_A.png)

因为它使用了不同的颜色来表示“smoke”类别，这个图表去除了大量文本，可能比前一个图表更容易理解。

然而，百分比的问题依然存在。我们如何做得更好？

## 3. 有效传达双变量关系：更好的选择

一种更好地可视化交叉表结果的方法是使用 **by( )** 选项。这样可以将第二个 **over( )** 变量的每个值分成一个单独的面板，每个值都有一个单独的面板。

注意：我们可以使用 **col( )** 选项来控制图表的排列方式，该选项指示子图应该有多少列。例如，**col(2)** 指定子图应该排列成两列。

另一个*非常重要*的提示：许多关键选项必须出现在 **by( )** 选项*内部*。在下面的代码中，**by( )** 从第二行开始，直到第三行末才关闭：

```Stata
graph bar, over(smoke, gap(.2))  ///
  by(agecats, col(2) note("")     ///
  title("Age Group & Smoking Status", box color(white) span fcolor(black) lcolor(black) bexpand) ///
  subtitle(, color(gs4) fcolor(gs15) lcolor(midblue) lwidth(vthin) bexpand) /// 
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// 格式化条标签
  asyvars ///
  legend(pos(6) row(1)) /// 将图例放置在6点钟位置并排列成一行
  ylab(, glcolor(gs15) glstyle(solid)) /// 添加浅灰色实线水平线
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// 格式化第一个烟类别的外观
  bar(2, fcolor(blue%85) fintensity(inten100) lcolor(midblue) lwidth(vthin)) /// 格式化第二个烟类别的外观
  scheme(white_jet) /// 指定模板
  ytitle(Percent of Sample) /// y 轴标题
  graphregion(margin(vsmall)) /// 图表与外边缘之间的小边距
  xsize(6.5) ysize(4.5) // 指定图表尺寸（英寸）
```

这会生成如下图表：

![使用 **by( )** 选项的柱状图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*W8-p5yJJ32oiMwK1ScuX9Q.png)

注意：我们也可以添加 **asyvars** 选项，如果我们不想要那么多文本或更喜欢一些颜色：

```Stata
graph bar, over(smoke, gap(.2))  ///
  by(agecats, col(2) note("") ///
  title("Age Group & Smoking Status", box color(white) span fcolor(black) lcolor(black) bexpand) ///
  subtitle(, color(gs4) fcolor(gs15) lcolor(midblue) lwidth(vthin) bexpand) ///
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// 格式化条标签
  asyvars ///
  legend(pos(6) row(1)) /// 将图例放置在6点钟位置并排列成一行
  ylab(, glcolor(gs15) glstyle(solid)) /// 添加浅灰色实线水平线
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// 格式化第一个烟类别的外观
  bar(2, fcolor

(blue%85) fintensity(inten100) lcolor(midblue) lwidth(vthin)) /// 格式化第二个烟类别的外观
  scheme(white_jet) /// 指定模板
  ytitle(Percent of Sample) /// y 轴标题
  graphregion(margin(vsmall)) /// 图表与外边缘之间的小边距
  xsize(6.5) ysize(4.5) // 指定图表尺寸（英寸）
```

![使用 **asyvars** 选项的双变量关系柱状图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*Od5i6ZKyqy13Zikhkdw2JA.png)

使用 **by( )** 选项的优点在于，它使我们可以准确地比较交叉表中的结果：我们可以看到每个年龄组内的不吸烟者与吸烟者的百分比。换句话说，百分比在每个四个面板中总和为 100，而不是在图表中的所有条中总和为 100。

缺点是，如果类别数量很多，这个图表会很难阅读（想象一下有 8、9 或 10 个子图而不是我们这里的 4 个）。这可能会让我们感觉像是在试图理解多个不同的图表而不是一个。

其次，比较不在同一行的年龄组（例如，比较 14-19 岁与 27-45 岁的吸烟者百分比）有点烦人。换句话说，我们没有奢侈的从左到右简单比较条高度的机会。

这就引出了 `catplot`，这是由 Stata 大师 Nicholas J. Cox 编写的[外部命令](https://journals.sagepub.com/doi/pdf/10.1177/1536867X0400400209)。

安装方法如下：

```Stata
ssc install catplot, replace
```

`catplot` 外部命令的操作方式与我们迄今使用的 -graph bar- 命令非常相似。对于我们的目的，最重要的选项是 **percent( )**。这个选项指示 *哪个变量的值应被视为分母*。在交叉表的语言中，这个选项指定当我们要求列百分比总和为 100 时，哪个变量排列在 *列* 中。在下面的代码中，我们指定这是 **agecats** 变量，而不是 **smoke** 变量。

另外两个重要的选项是 **recast(bar)** 和 **asyvars**。第一个选项确保图表显示为*垂直*柱状图（提示：使用 **recast(hbar)** 来生成*水平*柱状图）。第二个选项将“smoke”的不同值渲染为不同的颜色。以下是注释代码：

```Stata
catplot smoke agecats, /// 使 smoke 成为第一个类别，agecats 成为第二个
    percent(agecats) /// 百分比基于 agecats 的所有案例
    recast(bar) asyvars /// 生成柱状图；用不同颜色区分 smoke 的不同值
    legend(pos(3)) /// 将图例放置在3点钟位置
    legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) /// 添加图例标题
    bargap(10) /// 指定条之间的间距
    blabel(bar, color(white) format(%4.0f) size(small) pos(inside)) /// 格式化条标签
    ylab(, glcolor(gs15) glstyle(solid)) /// 添加浅灰色实线水平线
    b1title(Age Group) /// 标题 x 轴
    ytitle(Percent of Sample) /// 标题 y 轴
    graphregion(margin(vsmall)) /// 图表与外边缘之间的小边距
    xsize(6.5) ysize(4.5) /// 指定图表尺寸（英寸）
    scheme(gg_set3) /// 指定模板
```

我们得到如下图表：

![catplot 图](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*nVYMjZCfEIAzWG6MKFl10g.png)

注意：每个年龄组被视为分母，所以我们可以轻松地看到——在每个年龄组内——不吸烟者和吸烟者的百分比。这比我们最初使用 Stata 的 -graph bar- 命令的尝试有了很大改进。

而且，与上面的 **by( )** 选项相比，我们可以轻松地从左到右查看吸烟者/不吸烟者条最高和最低的年龄组。

同样，`catplot` 的语法与 -graph bar- 语法非常相似。例如，我们可以轻松地使用 **bar(1, ...)** 和 **bar(2, ...)** 选项分别更改第一个和第二个 smoke 类别的颜色。下面的代码提供了一个示例，还更改了图例位置，添加了标题，并进行了其他一些美学调整：

```Stata
catplot smoke agecats, ///
percent(agecats) ///
recast(bar) asyvars ///
bargap(5) ///
blabel(bar, color(white) format(%4.0f) size(small) pos(inside)) /// 将格式改为整数
ylab(, glcolor(gs15) glstyle(solid)) ///
b1title(Age Group) ///
bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) ///
bar(2, fcolor(navy%85) fintensity(inten100) lcolor(blue) lwidth(vthin)) ///
ytitle(Percent of Sample) ///
graphregion(margin(vsmall)) xsize(6.5) ysize(4.5) ///
title("Example of How to Use -catplot-", box color(white) span fcolor(black) lcolor(black) bexpand) /// 标题
subtitle("Smoking Status & Age Group", box color(white) span fcolor(black) lcolor(black) bexpand) /// 副标题
legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) ///
legend(ring(0) pos(11)) ///
scheme(white_jet) /// 指定模板
```

我们得到如下图表：

![使用 catplot 的自定义图表](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*oO-x_VDYJmKVXZTL9LHRVw.png)

完成！我们现在有了一个图表，展示了上述交叉表的可视化：smoke 的百分比基于 agecats 每个类别的所有观察值。

### 3.1 显示“总计”类别

一个我们交叉表有但上面的图表没有的细节是“总计”列。也就是说，我们的图表没有告诉我们样本中不吸烟者和吸烟者的总体百分比。

如何制作一个显示总百分比的可视化？

一个非常简单的选项是使用我们上面使用的 **by( )** 选项，该选项方便地包括一个 **total** 子选项（基本语法只是简单的 **by(agecats, total)**）。通过包含这个 **total** 子选项，我们会得到一个额外的子图，显示样本中不吸烟者和吸烟者的总体百分比：

![使用 **by(agecats)** 选项和 **total** 子选项的图表](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*HSrwHeeietePmsEj-NZ3Iw.png)

这里的优点是添加 **total** 子选项非常简单。它使图表不对称，但如果你能接受，这是一种非常快速简单的方式来展示整个样本的比较。

但是如果我们想使用 `catplot` 而不是 **by( )** 选项呢？

幸运的是，有一种方法，它涉及一个非常巧妙的技巧。尽管比使用 **by( , total)** 方法复杂一些，但也不算太难。

操作步骤如下：

1. **首先使用 -preserve- 命令保存数据**。建议在命令窗口手动执行此操作（Stata 似乎不喜欢我在 .do 文件中运行它）。（对于不熟悉的人来说，这个命令基本上让你暂时更改数据集的结构，然后通过执行 -restore- 命令快速恢复数据到保存前的状态。）
2. **接下来，使用 -expand- 命令扩展数据集**，并在后面加上“2”。这将简单地将整个数据集翻倍。
3. **为出现在 x 轴上的变量创建一个新类别**。
4. **为新类别添加一个值标签**。
5. **使用修订后的分类变量制作图表**。
6. **在命令窗口中执行 -restore- 命令（不要从 .do 文件中执行）以返回到保存前的数据集（*别忘了执行这一步！*）**



以下是我们如何在数据集中使用此技术：

```Stata
preserve // 手动在命令窗口执行此操作

*-Expand 数据集并为 agecats 添加新值

expand 2, gen(new)
replace agecats=5 if new // 如果“new”等于1，则使agecats等于5
label def agecats 5 "Total", modify
```

简而言之，我们将数据集翻倍，然后使用新的一半数据创建“总计”类别。

所有功劳归于 [Andrew Musau](https://www.statalist.org/forums/member/4687-andrew-musau)（他慷慨地在 [Statalist](https://www.statalist.org/forums/) 上为我提供了示例代码）和 Nicholas J. Cox（他提供了他在 [Stata Journal 文章](https://journals.sagepub.com/doi/pdf/10.1177/1536867X1401400117) 中发表的链接）。

然后我们可以运行以下代码：

```Stata
catplot smoke agecats, ///
percent(agecats) ///
recast(bar) ///
asyvars ///
legend(pos(3)) ///
bargap(10) ///
blabel(bar, color(white) format(%4.0f) size(small) pos(inside)) /// 将格式改为整数
ylab(, glcolor(gs15) glstyle(solid)) ///
b1title(Age Group) ///
bar(1, fcolor(gs4%85) fintensity(inten100) lcolor(black) lwidth(vthin)) ///
bar(2, fcolor(red%90) fintensity(inten100) lcolor(yellow) lwidth(vthin)) ///
ytitle(Percent of Sample) ///
graphregion(margin(vsmall)) xsize(6.5) ysize(4.5) ///
title("Example of How to Create a 'Total' Category", box color(white) span fcolor(black) lcolor(black) bexpand) /// 标题
subtitle("Smoking Status & Age Group", box color(white) span fcolor(black) lcolor(black) bexpand) /// 副标题
legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) ///
legend(ring(0) pos(11)) ///
scheme(white_jet) /// 指定模板
```

这会生成如下图表：

![带有“总计”类别的 catplot 图表](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*0GGJ8f-J7k-oM8a-8sUwhw.png)

完成了！我们现在有了一个包含“总计”类别的“年龄组”图表，使我们能够看到每个年龄组内以及整个样本的不吸烟者和吸烟者的百分比。

最后，记住要使用命令窗口并**在完成后执行 -restore-** 以便可以恢复到原始数据集！

最后，这里是如何使用这个技巧以及 `catplot` 外部命令的另一个示例。在这个例子中，我们将使用重新编码的医生访问变量（“ftv”），展示“smoke”的“总计”类别（而不是“agecats”），并进行一些其他美学调整：

```Stata
*-创建一个新的变量（孕期第一个三个月的医生访问次数）
fre ftv 
recode ftv (3 4 6=3), gen(ftvcats)
label define ftvcats 0 "Zero" 1 "One" 2 "Two" 3 "Three or More", replace
label values ftvcats ftvcats
fre ftvcats

*-接下来保存数据
preserve // 手动在命令窗口执行此操作

*-现在扩展数据集并为“smoke”添加另一个类别
expand 2, gen(new)
replace smoke=3 if new
label def smoke 3 "Total", modify

*-制作 catplot
catplot smoke ftvcats, ///
percent(smoke) /// 使百分比在每个烟类别内
recast(hbar) /// 生成水平图表
asyvars /// 用不同颜色区分次要类别
var2opts(label(angle(vertical))) /// 控制垂直轴值的角度
blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// 格式化条标签
ylab(0(10)60, glcolor(black) glstyle(solid) glwidth(.2)) /// 添加浅灰色实线水平线
bar(1, fcolor(midblue%95) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// 格式化条的外观
bar(2, fcolor(red%95) fintensity(inten100) lcolor(yellow) lwidth(vthin)) ///
bar(3, fcolor(black%95) fintensity(inten100) lcolor(lime) lwidth(vthin)) ///
scheme(black_jet) /// 指定模板
l1title(Number of Doctor Visits in 1st Trimester, margin(medium)) /// 标题垂直轴（b1title 和 ytitle 在这种情况下会标题水平轴）
ytitle(Percent of Sample) /// 标题 y 轴
graphregion(fcolor(black) margin(vsmall)) /// 图表与外边缘之间的小边距
title("Another Example of Using a 'Total' Category", box color(white) span fcolor(black) lcolor(black) bexpand) /// 标题
subtitle("Smoking Status & Doctor Visits in First Trimester", box color(white) span fcolor(black) lcolor(black) bexpand) /// 副标题
legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) ///
legend(ring(1) pos(3) col(1)) ///
xsize(6.5) ysize(4.5) /// 指定图表尺寸（英寸）
plotregion(fcolor(gs5)) // 着色图表内区域

*-完成图表后，恢复数据
restore // 手动在命令窗口执行此操作
```

最后一个图表！

![使用“总计”类别和 catplot 的示例](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*l7xzgId5iUlX6Ia9V-hSMQ.png)

这里的一个关键区别是我们指定了 **percent(smoke)**，这意味着我们希望将每个烟类别视为分母。简而言之，我们希望将不吸烟者视为一个样本，吸烟者视为另一个样本，然后将两者结合视为第三个（“总计”）样本。因此，每个特定的条 *颜色* 总和为 100%。

与之前的图表不同，我们需要小心解读这个图表（非常容易误解这样的柱状图！）。

知道这些，我们可以说，例如，48% 的不吸烟者在孕期第一个三个月内没有去看医生，但这个百分比在吸烟者中更高（61%）。但整个样本呢？这个图表告诉我们，整个样本中有 53% 的人在孕期第一个三个月内没有去看医生。只有 6% 的样本在孕期第一个三个月内看了三次或更多医生。

就是这样！希望这个指南对你有所帮助。祝你好运并享受这个过程！

