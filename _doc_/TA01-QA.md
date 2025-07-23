
# TA01：连享会助教答疑手册

> **任务：** 课程提问收集&内部协作整理   
> **Update：** `2025/4/23 10:07`

&emsp; 


开课前，建议大家熟悉如下工具：

- **基本工具**：[腾讯文档](https://docs.qq.com/)，Stata 命令：[lianxh](https://www.lianxh.cn/details/1296.html), [ihelp](https://www.lianxh.cn/details/1416.html), [getiref](https://www.lianxh.cn/details/1382.html)
- **网站**：[连享会主页](https://www.lianxh.cn)，[连享会FAQs](https://gitee.com/arlionn/PX/wikis/)
- **AI 工具**：ChatGPT, [DeepSeek](https://www.lianxh.cn/details/1566.html)，[豆包](https://www.doubao.com/)，[kimi](https://kimi.moonshot.cn/) 等
- **安装软件**：[VSCode](https://code.visualstudio.com/)-[Markdown](https://www.lianxh.cn/details/1456.html)-[编辑器](https://www.lianxh.cn/details/1490.html)，[Snipaste](https://www.lianxh.cn/details/1111.html)-截图工具，[Mathpix](https://mathpix.com/)-[数学公式识别](https://www.lianxh.cn/details/284.html)
- **注册账号**：建议注册一个 [GitHub](https://github.com) 账号，一个 [码云-gitee](https://gitee.com) 账号，方便后续使用 [GitHub Copilot](https://github.com/features/copilot)、上传答疑文档和推文。


--- - --

## 1. 工作概览

答疑工作主要分为两个部分：
  
- **A1. 课程微信群答疑**。开课前一周到课程回放结束前，学员会在微信群中提问，助教需要及时解答。详见 [A1. 课程微信群答疑](#qa-a1)。
  - **参与人**：所有助教，不分组。

- **每日答疑文档收集和整理** (上课期间)。
  - **S1. 收集问题**。每日早 9:30 通过**腾讯文档**在课程群内收集学员的问题。详见 [S1. 收集学员的提问](#qa-s1)。
  - **S2-预回答**。在 Markdown 中整理成 Q-A 形式，由助教预先回答约 80% 的问题。当日值班小组成员预先回答力所能及的问题 (Part1)，留下 10-15 个无法应对的问题发给老师解答 (Part2)；参见 [3. 答疑方法和工具](#qa-tools)。
  - **S3-给老师发送答疑文档**。将 Part1 和 Part2 中问题编辑为 **.md** 或 **.docx** 文档，在正式答疑前 10 分钟发给讲师。注意：Part2 中问题放在文档前面，方便老师查看；Part2 部分放在后面，便于老师了解学员疑问并补充回答。
  - **S4-发布答疑文档**。老师答疑结束后，助教团队分工整理答疑 Q-A 文档 (谁整理某题谁署名)；由当日小组长将最终 Q-A 文档发布到 [连享会-FAQs](https://gitee.com/arlionn/PX/wikis/) 中对应的课程文件夹下。详见 [S4. 发布答疑文档](#qa-s4)。


--- - --

&emsp;

## 2. 工作流程详解

### A1. 课程微信群答疑{#qa-a1}

微信群答疑时间为开课前两周至结束后两周。开课前主要解答软件安装和基础问题；上课期间及课后，学员会针对授课内容和代码实操提出问题，需及时解答。

- **协作：** 答疑工作不做分组，大家团队协作，能者多劳；回答问题少的同学可以在问题整理等其他环节多承担一些。工作表现作为入选「连享会-助教群英会」的重要依据。
- **工具和答疑方式：** 如果学员提的问题不够清晰，可以要求他把问题背景说清楚。答疑过程中可以借助 AI 工具，也可以直接发送与问题密切相关的推文链接。详见 [3. 答疑方法和工具](#qa-tools)。


### S1. 收集学员的提问{#qa-s1}

- **工具：** 腾讯文档(在线收集表)
- **使用途径：**
  - 手机端：微信小程序
  - 电脑端：腾讯文档官网 (https://docs.qq.com/desktop)
- **演示截图** (以手机端为例)：
  ![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20200731-龙志能-课程问题收集步骤1.png)

- **参考模板** (仅供参考)
  - 收集主题：xxx班-第 # 日课程提问
  - 收集说明：【助教：XX】邀请大家参与问题收集 · 搜集 XX 班学员的问题
  - 输入问题：请尽量详细描述您的问题：
- **电脑端更多设置：** 限制填写次数&多种分享方式（微信小程序和链接）
  ![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20200731-龙志能-课程提问电脑端程序.png)


### S2. 预回答{#qa-s2}

学员提到的多数问题都可以在往期课程的 [连享会-FAQS](https://gitee.com/arlionn/PX/wikis/) 或连享会[推文](https://www.lianxh.cn) 中找到答案。因此助教在看到学员的提问后，首先在这两个渠道搜索是否有相关的解答。

- 对于高度契合的问题，用 `lianxh` 和 `songbl` 命令搜索已有解答，将推文链接插入答疑文档即可。注意：请务必使用 `lianxh 关键词, md nocat` 命令，生成 Markdown 格式的链接，方便后续发布到 Wiki 中。
- 对于不太契合的问题，助教可以借助 AI 工具编排答案，辅以几个与问题相关的推文链接。注意：所有回答都要用 Markdown 格式，方便后续发布到 Wiki 中。
- 提供解答过程中，尽量提供准确的参考文献和推文链接，方便学员后续查阅。参考文献可以采用 `getiref` 命令快速生成引文信息，附加 PDF 和 Google 学术链接。详见 [3. 答疑方法和工具](#qa-tools)。
- 请务必在答案下方另起一行注明回答人姓名，方便后期更新：
  `(回答人：xxxx)` 

小组内部协作方法，参见 [3f. 内部协作整理](#qa-internal)。

### S3. 给老师发送答疑文档{#qa-s3}

- 发送给老师的 **QA 文档标题**为：`xx班-FAQs-Day#.md`。
- 有些老师喜欢用 Word 文档 (请小组长预先与老师沟通)，可以用 Typora 或 VScode 将 `.md` 文档转换为 Word 版本发给老师。
  - 用 Typora 转 Word 前，需预先安装 [Pandoc](https://pandoc.org/installing.html)。详见：[五分钟Typora教程](https://www.lianxh.cn/details/1017.html)。
  - 用 VScode 转 Word 前，需预先安装 [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) 或 [Markdown Preview Enhanced](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced) 插件。详见：[VScode：实用 Markdown 插件推荐](https://www.lianxh.cn/details/1390.html)。
- **QA 文档格式**。按照 [此格式](https://gitee.com/arlionn/PX/wikis/2025Spring/A/A-Day1-FAQs) 的格式整理好：
    ```Markdown
    --------文档标题：--- xx班-Day#-答疑.md---------

    ### Q1. 问题描述
    >**A:** 
    解答文字 

    ### Q2. 问题描述
    >**A:** 解答文字
    ```

### S4. 发布答疑文档{#qa-s4}

- **负责人**：当日值班小组的组长。协调各位组员对当日 QA 文档进行整理和修改。
- **文档要求**：确保文档格式统一，内容准确。有些老师上课回答问题的时候会采用语音识别，形成的文字会比较口语化，可以把这些文字借助 AI 工具进行修改和润色。必要时可以补充一些参考文献、网页链接，以及连享会的推文进行扩展。
  - **文档标题**：`xx班-FAQs-Day#.md`。
  - **文档格式**：按照 [此格式](https://gitee.com/arlionn/PX/wikis/2025Spring/A/A-Day1-FAQs) 的格式整理好。
- **发布方式**：
  - **课程主页 Wiki：** 在课程主页 Wiki 中发布当天的答疑文档链接，方便学员查阅。进入 [连享会 QA-Wikis](https://gitee.com/arlionn/PX/wikis/Home) 页面后，在左侧找到对应的课程文件夹：`单击小圆圈 > 添加新页面 > 贴入当天的答疑文档 > 填写标题 > 保存`，形成文档链接。
  - **微信群**：(1) 发布当天的答疑文档链接；(2) 将 `xx班-FAQs-Day#.md` 转换为 PDF 格式，发布在微信群中。


&emsp; 

## 3. 答疑方法和工具{#qa-tools}

大家在回答问题过程中，可以通过如下途径搜索相关问题，若已经有解答，可以把对应的链接发给学员。也可以做个基本的答疑手册，把这些链接汇总起来，放在课程主页的 Wiki 里，每次学员提问之前可以先看看是否已经有答案。
- [连享会主页](https://www.lianxh.cn)；[连享会知乎](https://www.zhihu.com/people/arlionn/)
- [寒假班课程主页](https://gitee.com/arlionn/PX)
- [以往现场班 FAQs](https://gitee.com/arlionn/PX/wikis/%E8%BF%9E%E7%8E%89%E5%90%9B-Stata%E5%AD%A6%E4%B9%A0%E5%92%8C%E5%90%AC%E8%AF%BE%E5%BB%BA%E8%AE%AE.md?sort_id=2662737) | [更早的FAQs](https://gitee.com/arlionn/Course/wikis/Stata%E5%B8%B8%E7%94%A8%E9%93%BE%E6%8E%A5.md?sort_id=2018224)

### 3a. 连享会主页搜索

很多早期学员提出的问题都已经写成了推文，约半数以上的学员提供都可以通过这些推文得到解答。

非 Stata 用户，可以在 <https://www.lianxh.cn> 上可以直接搜索关键词，找到相关的推文和文章，进而把链接发到微信群中。

### 3b. AI 助手
你可以使用 ChatGPT, DeepSeek，豆包等工具来回答学员的问题。几点建议：
- 有些学员的问题问的很笼统，所以在借助 AI 提问时，可以自己修改一下提示词。
- 可以通过截图或链接来分享答案。另见，[DeepSeek对话分享方法](https://www.lianxh.cn/details/1566.html)。

### 3c. lianxh 命令 - 搜推文

在 Stata 中使用 `lianxh` (或 `songbl`) 命令，可以快速搜索连享会的推文，形成分享链接和文本。
- **安装方法：** `ssc install lianxh`
- **常用命令**：(参见 `help lianxh` 或 [lianxh命令介绍](https://gitee.com/arlionn/lianxh) )
  - `lianxh DID 倍分法` // 搜索包含关键词的推文
  - `lianxh DID, w nocat` // 以文本链接方式呈现推文，**便于在微信群中分享**
  - `lianxh DID, m nocat` // 以 Markdown 格式呈现推文，**便于插入推文或答疑文档** 

### 3d. ihelp 命令 - PDF 帮助文档

有时候在答疑文档或推文中插入 Stata 官方的 PDF 帮助文档，并指明页码，有助于解释一些复杂的公司和例子。

- **安装方法：** `ssc install ihelp`
- **使用方法：** 
  - `ihelp xtreg` // 在浏览器中打开 PDF 帮助文档
  - `ihelp xtabond, w` // 微信群分享 xtabond 命令的 PDF 文档链接
  - `ihelp xtabond, m` // Markdown 格式，适合插入答疑文档和推文

### 3e. getiref 命令 - 引用文献

如果回答中涉及到某些重要的文献可以使用 `getiref` 命令快速生成引文信息。

- **安装方法：** `ssc install getiref`
- **使用方法：** [getiref 命令](https://www.lianxh.cn/details/1382.html) 可以根据 DOI 号快速生成引文信息，附加 PDF 和 Google 学术链接。
- **常用命令：**
  - `getiref 10.1257/aer.109.4.1197, m` // Markdown 格式，适合插入答疑文档和推文。附加选项 `cite` 可以生成引用格式。
  - `getiref 10.1257/aer.109.4.1197, w` // 文本格式，适合微信群分享

### 3f. 内部协作整理{#qa-internal}

- **使用场景：**
  - 收集课程提问中，助教协作回答部分问题，并挑选问题给老师。
  - 老师答疑完成后，助教团队分工整理答疑，谁整理某题谁署名。
- **使用工具：** 腾讯文档(在线文档)
- **使用途径:**
  - 手机端：微信小程序
  - 电脑端：腾讯文档官网(https://docs.qq.com/desktop)
- 演示截图(建议使用电脑端)
  ![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20200731-龙志能-内部协作电脑端（准备）.png)
  ![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20200731-龙志能-内部协作电脑端.png)

&emsp;

## 4. 其他工具

### 4.1 数学公式和图片
- **数学公式**：可以使用 [Mathpix](https://www.lianxh.cn/details/284.html) (收费) 或者 [simpletex](https://simpletex.cn/) (推荐，免费) 来识别数学公式。
  - [Markdown中书写LaTeX数学公式简介](https://www.lianxh.cn/details/250.html)
- **图片**：可以使用 Snipaste 截图工具，直接截图到剪贴板中，然后粘贴到 Markdown 文档中。Snipaste 也可以将截图保存为图片文件，方便后续使用。
- **图片上传到连享会图床**：所有插入 QA 文档和连享会推文中的图片都必须上传到连享会的图床上，设置方法：[连享会 PicGo 图床配置](https://github.com/arlionn/lianxhta/wiki/lianxh_PicGo)。

### 4.2 Markdown 编辑器

  - 连玉君, 2020, [Markdown：五分钟Markdown教程](https://www.lianxh.cn/details/300.html), 连享会 No.300.
  - 初虹, 2021, [学术论文写作新武器：Markdown-上篇](https://www.lianxh.cn/details/603.html), 连享会 No.603.
  - 初虹, 2021, [学术论文写作新武器：Markdown-下篇](https://www.lianxh.cn/details/604.html), 连享会 No.604.
  - 初虹, 2021, [学术论文写作新武器：Markdown-中篇](https://www.lianxh.cn/details/605.html), 连享会 No.605.
  - 宋森安, 2021, [用Markdown制作幻灯片-五分钟学会Marp（上篇）](https://www.lianxh.cn/details/656.html), 连享会 No.656.
  - 宋森安, 2021, [用Markdown制作幻灯片-五分钟学会Marp（下篇）](https://www.lianxh.cn/details/657.html), 连享会 No.657.
  - 连玉君, 2024, [VScode：实用 Markdown 插件推荐](https://www.lianxh.cn/details/1390.html), 连享会 No.1390.
  - 初虹, 2022, [Markdown-LaTeX：经管人的VSCode配置大全](https://www.lianxh.cn/details/1004.html), 连享会 No.1004.


&emsp;

## 5. 注意事项

- 腾讯文档在线收集表后台可以导出 xls 格式，复制问题那一列到 Markdown 文档或者腾讯文档（在线文档）中稍加整理即可使用。也可以借助 AI 工具直接转换为 Markdown 格式。
- 老师精力有限，助教可以收集过程中（如：寒假大课：中午一次，下午第二次课结束后一次）回答部分技术性问题，并挑选 10-15 个问题留给老师回答。
- 答疑结束后，及时整理老师答疑，并回答收集表中但老师并未回答的内容，最终形成 FAQs，整理到码云仓库中。
- 当天的问卷可以在当晚 21 点左右停止收集，第二天重新开一个问卷。


