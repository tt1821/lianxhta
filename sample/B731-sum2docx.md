
# Stata 推文：全面解析 `sumdocx` 命令的使用与应用

在 Stata 中进行数据分析时，经常需要将结果以表格的形式输出到报告或论文中。如果你正在撰写学术论文或准备数据分析报告，那么如何将描述性统计、回归结果等内容以规范的表格格式导出，可能是你常遇到的一个问题。幸运的是，Stata 提供了 `sumdocx` 命令，可以轻松将分析结果导出到 Word 文档中。

本文将详细介绍如何使用 `sumdocx` 命令，帮助大家理解其基本用法，并提供一些高级示例，帮助你在实际工作中生成符合期刊要求的输出格式。我们将一步步展示如何使用该命令，并通过实际的操作示例来说明如何定制输出内容和格式。

简要本文的目的，并说明连享会此前发布的同类推文，如

- [云锋](https://www.lianxh.cn/search.html?s=云锋), 2020, [Stata：一文搞定论文表1——基本统计量列表](https://www.lianxh.cn/details/22.html), 连享会 No.22.
- [云锋](https://www.lianxh.cn/search.html?s=云锋), 2020, [sumup：快速呈现分组统计量](https://www.lianxh.cn/details/65.html), 连享会 No.65.
- [何庆红](https://www.lianxh.cn/search.html?s=何庆红), 2020, [baselinetable命令：论文基本统计量表格输出到Excel和Word](https://www.lianxh.cn/details/246.html), 连享会 No.246.
- [姜昊](https://www.lianxh.cn/search.html?s=姜昊), 2022, [Stata：分类表述性统计量与论文表1定制-table1_mc命令](https://www.lianxh.cn/details/1044.html), 连享会 No.1044.
- [王美庭](https://www.lianxh.cn/search.html?s=王美庭), 2020, [Stata：毕业论文大礼包 A——实证结果输出命令大比拼-asdoc-esttab-reg2docx](https://www.lianxh.cn/details/261.html), 连享会 No.261.

## 1. `sumdocx` 命令概述

`sumdocx` 是一个非常有用的 Stata 命令，主要用于将变量的描述性统计输出结果直接导出到 Word 文档中。你可以通过该命令控制表格的格式、选择输出的统计量，以及添加标题和注释等。

### 1.1. 命令的安装和帮助

在开始使用 `sumdocx` 之前，你需要先安装它。在 Stata 中执行以下命令来安装 `sumdocx`：

```stata
ssc install sumdocx, replace
```

安装完成后，你可以通过以下命令查看命令的详细帮助信息：

```stata
help sumdocx
```

帮助文件中详细列出了命令的各项选项和用法，包括如何选择要输出的统计量、如何设置表格标题和注释等。阅读帮助文件将有助于你更好地理解命令的使用方法。

## 2. 基本用法

### 2.1. 生成描述性统计

我们先从一个简单的例子开始，使用 Stata 自带的 **nlsw88.dta** 数据集来演示 `sumdocx` 的基本功能。我们将输出一些常见的描述性统计量，如均值、标准差、最小值、最大值等。

首先，加载数据集：

```stata
sysuse "nlsw88.dta", clear
```

假设我们要对变量 `age`、`wage` 和 `tenure` 进行描述性统计，并将结果输出到 Word 文件中，可以使用以下命令：

```stata
sumdocx age wage tenure using "C:/temp/summary.docx", replace
```

运行此命令后，Stata 会生成一个名为 `summary.docx` 的 Word 文件，文件中包含了 `age`、`wage` 和 `tenure` 变量的描述性统计结果。默认情况下，`sumdocx` 会计算以下统计量：

- **N**（样本量）
- **mean**（均值）
- **sd**（标准差）
- **min**（最小值）
- **max**（最大值）

你可以根据需要修改输出的内容。

### 2.2. 选择输出的统计量

默认情况下，`sumdocx` 会输出所有常见的描述性统计量。如果你只需要某些统计量，可以使用 `stats()` 选项来指定。以下示例只输出均值和标准差：

```stata
sumdocx age wage tenure using "C:/temp/summary.docx", replace ///
    stats(mean sd)
```

这将会生成一个包含均值和标准差的表格。

### 2.3. 设置表格标题

在输出的 Word 文件中，我们可以添加表格的标题，以使表格更具可读性。例如：

```stata
sumdocx age wage tenure using "C:/temp/summary.docx", replace ///
    stats(mean sd min max) ///
    title("Summary Statistics for Age, Wage, and Tenure")
```

通过 `title()` 选项，你可以为表格添加标题。这样输出的表格将更加清晰，并且更符合期刊的格式要求。

### 2.4. 添加注释

除了标题之外，你还可以为表格添加注释，这样可以在表格下方说明数据的来源或解释特定的统计结果。例如：

```stata
sumdocx age wage tenure using "C:/temp/summary.docx", replace ///
    stats(mean sd min max) ///
    title("Summary Statistics for Age, Wage, and Tenure") ///
    note("Data source: nlsw88.dta")
```

这样，输出的 Word 文件中除了表格之外，还会包含一条注释，标明数据的来源。

## 3. 高级用法

在实际工作中，我们常常需要生成更加复杂的表格。`sumdocx` 命令支持一些高级选项，能够满足不同的格式要求。

### 3.1. 使用通配符选择变量

如果你想输出以某些特定字母开头或包含特定字符的变量，可以使用通配符。例如，如果你想选择所有名称中包含 "wage" 或 "weight" 的变量，可以使用以下命令：

```stata
sumdocx *wage* *weight* using "C:/temp/summary.docx", replace ///
    stats(mean sd min max) ///
    title("Summary Statistics for Wage and Weight")
```

这种方法非常方便，尤其是当你有多个变量需要输出时，不必手动指定每个变量的名称。

### 3.2. 使用所有变量

如果你想输出数据集中所有变量的描述性统计，可以使用 `_all` 关键字：

```stata
sumdocx _all using "C:/temp/summary.docx", replace ///
    stats(mean sd min max) ///
    title("Summary Statistics for All Variables")
```

这样，命令会计算数据集中所有变量的描述性统计量。

### 3.3. 合并多个表格

有时我们可能需要将多个输出合并成一个 Word 文件。`sumdocx` 提供了 `append` 选项，可以将多个输出追加到同一个 Word 文件中。例如：

```stata
sumdocx age wage tenure using "C:/temp/summary1.docx", replace ///
    stats(mean sd min max) ///
    title("Summary Statistics for Age, Wage, and Tenure")
sumdocx _all using "C:/temp/summary1.docx", append ///
    stats(mean sd min max) ///
    title("Summary Statistics for All Variables")
```

这样，你就可以将多个输出结果合并到一个文件中，而不必每次都生成新的文件。

### 3.4. 定制输出格式

除了选择统计量和变量外，`sumdocx` 还允许你定制输出表格的格式。你可以控制数字的显示格式，指定列宽，设置字体等。以下是一个简单的示例：

```stata
sumdocx age wage tenure using "C:/temp/summary.docx", replace ///
    stats(mean(%9.2f) sd(%9.2f)) ///
    title("Formatted Summary Statistics") 
```

这个命令将均值和标准差输出为 2 位小数的格式。

## 4. 结语
……


&emsp;

## 4. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh sum2docx baselinetable addest 论文表1 sumup tabout tabel1 asdoc, nocat md0`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

- [丁晨](https://www.lianxh.cn/search.html?s=丁晨), 2021, [Stata结果输出：addest自己定制输出的统计量](https://www.lianxh.cn/details/776.html), 连享会 No.776.
- [云锋](https://www.lianxh.cn/search.html?s=云锋), 2020, [Stata：一文搞定论文表1——基本统计量列表](https://www.lianxh.cn/details/22.html), 连享会 No.22.
- [云锋](https://www.lianxh.cn/search.html?s=云锋), 2020, [sumup：快速呈现分组统计量](https://www.lianxh.cn/details/65.html), 连享会 No.65.
- [何庆红](https://www.lianxh.cn/search.html?s=何庆红), 2020, [baselinetable命令：论文基本统计量表格输出到Excel和Word](https://www.lianxh.cn/details/246.html), 连享会 No.246.
- [兰茜](https://www.lianxh.cn/search.html?s=兰茜), 2021, [Stata结果输出：tabout命令详解](https://www.lianxh.cn/details/550.html), 连享会 No.550.
- [卢梅](https://www.lianxh.cn/search.html?s=卢梅), 2019, [asdoc：Stata 结果输出又一利器！](https://www.lianxh.cn/details/115.html), 连享会 No.115.
- [姜昊](https://www.lianxh.cn/search.html?s=姜昊), 2022, [Stata：分类表述性统计量与论文表1定制-table1_mc命令](https://www.lianxh.cn/details/1044.html), 连享会 No.1044.
- [朱磊](https://www.lianxh.cn/search.html?s=朱磊), 2020, [Stata：今天你table了吗？二维列表和三维列表范例](https://www.lianxh.cn/details/376.html), 连享会 No.376.
- [杨涵慧](https://www.lianxh.cn/search.html?s=杨涵慧), 2024, [令人相见恨晚的 Stata 命令](https://www.lianxh.cn/details/1463.html), 连享会 No.1463.
- [王文泽](https://www.lianxh.cn/search.html?s=王文泽), 2021, [Stata结果输出-addest：自己添加统计量](https://www.lianxh.cn/details/540.html), 连享会 No.540.
- [王美庭](https://www.lianxh.cn/search.html?s=王美庭), 2020, [Stata：毕业论文大礼包 A——实证结果输出命令大比拼-asdoc-esttab-reg2docx](https://www.lianxh.cn/details/261.html), 连享会 No.261.
- [王美庭](https://www.lianxh.cn/search.html?s=王美庭), 2020, [Stata：毕业论文大礼包 B——实证结果输出之搜狗短语](https://www.lianxh.cn/details/262.html), 连享会 No.262.
- [连享会](https://www.lianxh.cn/search.html?s=连享会), 2020, [Stata结果输出：tabout-用-Stata-输出高品质表格](https://www.lianxh.cn/details/349.html), 连享会 No.349.

---

目前我们已经覆盖了 `sumdocx` 命令的基本用法和一些高级功能，帮助大家更好地在实际工作中生成期刊所需的表格格式。接下来，我们将继续探讨如何应对一些常见问题和解决方法，确保你在使用 `sumdocx` 时能够顺利输出符合期刊格式的结果。

**（请注意：请自行在电脑上运行上述代码，如果有错误的话就及时更新，然后把输出的结果以截图的方式插入推文中。）**

