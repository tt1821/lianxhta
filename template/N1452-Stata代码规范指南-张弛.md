
> **作者**：张弛 (中山大学)        
> **邮箱**：<zhangch369@mail2.sysu.edu.cn>    

&emsp;

> **编者按**：本推文整理自 [The DIME Analytics Coding Guide](https://worldbank.github.io/dime-data-handbook/coding.html#the-dime-analytics-stata-style-guide)，相关代码和资料来自 [dime-data-handbook](https://github.com/worldbank/dime-data-handbook?tab=readme-ov-file)。

&emsp;
&emsp;

## 1. 简介

在研究生阶段，许多学生已具备扎实的理论基础，但仍需提升实践技能。这就像一位建筑专业的毕业生，虽然能够有效地勾勒、描述和讨论新建筑的概念与要求，却缺乏相应的技术能力，无法按照专业标准绘制建筑图纸，以供他人使用和理解。

在这个数据驱动的时代，从事定量研究的人员必须精通协作编程，这一要求远超仅仅通过编程得出正确结果的层面。因此，本文旨在提供一个通用方案，为程序员的协作编程提供建议。优秀的代码不仅能够产生正确的结果，还应易于其他专业人士阅读和修改。

尽管有许多书籍介绍了不同类型的代码编写风格，但关于 Stata 代码编写风格的资料仍然相对匮乏。DIME Analytics Stata 风格指南旨在促进 Stata 代码风格的使用、改进、共享和标准化。

&emsp;

## 2. 如何写出好的代码

优秀的代码应具备两个关键要素：

- 代码必须完全正确，能够顺利运行并产生准确的结果；
- 对于未曾接触过这段代码的人或很久未查看此代码的用户而言，这段代码应具备实用性和可理解性。许多研究人员只接受过如何编写正确代码的培训。然而，当代码在计算机上成功运行并获得预期结果时，编写代码的工作仅完成了一半。一份优质的代码应该易于阅读和复制，便于他人发现和及时修正错误。优秀的代码在发布前能够更轻松地通过他人的审核，发布后也更容易被重复使用。

可以从以下三个方面评估代码的优劣：

- 第一，代码结构：优秀的代码结构应便于研究人员快速定位不同输出的代码位置，明确各部分代码对应的输出或文件。同时，代码之间应保持一定的独立性，以便于单独运行。
- 第二，代码可读性：代码的可读性应足够强，以便于理解代码本身及其背后的含义。代码语法不应过于复杂，不应需要过多的技术知识才能阅读，应该使用大家都能理解的命令。
- 第三，代码风格：代码风格涉及代码中非代码部分的表达，包括变量命名、缩进和空格的使用。这些元素应帮助读者快速理解代码所传达的含义。

在编写代码时，`help` 命令至关重要。即使是最常用的命令，也总有新的知识值得学习。在编程过程中，随时打开帮助文件是个好习惯，这样可以方便查找命令或回顾那些不熟悉或已遗忘的功能。

&emsp;

## 3. 本书的代码介绍

本书提供了众多代码撰写案例，这些案例在结构、可读性和风格上都表现优良。大部分代码示例为 Stata 代码，因为它是最常用的编程语言之一。此外，本书中的代码和数据大多数基于 Stata 已预配的数据集，用户无需额外下载。同时，代码框中展示了规范且清晰的代码写作示例。在本书中，代码风格的样式如下：

```Stata
* Load the auto dataset
sysuse auto.dta , clear
* Run a simple regression
reg price mpg rep78 headroom, coefl
* Transpose and store the output
matrix results = r(table)
* Load the results into memory
clear
svmat results, n(col)
```

本书的代码框尽量使用 Stata 内置命令，但也会包含用户编写的命令，特别是在这些命令提供了重要的新功能时。特别提到的是，由 DIME Analytics 开发的两个 Stata 命令套件：`ietoolkit` 和 `iefieldkit`，它们旨在标准化核心数据收集、管理和分析的工作流程。

要运行包含用户编写命令的代码示例，首先需要安装这些命令。发布 Stata 用户编写命令的最常见平台是 [SSC 档案库](https://ideas.repec.org/s/boc/bocode.html)。书中提到的所有用户编写命令均可在 SSC 档案库中找到。从 SSC 档案库安装命令非常简单，只需输入 `ssc install randtreat` 即可。

虽然每次使用新软件包时可能会感到困惑，但这是设置 Stata 并从他人的成果中受益的最佳方式。一旦掌握了如何安装和使用这些命令，这种困惑便会消失。在编写依赖于用户编写命令的代码时，最佳实践是在主 do 文件的开头安装这些命令，这样用户就不必手动搜索软件包了。

&emsp;

## 4. DIME Analytics Stata 代码风格指南

在计算机科学中，编程语言通常都有相关的风格指南。这些指南有时是官方且广泛认可的，例如 Python 的 [PEP8](https://peps.python.org/pep-0008/)。更常见的是一些非官方但广为接受的风格指南，比如 Hadley Wickham 的 R 语言 [Tidyverse 风格指南](https://style.tidyverse.org/index.html) 或 JavaScript 的 [标准风格指南](https://standardjs.com/#the-rules)。

大型软件公司通常会为其项目中使用的所有编程语言维护专属的风格指南，但这些指南并不总是对外公开。虽然美学是代码风格指南的重要组成部分，但并非其存在的主要目的。风格指南的真正作用在于帮助有可能合作的程序员共享约定和理解，从而使用统一的代码语言实现各种目标。此外，它们还提升了所有使用该语言的程序员所编写代码的质量。通过遵循统一的风格，新手程序员能够从经验丰富的开发者身上学习，了解哪些编码实践更容易出错，哪些更为稳健。

最有效的代码风格指南往往是那些被广泛采用的。普遍认可的风格约定使程序员能够更轻松地借鉴彼此的解决方案和在线示例，从而避免可能在后期才被发现的错误。此外，全球标准化的代码风格指南也促进了程序员之间的协作，帮助他们更有效地解决彼此的问题，并在不同项目和团队之间实现知识的共享。

在使用代码风格指南时，人们可以根据个人偏好对代码进行调整，但其主要目的是确保代码的标准化书写和质量，尤其是在协作编程时。DIME Analytics 制定了这份 Stata 风格指南，以提升所有 Stata 代码的质量。尽管我们不必严格遵循这一风格指南，本文所介绍的所有风格规则均体现了 DIME Analytics 对编码方式的建议。本文最重要的建议是，确保代码风格的一致性。

### 4.1 代码注释

代码注释虽然不会改变代码的输出或运行方式，但缺乏注释的代码对于其他读者来说往往难以理解。若代码没有充分的注释来阐明其意图和功能，后续的更新或编辑将耗费更多时间。

注释的数量非常重要：它不仅要解释代码在做什么，还要说明为什么要这样写。一般而言，编写需要较少解释的简单代码更为可取，而不是在有限的空间中使用复杂的方法，除非这些复杂的写法是被广泛接受的。

本文提供以下三种常用的 Stata 代码注释方法：

```Stata
* 法1 多行注释法
/*
This is a do-file with examples of comments in Stata. This
type of comment is used to document all of the do-file or a large
section of it
*/

* 法2 注释一行法
* Standardize settings, explicitly set version, and clear memory

* 法3 行内注释
sysuse auto.dta // Built in dataset
```

### 4.2 使用命令缩写

在 Stata 中，命令通常可以缩写。如果帮助文件顶部的语法部分用下划线标出命令名的一部分，则该命令可以缩写。然而，仅内置命令可以进行缩写，用户编写的命令则不适用。虽然 Stata 允许将某些命令缩写为一两个字符，但这样可能会造成困惑——两个字母的缩写往往难以以清晰的方式被识别，并与完整命令的功能建立联系。

因此，在代码中，命令的缩写应不少于三个字符，除了 `tw` 代表 `twoway` 和 `di` 代表 `display`。缩写应仅在存在广泛接受的情况下使用。像 `local`、`global`、`save`、`merge`、`append` 和 `sort` 等常用命令应始终保持完整，不应缩写。下表列出了常见 Stata 命令的可接受缩写形式。

| Abbreviation | Command   |
|:------------:|:---------:|
|      tw      |  twoway   |
|      di      |  display  |
|      gen     | generate  |
|      mat     |  matrix   |
|      reg     |  regress  |
|      lab     |  label    |
|      sum     | summarize |
|      tab     | tabulate  |
|      bys     |  bysort   |
|      qui     | quietly   |
|      noi     | noisily   |
|      cap     | capture   |
|     forv     | forvalues |
|     prog     | program   |
|     hist     | histogram |

变量名称不应缩写，而应完全拼写。如果后续引入的变量使得缩写不再唯一，代码可能会受到影响。`ieboilstart` 命令默认执行 `set varabbrev off` (即禁止使用命令缩写)，因此任何使用变量缩写的代码将无法运行。此外，在 Stata 中使用通配符和列表 (如 `*`、`?` 和 `-`) 来列出变量也不推荐，因为如果数据集发生变化或只是重新排序，代码的功能可能会随之改变。要捕获某种类型的所有变量，最好使用 `unab` 或 `lookfor` 在本地宏中构建变量列表，这样可以确保变量的正确性，并保证它们按正确的顺序排列。

在 Stata 和其他编程语言的示例代码中，`foreach` 或 `forvalues` 中的局部变量通常使用类似 `i` 或 `j` 的简单名称。然而，我们更推荐使用更具描述性的名称来命名这些索引。单一字母索引仅在一般示例、使用 `i` 进行迭代循环以及使用 `i` 和 `j` 进行嵌套循环时是可以接受的。最佳做法是让索引名称能够描述代码循环的内容，例如“家庭成员”、“作物”或“药物”。即使是计数变量，也应明确命名。这样能够提升代码的可读性，特别是在处理嵌套循环时。以下提供了一些示例：

```Stata
* 好的案例1
* Loop over crops
foreach crop in potato cassava maize {
    * do something to `crop'
}

* 好的案例2
* Loop over crops
local crops potato cassava maize
foreach crop of local crops {
    * Loop over plot number
    forvalues plot_num = 1/10 {
        * do something to `crop' in `plot_num'
    } // End plot loop
} // End crop loop

* 不好的案例：使用i作为索引
* Loop over crops
foreach i in potato cassava maize {
    * do something to `i'
}
```

在 Stata 中，添加一个或多个空格不会影响代码的执行，但可以显著提升代码的可读性。大多数研究人员在使用 PowerPoint 和 Excel 等软件时，已经习惯了合理使用空格：PowerPoint 演示文稿中的文本对齐，Excel 表格中的行和列也结构分明。这一原则同样适用于代码编写。

在下面的示例中，完全相同的代码分别写了两次。其中，改进后的示例通过添加空格来强调代码的中心对象是变量 `employed`。这种组织方式不仅提高了代码的可读性，还使得潜在的错误更容易被发现。

```Stata
* 原始，可被接受的代码版本
* Create dummy for being employed
gen employed = 1
replace employed = 0 if (_merge == 2)
lab var employed "Person exists in employment data"
lab def yesno 1 "Yes" 0 "No"
lab val employed yesno

* 改进的代码版本
* Create dummy for being employed
gen      employed = 1
replace  employed = 0 if (_merge == 2)
lab var  employed "Person exists in employment data"
lab def           yesno 1 "Yes" 0 "No"
lab val  employed yesno
```

缩进可以显著提升代码的可读性。任何在循环中重复的代码段或依赖于 `if` 语句的代码段，都应相对于循环或条件的括号缩进四个空格。如果某个代码段位于嵌套循环中，则应再额外缩进四个空格，总共比最外层循环多缩进八个空格。

在一些代码编辑器中，可以使用 Tab 键来实现这种缩进。然而，由于 Stata 的 do-file 编辑器中的 Tab 键在跨平台时 (例如在 GitHub 上发布代码时) 显示不一致，因此建议手动插入四个空格，而不是使用 Tab 键。以下是一个示例：

```Stata
* 好的代码
* Loop over crops
foreach crop in potato cassava maize {
    * Loop over plot number
    forvalues plot_num = 1/10 {
        gen crop_`crop'_`plot_num' = "`crop'"
    }
}
* or
local sampleSize = `c(N)'
if (`sampleSize' <= 100) {
    gen use_sample = 0
}
else {
    gen use_sample = 1
}

* 不好的代码
* Loop over crops
foreach crop in potato cassava maize {
* Loop over plot number
forvalues plot_num = 1/10 {
gen crop_`crop'_`plot_num' = "`crop'"
}
}

* or
local sampleSize = `c(N)'
if (`sampleSize' <= 100) {
gen use_sample = 0
}
else {
gen use_sample = 1
}
```

所有条件 (真/假) 表达式应至少用一组括号括起来。逻辑表达式的否定应使用感叹号 (`!`)，而不是波浪号 (`~`)。此外，建议使用显式的真值检查 (例如 `if 'value' == 1`)，而非隐式检查 (例如 `if 'value'`)。在检查缺失值时，应使用 `missing('var')` 函数，而不是类似于 `if 'var' >= .` 的表达式。在编写代码时，务必考虑缺失值是否会影响条件表达式的判断，并做出相应调整。以下是代码示例：

```Stata
* 好的代码
replace gender_string = "Woman" if  (gender == 1)
replace gender_string = "Man"   if ((gender != 1) & !missing(gender))

* 不好的代码
replace gender_string = "Woman" if  gender == 1
replace gender_string = "Man"   if (gender ~= 1)
```

在适用的情况下，应优先使用 `if-else` 语句，而不是两个独立的 `if` 语句。使用 `if-else` 结构能够清晰地表明这两个条件是互斥的，从而提升代码的可读性。此外，当需要修改条件时，这种写法更不易出错，且更容易理解和维护。

```Stata
* 好的代码
if (`sampleSize' <= 100) {
    * do something
}
else {
    * do something else
}

* 不好的代码
if (`sampleSize' <= 100) {
    * do something
}
if (`sampleSize' > 100) {
    * do something else
}
```

### 4.3 代码路径

所有文件路径应始终用双引号括起来，并且文件夹层级应使用正斜杠 (`/`)。Mac 和 Linux 系统无法读取使用反斜杠的文件路径，且反斜杠在代码中具有其他功能，因而难以通过查找替换去除。文件名应使用小写字母和破折号 (如 `my-file.dta`)，并且文件路径应始终包含文件扩展名 (如 `.dta`、`.do`、`.csv` 等)。省略扩展名可能导致歧义。

文件路径应始终使用绝对路径和动态路径。绝对路径是指从计算机根文件夹开始的完整路径，例如 Windows 系统中的 `C:/` 或 macOS 系统中的 `/Users/`。这样可以确保文件始终位于正确的文件夹中。

除非命令明确要求使用 `cd` 命令，否则应避免使用它。使用 `cd` 命令时，容易在其他项目文件夹中覆盖文件，因为许多 Stata 命令在运行时会依赖于当前工作目录，因此 Stata 的工作目录可能会在没有警告的情况下发生改变。

虽然相对路径在许多其他编程语言中很常见，但在 Stata 中，如果未能基于运行代码的文件位置使用相对路径，可能会存在较高的风险。相对路径是相对于工作目录的，而不是相对于正在运行的代码文件。

动态文件路径通过全局宏来定义根文件夹的位置。这些全局宏应在一个中心主 do-file 中设置。使用存储在全局宏中的根文件夹路径，使得在 Stata 中编写的文件路径能够像相对路径一样工作。这样也能实现 `cd` 命令通常希望达到的功能：在新系统上执行代码只需更新一个位置的文件路径全局宏。如果全局宏的名称是唯一的，就不会出现文件被保存到错误项目文件夹的风险。可以根据需要创建多个文件夹全局宏，推荐采用这种做法。以下是相关的代码示例：

```Stata
* 好的案例
global myDocs = "C:/Users/username/Documents"
global myProject = "${myDocs}/MyProject"
use "${myProject}/my-dataset.dta", clear

* 不好的案例:相对路径
cd "C:/Users/username/Documents/MyProject"
use MyDataset.dta

* 不好的案例:直接从路径打开dta
use "C:/Users/username/Documents/MyProject/MyDataset.dta"
```

### 4.4 使用换行符号

长行代码难以阅读，往往需要左右滚动才能查看完整内容。当代码行超出常规界面的宽度时，应考虑换行。常见的换行长度大约为 80 个字符。Stata 的 do 文件编辑器及其他代码编辑器通常提供可见的换行指导线。在此长度附近，使用 `///` 可以在代码编辑器中断行，同时向 Stata 表明代码将在下一行继续。较新版本的 Stata do 文件编辑器和许多其他代码编辑器也具有自动换行的功能。

然而，我们并不推荐过度依赖自动换行功能；相反，建议主动使用 `///` 进行换行，以确保换行位置能够保持代码的可读性。使用 `///` 断行时，不需在代码中垂直对齐，因为缩进本身可以反映命令如何延续到新行。断行应在需要的地方起作用。与使用 `//` 注释一样，在 `///` 后添加注释也是一个好习惯，特别是在将单个命令的不同功能部分分开以提高清晰度时。

需要注意的是，`#delimit` 命令仅适用于高级函数编程，且在分析代码中并不推荐使用 (Cox, 2005)。绝不可使用 `/* */` 来编写单行代码，因为这样做会分散注意力，并使代码格式难以追踪；这些字符应仅用于书写常规注释。合理使用换行和缩进可以有效强调 Stata 命令中选项、逗号或其他语法功能的位置。

```Stata
* 好的代码
graph hbar invil      /// Proportion in village
      if (priv == 1)   /// Private facilities only
    , over(statename, sort(1) descending)    /// Order states by values
      blabel(bar, format(%9.0f))             /// Label the bars
      ylab(0 "0%" 25 "25%" 50 "50%" 75 "75%" 100 "100%") ///
      ytit("Share of private primary care visits made in own village")

* 不好的代码
#delimit ;
graph hbar
    invil if (priv == 1)
  , over(statename, sort(1) descending) blabel(bar, format(%9.0f))
    ylab(0 "0%" 25 "25%" 50 "50%" 75 "75%" 100 "100%")
    ytit("Share of private primary care visits made in own village");
#delimit cr

* 丑陋的代码
graph hbar /*
*/    invil if (priv == 1)
```

### 4.5 使用模板代码

样板代码由几行代码组成，通常位于代码文件的顶部，其目的是尽可能统一不同用户在运行相同代码时的设置。在 Stata 中，无法保证任何两个版本的安装始终以完全相同的方式执行代码。虽然在绝大多数情况下，它们的运行结果基本一致，但并非总是如此，样板代码的引入可以降低这种风险。

DIME Analytics 开发了 `ieboilstart` 命令，以实现许多常用的样板设置，这些设置根据特定的 Stata 安装版本进行了优化。执行版本设置需要两行代码，以避免因 Stata 版本不同而导致的结果差异。此外，该命令还关闭了 `more`，防止代码在等待显示更多输出时挂起；关闭了 `varabbrev`，避免使用缩写的变量名；并最大化了允许的内存使用和矩阵大小，以防止其他机器因违反系统限制而拒绝运行代码 (例如，Stata/SE 和 Stata/IC 允许的最大变量数不同，Stata 14 和 Stata 15 也存在类似情况，因此在其中一个版本编写的代码可能无法在另一个版本中成功运行)。

最后，它清除了 Stata 内存中的所有存储信息，包括未安装的程序和全局变量，以使 Stata 尽可能接近一个全新会话的状态。

```Stata
* ieboilstart命令使用方法：在do文件加这两句代码
ieboilstart, version(13.1)
`r(version)'

* 其他统一代码的可接受的方式
set more off , perm
clear all
set maxvar 10000
version 13.1
```

### 4.6 其他提醒

1. 多张图应写作 `tw (xx)(xx)(xx)`，而不是 `tw xx||xx||xx`。

2. 在简单计算式中，除了 `^` 之外，每个二元运算符周围都需留有空格，应写作 `gen z = x + y` 和 `gen z = x^2`。在涉及运算顺序时，可以适当调整空格和括号：应写作 `hours + (minutes/60) + (seconds/3600)`，而不是 `hours + minutes / 60 + seconds / 3600`。

3. 对于较长的表达式，`+` 和 `-` 运算符应放在新行的开头，而 `*` 和 `/` 则应内联使用，例如：

   ```stata
   gen newvar = x ///
                - (y/2) ///
                + a * (b - c)
   ```

4. 将代码输出到结果窗口的效率较低，请尽量使用 `qui`，并在主脚本中使用 `run file.do` 而非 `do file.do`。

5. 为了减少命令窗口的输出，应在 do 文件中谨慎使用 `sum` 和 `tab` 等命令，除非它们用于存储 `r` 类统计数据。在这种情况下，使用 `qui` 前缀可以避免过多输出。同时，使用 `qui` 前缀获取 `reg` 等命令的输出也会更快。

&emsp;

## 5. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh 代码, m`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

- 专题：[倍分法DID](https://www.lianxh.cn/blogs/39.html)
  - 严安冬, 2024, [Stata：异质性DID代码详解](https://www.lianxh.cn/details/1399.html), 连享会 No.1399.
- 专题：[AI专题](https://www.lianxh.cn/blogs/55.html)
  - 于凡, 2024, [AI可以编写Stata代码吗？](https://www.lianxh.cn/details/1348.html), 连享会 No.1348.
  - 连玉君, 2024, [Stata+ChatGPT4o：AI代码助手好酸爽！](https://www.lianxh.cn/details/1393.html), 连享会 No.1393.
 
- 专题：[其它](https://www.lianxh.cn/blogs/33.html)
  - 卢昱霏, 2024, [小白和青椒：写作、展示、代码管理与审稿技巧](https://www.lianxh.cn/details/1355.html), 连享会 No.1355.
- 专题：[工具软件](https://www.lianxh.cn/blogs/23.html)
  - 吴小齐, 2024, [强大的Kimi：中国版ChatGPT平替](https://www.lianxh.cn/details/1423.html), 连享会 No.1423.
- 专题：[Stata绘图](https://www.lianxh.cn/blogs/24.html)
  - 孙晓艺, 2024, [Stata绘图大礼包：27个常用的可视化范例及代码](https://www.lianxh.cn/details/1372.html), 连享会 No.1372.
  - 张铁岳, 2022, [知乎热议：你见过最烂的代码长什么样子？](https://www.lianxh.cn/details/1092.html), 连享会 No.1092.
- 专题：[Stata命令](https://www.lianxh.cn/blogs/43.html)
  - 李原, 2024, [Stata：合并具有不同国家代码的数据集](https://www.lianxh.cn/details/1360.html), 连享会 No.1360.
- 专题：[Stata教程](https://www.lianxh.cn/blogs/17.html)
  - 汪京, 2024, [Stata代码规范指南](https://www.lianxh.cn/details/1377.html), 连享会 No.1377.
- 专题：[论文写作](https://www.lianxh.cn/blogs/31.html)
  - 秦范, 2022, [Stata：论文重现代码模板](https://www.lianxh.cn/details/882.html), 连享会 No.882.
  - 谢泽荟, 2023, [Stata：聚类标准误代码介绍](https://www.lianxh.cn/details/1189.html), 连享会 No.1189.
- 专题：[Stata程序](https://www.lianxh.cn/blogs/26.html)
  - 连玉君, 2023, [Stata编程：快速查找Stata代码片段](https://www.lianxh.cn/details/1319.html), 连享会 No.1319.
  - 陈卓然, 2022, [语法高亮：LaTeX中的彩色Stata代码](https://www.lianxh.cn/details/944.html), 连享会 No.944.
- 专题：[回归分析](https://www.lianxh.cn/blogs/32.html)
  - 陈波, 2021, [Stata：一行代码实现安慰剂检验-permute](https://www.lianxh.cn/details/731.html), 连享会 No.731.
  - 陈波, 2022, [Stata：一行代码绘制平行趋势图-eventdd](https://www.lianxh.cn/details/927.html), 连享会 No.927.
