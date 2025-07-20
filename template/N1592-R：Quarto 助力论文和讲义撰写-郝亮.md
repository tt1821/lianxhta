
> **作者**：郝亮 (北京工业大学)    
> **邮箱**：<haoliangcfa@126.com>

&emsp;

- **Title**: Quarto 助力论文和讲义撰写
- **Keywords**: Quarto, R, 论文排版, 讲义排版

&emsp;

在学术论文写作和教学讲义制作中，良好的排版和格式至关重要。**Quarto** 是由 Posit 开发的一种通用文档工具，可以看作升级版的 R Markdown。借助 Quarto，我们可以使用简洁的 Markdown 语法撰写内容，并插入 R 或 Python 代码，随后一键导出精美的论文、报告或幻灯片。更强的是，社区和官方提供了丰富的模板，帮我们快速套用论文期刊格式或幻灯片样式，大大节省排版时间。本文将基于 RStudio 平台，用通俗易懂的语言手把手介绍如何安装 RStudio 和 Quarto、下载并使用 Quarto 模板来撰写论文和讲义。

&emsp;

## 1. 准备工作：安装 RStudio 和 Quarto

在开始之前，我们需要准备好工具。**RStudio** 是常用的开发工具；**Quarto** 则是具体执行渲染的引擎。下面是具体步骤：

### 1.1 安装 R 和 RStudio

请从 [R 官方网站](https://posit.co/download/rstudio-desktop/)下载安装 R 语言。具体安装步骤请详见连享会推文：[R/RStudio下载安装指南](https://www.lianxh.cn/details/1333.html)，这里不再赘述。安装完成后，启动 RStudio。

### 1.2 安装 Quarto

Quarto 是一个独立程序，需要单独安装。访问 [Quarto 官方网站](https://quarto.org/docs/get-started/)下载适用于您操作系统的 Quarto 安装包并完成安装。安装成功后，可在命令提示符中运行 `quarto --version` 检查版本。确保 RStudio 能找到 Quarto (在 RStudio 顶部菜单栏中，应该能看到 Render 按钮，如果没有，重启 RStudio 或检查 Quarto 是否正确安装)。

完成以上步骤后，环境就配置好了！接下来，我们将在 RStudio 中使用 Quarto 模板来创建论文或讲义示例。

&emsp;

## 2. 使用 Quarto 模板的一般步骤

Quarto 模板是预先设计好的文档样式和结构包。通过它，我们可以快速生成符合某种期刊或风格要求的初始文档，然后只需专注于撰写内容。使用 Quarto 模板的一般流程如下：

1. **打开 RStudio 并新建项目**：建议为每个新论文/讲义创建一个单独的文件夹。您可以在 RStudio 中选择 `File -> New Project...` 创建一个新项目，或直接在现有项目/文件夹中操作。
2. **打开 RStudio Terminal**：在 RStudio 中，点击菜单 `Tools -> Terminal -> New Terminal` 打开终端窗口。我们将在这里输入 Quarto 命令来获取模板。
3. **运行获取模板命令**：Quarto 使用命令行来下载和应用模板。基本命令格式为：`quarto use template 用户名/仓库名`。例如，我们稍后将演示获取 Elsevier 期刊模板的命令是：`quarto use template quarto-journals/elsevier`。(读到这里，您可以直接跳转模版示例部分，这样会更直观！) 
4. **信任模板来源**：首次运行模板命令时，终端可能会提示是否信任该模板的作者？(Do you trust the authors of this template? (Y/n))。这是因为模板通常来自 GitHub 开源仓库。输入 `Y` 并回车表示信任。
5. **指定项目文件夹名称**：接着，终端会询问要将模板复制到哪个文件夹。您可以输入一个新文件夹名，例如 `elsevier-paper` (不要使用中文路径) 并回车。Quarto 将在当前目录下创建该文件夹，并将模板文件复制进去。
6. **打开模板示例文件**：当命令执行完成后，您会看到一个新文件夹。其中通常包含一个示例 `.qmd` 文件 (例如 elsevier-paper.qmd*)、参考文献文件 (*.bib) 以及 Quarto 配置文件等。在 RStudio 文件窗格中点击打开 `.qmd` 文件，就可以看到模板自带的示例内容。
7. **渲染查看效果**：点击 RStudio 顶部的绿色小按钮 `Render`。RStudio 将调用 Quarto 将 `.qmd` 文件渲染成目标格式。渲染完成后，RStudio 的 Viewer 窗格会显示输出结果预览。如果是 PDF，可在项目文件夹中找到生成的 PDF 文件，用 PDF 阅读器打开查看效果。

按照以上步骤，我们就成功套用了一个 Quarto 模板的示例。下面，我们通过三个具体模板案例，来进一步讲解模板的获取和使用方法，并展示每种模板最终生成的效果截图。

&emsp;

## 3. 模板示例

### 3.1 模版示例1：官方期刊论文模板 (Elsevier) 

学术期刊通常有严格的格式要求，Quarto 官方提供了[期刊模板库](https://quarto.org/docs/extensions/listing-journals.html#:~:text=computorg)，涵盖常见出版社和学协会的格式，例如 ACM、ACS、APA、IEEE、Elsevier 等。这些模板由 Posit 官方或社区提供，免费开源，非常适合撰写学术论文。

**模板介绍**：以 Elsevier 模板为例。Elsevier 是著名的学术出版社，其期刊具有统一的格式风格。Quarto 的 Elsevier 模板可以帮助我们直接生成符合 Elsevier 期刊要求的论文初稿，包括页面大小、字体、标题格式、参考文献样式等都已设置好。

**获取方法**：在 RStudio 的 Terminal 中运行以下命令：

```r
quarto use template quarto-journals/elsevier
```

根据终端提示，输入 `Y` 信任作者，并输入目录名称 (例如 elsevier-paper-test)。Quarto 会从 GitHub 上的 `quarto-journals/elsevier` 仓库下载模板。随后它会新建  elsevier-paper-test 文件夹，里面包含一个示例文件 elsevier-paper-test.qmd 以及必要的资源文件。

**在 RStudio 中打开和使用**：展开新建的文件夹，双击打开 elsevier-paper-test.qmd。您会看到文档开头有一段 YAML 配置，包含题目、作者等占位符。下面是正文示例，包括章节、公式、图表引用等格式。此时您可以先尝试点击 `Render` 按钮进行渲染。RStudio 将生成 Elsevier 风格的 PDF 文件并在 Viewer 中预览。如果一切正常，您将看到 PDF 文档的版式与期刊模板一致：题目作者居中显示、两栏正文、参考文献格式符合理工科学术期刊规范。

**最终效果**：Elsevier 模板生成的论文外观专业，适合准备投稿国际期刊。渲染后可以看到论文题目、作者、摘要、关键词、正文、参考文献等都已经按照 Elsevier 期刊的标准排版，非常省心。(具体论文内容导入见第四部分) 

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20154044.png)

### 3.2 模版示例2：Hikmah 学术论文模板

除了官方期刊格式，Quarto 也为很多科研工作者也提供了**美观通用的学术论文模板**。这里介绍由 Andrew Heiss 提供的 **[Hikmah Academic](https://github.com/andrewheiss/hikmah-academic-quarto)** Quarto 模板。该模板注重清晰雅致的版式，非常适合一般学术论文写作和毕业论文等。

**模板介绍**：Hikmah (一词源自阿拉伯语“智慧”之意) 模板包含了一系列自定义格式。它提供了优雅的细致排版，包括：

- 精美 PDF 格式：版式美观的单栏论文 PDF，适合期刊初稿、自用论文。
- 稿件双倍行距 PDF：符合投稿要求的双倍行距版本，便于同行评审批注。
- 稿件双倍行距 Word：导出为 Word 格式，便于在微软 Word 中打开编辑。
- 稿件双倍行距 ODT：OpenDocument 格式，与 LibreOffice 兼容。
- 审稿意见回复：用于撰写回复审稿人意见的信函模板。

可以看出，Hikmah 模板几乎涵盖了论文写作的各个阶段需求，从初稿到投稿格式，再到审稿回复，非常贴心。

**获取方法**：在 RStudio Terminal 中输入以下命令：

```r
quarto use template andrewheiss/hikmah-academic-quarto
```

首次使用请按提示信任作者，并输入目录名 (例如 hikmah-paper)。命令将从 GitHub 上 `andrewheiss/hikmah-academic-quarto` 仓库中下载模板并创建项目。下载完成后，新文件夹内包含示例 `.qmd` 文件，参考文献文件 *references.bib* 等。

**在 RStudio 中打开和使用**：打开 *hikmah-paper.qmd* 可以看到，这份模板的 YAML 区已填写示例标题、作者等，并指定使用 Hikmah 提供的格式 (例如 `format: hikmah-pdf`)。向下滚动，可以看到正文示例，包括题目页、摘要、章节、小节、引用、参考文献等各部分。直接点击 `Render` 按钮，Quarto 会基于 Hikmah 模板生成 PDF。几秒钟后，您看到渲染结果如下图。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20163038.png)

渲染出的 Hikmah 风格论文看起来**专业且美观**。如上图所示，标题页中标题和作者信息排版精致，正文有良好的留白和易读的字体，章节标题醒目，参考文献按照引用顺序整齐排列。该模板非常适合**学术论文写作练习**、**课程报告**以及**毕业论文**等场景，让你专注内容的同时保证排版符合学术规范。

### 3.3 模版示例3：幻灯片讲义模板

Quarto 不仅能写论文，也能方便地制作幻灯片或讲义。通过 Quarto，我们可以用同一份 `.qmd` 文件生成 HTML 幻灯片、PDF 幻灯片甚至 PowerPoint。下面我们介绍一个基于 HTML5 幻灯片框架 Reveal.js 的模板 —— **UNHCR Quarto Revealjs 模板**，它展示了如何套用特定机构的幻灯片样式。

**模板介绍**：UNHCR 是联合国难民署，该模板为其演示文稿设计了统一的外观主题 (蓝色调的 PPT 风格)。通过这个模板，可以快速创建带有 UNHCR 品牌标识的幻灯片。当然，非联合国机构的读者也可将其视作自定义幻灯片主题的示例。

**获取方法**：在 RStudio Terminal 中运行：

```r
quarto use template unhcr-dataviz/quarto-revealjs-unhcr
```

按照提示信任并输入文件夹名 (例如 unhcr-slides)。Quarto 将下载该模板并生成示例文件和资源。与之前类似，新项目下会有一个 qmd 示例幻灯片文件和相关的图片、样式等文件。

**在 RStudio 中打开和使用**：用 RStudio 打开 unhcr-slides.qmd。可以看到 YAML 部分指定了 `format: revealjs`，以及演示文稿的标题、副标题等。您可以直接点击 `Render` 来生成 HTML 幻灯片。渲染完成后，RStudio Viewer 会打开生成的幻灯片，您可以在 `Viewer` 中点击幻灯片或者用方向键翻页来预览。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20172639.png)

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20172653.png)

图为模板生成的幻灯片示例。该模板应用了统一的 UNHCR 品牌样式，适合制作机构宣讲或公益主题的讲义幻灯片。生成的幻灯片采用了 Reveal.js 引擎，可以在网页中通过左右切换播放。UNHCR 模板使幻灯片风格简洁专业，适合**机构介绍、培训讲义**等场景。

### 3.4 小结

通过以上三个示例，我们了解了在 RStudio 上使用 Quarto 模板的完整流程：从安装环境、获取模板，到加载示例文件、渲染查看效果。Quarto 模板极大地方便了学术论文和讲义的排版工作，让我们可以专注于内容创作，而将格式细节交给模板去处理。

当您需要撰写论文时，可以先查找有没有符合期刊要求的 Quarto 模板；写讲义或制作报告时，也可以搜索相关主题的幻灯片模板。不论是官方提供的还是社区共享的，**大部分 Quarto 模板都是免费且开源**的，我们可以放心使用。Quarto 官方网站列出了丰富的扩展和模板列表，涵盖期刊格式、简报幻灯片、书籍网站等。

&emsp;

## 4. 我如何将自己论文的内容复制进模板？

我以模版示例 1 为例，展现如何将自己论文内容复制到 Quarto 模板中，具体步骤如下：

我们假设你已经按照推文里的说明，下载好了 `quarto-journals/elsevier` 模板，并成功渲染出示例 PDF 文件。现在你想把自己写的论文内容替换进去 (即已经完成 3.1 的全部内容)。下面就是完整步骤：

### 4.1 第一步：打开 `.qmd` 文件

1. 用 RStudio 打开你项目文件夹，比如叫做 `elsevier-paper-test`。
2. 在文件面板中，双击打开 `elsevier-paper-test.qmd` 文件。
3. 你会看到顶部是 `YAML` 配置 (用 `---` 包起来的一块)，下面是正文内容。

### 4.2 第二步：修改标题等信息

如下面两图，对应修改题目、子标题、作者信息、摘要、关键词等相应信息即可。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20143553.png)

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20150653.png) 

**说明**： YAML 就像是“论文的封面信息”+“排版设置”，**不能删掉**，但你可以把 title、author、date 替换成你自己的。

### 4.3 第三步：复制正文到正文区域

正文部分一般从 `# Introduction` (一级标题) 开始，一直到 `# References` (参考文献) 前为止。

假设你自己论文写在 Word 或 Markdown 文件里，复制内容时注意：

- 每个章节用 `#` (一级标题)  或 `##` (二级标题) 开头；
- 段落之间空一行；
- 数学公式写成 `$公式$` 或 `$$公式$$`；
- 图表和表格暂时不复制 (我们下一步说)。

例如：

```r
markdown
# Introduction

In recent years, China has vigorously promoted green and low-carbon transformation. According to the national "dual carbon" strategy, cities play a key role in low-carbon development .

# Theoretical Framework

We use the extended Environmental Kuznets Curve (EKC) theory as the analytical framework ..

# Empirical Strategy

This article uses a two-way fixed effects model to conduct regression analysis on Chinese prefecture level cities ..

# Conclusion

The results show ..
```

### 4.4 第四步：插入引用

假设我们要在 Introduction 的最后一句的后面加上引用，例如：

```r
markdown
In recent years, China has vigorously promoted green and low-carbon transformation. According to the national "dual carbon" strategy, cities play a key role in low-carbon development.[@ferreira2011board]
```

你需要：

1. 在项目文件夹中找到或创建一个 `references.bib` 或 `bibliography.bib` 文件 (模板里通常已有，没有就自行建立)；
2. 用 Rstudio 打开此文件，将你引用的文献添加进去，用 `.bib` 格式。例如：

```r
@article{ferreira2011board,
  title={Board structure and price informativeness},
  author={Ferreira, Daniel and Ferreira, Miguel A and Raposo, Clara C},
  journal={Journal of Financial economics},
  volume={99},
  number={3},
  pages={523--545},
  year={2011},
  publisher={Elsevier}
}
```

PS：输入这些信息很繁琐，这里介绍一个小技巧。你可以在 google scholar 中搜索想要引用的文献，按照下图红框处操作，直接复制粘贴即可。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20145632.png)

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%2014565.png)

然后你在正文引用处写 `[@ferreira2011board]` (通常你在 .bib 文件中添加完引用文献的信息后，它会随着你的输入自动联想添加)，Quarto 渲染时就能自动变成 `[1]`，并在最后自动生成参考文献格式！

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20153416.png)

### 4.5 第五步：插入图表

在 `elsevier-paper-test` 文件夹下，建立 `img` 子文件夹，将需要的图表复制进该文件夹，之后直接拖拽至 elsevier-paper-test.qmd 相应位置即可。

### 4.6 第六步：保存并点击 Render 按钮

点击 RStudio 右上角的绿色 `Render` 按钮。

几秒钟后，就会生成带有你论文内容的 PDF，完全按照 Elsevier 的格式来排版。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-05-17%20154044.png)

### 4.7 小贴士

- 不要直接删除模板中的 `format` 设置和 `bibliography` 配置，否则渲染会失败。
- 如果你论文还没写完，可以先只复制前两节，看效果是否符合预期。
- 建议边改边 `Render`，每次修改一小部分，及时预览效果。

&emsp;

## 5. 延伸阅读

- Quarto 官方文档 (英文) [-Link-](https://quarto.org/docs/get-started/hello/rstudio.html#:~:text=1,latest release of RStudio)
- Quarto 官方期刊模板列表 [-Link-](https://quarto.org/docs/extensions/listing-journals.html#:~:text=computorg)
- Andrew Heiss 的 Hikmah 模板仓库 [-Link-](https://github.com/andrewheiss/hikmah-academic-quarto)

&emsp;

## 6. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh latex markdow, m`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

- 专题：[工具软件](https://www.lianxh.cn/blogs/23.html)
  - 刘思勤, 2021, [LaTeX-如何使用 Overleaf 撰写论文](https://www.lianxh.cn/details/801.html), 连享会 No.801.
  - 刘思勤, 2021, [LaTeX-如何使用Overleaf生成PPT](https://www.lianxh.cn/details/806.html), 连享会 No.806.
  - 连玉君, 2021, [AxMath-设置字体-伪装成Word数学公式](https://www.lianxh.cn/details/668.html), 连享会 No.668.
- 专题：[结果输出](https://www.lianxh.cn/blogs/22.html)
  - 刘欣妍, 2020, [Stata结果输出：Excel结果表变身LaTeX表格](https://www.lianxh.cn/details/495.html), 连享会 No.495.
- 专题：[Markdown-LaTeX](https://www.lianxh.cn/blogs/30.html)
  - 初虹, 2022, [Markdown-LaTeX：经管人的VSCode配置大全](https://www.lianxh.cn/details/1004.html), 连享会 No.1004.
  - 初虹, 2021, [Markdown图片神器：PicGo-让你爱上笔记与分享](https://www.lianxh.cn/details/769.html), 连享会 No.769.
  - 初虹, 2022, [Markdown笔记利器：Typora主题-最美的模板](https://www.lianxh.cn/details/1018.html), 连享会 No.1018.
  - 初虹, 2021, [学术论文写作新武器：Markdown-上篇](https://www.lianxh.cn/details/603.html), 连享会 No.603.
  - 初虹, 2021, [学术论文写作新武器：Markdown-下篇](https://www.lianxh.cn/details/604.html), 连享会 No.604.
  - 初虹, 2021, [学术论文写作新武器：Markdown-中篇](https://www.lianxh.cn/details/605.html), 连享会 No.605.
  - 初虹, 2024, [让「记录」变得简单：Markdown使用详解](https://www.lianxh.cn/details/1456.html), 连享会 No.1456.
  - 华晨, 2020, [Stata结果输出：用esttab生成带组别名称的LaTeX回归表格](https://www.lianxh.cn/details/352.html), 连享会 No.352.
  - 周璐, 2021, [LaTeX小白入门：TeXLive安装及使用](https://www.lianxh.cn/details/650.html), 连享会 No.650.
  - 宋慧慧, 2022, [Stata结果输出：用esttab命令绘制LaTeX表格](https://www.lianxh.cn/details/948.html), 连享会 No.948.
  - 宋森安, 2021, [用Markdown制作幻灯片-五分钟学会Marp（上篇）](https://www.lianxh.cn/details/656.html), 连享会 No.656.
  - 宋森安, 2021, [用Markdown制作幻灯片-五分钟学会Marp（下篇）](https://www.lianxh.cn/details/657.html), 连享会 No.657.
  - 左祥太, 2021, [DAG-有向无环图：如何用LaTeX快速绘制？-B017-](https://www.lianxh.cn/details/715.html), 连享会 No.715.
  - 曹琳君, 2021, [Axmath：一款Word数学公式编辑神器](https://www.lianxh.cn/details/609.html), 连享会 No.609.
  - 李刚, 2019, [毕业季10分钟Markdown简历——风格瞬间变](https://www.lianxh.cn/details/4.html), 连享会 No.4.
  - 李原, 2024, [使用 Quarto 打造你的个人主页](https://www.lianxh.cn/details/1338.html), 连享会 No.1338.
  - 李缘, 2020, [Markdown：一键将-Word-转换为-Markdown](https://www.lianxh.cn/details/299.html), 连享会 No.299.
  - 王卓, 2024, [Stata结果输出：Stata-to-LaTeX指南](https://www.lianxh.cn/details/1410.html), 连享会 No.1410.
  - 王美庭, 2020, [Stata：毕业论文大礼包 C——新版 esttab](https://www.lianxh.cn/details/263.html), 连享会 No.263.
  - 王胜文, 2022, [Latex系列2：Beamer入门-学术范儿的幻灯片](https://www.lianxh.cn/details/1015.html), 连享会 No.1015.
  - 王胜文, 2022, [Latex系列2：Beamer入门-学术范儿的幻灯片](https://www.lianxh.cn/details/971.html), 连享会 No.971.
  - 王胜文, 2022, [Latex系列3：使用Overleaf制作个人简历](https://www.lianxh.cn/details/998.html), 连享会 No.998.
  - 王胜文, 2022, [Stata：输出LaTeX表格](https://www.lianxh.cn/details/1008.html), 连享会 No.1008.
  - 王胜文, 2023, [VScode编辑器：常用快捷键-Markdown专题](https://www.lianxh.cn/details/1174.html), 连享会 No.1174.
  - 袁子晴, 2021, [Stata+LaTex：绘制流程图](https://www.lianxh.cn/details/652.html), 连享会 No.652.
  - 袁子晴, 2021, [Stata：输出漂亮的LaTeX表格-T222](https://www.lianxh.cn/details/613.html), 连享会 No.613.
  - 连享会, 2020, [Markdown常用LaTex数学公式](https://www.lianxh.cn/details/243.html), 连享会 No.243.
  - 连享会, 2020, [Word,MathType与LaTeX公式](https://www.lianxh.cn/details/291.html), 连享会 No.291.

  - 连玉君, 2022, [LaTeX：TeXStudio编辑器快捷键大全](https://www.lianxh.cn/details/945.html), 连享会 No.945.
  - 连玉君, 2020, [Markdown中书写LaTeX数学公式简介](https://www.lianxh.cn/details/250.html), 连享会 No.250.
  - 连玉君, 2020, [Markdown图床：我们的图片存在哪里？](https://www.lianxh.cn/details/296.html), 连享会 No.296.
  - 连玉君, 2020, [Markdown教程之LaTeX数学公式简介](https://www.lianxh.cn/details/298.html), 连享会 No.298.
  - 连玉君, 2020, [Markdown：五分钟Markdown教程](https://www.lianxh.cn/details/300.html), 连享会 No.300.
  - 连玉君, 2024, [Markdown：连老师的Typora主题模板css文件下载](https://www.lianxh.cn/details/1426.html), 连享会 No.1426.
  - 连玉君, 2022, [Marp幻灯片模板：用Markdown快速写幻灯片](https://www.lianxh.cn/details/1059.html), 连享会 No.1059.
  - 连玉君, 2020, [Stata与LaTeX的完美结合](https://www.lianxh.cn/details/485.html), 连享会 No.485.
  - 连玉君, 2024, [TeXStudio：无法正常显示中文字符怎么办？](https://www.lianxh.cn/details/1488.html), 连享会 No.1488.
  - 连玉君, 2024, [VScode：实用 Markdown 插件推荐](https://www.lianxh.cn/details/1390.html), 连享会 No.1390.
  - 连玉君, 2019, [mdnice.cn——Markdown、知乎、公众号排版神器](https://www.lianxh.cn/details/83.html), 连享会 No.83.
  - 连玉君, 2019, [两种网页转Markdown的简便方法](https://www.lianxh.cn/details/32.html), 连享会 No.32.
  - 连玉君, 2020, [在 Markdown 中使用表情符号](https://www.lianxh.cn/details/172.html), 连享会 No.172.
  - 连玉君, 2021, [用VScode正则表达式转换Markdown和LaTeX链接](https://www.lianxh.cn/details/839.html), 连享会 No.839.
  - 连玉君, 2020, [神器-数学公式识别工具-mathpix](https://www.lianxh.cn/details/284.html), 连享会 No.284.
  - 连玉君, 2020, [连玉君Markdown笔记](https://www.lianxh.cn/details/286.html), 连享会 No.286.
  - 陈卓然, 2022, [LaTeX小技巧：简单实用的Beamer模板](https://www.lianxh.cn/details/904.html), 连享会 No.904.
  - 陈卓然, 2022, [语法高亮：LaTeX中的彩色Stata代码](https://www.lianxh.cn/details/944.html), 连享会 No.944.
  - 陈楚, 2022, [Stata+Markdown：输出幻灯片+PDF+Word-markstat](https://www.lianxh.cn/details/1137.html), 连享会 No.1137.
  - 陈波, 2021, [学术范儿的幻灯片制作：Beamer in Rmarkdown](https://www.lianxh.cn/details/764.html), 连享会 No.764.
  - 马云宁, 2022, [Markdown写作神器：五分钟Typora教程](https://www.lianxh.cn/details/1017.html), 连享会 No.1017.
