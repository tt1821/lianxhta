# R 语言绘制供需曲线指南

> 作者：张祖冲（江苏大学）  
> 邮箱：zzchong1228@163.com

本文基于 [Create supply and demand economics curves with ggplot2](https://www.andrewheiss.com/blog/2017/09/15/create-supply-and-demand-economics-curves-with-ggplot2/) 的内容，详细介绍如何使用 R 语言绘制经济学中的供需曲线图。

## 准备工作

首先，需要安装并加载必要的 R 包。如果未安装这些包，需要先运行以下命令：

```R
install.packages("ggplot2")
install.packages("dplyr")
install.packages("Hmisc")
```

然后加载这些包：

```R
library(dplyr)      # 用于数据处理
library(ggplot2)    # 用于绘图
library(Hmisc)      # 提供贝塞尔曲线函数
```

## 创建基本供给曲线

首先使用 `Hmisc` 包中的 `bezier` 函数生成一条平滑的供给曲线。该函数通过三个控制点 (1,1)、(8,5) 和 (9,9) 定义一条二次贝塞尔曲线，并生成曲线上的 100 个坐标点：

```R
supply <- Hmisc::bezier(x = c(1, 8, 9),
                        y = c(1, 5, 9)) %>%
  as_data_frame()

ggplot(supply, aes(x = x, y = y)) +
  geom_path(color = "#0073D9", size = 1) +
  theme_classic() +
  coord_equal()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513140610839.png)

## 绘制多条供给曲线

接下来，创建两条不同的供给曲线（一条直线和一条曲线），然后与原曲线一起在同一图表上展示：

```R
# 创建一条直线形式的供给曲线
supply1 <- Hmisc::bezier(x = c(1, 5, 9),
                         y = c(1, 5, 9)) %>%
  as_data_frame()

# 创建另一条不同形态的供给曲线
supply2 <- Hmisc::bezier(x = c(1, 9, 9),
                         y = c(1, 2, 9)) %>%
  as_data_frame()

# 合并三条曲线数据并添加标识符
all_supply_curves <- bind_rows(supply, supply1, supply2, .id = "id")

# 绘制三条供给曲线
ggplot(all_supply_curves, aes(x = x, y = y, colour = id)) +
  geom_path(size = 1) +
  scale_color_manual(values = c("#0073D9", "#001F40", "#80DBFF")) +
  theme_classic() +
  coord_equal()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513144104091.png)

## 添加需求曲线

下面创建一条需求曲线，并与供给曲线一起展示：

```R
# 创建需求曲线（通常为向下倾斜）
demand <- Hmisc::bezier(c(1, 3, 9),
                        c(9, 3, 1)) %>%
  as_data_frame()

# 在同一图表中绘制供给曲线和需求曲线
ggplot(mapping = aes(x = x, y = y)) +
  geom_path(data = supply, color = "#0073D9", size = 1) +  # 蓝色供给曲线
  geom_path(data = demand, color = "#FF4036", size = 1) +  # 红色需求曲线
  theme_classic() +
  coord_equal()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513144611172.png)

## 寻找供需均衡点

在经济学分析中，供需曲线的交点代表市场均衡。下面通过函数插值和求根方法找到这个均衡点：

```R
# 创建供给曲线的函数近似
# rule = 2 表示在曲线范围外进行外推而非返回NA
fun_supply <- approxfun(supply$x, supply$y, rule = 2)

# 测试该函数在几个点的值
fun_supply(c(2, 6, 8))
## [1] 1.590161 4.521605 6.805785

# 创建需求曲线的函数近似
fun_demand <- approxfun(demand$x, demand$y, rule = 2)

# 找到两曲线相交的x坐标（供需平衡点）
intersection_funs <- uniroot(function(x) fun_supply(x) - fun_demand(x), c(1, 9))
intersection_funs
## $root
## [1] 4.654098
##
## $f.root
## [1] 0.000002875289
##
## $iter
## [1] 5
##
## $init.it
## [1] NA
##
## $estim.prec
## [1] 0.00006103516
```

## 可视化均衡点

现在在图表中标记出均衡点：

```R
# 绘制带有均衡点x坐标参考线的供需图
ggplot(mapping = aes(x = x, y = y)) +
  geom_path(data = supply, color = "#0073D9", size = 1) +
  geom_path(data = demand, color = "#FF4036", size = 1) +
  geom_vline(xintercept = intersection_funs$root, linetype = "dotted") +
  theme_classic() +
  coord_equal()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513150544372.png)

添加均衡价格（y坐标）参考线：

```R
# 计算均衡点的y坐标（价格）
y_root <- fun_demand(intersection_funs$root)

# 绘制带有x和y参考线的完整均衡图
ggplot(mapping = aes(x = x, y = y)) +
  geom_path(data = supply, color = "#0073D9", size = 1) +
  geom_path(data = demand, color = "#FF4036", size = 1) +
  geom_vline(xintercept = intersection_funs$root, linetype = "dotted") +
  geom_hline(yintercept = y_root, linetype = "dotted") +
  theme_classic() +
  coord_equal()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513151306653.png)

## 创建通用交点计算函数

为了便于重复使用，创建一个通用函数来计算任意两条曲线的交点：

```R
# 创建计算两条曲线交点的通用函数
# curve1和curve2应为包含x和y列的数据框
curve_intersect <- function(curve1, curve2) {
  # 近似两条曲线的函数形式
  curve1_f <- approxfun(curve1$x, curve1$y, rule = 2)
  curve2_f <- approxfun(curve2$x, curve2$y, rule = 2)
  
  # 计算两曲线在x轴方向的交点
  point_x <- uniroot(function(x) curve1_f(x) - curve2_f(x),
                     c(min(curve1$x), max(curve1$x)))$root
  
  # 找到该x值对应的y值
  point_y <- curve2_f(point_x)
  
  # 返回交点坐标
  return(list(x = point_x, y = point_y))
}

# 使用该函数计算供需曲线交点
intersection_xy <- curve_intersect(supply, demand)
intersection_xy
## $x
## [1] 4.654098
##
## $y
## [1] 3.395557
```

## 绘制带连接线的均衡点

现在可以更清晰地展示均衡点，并用虚线连接到坐标轴：

```R
# 将交点坐标转换为数据框
intersection_xy_df <- intersection_xy %>% as_data_frame()

# 绘制带坐标轴连接线的均衡图
ggplot(mapping = aes(x = x, y = y)) +
  geom_path(data = supply, color = "#0073D9", size = 1) +
  geom_path(data = demand, color = "#FF4036", size = 1) +
  # 从x轴到交点的垂直连接线
  geom_segment(data = intersection_xy_df,
               aes(x = x, y = 0, xend = x, yend = y), lty = "dotted") +
  # 从y轴到交点的水平连接线
  geom_segment(data = intersection_xy_df,
               aes(x = 0, y = y, xend = x, yend = y), lty = "dotted") +
  # 标记交点
  geom_point(data = intersection_xy_df, size = 3) +
  labs(x = "Quantity", y = "Price") +
  theme_classic() +
  coord_equal()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513160545215.png)

## 模拟需求变化对均衡的影响

经济学分析中一个常见场景是研究需求变化对市场均衡的影响。下面创建第二条需求曲线，展示需求增加时的新均衡：

```R
# 创建表示需求增加的第二条需求曲线
demand2 <- Hmisc::bezier(c(3, 5, 11),
                         c(11, 5, 3)) %>%
  as_data_frame()

# 计算供给曲线与两条需求曲线的交点
intersections <- bind_rows(curve_intersect(supply, demand),
                           curve_intersect(supply, demand2))

# 绘制完整的比较图
ggplot(mapping = aes(x = x, y = y)) +
  geom_path(data = supply, color = "#0073D9", size = 1) +
  geom_path(data = demand, color = "#FF4036", size = 1, linetype = "dashed") +
  geom_path(data = demand2, color = "#FF4036", size = 1) +
  geom_segment(data = intersections,
               aes(x = x, y = 0, xend = x, yend = y), lty = "dotted") +
  geom_segment(data = intersections,
               aes(x = 0, y = y, xend = x, yend = y), lty = "dotted") +
  geom_point(data = intersections, size = 3) +
  labs(x = "Quantity", y = "Price") +
  theme_classic() +
  coord_equal()
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513160744937.png)

## 创建专业经济学图表

最后，添加标签、箭头和注释，创建一个专业的经济学教学图表：

```R
# 创建曲线标签的数据框
plot_labels <- data_frame(label = c("S", "D[1]", "D[2]"),
                          x = c(8, 1, 5),
                          y = c(8, 8, 8))

# 绘制完整的专业图表
ggplot(mapping = aes(x = x, y = y)) +
  # 绘制供给曲线
  geom_path(data = supply, color = "#0073D9", size = 1) +
  # 绘制原始需求曲线（虚线）
  geom_path(data = demand, color = "#FF4036", size = 1, linetype = "dashed") +
  # 绘制新的需求曲线
  geom_path(data = demand2, color = "#FF4036", size = 1) +
  # 添加连接到坐标轴的虚线
  geom_segment(data = intersections,
               aes(x = x, y = 0, xend = x, yend = y), lty = "dotted") +
  geom_segment(data = intersections,
               aes(x = 0, y = y, xend = x, yend = y), lty = "dotted") +
  # 添加曲线标签（使用数学表达式）
  geom_text(data = plot_labels,
            aes(x = x, y = y, label = label), parse = TRUE,
            family = "Source Sans Pro") +
  # 添加表示需求变化方向的箭头
  annotate("segment", x = 3.5, xend = 4.5, y = 6, yend = 7,
           arrow = arrow(length = unit(1, "lines")), colour = "grey50") +
  # 标记均衡点
  geom_point(data = intersections, size = 3) +
  # 自定义坐标轴刻度和标签
  scale_x_continuous(expand = c(0, 0), breaks = intersections$x,
                     labels = expression(Q[1], Q[2])) +
  scale_y_continuous(expand = c(0, 0), breaks = intersections$y,
                     labels = expression(P[1], P[2])) +
  # 添加标题和副标题
  labs(x = "Quantity", y = "Price",
       title = "Rightward shift in demand",
       subtitle = "As demand increases, so does price") +
  coord_equal() +
  # 设置主题和字体
  theme_classic(base_family = "Source Sans Pro") +
  theme(plot.title = element_text(family = "Source Sans Pro Semibold", size = rel(1.3)))
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513161742071.png)

## 完整代码示例

下面是创建一个完整经济学供需分析图的代码：

```R
# 创建供给曲线
supply <- Hmisc::bezier(c(1, 8, 9),
                        c(1, 5, 9)) %>%
  data.frame()

# 创建两条需求曲线
demand1 <- Hmisc::bezier(c(1, 3, 9),
                         c(9, 3, 1)) %>%
  data.frame()

demand2 <- Hmisc::bezier(c(3, 5, 11),
                         c(11, 5, 3)) %>%
  data.frame()

# 计算均衡点
intersections <- bind_rows(curve_intersect(supply, demand1),
                           curve_intersect(supply, demand2))

# 创建标签数据
plot_labels <- data_frame(label = c("S", "D[1]", "D[2]"),
                          x = c(8, 1, 5),
                          y = c(8, 8, 8))

# 绘制完整图表
ggplot(mapping = aes(x = x, y = y)) +
  geom_path(data = supply, color = "#0073D9", size = 1) +
  geom_path(data = demand, color = "#FF4036", size = 1, linetype = "dashed") +
  geom_path(data = demand2, color = "#FF4036", size = 1) +
  geom_segment(data = intersections,
               aes(x = x, y = 0, xend = x, yend = y), lty = "dotted") +
  geom_segment(data = intersections,
               aes(x = 0, y = y, xend = x, yend = y), lty = "dotted") +
  geom_text(data = plot_labels,
            aes(x = x, y = y, label = label), parse = TRUE,
            family = "Source Sans Pro") +
  annotate("segment", x = 3.5, xend = 4.5, y = 6, yend = 7,
           arrow = arrow(length = unit(1, "lines")), colour = "grey50") +
  geom_point(data = intersections, size = 3) +
  scale_x_continuous(expand = c(0, 0), breaks = intersections$x,
                     labels = expression(Q[1], Q[2])) +
  scale_y_continuous(expand = c(0, 0), breaks = intersections$y,
                     labels = expression(P[1], P[2])) +
  labs(x = "Quantity", y = "Price",
       title = "Rightward shift in demand",
       subtitle = "As demand increases, so does price") +
  coord_equal() +
  theme_classic(base_family = "Source Sans Pro") +
  theme(plot.title = element_text(family = "Source Sans Pro Semibold", size = rel(1.3)))
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/undefined20250513163531861.png)

