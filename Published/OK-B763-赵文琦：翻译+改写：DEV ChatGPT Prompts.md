&emsp;

> **作者：** 赵文琦 (浙江大学)        
> **E-mail:**  <wenqi.zhao@zju.edu.cn>   

> **Source:** 本推文翻译、改编自以下论文，特此致谢！
>- [DEV ChatGPT Prompts](https://github.com/PickleBoxer/dev-chatgpt-prompts)


- Title：善用大模型 (LLM) 提升科研效率：ChatGPT 提示词精选与实操指南
- keywords：大模型，AI 助手，提示词，编程



## 1. 简介：为什么研究者需要掌握 ChatGPT 提示词？

在当今科研环境中，高效利用人工智能工具已成为提升研究效率的关键因素。无论是数据清洗、模型构建、代码优化还是论文写作，大语言模型 (LLM) 都能显著减少重复性工作，让研究者将更多精力集中在创新思考上。本文精选了一系列面向开发者的 ChatGPT 提示词，并结合计量经济学、统计分析等研究场景进行了扩充，旨在为 Stata/R/Python 用户提供一套实用的 AI 对话指南。

## 2. 高效提示词的核心原则
与生活中许多事情一样，使用GPT-4时，你付出多少就会收获多少。在这种情况下，提供更多的上下文、指令和指导通常会产生更好的结果。

以下是一些改进的技巧和方法：

- 拆分你的提示词：
尝试将你的提示词和期望的结果分成多个步骤。对于复杂的计量模型或数据处理流程，先让AI理解数据结构，再讨论方法选择，最后才要求生成代码，这样的成功率远高于一步到位。
- 提供示例：
提供预期的输入、数据和输出以提高准确度和质量。在处理特殊格式的数据（如问卷结果、特定格式的文献数据库）时，提供1-2个具体示例会大幅提高AI理解准确度。
- 具体明确：
列出你知道的内容、需要的内容以及不需要包含的内容。明确指出使用的编程语言、包/库、处理步骤和预期输出格式，这对于生成可直接使用的代码至关重要。
- 要求它反思：
让AI反思其生成内容是提高可靠性的关键步骤，尤其适用于复杂模型设定、代码逻辑和研究设计等场景。可以问它"你为什么错了？"或者让它反思自己的回应。

## 3. 多轮提示法：提升代码质量的结构化流程
这是一套适用于各类编程任务的结构化提示流程，特别适合科研代码的优化与重构：

### 3.1 代码风格改写
让GPT-4重写你的代码为你想要的风格。

```stata
>Prompt: 
Review the following code and re-write it to modern es6 programming standards and formatting:

[insert code here]
```

### 3.2 审查代码中的逻辑错误和安全问题
获取关于逻辑或安全问题的建议。重要的是我们不要要求重构代码，只要求重构背后的理由。

```stata
>Prompt: 
Review your provided code 'tempFunction' for any logical or security concerns and provide a list of recommendations.
```

### 3.3 验证建议 (reflexion)
验证提供的建议。`reflexion` 是一种强大的技术，可以提高初始建议的准确性，并尝试消除任何 AI 幻觉。这并不总是必需的，但如果你对任何建议不确定，值得一问。

```stata
>Prompt: 
Review your above recommendations. Tell me why you were wrong and if any recommendations were overlooked or incorrectly added?
```

### 3.4 编写代码
结合审查、建议和反思，让GPT-4编写你的新代码。

```stata
>Prompt: 
Re-write the 'tempFunction' function based off your review and recommendations.
```

### 3.5 创建测试
创建一些简单的测试，我们可以在本地运行并验证结果。

```stata
>Prompt: 
Create two [ define technology ] tests for the above 'tempFunction' function. One that is expected to pass and one that is expected to fail.
```

## 4. 重写提示词
当你的初始提示词过于简单或模糊时，可以让ChatGPT帮你优化，将它们变成 "voyage inspirant" 类型的大师级提示词。

```stata
>Prompt:
[your prompt]

Re-write the above text to be more verbose and include a lot of superfluous description about each thing, use very painting language.
```

```stata
>Prompt: 
I'll provide a chatGPT prompt. You'll ask questions to understand the audience and goals, then optimize the prompt for effectiveness and relevance using the principle of specificity.
```

## 5. 寻求替代方案
当对AI生成的代码或解决方案不满意时：

```stata
>Prompt: 
I'll provide you with a piece of code that I made and 
I need you give me alternatives to do the same in other way:

[INSERT YOUR CODE HERE]
```

## 6. 代码文档的解释

```stata
>Prompt1#: 
I don't know how to code, but I want to understand how this works. Explain the following code to me in a way that a non-technical person can understand. Always use Markdown with nice formatting to make it easier to follow. Organize it by sections with headers. Include references to the code as markdown code blocks in each section. The code:

[insert code here]

Prompt2#:
Please add comprehensive documentation for [file or module name], including clear and concise explanations of its purpose, design, and implementation. Consider including examples of how to use the module, as well as any relevant diagrams or flow charts to help illustrate its workings. Ensure that the documentation is easily accessible to other developers and is updated as the module evolves. Consider using documentation tools such as inline comments, markdown files, or a documentation generator to simplify the process.

[insert code here]
```

**编写条款和条件**

```stata
>Prompt: 
Create terms and services for my website about an [AI tool] called [name].
```

**生成操作指南**

```stata
>Prompt: 
Write a cheat sheet for [markdown formatting].
```

**生成 README 文件**

```stata
>Prompt: 
Generate documentation for the code below. You should include detailed instructions to allow a developer to run it on a local machine, explain what the code does, and list vulnerabilities that exist in this code.

[enter code]
```

**编写详细 blogs**

```stata
>Prompt: 
Write a detailed blog on How to build a [COVID tracker] using React with proper structuring of code.

[enter code]
```

**解释代码**
不要花时间尝试弄清楚代码的工作原理，只需让ChatGPT为你解释它

```stata
>Prompt: 
Context: I'm starting a new position as backend developer and I have to start to understand how some functions are working
Technologies: [INSERT YOUR TECHNOLOGIES HERE]
You have to: explain me the code line by line

[INSERT YOUR CODE HERE]
```

**架构图**
使用 Mermaid 创建架构图

```stata
>Prompt: 
Write the Mermaid code for an architecture diagram for this solution [DESCRIBE SOLUTION]
```

**实体关系图**
使用 Mermaid  创建实体关系图

```stata
>Prompt: 
Write the Mermaid code for an entity relationship diagram for these classes [INSERT CLASSES]
```

## 7. 代码重构
代码重构是软件开发中的一个重要过程，旨在提高现有代码的质量、可读性和可维护性，而不改变其功能。重构可以提高代码效率，减少错误，使其更容易在未来修改或扩展。借助ChatGPT的帮助，你可以有效地重构你的代码并实现更好的代码结构。

```stata
>Prompt: 
I have a piece of code and I need you do a refactor of it:

[INSERT YOUR CODE HERE]
```

**老代码现代化**

```stata
>Prompt: 
Refactor the following code to modern es6 programming standards:

[INSERT YOUR CODE HERE]
```


**代码拆分**
如果你有一条做太多事情的长代码，你可以要求GPT-4将其重构为多条代码。

```stata
>Prompt: 
Refactor the following code into multiple methods to improve readability and maintainability:

[INSERT YOUR CODE HERE]
```

## 8. 总结
大语言模型的有效使用很大程度上依赖于精心设计的提示词。提示词工程的核心在于遵循几项基本原则：首先是任务拆分，即将复杂任务分解为多个连续步骤，每步专注于单一目标，从而提高 AI 回答的精确度；其次是提供具体示例，包含输入数据和期望输出，帮助 AI 更准确地理解需求；第三是明确表达需求，详细指定语言、框架和格式要求等关键信息，减少潜在歧义；最后是鼓励 AI 反思，让模型审视自己的回答，从而增强结果可靠性。最重要的是，应将 AI 视为协作者而非替代者，始终保持对输出的批判性评估。掌握这些提示词工程技巧将帮助研究者和开发者更有效地利用 ChatGPT 等大语言模型，在各类编程和研究任务中显著提升工作效率和输出质量。

## 9. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh AI助手 LLM, md0`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

- 专题：[数据处理](https://www.lianxh.cn/blogs/25.html)
  - [初虹](https://www.lianxh.cn/search.html?s=初虹), 2022, [Stata：fillmissing-缺失值填充-数值和文字的前后填充！](https://www.lianxh.cn/details/1050.html), 连享会 No.1050.
- 专题：[工具软件](https://www.lianxh.cn/blogs/23.html)
  - [吴小齐](https://www.lianxh.cn/search.html?s=吴小齐), 2024, [强大的Kimi：中国版ChatGPT平替](https://www.lianxh.cn/details/1423.html), 连享会 No.1423.
- 专题：[AI专题](https://www.lianxh.cn/blogs/55.html)
  - [吴欣洋](https://www.lianxh.cn/search.html?s=吴欣洋), 2025, [AI自动生成研究假设，靠谱吗？流程与挑战](https://www.lianxh.cn/details/1588.html), 连享会 No.1588.
- 专题：[内生性-因果推断](https://www.lianxh.cn/blogs/19.html)
  - [张琪琳](https://www.lianxh.cn/search.html?s=张琪琳), 2025, [CClaRA-扒了四万篇论文：如何论证因果关系？](https://www.lianxh.cn/details/1589.html), 连享会 No.1589.
  - [连小白](https://www.lianxh.cn/search.html?s=连小白), 2025, [AI助手系列：napkin.ai-文字转换为图片和表格](https://www.lianxh.cn/details/1540.html), 连享会 No.1540.
  - [连小白](https://www.lianxh.cn/search.html?s=连小白), 2025, [AI助手系列：借助AI工具复现高质量图形](https://www.lianxh.cn/details/1584.html), 连享会 No.1584.
  - [连小白](https://www.lianxh.cn/search.html?s=连小白), 2025, [AI工具！AI工具分类大集合](https://www.lianxh.cn/details/1587.html), 连享会 No.1587.
  - [连玉君](https://www.lianxh.cn/search.html?s=连玉君), 2023, [连玉君：我与ChatGPT聊了一个月](https://www.lianxh.cn/details/899.html), 连享会 No.899.
