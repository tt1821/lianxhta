
> 作者：王烨文（武汉大学）
> 邮箱：<yw990818@163.com>

&emsp;

> **Source**：   
> Chernozhukov, V. & Hansen, C. & Kallus, N. & Spindler, M. & Syrgkanis, V. (**2024**): Applied Causal Inference Powered by ML and AI. [CausalML-book.org](https://causalml-book.org/); arXiv:2403.02467. [-PDF-](https://arxiv.org/pdf/2403.02467)，[Website](https://causalml-book.org/), [github](https://github.com/CausalAIBook)

&emsp;

- **Title**: 新书免费读：CausalMLBook-因果机器学习
- **Keywords**: 因果推断, 机器学习, 因果机器学习, ML+AI, 新书推荐, 新书推介, Jupyter Notebook 

&emsp;

> **编者按：** 我为什么推荐这本书？
> - 书中涵盖了因果推断领域的最新进展，融合了机器学习与人工智能的最新技术，提供了一个统一的分析框架，适用于复杂数据环境下的因果推断。
> - 可以免费在线 [分章阅读]()，也可以下载 [PDF](https://arxiv.org/pdf/2403.02467) 
> - 所有章节都沟通了 [Jupyter Notebook](https://github.com/CausalAIBook/CausalML-book)，供大家干中学。
> - 作者全是大牛，包括：
>    - 麻省理工学院 (MIT) 的 [Victor Chernozhukov](https://www.mit.edu/~vchern/)
>    - 芝加哥大学布斯商学院 (University of Chicago Booth) 的 [Christian B. Hansen](https://voices.uchicago.edu/christianhansen/)
>    - 康奈尔大学 (Cornell University) 的 [Nathan Kallus](https://nathankallus.com/)
>    - 汉堡大学 (University of Hamburg) 的 [Martin Spindler](https://www.bwl.uni-hamburg.de/en/statistik/team/spindler.html)
>    - 微软研究院 (Microsoft Research) 的 [Vasilis Syrgkanis](https://vsyrgkanis.com/)


&emsp;

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20250604164839.png)




&emsp;

## 1. 简介

**因果机器学习**作为一个新兴而迅速发展的交叉领域，正成为经济学、统计学与计算机科学等领域共同关注的核心话题。与传统机器学习专注于"预测"不同，这一领域试图整合因果推断的严谨理论框架与机器学习的灵活预测能力，使因果推断在面对高维、非结构化数据时依然具备解释力。

在这一背景下，《Applied Causal Inference Powered by ML and AI》（以下简称《CausalML》）应运而生。该书由 Victor Chernozhukov、Christian Hansen、Nathan Kallus、Martin Spindler 和 Vasilis Syrgkanis 多位领域领军人物联合撰写，旨在构建一个统一的分析框架，让因果推断方法在复杂数据环境中达到"可信、可用、可扩展"的目标。

《CausalML》的 arXiv 预印本发布于 2024 年 3 月，尽管问世时间尚短，但已经在学术论坛和社区中引起广泛关注。诸如 StackExchange 等平台上，不乏将其誉为"目前最好的因果推断入门书籍"的评论，称其"在直觉讲解与技术细节之间取得了良好平衡"，并赞赏其丰富的自学型代码资源。

特别值得一提的是，以下两位因果推断领域的顶尖学者对《CausalML》给予了高度评价：

> Joshua Angrist（2021 年诺贝尔经济学奖得主）：MIT 经济学教授，对因果推断中的自然实验与工具变量法有重要贡献，因"对因果关系分析的方法论贡献"获 2021 年诺贝尔经济学奖。其著作推广了因果推断在社会科学的应用。

![20250324164635](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20250324164635.png)

> Judea Pearl（结构化因果模型创始人）：UCLA 计算机科学荣誉退休教授，创立了结构因果模型（SCM）理论，对因果推断产生深远影响。因其在概率和因果推理方面的贡献荣获 2011 年 ACM 图灵奖。

![20250324164814](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20250324164814.png)

&emsp;

## 2. 关于本书

该书延续了结构方程模型的理论基础及其在机器学习中的现代等价形式——有向无环图（DAGs）与结构因果模型（SCMs）。尤其在第七章中，详细讨论了 Judea Pearl 提出的“因果推断第一定律”和“后门准则”等关键概念，这种理论继承性使得《CausalML》不仅是方法创新的产物，也是因果推断发展脉络中的重要一环。

书中尝试通过机器学习来解决 Pearl 框架在实际操作中面临的可计算性和灵活性挑战，从而填补了“为什么”与“如何做”之间的空白。也就是说，Pearl 提供了因果机制的形式语言，而《CausalML》进一步提供了在复杂数据背景下可操作的估计策略。

《CausalML》的核心思想在于将机器学习融入因果推断之中，而非仅将其作为前置预测步骤。该书展示了如何在满足统计推断有效性的前提下，利用现代预测算法来估计复杂模型中的冗余参数。

在这一框架下，机器学习不再是“黑箱”，而是被系统地调整以满足因果识别的需求。以 DML 方法为例，其核心在于 Neyman 正交性，并借助交叉验证（cross-fitting）技术来抵消高维预测误差对因果参数估计的干扰。这种方法论为因果推断提供了兼具灵活性与严谨性的分析手段，在处理传统方法难以胜任的复杂现实问题时尤为有效。

### 2.1 作者简介

《CausalML》由五位在因果推断与机器学习交叉领域均有深厚学术背景的学者共同撰写，他们分别是：

以下是你要求的格式转换结果：

* [Victor Chernozhukov](https://www.mit.edu/~vchern/), 麻省理工学院 (MIT)
* [Christian B. Hansen](https://voices.uchicago.edu/christianhansen/), 芝加哥大学布斯商学院 (University of Chicago Booth)
* [Nathan Kallus](https://nathankallus.com/), 康奈尔大学 (Cornell University)
* [Martin Spindler](https://www.bwl.uni-hamburg.de/en/statistik/team/spindler.html), 汉堡大学 (University of Hamburg)
* [Vasilis Syrgkanis](https://vsyrgkanis.com/), 微软研究院 (Microsoft Research)


| **作者**            | **主要学术机构**                      | **核心研究领域/贡献**                                                                                                                                    |
| ------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Victor Chernozhukov | Massachusetts Institute of Technology | 致力于高维计量经济学与统计学的理论与应用，尤其在双重机器学习（DML）、高维因果推断、政策评估、分位数回归等领域具有奠基性贡献。                            |
| Christian Hansen    | University of Chicago                 | 计量经济学领域的权威之一，在工具变量、稳健标准误、面板数据模型等方面具有深远影响，奠定了现代实证研究中可靠推断的理论基础。                               |
| Nathan Kallus       | Cornell Tech, Cornell University      | 研究可信赖的机器学习及其在个性化决策中的应用，涵盖因果推断、稳健优化、算法公平性等，致力于开发兼具统计严谨性与实践性的决策支持方法。                     |
| Martin Spindler     | University of Hamburg                 | 工作重点在计量经济学、统计学与机器学习的交叉领域，关注因果推断与高维方法在政策评估、金融分析中的应用，推动方法在学术界与业界落地。                       |
| Vasilis Syrgkanis   | Stanford University                   | 研究融合机器学习、因果推断、计量经济学与算法博弈论，在双重机器学习、工具变量方法及异质性处理效应估计等方面贡献突出，是 EconML 等开源工具包的核心开发者。 |

### 2.2 配套资料

《CausalML》不仅在理论层面构建了完整的因果机器学习框架，更在实践层面提供了丰富的配套资源，形成了一个涵盖多语言、多平台的完整生态系统。这些资源极大地降低了读者学习和应用相关方法的门槛。

#### 2.2.1 官方网站

- **书籍官网** ([https://causalml-book.org/](https://causalml-book.org/))
  作为本书的数字中心，官方网站免费提供了完整的 PDF 版本，以及所有章节的配套代码，读者可以直接下载运行，实现"边学边做"的互动式学习体验。特别值得一提的是，网站展示了 Python、R、Stata 三种主流数据分析语言的实现代码。

- **GitHub 仓库** ([CausalAIBook](https://github.com/CausalAIBook))
  GitHub 仓库托管了所有代码资源，包括数据集、示例脚本和扩展材料。仓库采用开源协议，鼓励社区贡献和反馈，形成了活跃的学习社区。

#### 2.2.2 核心软件包

书中介绍的方法论得到了多个成熟开源软件包的支持，这些包不仅实现了书中的核心算法，还提供了丰富的扩展功能：

**DoubleML**

- 官网：[https://docs.doubleml.org/stable/](https://docs.doubleml.org/stable/)
- GitHub：[https://github.com/DoubleML/doubleml-for-py](https://github.com/DoubleML/doubleml-for-py)
- 特点：提供 Python 和 R 双版本，分别基于 scikit-learn 和 mlr3 生态系统构建。实现了双重机器学习的完整框架，包括部分线性模型（PLR）、部分线性工具变量模型（PLIV）、交互模型（IRM）等。该包由本书作者团队主导开发，是学习和应用 DML 方法的首选工具。

**EconML**

- 官网：[https://econml.azurewebsites.net/](https://econml.azurewebsites.net/)
- GitHub：[https://github.com/py-why/EconML](https://github.com/py-why/EconML)
- 特点：由微软研究院开发，本书作者之一 Vasilis Syrgkanis 是核心贡献者。该包专注于异质性处理效应估计，实现了因果森林、元学习器、双重机器学习、DEEP-IV 等多种前沿方法，并支持超参数自动调优、并行计算和 SHAP 可解释性分析。特别适合需要精细化异质性效应建模、政策优化和高维情境下的应用。

**Stata Machine Learning**

- 官网：[https://statalasso.github.io/](https://statalasso.github.io/)
- 特点：为 Stata 用户提供了 Lasso、弹性网等高维方法的实现，与书中第 3-4 章的内容紧密相关。该项目使得传统计量经济学研究者能够在熟悉的 Stata 环境中应用双重机器学习方法。

**Double ML for R**

- 官网：[https://thomaswiemann.com/ddml/](https://thomaswiemann.com/ddml/)
- 特点：为 R 用户提供了灵活的 DML 实现，支持自定义学习器和多种因果参数估计，与 DoubleML 的 R 版本形成互补。

#### 2.2.3 相关因果推断工具

除了直接支持书中方法的软件包，因果推断领域还有其他重要工具，它们从不同角度丰富了因果机器学习的生态系统：

**DoWhy**

- 官网：[https://www.pywhy.org/dowhy/v0.12/](https://www.pywhy.org/dowhy/v0.12/)
- 定位：DoWhy 是 PyWhy 生态系统的核心组件，专注于因果推断的"四步法"：建模、识别、估计和反驳。它强调因果假设的明确化和敏感性分析，提供了基于图模型的因果识别算法。虽然 DoWhy 更偏重因果识别的理论框架，但其与 EconML 等估计库具有良好的协同能力，使得用户可以在明确因果假设后，无缝调用机器学习方法进行估计。

**CausalML**

- 官网：[https://causalml.readthedocs.io/en/latest/about.html](https://causalml.readthedocs.io/en/latest/about.html)
- 定位：由 Uber 开发的因果推断包，专注于营销和产品决策中的增量建模（Uplift Modeling）。该包实现了多种用于估计个体处理效应的算法，包括因果树、元学习器等，特别适合 A/B Tests 后的深度分析和用户分层。

&emsp;

## 3.  分章节内容介绍

### 3.1 核心内容部分

第 1 章回顾了在线性回归中进行预测性推断的基本原理，聚焦于高维场景下的普通最小二乘法及其统计特性。主要讨论了过拟合问题、样本分割等用于评估模型预测能力的技术手段，并强调了理解线性模型的局限性对于深入学习更复杂建模方法的重要意义。

第 2 章转向因果推断的基础——随机对照试验（RCT），通过引入潜在结果框架和平均处理效应（ATE）等关键概念，阐述了随机化如何消除选择偏误，并探讨了控制变量调整在提高估计精度和识别异质性处理效应中的作用。

第 3 章深入探讨了高维线性回归中的预测建模问题，重点介绍了 Lasso 等正则化方法如何在特征维度远大于样本量($p \gg n$)的背景下，通过施加稀疏性假设来构建有效的预测模型。

在此基础上，第 4 章介绍了如何在高维线性回归中进行统计推断，特别是 Double Lasso 方法及 Neyman 正交性原理如何在高维控制变量背景下为特定参数构造有效置信区间，即使控制变量数量极多，依然能够实现有效推断。

第 5 章讨论了在观察性研究中，处理效应不是随机的但依赖于可观测变量的情境下，如何基于条件可忽略性假设，通过回归调整或倾向得分匹配等方法识别平均因果效应，强调了"同类相比"的核心思想。

第 6 章介绍了线性结构方程模型（SEMs）和有向无环图（DAGs）作为表达和理解因果关系的工具。通过图结构，可以判断哪些变量需要被控制以满足条件可忽略性（即消除混杂），并指出如果错误地控制了对撞变量（Collider），可能反而引入偏误（Collider Bias）。

第 7 章将结构方程模型与 DAGs 扩展至非线性、非参数场景，介绍了 d-separation、后门准则（Backdoor Criterion）及反事实因果图（SWIGs）等概念。

第 8 章全面介绍了非线性机器学习方法，包括决策树（Decision Trees）、随机森林（Random Forests）、梯度提升树（Gradient Boosted Trees）以及神经网络（Neural Networks）等，这些方法为构建复杂非线性关系提供了强大工具。

紧接着，第 9 章探讨了如何在上述机器模型中进行统计推断，并重点介绍了双重机器学习（DML）方法。该方法通过结合 Neyman 正交与交叉验证技术（Cross-Fitting），使得在使用复杂机器学习模型的同时，仍能对低维目标参数（如 ATE）进行有效推断。

第 10 章聚焦于特征工程，探讨了如何将文本、图像等非结构化数据类型转化为可用于因果推断的结构化数据，涵盖了从主成分分析到 Autoencoders，再到 BERT、ResNet50 等预训练神经网络模型提取深层语义特征的方法。

### 3.2 高级主题部分

第 11 章进一步深化对 DAGs 的理解，详细探讨了如何识别"好"控制变量（有助于识别因果效应）与"坏"控制变量（可能引入偏误）的区别，强化了因果推断的直观性和逻辑性。

第 12 章聚焦于存在混杂因素时的因果推断问题，介绍了包括敏感性分析、工具变量法、代理控制变量法（Proxy Controls）在内的识别策略，为在无法全面控制混杂因素的条件下提供了潜在解决路径。

第 13 章将 DML 方法扩展应用于工具变量框架中，特别探讨了在弱工具变量场景下如何实现稳健推断。

第 14 章介绍了异质性处理效应的估计方法，涵盖了因果森林（Causal Forests）等技术，通过估计条件平均处理效应（CATEs）以揭示不同样本间的处理效应差异。

第 15 章进一步介绍了多种用于 CATE 估计的机器学习元学习器，如 T-Learner、S-Learner 和 X-Learner，并讨论了模型选择与性能评估的方法。

第 16 章将 DML 应用于双重差分模型中，重点探讨了在高维控制变量存在下，如何基于条件平行趋势假设实现 ATE 的有效估计。

最后一章（第 17 章）介绍了断点回归设计（RDD），并讨论了在存在多个控制变量的情况下，如何利用机器学习方法提升 RDD 估计的精度与稳健性。

### 3.3 基本框架

以下是《CausalML》的基本架构，方便大家更好地了解这本书：

**_CORE MATERIAL_**

- 1 Predictive Inference with Linear Regression in Moderately High Dimensions
- 2 Causal Inference via Randomized Experiments
- 3 Predictive Inference via Modern High-Dimensional Linear Regression
- 4 Statistical Inference on Predictive Effects in High-Dimensional Linear Regression Models
- 5 Causal Inference via Conditional Ignorability
- 6 Causal Inference via Linear Structural Equations
- 7 Causal Inference via Directed Acyclical Graphs and Nonlinear Structural Equation Models
- 8 Predictive Inference via Modern Nonlinear Regression
- 9 Statistical Inference on Predictive and Causal Effects in Modern Nonlinear Regression Models
- 10 Feature Engineering for Causal and Predictive Inference

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20250618010037.png)

**_TOPICS_**

- 11 Deeper Dive into DAGs, Good and Bad Controls
- 12 Unobserved Confounders, Instrumental Variables, and Proxy Controls
- 13 DML for IV and Proxy Controls Models and Robust DML Inference under Weak Identification
- 14 Statistical Inference on Heterogeneous Treatment Effects
- 15 Estimation and Validation of Heterogeneous Treatment Effects
- 16 Difference-in-Differences
- 17 Regression Discontinuity Designs

&emsp;

## 4. 总结

《CausalML》是一本在因果推断领域具有重要意义的前沿著作。它不仅系统地梳理了因果推断的基本原理，更在此基础上深度融合了机器学习与人工智能方法，为高维数据背景下的因果识别与推断提供了全新范式。

这本书的最大亮点在于其围绕"去偏机器学习"构建了统一框架，系统引入了 Neyman 正交、交叉验证等技术。这使得我们可以在放宽传统线性模型假设的前提下，仍然获得具有理论保障的无偏估计。正是在这一意义上，《CausalML》回应了“可信度革命”提出的挑战，为实证研究在数据复杂性日益上升的现实中提供了解决方案。

此外，该书还配套提供了 Python、R、Stata 等多语言的代码实现。特别是基于开源包（如 DoubleML 和 EconML）的实现，以及涵盖多种实际场景的数据示例，极大提升了读者的可操作性和学习效率。

作为一部汇聚经济学、统计学与计算机科学多位顶尖学者智慧的跨学科著作，《CausalML》不仅是因果推断方法演进的里程碑，也代表着未来实证研究从"统计识别"走向"算法驱动"推断的一种方向性尝试。无论对于计量经济学者、数据科学家还是希望将因果分析引入复杂实证问题的政策研究人员，它都提供了理论坚实、工具完备的前沿解决方案。

&emsp;

## 5. 相关推文

> Note：产生如下推文列表的 Stata 命令为：
> `lianxh 机器学习 DML, md nocat ex(专题 助教)`
> 安装最新版 `lianxh` 命令：
> `ssc install lianxh, replace`
  
  - 万储诚, 2025, [IV：形形色色的IV](https://www.lianxh.cn/details/1583.html), 连享会 No.1583.
  - 仵荣鑫, 2022, [知乎热议：如何学习机器学习](https://www.lianxh.cn/details/983.html), 连享会 No.983.
  - 全禹澄, 2021, [机器学习如何用？金融+能源经济学文献综述](https://www.lianxh.cn/details/670.html), 连享会 No.670.
  - 关欣, 2022, [机器学习在经济学领域的应用前景](https://www.lianxh.cn/details/905.html), 连享会 No.905.
  - 冯乔, 2023, [Stata中的堆栈泛化和机器学习-pystacked](https://www.lianxh.cn/details/1317.html), 连享会 No.1317.
  - 吕卓阳, 2021, [MLRtime：如何在 Stata 调用 R 的机器学习包？](https://www.lianxh.cn/details/85.html), 连享会 No.85.
  - 吴小齐, 2023, [R语言：L2 Boosting 在经济学中的应用](https://www.lianxh.cn/details/1288.html), 连享会 No.1288.
  - 吴欣洋, 2025, [AI自动生成研究假设，靠谱吗？流程与挑战](https://www.lianxh.cn/details/1588.html), 连享会 No.1588.
  - 张弛, 2025, [大语言模型到底是个啥？通俗易懂教程](https://www.lianxh.cn/details/1600.html), 连享会 No.1600.
  - 张瑞钰, 2021, [知乎热议：纠结-计量经济、时间序列和机器学习](https://www.lianxh.cn/details/585.html), 连享会 No.585.
  - 李俊奇, 2025, [Python-EconML包：快速上手动态双重机器学习](https://www.lianxh.cn/details/1577.html), 连享会 No.1577.
  - 李占领, 2020, [Semantic scholar：一款基于机器学习的学术搜索引擎](https://www.lianxh.cn/details/177.html), 连享会 No.177.
  - 李金桐, 2023, [因果推断：双重机器学习-ddml](https://www.lianxh.cn/details/1221.html), 连享会 No.1221.
  - 樊嘉诚, 2021, [Stata：机器学习分类器大全](https://www.lianxh.cn/details/505.html), 连享会 No.505.
  - 浦进博, 2024, [合成控制法最新进展！机器学习+SCM！-qcm](https://www.lianxh.cn/details/1515.html), 连享会 No.1515.
  - 王卓, 2023, [Python：从随机实验到双重机器学习](https://www.lianxh.cn/details/1204.html), 连享会 No.1204.
  - 田原, 2020, [支持向量机：Stata 和 Python 实现](https://www.lianxh.cn/details/149.html), 连享会 No.149.
  - 米书颖, 2024, [机器学习：大佬建议的标准动作](https://www.lianxh.cn/details/1470.html), 连享会 No.1470.
  - 罗宇恒, 2025, [美国生命末期健康照护预测建模](https://www.lianxh.cn/details/1541.html), 连享会 No.1541.
  - 董洁妙, 2022, [Stata：双重机器学习-多维聚类标准误的估计方法-crhdreg](https://www.lianxh.cn/details/1036.html), 连享会 No.1036.
  - 赵莹, 2022, [知乎热议：机器学习在经济学的应用前景](https://www.lianxh.cn/details/938.html), 连享会 No.938.
  - 连享会, 2021, [Stata-Python交互-7：在Stata中实现机器学习-支持向量机](https://www.lianxh.cn/details/557.html), 连享会 No.557.
  - 连享会, 2024, [连享会公开课：经济学中的机器学习——7月3日-免费参与](https://www.lianxh.cn/details/1415.html), 连享会 No.1415.
  - 连小白, 2025, [Kaggle-数据科学平台：找数据、搜代码一网打尽](https://www.lianxh.cn/details/1586.html), 连享会 No.1586.
  - 连小白, 2025, [Python常用包盘点：经济与金融领域的必备工具包](https://www.lianxh.cn/details/1568.html), 连享会 No.1568.
  - 马丁, 刘梦真, 2021, [机器学习：随机森林算法的Stata实现](https://www.lianxh.cn/details/568.html), 连享会 No.568.
