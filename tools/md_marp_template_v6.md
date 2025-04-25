````markdown
---
marp: true
size: 16:9        # 宽版：4:3
paginate: true  
footer: '连享会 &emsp; [lianxh.cn](https://www.lianxh.cn)'
---

<style>
/*一级标题局中*/
section.lead h1 {
  text-align: center; /*其他参数：left, right*/
}
section {
  font-size: 22px;      /* 正文字号 */
}
h1 {
  color: blackyellow;   /* 标题的颜色 */
  /*font-size: 28px; */ /* 标题的字号, 其它标题也可以这样修改 */
}
h2 {
  color: green;
}
h3 {
  color: darkblue;
}
h4 {
  color: brown;
}
/* 右下角添加页码 */
section::after {
  content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total); 
}
header,
footer {
  position: absolute;
  left: 50px;
  right: 50px;
  height: 25px;
}
</style>

<!--顶部文字-->
[lianxh.cn](https://www.lianxh.cn/news/46917f1076104.html) 

<br>

<!--封面图片-->
![bg right:50% w:400 brightness:. sepia:50%](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20220722114227.png) 

<!--幻灯片标题-->
# Marp 参数设置

<br>
<br>

<!--作者信息-->
[连玉君](https://www.lianxh.cn) (中山大学)
arlionn@163.com

<br>


---

## 二级标题

### 三级标题

#### 四级标题

---

<!-- backgroundColor: #FFFFF9 -->

## 改变背景颜色

在幻灯片页面首行加入如下语句：

- 背景为淡黄色
  - `<!-- backgroundColor: #FFFFF9 -->`
- 背景为淡灰色
  - `<!-- backgroundColor: #C1CDCD -->`
- 背景颜色为白色  
  - `<!-- backgroundColor: white -->`
- 背景颜色为深蓝色
  - `<!-- backgroundColor: darkblue -->`

---
<!-- backgroundColor: white -->

## 图片靠右

- `bg right:60% w:700`
  - 图片靠右
  - 宽度为 700px
  - 占据屏幕宽度的 60%

![bg right:60% w:700](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20220722114227.png)

---

## 图片靠左

- `bg left:40% h:200`：图片靠左，高度为 200px，占据屏幕宽度的 40%

![bg left:40% h:200](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20220722114227.png)

````