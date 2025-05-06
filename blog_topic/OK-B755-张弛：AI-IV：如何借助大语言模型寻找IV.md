# B755：AI-IV：如何借助大语言模型寻找IV？

- 请写一篇推文，介绍文章中提到的方法，并确保通过自己的 AI 工具进行实际测试。
- 最好使用像 Kimi 或者豆包这样的工具，因为它们的提示词和对话过程可以分享。DeepSeek 则无法分享。我个人使用的是 ChatGPT，但因为很多人没有账户，所以如果你分享的结果基于 ChatGPT，别人可能打不开。
- 最好挑选一篇最近一到两年内在国内顶级期刊（如《经济研究》或《管理世界》）上发表的论文，看看其中是否应用了工具变量法。或者，你也可以选择一个当前热门话题，在相关顶级期刊的多篇文章中发现内生性问题，然后尝试使用文章中介绍的方法寻找工具变量。你可以检验一下在这些顶刊文章已经给出的工具变量基础上，是否还能找到额外的有效工具变量。

Han, S. (2024). Mining Causality: AI-Assisted Search for Instrumental Variables (Version 2). arXiv. [Link](https://doi.org/10.48550/arXiv.2409.14202) (rep), [PDF](https://arxiv.org/pdf/2409.14202.pdf), [Google](<https://scholar.google.com/scholar?q=Mining Causality: AI-Assisted Search for Instrumental Variables (Version 2)>).

- 工具变量法（IVs）是因果推断的主流实证策略。寻找工具变量依赖研究者的创造性思维，而论证其有效性（尤其是排除性限制）常需修辞技巧。本文提出利用大语言模型（LLMs）通过叙事和反事实推理搜索新工具变量，其原理类似人类研究过程，但 LLMs 可极大加速搜索并探索海量可能性。我们设计了多步骤角色扮演提示策略，有效模拟经济主体决策逻辑并引导模型处理现实场景。方法应用于教育回报率、供需关系、同伴效应三大经典案例，并扩展至寻找回归 / 双重差分控制变量及断点设计运行变量。

There are at least four benefits to pursuing this AI-assisted approach to discovering IVs. 
- First, researchers can conduct a systematic search at a speedy rate, while adapting to the particularities of their settings. 
- Second, interacting with AI tools can inspire ideas for possible domains for novel IVs. 
- Third, the systematic search could increase the possibility of obtaining multiple IVs, which would then enable formal (i.e., statistical) testing of their validity via over-identifying restrictions. 
- Fourth, having a list of candidate IVs would increase the chances of finding actual data that contain IVs or guide the construction of such data, including the design of experiments to generate IVs.
