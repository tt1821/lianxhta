
> **作者：** 左祥太 (武汉纺织大学)  
> **邮箱：** <Shutter_Z@outlook.com> 

&emsp; 

- **Title**: Stata 可视化：biplot 一图看尽方差、相关性和主成分
- **Keywords**: Stata 绘图，方差分解，主成分分析

&emsp; 

## 1. biplot 命令介绍

通过使用 `biplot` 可以将一份数据集的分布特征生动地展现在一副二维图上。`biplot` 将会同时显示观测值和变量的相对位置。标记符号（点）会显示指定的标签，而变量则用箭头表示。所有特征的观测值将被投射到两个维度上，从而使观测值之间的距离近似保留（压缩至两个维度的主成分）。箭头之间角度的余弦近似于变量之间的相关性，箭头的长短近似于表示该变量的方差。

&emsp;

## 2. biplot 基本语法

`biplot` 语法中的 **[, options]** 划分了主要选项、行选项与列选项，本文将着重介绍几个最基本的实用项，其余选项以及介绍可以通过 `help biplot` 查看。

```stata
biplot varlist [if] [in] [, options]
```

- **rowlabel:** 用于指定图中点的标签
- **alpha:** 默认值为 0.5，此时绘制对称比例双平面图；取 1 时绘制主成分双平面图 (也称 row-preserving biplot) 
- **stretch:** 伸展列 (变量) 箭头，可简单记忆为调整图中箭头的长短
- **table:** 用于设定是否需要返回图中点的坐标
- **rowover:** 是否需要识别来自不同组别的变量
- **colopts(name()):** 更改图例中箭头的名称
- **rowopts(name()):** 更改图例中点的名称
- **title:** 设置图片标题
- **autoaspect:** 根据数据内容自动调整图片长宽比，默认为 1

&emsp;

## 3. Stata 实例

### 3.1 图形详解

在使用 `biplot` 绘图前需要下载安装该命令至本地：

```stata
ssc install biplot
```

接下来调用该命令所提供的数据集进行绘图操作，为了方便后期图片中颜色的显示，我们采用连享会上期推文中的 `schemes` 对图片风格进行设置，具体参考：[Stata 绘图：用 Stata 绘制一打精美图片-schemes| 连享会主页 (lianxh.cn)](https://www.lianxh.cn/news/0f2537275983f.html) 。

首先我们调用命令集所提供的数据文件，并观察数据的基本结构与变量名称。

```stata
. webuse renpainters			//调用数据

clear
input str15 painter byte(composition drawing colour expression)
"Del Sarto"       12 16  9  8
"Del Piombo"       8 13 16  7
"Da Udine"        10  8 16  3
"Giulio Romano"   15 16  4 14
"Da Vinci"        15 16  4 14
"Michelangelo"     8 17  4  8
"Fr. Penni"        0 15  8  0
"Perino del Vaga" 15 16  7  6
"Perugino"         4 12 10  4
"Raphael"         17 18 12 18
end
```

然后我们设置一副白色的背景风格，方便后续颜色的展示，最后通过 `biplot` 绘制一幅最基本的图片。

```stata
. set scheme white_tableau //设定绘图风格为white_tableau
                           //本推文的输出风格
. set scheme s2color  // Stata 默认

. biplot composition-expression	//简单绘制图片

/* 以下为 Stata 窗口显示内容 */
Biplot of 10 observations and 4 variables

    Explained variance by component 1  0.6700
    Explained variance by component 2  0.2375
             Total explained variance  0.9075
```

Stata 的窗口展示了每个组成维度的解释性方差，不难发现降维后的两个维度对原样本的方差解释力度达到了 90.75% ，即在一个可以接受的范围内还原了原始数据 90.75 %的数据特征。

![image-20211128105729604](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-20211128105729604.png)

图形解读如下：

- **点：** 上图中两点之间的距离近似于真实的多变量空间中两个观测点之间的欧氏距离。以第 4 和第 5 条样本为例，在原始数据中两个样本的取值一样，根据四维空间中欧氏距离的计算公式有：

$$
distance(x,y)=\sqrt{\sum_{i=1}^4(x_i-y_i)^2}\\
x_1=y_1=15,x_2=y_2=16,x_3=y_3=4,x_4=y_4=14\\
\rightarrow \ distance=0
$$

​		可得两点之间在四维空间中的距离为 0，所以压缩至二维空间，在 `bioplot` 图中表现为重合状态，观察上图可知样本 4 和样本 5 重合。

- **箭头：** 上图中线条的长度近似于变量的方差，即线条越长，方差越大。可以看到 **colour** 在所有变量中的方差最大，而 **drawing** 的方差最小。
- **夹角：** 线条之间角度的余弦值，可以近似于它们所对应变量之间的关联性。角度越接近 90 度 ($\cos90^{\circ} = 0$) 或 270 度 ($\cos270^{\circ} = 0$) ，相关度就越小。同理，0 度或 180 度的角度分别反映了 1 或 -1 的关联性。上图展现了 **composition** 和 **expression** 之间的强正相关关系， **colour** 和 **drawing** 之间的强负相关关系，而 **composition** 与 **colour** 以及 **drawing** 之间则表现为弱相关关系。



### 3.2 图形美化

为了方便对点标签的识别，引入 `rowlabel` 选项将变量 *painter* 中的文本作为上图中点的标签，同时通过 `rowopts(name())` 和 `colopts(name())` 分别改变图例中点和箭头的名称，并通过 `title` 选项为图加上标题。

```stata
biplot composition-expression, rowlabel(painter) ///
	rowopts(name(Painters)) colopts(name(Attributes)) ///
	title(Renaissance painters)
```

![image-20211128110313998](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-20211128110313998.png)

接下来通过 `alpha` 选项和 `stretch` 选项使得图片看起来更加舒适。

```stata
biplot composition-expression, rowlabel(painter) ///
	rowopts(name(Painters)) colopts(name(Attributes)) ///
	title(Renaissance painters) ///
	alpha(1) stretch(10)
```

![image-20211128110659582](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-20211128110659582.png)

同时我们可以利用 `table` 选项来返回图中各点的坐标值。

```stata
biplot composition-expression, rowlabel(painter) ///
	rowopts(name(Painters)) colopts(name(Attributes)) ///
	title(Renaissance painters) ///
	alpha(1) stretch(10) table
							   
/* 以下为 Stata 窗口显示内容 */
Biplot coordinates

        Painters |     dim1      dim2 
    -------------+--------------------
       Del Sarto |   1.2120    0.0739 
      Del Piombo |  -4.5003    5.7309 
        Da Udine |  -7.2024    7.5745 
    Giulio Rom~o |   8.4631   -2.5503 
        Da Vinci |   8.4631   -2.5503 
    Michelangelo |   0.1284   -5.9578 
        Fr Penni | -11.9449   -5.4510 
    Perino del~a |   2.2564   -0.9193 
        Perugino |  -7.8886   -0.8757 
         Raphael |  11.0131    4.9251 
    ----------------------------------

      Attributes |     dim1      dim2 
    -------------+--------------------
     composition |   6.4025    3.3319 
         drawing |   2.4952   -3.3422 
          colour |  -2.4557    8.7294 
      expression |   6.8375    1.2348 
    ----------------------------------
```

&emsp; 

最后，不知你是否觉得这个图片的风格很清爽，完全不同于 Stata 默认的粗笨风格。若想实现这个风格，只需在绘图之前执行如下命令，将绘图模板设定为 `white_tableau` 风格即可。

有关绘图模板的设定方法，参见往期推文：

- [Stata 绘图：一个干净整洁的-Stata-图形模板 qlean](https://www.lianxh.cn/news/9300f2b9f1960.html) 
- [Stata 黑白图形模板：中文期刊风格的纯黑白图形](https://www.lianxh.cn/news/79e3faf4e3dc3.html)

```stata
. ssc install schemepack, replace //安装white_tableau 模板
. set scheme white_tableau //设定绘图风格为white_tableau
```

若需重设为 Stata 默认绘图风格，可以按需执行如下两条命令之一：
```stata
. set scheme s2color  // Stata 默认, 彩色
. set scheme s2mono   // Stata 默认, 黑白
```

&emsp;

## 4. 结语

`biplot` 命令的出现很好地帮助我们在一张图内同时展现了变量之间的近似相关性、主成分等内容。与此同时，`biplot` 与 `schemes` 所提供的绘图背景相结合有助于我们画出更加惊艳的图片。

&emsp;

## 5. 参考资料

- Ulrich Kohler, Magdalena Luniak, 2005, Data Inspection using Biplots, **Stata Journal**, 5(2): 208–223. [-PDF-](https://journals.sagepub.com/doi/pdf/10.1177/1536867X0500500206)
- Stata manual, [**[MV]** biplot](https://www.stata.com/manuals/mvbiplot.pdf) 
- [GGE Biplot 双标图如何看？](http://wap.sciencenet.cn/blog-2577109-1191912.html)


&emsp;

## 6. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh 主成分 因子分析 相对重要, md0 nocat`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

- [周翔宇](https://www.lianxh.cn/search.html?s=周翔宇), 2022, [主成分分析 - 交互固定效应基础：协方差矩阵的几何意义](https://www.lianxh.cn/details/845.html), 连享会 No.845.
- [姚和平](https://www.lianxh.cn/search.html?s=姚和平), 2022, [Stata：特征值、特征向量与主成分分析-pca](https://www.lianxh.cn/details/886.html), 连享会 No.886.
- [左祥太](https://www.lianxh.cn/search.html?s=左祥太), 2021, [Stata 可视化：biplot 一图看尽方差、相关性和主成分](https://www.lianxh.cn/details/814.html), 连享会 No.814.
- [杨雨萱](https://www.lianxh.cn/search.html?s=杨雨萱), 2021, [domin：哪个自变量更重要？相对重要性分析最新命令](https://www.lianxh.cn/details/698.html), 连享会 No.698.
- [梁淑珍](https://www.lianxh.cn/search.html?s=梁淑珍), 2022, [论文复现：顶刊 JF 中的因子分析 - 谁更容易当 CEO？](https://www.lianxh.cn/details/993.html), 连享会 No.993.
- [王晓娟](https://www.lianxh.cn/search.html?s=王晓娟), [甘徐沁](https://www.lianxh.cn/search.html?s=甘徐沁), 2021, [regife：面板交互固定效应模型-Interactive Fixed Effect](https://www.lianxh.cn/details/42.html), 连享会 No.42.
- [胡雨霄](https://www.lianxh.cn/search.html?s=胡雨霄), 2020, [R2 分解：相对重要性分析 (Dominance Analysis)](https://www.lianxh.cn/details/52.html), 连享会 No.52.
- [郑宇](https://www.lianxh.cn/search.html?s=郑宇), 2021, [Stata：平行趋势不满足？主成分 DID 来帮你！- pcdid](https://www.lianxh.cn/details/760.html), 连享会 No.760.

