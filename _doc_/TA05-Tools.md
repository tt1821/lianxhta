to be update 

## Markdown 

  - 初虹, 2022, [Markdown-LaTeX：经管人的VSCode配置大全](https://www.lianxh.cn/details/1004.html), 连享会 No.1004.
  - 初虹, 2021, [学术论文写作新武器：Markdown-上篇](https://www.lianxh.cn/details/603.html), 连享会 No.603.
  - 初虹, 2021, [学术论文写作新武器：Markdown-下篇](https://www.lianxh.cn/details/604.html), 连享会 No.604.
  - 初虹, 2021, [学术论文写作新武器：Markdown-中篇](https://www.lianxh.cn/details/605.html), 连享会 No.605.
  - 宋森安, 2021, [用Markdown制作幻灯片-五分钟学会Marp（上篇）](https://www.lianxh.cn/details/656.html), 连享会 No.656.
  - 宋森安, 2021, [用Markdown制作幻灯片-五分钟学会Marp（下篇）](https://www.lianxh.cn/details/657.html), 连享会 No.657.
  - 连玉君, 2020, [Markdown：五分钟Markdown教程](https://www.lianxh.cn/details/300.html), 连享会 No.300.
  - 连玉君, 2024, [VScode：实用 Markdown 插件推荐](https://www.lianxh.cn/details/1390.html), 连享会 No.1390.


## AI 助手和提示词


::: {.callout-tip}
## 提示词：论文推介
> 手动操作部分：预先将论文 PDF 文档贴入 AI 助手对话框 (可以同时贴入多篇文献)

### 第一轮

1. 我想写一篇推文介绍附件中的论文，请帮我拟定一个提纲
:::


1. 在 Stata 中输入如下命令，以便获取论文的引文信息，并下载 PDF 文档到当前工作路径下：
```stata
getiref 2404.04301, md pdf arxiv
```
PDF 文档 **Zheng_2024_Robust_Nonparametric_Stochastic_Frontier_Analysis_(Version_1).pdf** (后文简称 `Zheng_2024.pdf`) 会被存入当前工作路径下的 **_temp_getref_** 文件夹。
1. 打开 ChatGPT 或其他 AI 助手，将 `Zheng_2024.pdf` 贴入对话框，并输入以下提示词：

```
```



## PDF 翻译

- <https://pdf2zh.com/>
  - Type：PDF 文档 | Link (在线文档)
  - Pages：首页 | 前 20 页 (长文档需要预先切割)