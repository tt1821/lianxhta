# 「论文复现」和「论文推介」类推文-写作指南

&emsp;

本文源于 [连享会·助教答疑和推文指南](https://file.lianxh.cn/KC/lianxh_TA_Guide.pdf) 第五部分。

> v1: `2023/4/10 16:24`   
> v2: `2023/7/11 14:59`  
> v3: `2024/10/29 15:33`
> v4: `2025/6/26 22:07`


## 5. 「论文复现」和「论文推介」类推文-写作指南

此类推文的风格类似于香樟推文，以及连享会发布的「论文复现」类推文，在 Stata 中输入 `lianxh 论文复现` 可以查阅相关推文，如：

  - [论文复现：顶刊JF中的因子分析-谁更容易当CEO？](https://www.lianxh.cn/news/a0daca718e58e.html)
  - [Stata论文复现：做一个优雅的码农](https://www.lianxh.cn/news/209224ad5a102.html)
  - [Stata论文复现：份额移动法工具变量(Shift-Share IV)](https://www.lianxh.cn/news/b3b8096a62205.html)
  - [Stata论文复现：社科领域Top5期刊复现资料](https://www.lianxh.cn/news/21a4b8d8117a4.html)
  - [Stata论文复现：女性领导人当选是否有助于更多女性从政-RD](https://www.lianxh.cn/news/8428cd3b4fd57.html)
  - [Stata论文复现：累进税率真的能减少不平等吗？](https://www.lianxh.cn/news/219df9d22f00b.html)
  - [Stata 论文复现：Temperature Shocks and Economic Growth](https://www.lianxh.cn/news/6f8cdf47e1ba0.html)
  - [Stata 论文复现：儿童权利公约对儿童健康的影响](https://www.lianxh.cn/news/d84937473a71d.html)
  - [Stata-JPE论文重现：资本深化与非平衡经济增长](https://www.lianxh.cn/news/91718ee7a984d.html)

### 5.1 需要提交的资料

- A. **中文精要**。用 Markdown 撰写；
    - 建议使用编辑器：VScode ([插件安装和使用](https://www.lianxh.cn/details/1490.html); [实用插件推荐](https://www.lianxh.cn/details/1390.html))
    - 你可以安装 **Markdown PDF** 或 **Markdown All in one** 等插件，将你的 Markdown 文档转换为 HTML 或 PDF 格式；
    - 你也可以使用 marp 制作一份与课程报告对应的幻灯片，参见 [Marp：用Markdown快速写幻灯片](https://www.lianxh.cn/news/148555c4f20ce.html) 以及 [DSbook-Marp 模板]()。
- B. **重现数据和代码**。建议参考 [T6_Chen_2019](https://www.jianguoyun.com/p/DZc_UaEQtKiFCBjr7OUEIAA) 的模式放置相关文档 (具体设定可以酌情增减)。
  - 文档名称：请使用我指定的文件夹名称，不要擅自修改。
  - 代码格式：原文作者提供的代码有时候排版很奇怪，请参考 [一些论文重现范本](https://www.jianguoyun.com/p/DVlZ9DYQtKiFCBil9O4EIAA) 中的代码格式进行调整。确保代码结构清晰，版面整洁、风格一致。
  ![20221117164308](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20221117164308.png)


### 5.2 中文精要写作要点
中文精要的目的是为了通过重现期刊论文中的关键结果，帮助学员更好地掌握课程中介绍的计量方法。要求日下：
- 全文 10000-15000 字。
- 文章背景介绍：
  - 1-2 页 (800-1500 字)
  - 重点介绍：研究了什么问题、基本方法、数据来源、主要结论
- 实证分析部分
  - 模型设定 (重点介绍对应专题中涉及的方法)
  - 关键变量的定义和含义
  - 作者的估计过程
  - 核心结果的讨论
  - 在介绍论文核心实证结果时，可以插入原文中的表格，并对作者提供的代码进行适当精简 (只选取有代表性的代码) 来说明表格中的结果对应的 Stata 代码。主要目的是使读者能够将论文中的结果和如何实操结合起来。 
- 其他相关文献扩展  (针对 **B 类**文献)
  - 介绍 2-3 篇引用该文的后续文献，重点介绍它们进行了拓展，或者从哪些新的角度 (样本、方法、变量等) 进行了分析。
  - 每篇半页篇幅即可


&emsp;


### 5.3 一些论文重现范本

- **下载：** Top 论文复现及中文精要范例
  - 坚果云：<https://www.jianguoyun.com/p/DVlZ9DYQtKiFCBil9O4EIAA>


- 这些都是连享会往期课程中使用的讲义，每篇文章都对应了**中文精要**、**Data\Codes**
- 部分论文还提供了使用 Marp 制作的幻灯片。 

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20221117122439.png)

&emsp; 

### 5.4 查找文献的来源

> [连享会：论文重现网站大全](https://www.lianxh.cn/news/e87e5976686d5.html)

- Mendeley 平台: [Discover Mendeley Data](https://data.mendeley.com/)
- 密歇根大学的 ICPSR: [ICPSR Data](https://www.icpsr.umich.edu/icpsrweb/)
- 哈佛大学的 IQSS: [Harvard Dataverse](https://dataverse.harvard.edu/)
  - 大而全
- Sebastian Kranz（Ulm University）: [Find Economic Articles with Data](https://ejd.econ.mathematik.uni-ulm.de/)
  - 可以指定期刊名称、代码类型等
- Replication WIKI: [Replication in the social sciences, particularly economics!](http://replication.uni-goettingen.de/wiki/index.php/Main_Page)



### 5.5 一些推文
  - [论文重现：外部命令的版本控制](https://www.lianxh.cn/news/e26dd9c9dd67a.html)
  - [Stata：论文重现代码模板](https://www.lianxh.cn/news/ca673ff4a27cc.html)
  - [可重复性研究：如何保证你的研究结果可重现？](https://www.lianxh.cn/news/6d3f9bbbdef36.html)



&emsp;

## 5.6 提交资料

- 请上传到坚果云 (1 分钟即可完成)：[点击提交](https://workspace.jianguoyun.com/inbox/collect/57246515e7e142b68971ac22f2bda5d0/submit)  
- 姓名写法：**FE-姓名** (在姓名前加上 `FE` 字样) 
- 注意事项：
  - 你可以更新你提交的资料，再次提交时，只需添加 `V1, V2, ……` 版本号标记即可，如「`张帅帅-V2-论文作者-年份.zip`」。
  - 请务必写明论文作者和年份。
