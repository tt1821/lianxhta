
> **作者：** 浦进博 (中山大学)    
> **邮箱：** <pzs143353@163.com> 

&emsp;

> **Source:** 本文主要翻译自 Naqvi, A. (2024). Stata package "geoboundary" version 1.1. Release date 24 November 2024. <https://github.com/asjadnaqvi/stata-geoboundary>，略有增减，特此致谢。 

&emsp;

- **Title**: Stata地图：geoboundary-轻松获取全球地图数据
- **Keywords**: Stata绘图, geoplot, 地图绘制, 在线地图数据, geoBoundary, gadm, ISO3

&emsp; 


地图数据往往难以获取，而在某些研究和分析场景下，我们需要直接在线获取地图数据以进行后续分析。此时，`geoboundary` 命令便派上用场！它不仅能够快速下载全球各级行政区划的边界数据，还可以通过与 Stata 的 `geoplot` 等命令配合，实现高效的地图可视化。

本文会介绍 `geoboundary` 的安装、使用和应用，并进一步展示如何结合下载的数据进行可视化分析。

&emsp;

## 1. geoboundary 简介

![geoboundary_banner](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20241219225251.png)

**geoboundary** 是由 [Asjad Naqvi](https://asjadnaqvi.github.io/) 博士新近发布的一个 Stata 外部命令，可直接从以下数据库下载国家级或全球行政区划数据：
- [geoBoundary](https://www.geoboundaries.org/): An open database of political administrative boundaries.
- [gadm.org](https://gadm.org/) v4.1: Provides maps and spatial data for all countries and their sub-divisions.

**主要特点：**
- **数据标准化**：支持 EPSG: 4326 ([WGS84](https://en.wikipedia.org/wiki/World_Geodetic_System)) 投影；
- **多级行政区划**：从国家边界 (ADM0) 到更细的区划级别 (ADM1-ADM5)，具体视国家而定，对于某些国家，数据可细化至第 5 级行政单位（即 `ADM5`）。
- **便于调用**：可与 Stata 的 `geoplot`、`spmap` 等命令无缝配合，进行可视化分析。

最后，需要强调的是，使用本包提供的数据即表示您同意 **7. 使用反馈和注意事项** 中列出的「免责声明」。

&emsp;

## 2. 引用规范

开发软件包需要耗费大量的编程、测试和修复时间。如果您使用了此包，引用将是对开发者工作的尊重和支持。

> Naqvi, A. (2024). Stata package "geoboundary" version 1.1. Release date 24 November 2024. <https://github.com/asjadnaqvi/stata-geoboundary> 

或者，您可以使用以下 BibTeX 格式引用：

```BibTeX
@software{geoboundary,
   author = {Naqvi, Asjad},
   title = {Stata package ``geoboundary''},
   url = {https://github.com/asjadnaqvi/stata-geoboundary},
   version = {1.0},
   date = {2024-11-25}
}
```

建议采用如下方式引用 geoBoundary 网站：

- Runfola, D. et al. (2020). geoBoundaries: A global database of political administrative boundaries. **PLOS ONE**, 15(4), e0231866. [Link](https://doi.org/10.1371/journal.pone.0231866) (rep), [PDF](http://sci-hub.ren/10.1371/journal.pone.0231866), [Google](<https://scholar.google.com/scholar?q=geoBoundaries: A global database of political administrative boundaries>).

&emsp;

## 3. 安装 geoboundary

本包可通过 SSC 或 GitHub 安装。GitHub 版本可能更为最新，包含错误修复、功能更新等，并可能改进语法或默认值。以下为版本说明。GitHub 版本最终会发布至 SSC。

**通过 SSC 安装（版本 v1.0）**：

```stata
ssc install geoboundary
```

**通过 GitHub 安装（版本 v1.1）**：

```stata
net install geoboundary, from("https://raw.githubusercontent.com/asjadnaqvi/stata-geoboundary/main/installation/") replace
```

以下代码用于设置绘图模板和字体，以便得到更佳的绘图效果：

```stata
ssc install schemepack, replace
set scheme white_tableau  

graph set window fontface "Arial Narrow"
```

&emsp;

## 4. geoboundary 的语法格式

### 4.1 获取元数据 (meta data)

以下是用于获取元数据的语法：

```stata
geoboundary meta, 
   [ 
    country(list) 
    iso(list) 
    level(list) 
    region(list) 
    any(list) 
    length(num) 
    strict 
    noseperator 
   ]
```

参阅 **4.3 meta 选项**，查看选项的具体含义。

### 获取边界数据 (boundary data)

以下是用于下载边界数据的语法：

```stata
geoboundary ISO3 codes, level(string) 
     [ convert name(str) replace remove ]
```

参阅 **4.4 boundary 选项**，查看选项的具体含义。

详细信息请参阅 `help geoboundary`。

### 4.3 meta 选项

- `country(string)`: 按国家名称搜索。目前不支持带空格的名称列表（将来会实现）。  
- `iso(string)`: 按 ISO3 代码搜索。  
- `region(string)`: 按区域名称搜索。正确的选项是世界银行区域分类，例如 LAC 表示拉丁美洲和加勒比地区。  
- `any(string)`: 在任何原始元数据列中搜索给定表达式。  
- `strict`: 将搜索严格限制为指定的表达式。例如，`region(NA)` 会返回 NA（北美）和 MENA（中东和北非），但如果指定 `strict`，则只返回 NA 区域的国家。  
- `level(string)`: 按 ADM 级别搜索。有效选项包括 ADM0、ADM1、ADM2、ADM3、ADM4 和 ADM5。  
- `length(numeric)`: 限制命令运行后显示的表格列的长度。如果表格跨多行显示且对齐不良（如遇到长名称时），此选项会非常有用。默认值为 `length(30)`。  
- `noseperator`: 在显示的表格中不添加分隔线。默认分隔符由 ISO3 确定。  

### 4.4 boundary 选项

- `geoboundary ISO3`: 定义单个或一组 3 位 ISO3 代码。如果代码无效，该国家将被跳过并显示错误信息。强烈推荐使用 `{cmd geoboundary meta}` 来检查正确的 ISO3 代码。全球地图的正确 ISO3 代码为 WLD。  
- `level(string)`: 有效选项为 ADM0、ADM1、ADM2、ADM3、ADM4、ADM5 或 ALL。也可以指定一个列表。如果选择 `all`，命令会尝试下载所有六个级别。如果某个级别不可用，将被跳过并显示错误信息。需要注意的是，较细的级别（例如 ADM4 或 ADM5）文件较大，请谨慎使用。  
- `source(name)`: 可选值为 `{opt source(geoboundary}`（默认值）或 `source(gadm)`。这两个数据集可能包含不同的边界数据，甚至不同的 ADM 级别可用性。需要注意，geoBoundaries 更新更频繁，但请在使用前检查。另外注意，GADM 是作为包含所有 ADM 级别的 ZIP 文件下载的，这意味着：（a）所有其他选项将被覆盖；（b）文件夹大小可能显著增加。其他数据源即将添加。  
- `replace`: 如果原始文件已存在，覆盖它们。  
- `convert`: 转换为 Stata shapefile 格式。  
- `name(string)`: 为 Stata shapefile 定义自定义名称。如果未使用此选项，默认名称格式为 `ISO3_ADMx`。例如，默认的 Stata shapefile 名称是 `USA_ADM1.dta` 和 `USA_ADM1_shp.dta`。  
- `remove`: 删除原始 shapefile 文件。此选项特别推荐用于批量下载。建议将下载的文件转换为 Stata 格式，然后使用此选项删除原始文件（如 `.shp`、`.prj`、`.shx`、`.dbf`），以避免文件夹大小急剧增长。  



&emsp;

## 5. 下载数据

在开始之前，请确保进入正确的工作路径，下载的地图文件将保存至此路径：

```stata
cd <mypath>

// e.g.
cd D:/data/GeoData  
```

### 5.1 元数据操作示例

#### 示例 1：查找国家代码
我们尝试查找 [ISO3 代码](https://zh.wikipedia.org/wiki/ISO_3166-1%E4%B8%89%E4%BD%8D%E5%AD%97%E6%AF%8D%E4%BB%A3%E7%A0%81) 为 `DOM` 的国家：

```stata
geoboundary meta, iso(dom) length(20)        // 搜索 ISO3 代码
geoboundary meta, any(dom) length(20)        // 搜索所有数据列
geoboundary meta, any(dom) length(20) strict // 严格匹配 DOM
```

- 第一个命令返回 Dominican Republic（多米尼加共和国）；  
- 第二个命令执行模糊搜索，并返回 Dominica（多米尼克）、Dominican Republic（多米尼加共和国）和 United Kingdom（英国）；  
- 第三个命令通过 `strict` 选项限制搜索，仅返回完全匹配 DOM 的结果。

#### 示例 2：模糊搜索关键词
例如，查找包含关键词 *island* 的国家：

```stata
geoboundary meta, any(island) length(15) nosep
geoboundary meta, any(island) length(15) strict nosep
```

- 第一个命令返回所有名称中包含 `island` 或 `islands` 的国家；  
- 第二个命令使用 `strict` 选项，仅返回名称中包含 `island` 的国家。

#### 示例 3：按区域查找国家
例如，查找属于北美（NA）区域的国家：

```stata
geoboundary meta, region(NA) length(15) 
geoboundary meta, region(NA) length(15) strict
```

- 第一个命令返回北美（North America, NA）以及中东和北非（MENA）区域的国家；
- 第二个命令仅返回北美区域的国家。

我们也可以指定下载 ECA 区域的国家列表：

```stata
geoboundary meta, region(ECA) length(15) strict
return list
```

命令返回两个本地变量 `r(geob)` 和 `r(gadm)`，分别对应可从 geoBoundaries 和 GADM 数据库下载的国家列表。这些列表可以直接用于后续命令。

扩展阅读：
- [Regional Classification](https://data.unicef.org/wp-content/uploads/2018/05/JME_Regional-Classifications.xlsx)
- [Definitions of world regions](https://ourworldindata.org/world-region-map-definitions)

### 5.2 边界数据操作示例

#### Step1: 下载原始 shapefile 文件
以下命令从 geoBoundaries 下载奥地利和德国的行政边界（ADM0 和 ADM1）数据：

```stata
geoboundary AUT DEU, level(ADM0 ADM1) replace
```

#### Step2: 转换为 Stata 格式
下载的 shapefile 文件可以转换为 Stata 格式：

```stata
geoboundary AUT DEU, level(ADM0 ADM1) replace convert
```

#### Step3: 删除原始文件以节省存储空间
如果不需要保留原始 shapefile 文件，可以使用 `remove` 选项删除它们。以下命令下载墨西哥的 ADM0、ADM1 和 ADM2 数据，并删除原始文件：

```stata
geoboundary MEX, level(ADM0 ADM1 ADM2) replace convert remove
```

#### 下载全球边界数据
下载全球的 ADM0 和 ADM1 边界数据：

```stata
geoboundary WLD, level(ADM0 ADM1) replace convert remove
```

&emsp;

## 6. 地图绘制

### 6.1 使用 spmap 或 geoplot 绘制地图

若您使用的是 Stata 16 或更低版本，则下载完数据后，可以加载地图数据并使用 `spmap` 命令绘制地图：

```stata
use WLD_ADM0, clear

spmap using WLD_ADM0_shp, id(_ID)
```

![20241220001621](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20241220001621.png)

对于 Stata 17 或更高版本的用户，可以使用 Stata 17 引入的 `geoplot` 命令绘制地图：

```stata
geoframe create WLD_ADM0

geoplot (line WLD_ADM0, lc(black)), tight
```

![world_geoplot](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/world_geoplot.png)


### 6.2 绘制更复杂的地图

如果已下载全球 ADM0 和 ADM1 边界数据，可以使用以下代码绘制更精美的地图：

```stata
geoframe create WLD_ADM0
geoframe create WLD_ADM1

geoplot ///
    (area WLD_ADM1, color(white) lc(red) lw(0.02)) ///
    (line WLD_ADM0, lc(gs3) lw(0.03)) ///
    , tight project(robinson) ///
      background(water) grid(lc(white) label) ///
      title("Global ADM0 and ADM1 boundaries") ///
      note("Source: https://www.geoboundaries.org/. Accessed using the #Stata #geoboundary package.", size(1.8))
```

![world_geoplot2](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/world_geoplot2.png)


### 6.3 下载、合并和绘制多个国家的边界

以下示例展示如何下载 DACH 国家（奥地利、瑞士和德国）的三个行政层级边界（ADM0、ADM1 和 ADM2），并将其合并后绘制地图（Stata 17 或更高版本）：

**Step 1：** 下载边界数据
```stata
geoboundary DEU AUT CHE,    ///
      level(ADM0 ADM1 ADM2) ///
      replace convert remove
```

**Step 2：**  创建地理框架
为每个国家的边界数据创建 `geoframe`：
```stata
geoframe create AUT_ADM0, replace
geoframe create AUT_ADM1, replace
geoframe create AUT_ADM2, replace

geoframe create CHE_ADM0, replace
geoframe create CHE_ADM1, replace
geoframe create CHE_ADM2, replace

geoframe create DEU_ADM0, replace
geoframe create DEU_ADM1, replace
geoframe create DEU_ADM2, replace
```

**Step 3：**  合并地理框架
将各国的边界合并为单一框架：
```stata
geoframe stack AUT_ADM0 CHE_ADM0 DEU_ADM0, into(dach0) replace
geoframe stack AUT_ADM1 CHE_ADM1 DEU_ADM1, into(dach1) replace
geoframe stack AUT_ADM2 CHE_ADM2 DEU_ADM2, into(dach2) replace
```

**Step 4：**  绘制合并后的地图
```stata
geoplot ///
    (area dach2 i._FRAME, lw(0.01) lc(white)) ///
    (line dach1 i._FRAME, lw(0.04) lc(white)) ///
    (line dach0 i._FRAME, lw(0.05) lc(black)) ///
    , tight project(peters) grid
```

![world_geoplot3](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/world_geoplot3.png)

### 6.4 应用实例：下载多个来源的边界数据

**Step 1：** 下载墨西哥的 geoBoundaries 数据
```stata
geoboundary mex, level(all) replace ///
      convert remove name(geob_mex)
```

**Step 2：** 下载墨西哥的 GADM 数据
```stata
geoboundary mex, level(all) replace ///
      convert remove name(gadm_mex) source(gadm)
```

**Step 3：** 创建地理框架
将两种来源的数据分别转换为 `geoframe`：
```stata
geoframe create geob_mex_ADM0, replace
geoframe create geob_mex_ADM1, replace
geoframe create geob_mex_ADM2, replace

geoframe create gadm_mex_ADM0, replace
geoframe create gadm_mex_ADM1, replace
geoframe create gadm_mex_ADM2, replace
```

**Step 4：** 绘制地图对比
基于 geoBoundaries 数据绘制的图形如下：
```stata
geoplot ///
	(area geob_mex_ADM2, color(white) lc(red) lw(0.02)) ///
	(line geob_mex_ADM1, lc(blue) lw(0.04)) ///
	(line geob_mex_ADM0, lc(black) lw(0.1)) ///
	, tight title("Mexico from geoBoundaries")
```

基于 GADM 数据绘制的图形如下：
```stata
geoplot ///
	(area gadm_mex_ADM2, color(white) lc(red) lw(0.02)) ///
	(line gadm_mex_ADM1, lc(blue) lw(0.04)) ///
	(line gadm_mex_ADM0, lc(black) lw(0.1)) ///
	, tight title("Mexico from GADM")
```

![geoboundary_mex1](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/geoboundary_mex1.png)

![geoboundary_mex2](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/geoboundary_mex2.png)



### 6.5 应用实例：对比不同来源的数据

以下示例展示了 Gambia（冈比亚）数据在 geoBoundaries 和 GADM 中的差异：

**Step 1：** 下载数据
```stata
geoboundary GMB, level(all) replace convert remove name(gadm_gmb) source(gadm) 
geoboundary GMB, level(all) replace convert remove name(geob_gmb)
```

**Step 2：** 创建地理框架
```stata
geoframe create geob_gmb_ADM0, replace
geoframe create geob_gmb_ADM1, replace
geoframe create geob_gmb_ADM2, replace

geoframe create gadm_gmb_ADM0, replace
geoframe create gadm_gmb_ADM1, replace
geoframe create gadm_gmb_ADM2, replace
```

**Step 3：** 绘制两种来源的数据
geoBoundaries 数据：
```stata
geoplot ///
	(area geob_gmb_ADM2, color(white) lc(red) lw(0.05)) ///
	(line geob_gmb_ADM1, lc(blue) lw(0.1)) ///
	(line geob_gmb_ADM0, lc(black) lw(0.2)) ///
	, tight title("The Gambia from geoBoundaries")
```

GADM 数据：
```stata
geoplot ///
	(area gadm_gmb_ADM2, color(white) lc(red) lw(0.05)) ///
	(line gadm_gmb_ADM1, lc(blue) lw(0.1)) ///
	(line gadm_gmb_ADM0, lc(black) lw(0.2)) ///
	, tight title("The Gambia from GADM")
```

**Step 5：** 叠加对比
```stata
geoplot ///
	(line geob_gmb_ADM2, lc(red) lw(0.2) label("geoBoundaries")) ///
	(line gadm_gmb_ADM2, lc(blue) lw(0.2) label("GADM")) ///
	, tight title("The Gambia: geoBoundaries vs GADM") ///
	glegend(layout(1 - 2) tsize(3.5))
```

![geoboundary_gmb1](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/geoboundary_gmb1.png)

![geoboundary_gmb2](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/geoboundary_gmb2.png)

![geoboundary_gmb3](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/geoboundary_gmb3.png)


&emsp;

## 7. 使用反馈和注意事项

> **反馈**

如遇到问题或需要改进功能，请访问 [GitHub 问题页面](https://github.com/asjadnaqvi/stata-geoboundary/issues) 提交反馈。


> **注意事项** 

该软件包采用全球标准化的行政边界分类体系。这种分类方式支持不同系统之间的整合。在该分类体系下，`ADM0` 始终表示国家边界，而 `ADM1` 则可能表示省、区或其他分类，具体视国家情况而定。部分国家的数据可以深入到第五级行政区划（`ADM5`）。

**注意事项 1**：GIS 文件通常体积较大且容易快速累积。请尽量只下载所需的数据，并避免重复下载相同的数据集。

**注意事项 2**：如果在下载多个数据集时，目标目录的磁盘写入速度低于下载速度，即使文件本身是有效的，也可能出现返回错误或 I/O 错误。这种情况可能发生在直接下载到云存储（例如 Dropbox、OneDrive 等）或使用较慢的硬盘（如 5400 转速的硬盘或非 SATA 硬盘）时。遇到这种情况时，可以选择将文件下载到其他存储位置，或者等待文件同步完成后再尝试操作。

> **免责声明**

本 Geographic Information System (GIS) 数据仅供信息或教育用途，不应作为法律或工程资源使用。尽管我们已尽力确保数据的准确性和可靠性，但这些数据是“按原样”提供的，不附带任何形式的保证。

通过本 GIS 软件包提供的数据，我们不对任何不准确、错误或遗漏，以及因使用数据而导致的任何决策或行动承担责任。用户需自行验证数据的准确性及其对特定目的的适用性，并对因使用数据所造成的后果负责。

请注意，由于更新、修正或其他修改，GIS 数据可能随时更改，恕不另行通知。建议用户访问原始数据来源或联系提供者以获取最新信息。

通过访问或使用本软件包提供的 GIS 数据，即表示您已了解并同意上述条款和条件。

&emsp;

## 8. 参考文献
- Naqvi, A. (2024). Stata package "geoboundary" version 1.1. Release date 24 November 2024. <https://github.com/asjadnaqvi/stata-geoboundary>
- Runfola, D. et al. (2020). geoBoundaries: A global database of political administrative boundaries. **PLOS ONE**, 15(4), e0231866. [Link](https://doi.org/10.1371/journal.pone.0231866) (rep), [PDF](http://sci-hub.ren/10.1371/journal.pone.0231866), [Google](<https://scholar.google.com/scholar?q=geoBoundaries: A global database of political administrative boundaries>)


&emsp;

## 9. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh `  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

- [万莉](https://www.lianxh.cn/search.html?s=万莉), 2021, [Stata绘图：世行可视化案例-条形图-密度函数图-地图-断点回归图-散点图](https://www.lianxh.cn/details/560.html), 连享会 No.560.
- [万莉](https://www.lianxh.cn/search.html?s=万莉), 2020, [Stata：空间计量之用-spmap-绘制地图](https://www.lianxh.cn/details/401.html), 连享会 No.401.
- [冷萱](https://www.lianxh.cn/search.html?s=冷萱), 2021, [Stata：我和她离多远？基于百度地图API的地理距离计算](https://www.lianxh.cn/details/509.html), 连享会 No.509.
- [张弛](https://www.lianxh.cn/search.html?s=张弛), 2024, [Stata 绘图：用 `geoplot` 绘制中国地图](https://www.lianxh.cn/details/1401.html), 连享会 No.1401.
- [彭显耿](https://www.lianxh.cn/search.html?s=彭显耿), 2020, [GIS地图制作栅格计算器的应用](https://www.lianxh.cn/details/475.html), 连享会 No.475.
- [浦进博](https://www.lianxh.cn/search.html?s=浦进博), [邓铨雄](https://www.lianxh.cn/search.html?s=邓铨雄), 2024, [Stata绘图：地图神器geoplot](https://www.lianxh.cn/details/1383.html), 连享会 No.1383.
- [王卓](https://www.lianxh.cn/search.html?s=王卓), 2022, [Python：绘制动态地图-pyecharts](https://www.lianxh.cn/details/922.html), 连享会 No.922.
- [王卓](https://www.lianxh.cn/search.html?s=王卓), 2024, [Stata绘图：政治关联可视化](https://www.lianxh.cn/details/1428.html), 连享会 No.1428.
- [王卓](https://www.lianxh.cn/search.html?s=王卓), 2024, [Stata绘图：政治关联可视化-B612](https://www.lianxh.cn/details/1427.html), 连享会 No.1427.
- [肖蕊](https://www.lianxh.cn/search.html?s=肖蕊), 2022, [Stata：地图绘制命令介绍-maptile](https://www.lianxh.cn/details/925.html), 连享会 No.925.
- [陈卓然](https://www.lianxh.cn/search.html?s=陈卓然), 2022, [Stata绘图：绘制二维地图和中国地图-geo2xy](https://www.lianxh.cn/details/984.html), 连享会 No.984.
