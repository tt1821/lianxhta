# B723-空间计量-莫兰指数-moransi-空间自相关检验

任务：以如下论文为基础，写一篇推文介绍这个检验方法和 stata 的实现。

- Kondo, Keisuke (2025) "Testing for Spatial Autocorrelation in Stata," RIEB Discussion Paper Series No.2025-03. [-PDF-](https://www.rieb.kobe-u.ac.jp/academic/ra/dp/English/DP2025-03.pdf), [github](https://github.com/keisukekondokk/moransi)

## 简介
The `moransi` command computes global and local Moran's *I* statistics in Stata.

简要说明一下前期相关推文：

  - 陈子厚, 2022, [Matlab：莫兰指数的编程实现](https://www.lianxh.cn/details/1085.html), 连享会 No.1085.
  - 陈子厚, 2021, [Stata：面板数据的莫兰指数计算与散点图绘制-xtmoran](https://www.lianxh.cn/details/778.html), 连享会 No.778.
  - 陈振环, 张少鹏, 2021, [Stata空间计量：莫兰指数绘图moranplot命令介绍](https://www.lianxh.cn/details/729.html), 连享会 No.729.


## Stata 实操
### SSC 安装

```stata
ssc install moransi, replace all

shellout moransi.pdf  // PDF 原文

help moransi
```

### Demo Files

See three applied examples in [`demo`](https://github.com/keisukekondokk/moransi/blob/main/demo) directory.

```
.
|-- columbus //Stata replication code and data
|-- japan\_mesh\_pop //Stata replication code and data
|-- japan\_muni\_unemp //Stata replication code and data
```

## Reference

- Kondo, Keisuke (2018) "MORANSI: Stata module to compute Moran's *I*," Statistical Software Components S458473, Boston College Department of Economics.
URL: <https://ideas.repec.org/c/boc/bocode/s458473.html>
- Kondo, Keisuke (2025) "Testing for Spatial Autocorrelation in Stata," RIEB Discussion Paper Series No.2025-03.
URL: <https://www.rieb.kobe-u.ac.jp/academic/ra/dp/English/DP2025-03.pdf>