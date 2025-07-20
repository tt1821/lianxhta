
> **作者**：涂云峰 (厦门大学)  
> **邮箱**：<tuyunfengtife@163.com>

&emsp;

- **Title**: 如何在 MATLAB 中安装和配置 Dynare
- **Keywords**: Matlab 应用, DSGE 模型, Dynare 工具，Windows 系统

&emsp;

## 1. 何谓 Dynare？

Dynare 是一个强大的经济学建模工具箱，广泛应用于 DSGE 模型、计量经济学、最优控制等领域。

你只需要用一个简单的 `.mod` 文件来描述模型结构，Dynare 就可以自动完成：

- 模型线性化与稳态求解
- 一阶/二阶近似
- 贝叶斯估计
- 冲击响应函数绘制

这意味着，不会求导、不熟悉状态空间，也能运行复杂的 DSGE 模型。

本文将介绍如何在 MATLAB 中安装和配置 Dynare 工具箱，并简要说明如何使用。

&emsp;

## 2. 配置前你需要准备什么？

在配置 Dynare 之前，请确保你已经具备以下基础环境：

- MATLAB：R2017a 或以上版本（推荐使用 64 位版本），[下载并安装 MATLAB](https://www.mathworks.com/help/install/ug/install-products-with-internet-connection.html)
- Windows 系统（本文以 Windows 为例）
- 能解压压缩文件的软件（如 WinRAR 或 7zip）
- 有管理员权限的用户账户（安装 Dynare 时需要）

&emsp;

## 3. 下载并安装 Dynare 安装包(Windows 为例)

### 3.1 访问官网下载 Dynare

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig1_官网图片_涂云峰.png)

具体步骤如下：

- 打开 Dynare 官网下载页面：<https://www.dynare.org/download/>；
- 在页面上选择适合您操作系统的版本 (Dynare 支持 Windows、macOS 和 Linux 等平台)；
- 点击下载链接并保存安装包到您的计算机。

### 3.2 解压 Dynare

下载完成后，您将得到一个 `dynare-6.3-win.zip` 压缩包。右键点击该压缩包，选择“解压到当前文件夹”。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig2_压缩包_涂云峰.png)

解压后，您会看到一个名为 `dynare-6.3`的文件夹。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig3_解压文件_涂云峰.png)

打开后如下所示：

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig4_文件内容_涂云峰.png)

### 3.3 配置环境变量

打开 MATLAB，并在命令窗口中输入以下命令，将 Dynare 安装目录添加到 MATLAB 的路径中：

```matlab
addpath('C:/path_to_dynare/dynare-6.3/matlab');
savepath;
```

替换 `path_to_dynare` 为您解压 Dynare 的实际路径, 即保存 `dynare-6.3` 文件夹的实际位置。

或者，在 MATLAB 软件上方，找到 `设置路径`(`set path`)，点击进入

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig5_设置路径_涂云峰.png)

将您解压 Dynare 的实际路径, 即保存 `dynare-6.3` 文件夹的实际位置切换至安装文件夹中的 matlab 文件夹下，然后点击保存。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig6_路径保存_涂云峰.png)

&emsp;

## 4. 测试配置是否成功

在 MATLAB 命令窗口输入以下指令：

```matlab
dynare
```

如果你看到如下返回结果：

```
This is Dynare version 6.3.
```

说明 Dynare 已成功配置完成。

如果提示找不到 `dynare`，请回到上一步，确认路径是否正确添加。

可以输入以下指令, 查找 dynare 的安装路径：

```matlab
which dynare
```

你可以看到以下返回结果：

```matlab
D:\Matlab2024b\dynare-6.3\matlab\dynare.m
```

检查其是否与所上一步安装路径是否一致。

&emsp;

## 5. 运行一个测试模型

Dynare 安装包自带了一些 `.mod` 模型文件（在 `examples` 文件夹内），可以用来测试运行环境。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig7_examples内容_涂云峰.png)

以 `example1.mod` 为例，在 matlab 中打开 `example1.mod` 可以看到：

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig8_example1code_涂云峰.png)

### 操作步骤：

在 matlab 命令框中，输入如下代码：

```matlab
cd D:\Matlab2024b\dynare-6.3\examples
dynare example1.mod
```

你会看到 MATLAB 命令窗口中出现求解过程的输出，同时弹出多个图像窗口，展示冲击响应函数、误差方差分解等结果。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Dynare配置_Fig9_example1结果_涂云峰.png)

&emsp;

## 6. 常见错误及解决方案

- **错误信息 1**：`dynare` 不是内部或外部命令
  - 可能原因：路径未添加成功。
  - 解决办法：确认 Dynare 安装目录已正确添加到 MATLAB 的路径中，使用 `addpath()` 并保存路径。
- **错误信息 2**：`mex` 文件运行失败
  - 可能原因：Dynare 与 MATLAB 位数不一致。
  - 解决办法：确保 Dynare 和 MATLAB 都是相同位数（例如都为 64 位）。如果不一致，重新安装相应版本的 Dynare。
- **错误信息 3**：图像不显示 / 求解中断
  - 可能原因：`.mod` 文件语法有误。
  - 解决办法：打开 `.mod` 文件，检查模型是否完整，确保每个方程都定义正确，且没有遗漏。
- **错误信息 4**：`dynare` 执行过程中停滞
  - 可能原因：内存不足或 MATLAB 版本较低。
  - 解决办法：确保计算机有足够的内存来运行复杂的模型，或尝试简化模型。也可以考虑升级 MATLAB 版本。
- **错误信息 5**：`Error using dynare` 错误提示：`failed to find model`
  - 可能原因：`.mod` 文件路径问题或文件名错误。
  - 解决办法：确保 `.mod` 文件在当前工作目录中，且文件名与命令行输入一致。
- **错误信息 6**：`Error using dynare` 错误提示：`model block does not exist`
  - 可能原因：`model` 块丢失或未正确书写。
  - 解决办法：确保 `.mod` 文件中包含 `model` 块，并且块内有足够的方程来描述模型。
- **错误信息 7**：`Initial conditions not found`
  - 可能原因：初始值缺失。
  - 解决办法：确保在 `.mod` 文件中为所有模型变量提供了适当的初始值或稳态值。
- **错误信息 8**：`Error using dynare` 错误提示：`Eigenvalues not computed`
  - 可能原因：系统矩阵不满秩或模型不稳定。
  - 解决办法：检查模型是否有误，确保方程数量和未知数数量相等，并且模型设定没有逻辑错误。
- **错误信息 9**：无法绘制冲击响应函数（IRF）
  - 可能原因：模型没有正确收敛。
  - 解决办法：可能由于模型中某些参数或设定导致无法找到稳定的解。检查模型的稳态条件，确保所有参数设定合理。
- **错误信息 10**：`Error using dynare` 错误提示：`Cannot allocate memory for large arrays`
  - 可能原因：数值解的内存需求过高。
  - 解决办法：尝试简化模型，降低参数数量或减少模拟的时间步数，或使用更高配置的计算机。
- **错误信息 11**：`Error using dynare` 错误提示：`QZ decomposition failed`
  - 可能原因：矩阵维度问题或数值不稳定。
  - 解决办法：检查模型的线性化过程，确保系统的状态空间维度正确。也可以尝试调整模型的参数，避免极端值。
- **错误信息 12**：`Warning: Only steady state values`
  - 可能原因：稳态求解失败。
  - 解决办法：检查模型的稳态方程，确保所有变量的稳态值已定义，且数值计算稳定。
- **错误信息 13**：`MATLAB Error: Unable to access`mex`files`
  - 可能原因：`mex` 文件未编译或路径设置问题。
  - 解决办法：重新编译 `mex` 文件，确保 `mex` 编译器配置正确。可以在 MATLAB 中运行 `mex -setup` 来选择合适的编译器。

### 扩展的解决方案：

- **模型收敛性问题**：有时候 Dynare 模型可能因为初始猜测不当而无法收敛。你可以使用 `stoch_simul` 命令中指定合适的起始值或进行手动调整。

- **内存不足**：如果模型非常复杂且数据量庞大，可能会面临内存溢出问题。此时可以尝试减少模拟的时间范围或使用 MATLAB 的 `clear` 命令释放不必要的内存。

- **复杂模型调试**：对于较复杂的模型，建议分阶段调试，从简单的模型开始逐步构建，以确保每个步骤都能正常运行。在添加新的方程或修改参数时，确保每次修改后都进行验证。

&emsp;

## 7. 小技巧与建议

- 自动加载 Dynare：可以在 MATLAB 的 `startup.m` 文件中写入 `addpath(...)`，每次启动自动加载。
- 建议创建模型目录：避免将 `.mod` 文件直接放入 Dynare 安装目录，另建工作目录更清晰。
- 模型调试建议：从简单线性模型开始，逐步引入参数估计和冲击模拟。
- 如果在安装过程中遇到问题，您可以参考 Dynare 官网的常见问题解答[FAQ](https://www.dynare.org/faq/)。

配置好环境只是第一步。以下资源可以帮助你进一步学习：

- Dynare 官方手册：https://www.dynare.org/manual/
- Dynare 中文简明教程：https://www.lianxh.cn/DSGE.html
- DSGE 理论教材推荐：《Recursive Macroeconomic Theory》（Ljungqvist & Sargent），[PDF-4E](https://www.sfu.ca/~kkasa/Recursive_Macroeconomic_Theory_Ljungqvist_Sargent_2018.pdf)，[PDF-2E](https://www.theigc.org/sites/default/files/2016/06/LS-RMT2ed-04.pdf))

&emsp;

## 8. 总结

按照本文的步骤，你已经成功完成了 MATLAB 和 Dynare 的配置。接下来，你可以开始编写和运行自己的 DSGE 模型，探索动态经济学模型的世界。无论是初学者还是有经验的研究者，Dynare 都能帮助你高效实现建模、估计与模拟。

如遇任何问题，别忘了参考 Dynare 的[官方手册](https://www.dynare.org/manual/)以及[常见问题解答](https://www.dynare.org/faq/)。希望本文能帮助你顺利开始你的模型研究。

如果你有任何建议或者想要了解更多内容，欢迎留言或私信我！我也会定期更新更多入门和进阶教程，帮助大家更好地理解和应用 Dynare。

&emsp;

## 9. 相关推文

> Note：产生如下推文列表的 Stata 命令为：  
> &emsp; `lianxh 理论模型 理论解释 理论成立 CGE DSGE matlab, m nocat`  
> 安装最新版 `lianxh` 命令：  
> &emsp; `ssc install lianxh, replace`

- 司绪, 2021, [MATLAB：Ramsey 模型的求解与模拟](https://www.lianxh.cn/details/736.html), 连享会 No.736.
- 张弛, 2024, [如何撰写理论模型类论文？](https://www.lianxh.cn/details/1491.html), 连享会 No.1491.
- 曹昊煜, 2021, [Matlab：数据包络分析 (DEA) 入门教程](https://www.lianxh.cn/details/717.html), 连享会 No.717.
- 李祉豪, 2024, [dsgenl 命令：用 Stata 估计 DSGE 模型](https://www.lianxh.cn/details/1531.html), 连享会 No.1531.
- 罗丹, 贾寰宇, 2025, [不要“拍脑袋”建模：如何有效连接统计证据与理论解释](https://www.lianxh.cn/details/1590.html), 连享会 No.1590.
- 连享会, 2024, [CGE 专题：理论与实操](https://www.lianxh.cn/details/1336.html), 连享会 No.1336.
- 连享会, 2020, [DSGE 模型的 Stata 实现简介](https://www.lianxh.cn/details/293.html), 连享会 No.293.
- 连享会, 2024, [连享会：助教入选通知-2024-CGE 专题](https://www.lianxh.cn/details/1368.html), 连享会 No.1368.
- 连玉君, 2025, [如何借助 AI 工具来伴读一篇理论类的论文？](https://www.lianxh.cn/details/1571.html), 连享会 No.1571.
- 连玉君, 2025, [异质性分析：揭示理论成立的边界条件](https://www.lianxh.cn/details/1555.html), 连享会 No.1555.
- 陈子厚, 2022, [Matlab：莫兰指数的编程实现](https://www.lianxh.cn/details/1085.html), 连享会 No.1085.
- 韩少真, 展金永, 2020, [珠联璧合 II：Jupyter Notebook 与 Matlab 之融合](https://www.lianxh.cn/details/116.html), 连享会 No.116.
