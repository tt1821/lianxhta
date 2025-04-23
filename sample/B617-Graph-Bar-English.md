

> **作者：** (xx大学)    
> **邮箱：** <xxxx>      

> **Source:** John V. Kane. 2024. Advanced Bar Graphs in Stata (Part 2): Visualizing Relationships Between Discrete Variables. Blog in The Stata Gallery. [-Link-](https://medium.com/the-stata-gallery/advanced-bar-graphs-in-stata-part-2-visualizing-relationships-between-discrete-variables-7afb8cb5aaf2)

> This guide covers how to make bar graphs that more effectively communicate bivariate relationships between discrete variables. It shows how to use Stata’s -graph bar- command as well as the user-written `catplot` package for this purpose. It provides code to improve key aesthetics and also reviews a clever technique for adding a “Total” category to bar graphs.


- **Title**: Advanced Bar Graphs in Stata (Part 2): Visualizing Relationships Between Discrete Variables
- **Keywords**: 


在上一篇推文 [Stata绘图：高级柱状图（一）-均值和置信区间-cibar-coefpl](https://www.lianxh.cn/details/1379.html) ([英文版](https://medium.com/the-stata-gallery/advanced-bar-graphs-in-stata-part-1-means-with-confidence-intervals-2dd9d3b399b4)) 中，I covered how to create bar graphs of means with confidence intervals. These can be very useful when communicating a relationship between one discrete variable and one continuuos variable.

A second, perhaps even more common, use of bar graphs is to show *percentages of categories* rather than means. We do this when we want to display variation in a *discrete* (i.e., non-continuous) variable, or a *relationship* between discrete variables.

Common as such graphs are, effectively communicating a relationship between two discrete variables can be tricky. The percentages shown in the bars, for example, may not be the percentages we really want to be comparing. On top of that, in terms of code, there are *lots* of graphing options to be mindful of, not all of which are very intuitive.

The purpose of this guide is to cover strategies for making our bar graphs as informative as possible, displaying the comparisons we really want to make, and how to use key options for improving aesthetics. It will also discuss the user-written `catplot` package and a clever technique for creating a “Total” category.

## Preliminaries

A couple quick things before we get started:

1. Graphs will either use the **stcolor** scheme (new to Stata v.18) or schemes from **schemepack**， 参见如下推文：
- 袁子晴, 2021, [史上最牛Stata绘图模板-schemepack：酷似R中的ggplot2](https://www.lianxh.cn/details/819.html), 连享会 No.819.

To install, simply execute:

```Stata 
ssc install schemepack, replace
```

You can then explore all your awesome new schemes by executing the following:

```Stata 
graph query, schemes
```

> Note: All graphs use “AbelPro-Regular” font, which can be downloaded [here](https://www.cufonfonts.com/font/abel-pro). For details on installing/using fonts that are not native to Stata, see [here](https://twitter.com/UptonOrwell/status/1605056863858565120).

## Starting Simple

Let’s begin by importing the lbw.dta data set (which features data on mothers and the birthweights of their newborn children):

```Stata 
*-Import lbw

webuse lbw.dta, replace
```

For the purposes of this guide, let’s create a four-category variable to capture which age category each mother is in (these are approximately grouped into quartiles). (Note: I cover data-cleaning commands in Stata, as well as the very useful -fre-package, [here](https://medium.com/the-stata-gallery/cleaning-data-in-stata-c9a98c8fda74).)

```Stata 
*-Create a four-category age variable
gen agecats=1 if inrange(age, 14, 19)
replace agecats=2 if inrange(age, 20, 23)
replace agecats=3 if inrange(age, 24, 26)
replace agecats=4 if inrange(age, 27, 45)

fre agecats

label define agecats 1 "14-19" 2 "20-23" 3 "24-26" 4 "27-45", replace
label values agecats agecats

tab agecats 
```

The syntax to create a simple bar graph of counts of the “agecats” variable is as follows:

```Stata 
*-Simple bar graph of counts

graph bar (count), over(agecats) 
```

This code gives us the following graph (note that the y-axis features *frequencies* rather than percentages):

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*ZxUNJMlxs_SAsbCCUYDkkg.png)

Of course, we can (and should!) add a variety of options. I’ve annotated them in the following code, but take special note of two options in particular:

1. the “gap(*#)” option, which is really a sub-option within the “over(*varname*)” option. This controls the amount of space between each bar of whichever variable appears within “over( )”. Lowering the value of “#” below 1 will *decrease* the distance between bars, and increasing “#” above 1 will increase the distance (relative to the default distance). This is a key aesthetic feature to be aware of.
2. the “b1title( )” option, which places a title on the x-axis. It took me a *long* time before I discovered this option existed. (The “xtitle( )” command does not work with bar graphs; bewilderingly, Stata will insist that “xaxis1 does not exist”.)

```Stata 
*-Bar graph of counts (with added options)

graph bar (count),  /// basic command for bar graph with counts
  over(agecats, gap(*.9)) /// identify the "over" variable; specify distance between bars (here)
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// format bar labels
  ylab(, glcolor(gs15) glstyle(solid)) /// add light gray, solid horizontal lines
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// format look of bars
  scheme(white_jet) /// specify scheme
  b1title(Age Group) /// x-axis title
  ytitle(Frequency) /// title y-axis
  graphregion(margin(vsmall)) /// small margin between plot and outer edge of graph
  xsize(6.5) ysize(4.5) // specify graph dimensions in inches
```

Which gives us this:

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*EfCniWTjZKBSWTVjHC3oUA.png)

Bar graph of counts (with added options)

Frequencies/counts are helpful, but sooner or later (and especially with larger datasets), you’re probably going to want to see percentages instead.

The syntax to do so simply requires removing the “(count)” specification in the first line of code. I’ve also changed the title of the y-axis to indicate that percentages, not frequencies, are shown.

```Stata 
*-Bar graph of percentages of categories

graph bar,  /// basic command for percentages
  over(agecats, gap(*.9)) /// identify the "over" variable; specify distance between bars (here)
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// format bar labels
  ylab(, glcolor(gs15) glstyle(solid)) /// add light gray, solid horizontal lines
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// format look of bars
  scheme(white_jet) /// specify scheme
  b1title(Age Group) /// x-axis title
  ytitle(Percent of Sample) /// title y-axis
  graphregion(margin(vsmall)) /// small margin between plot and outer edge of graph
  xsize(6.5) ysize(4.5) // specify graph dimensions in inches
```

We then get the following graph:


![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*RaiZD3y92POuk6b4BaNnDw.png)

Bar graph of percentages

**Communicating Bivariate Relationships Effectively: Some Not-Great Options**

Now suppose we want to convey the relationship between mothers’ ages and whether or not they report having smoked cigarettes during their pregnancy (Yes or No). Getting a cross-table is simple enough:

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*KePRYsUv34pTvwPl2Tmzjw.png)

Here we specified column percentages, which enables us to answer the following question: *within each age group, what is the percentage of mothers who smoked vs. didn’t smoke?* If we are looking for the presence of a *relationship* between these variables, what we’d expect is that the percentage of smokers vs. non-smokers would look very different as we move from column to column (indicating that whether or not a mother smoked can be, in part, predicted by knowing her age).

There is indeed some slight variation in percentages from column to column (e.g., ~45% of mothers ages 14–19 smoked, while it is just 30.6% among mothers ages 24–26), though for whatever it’s worth, a chi-squared test indicates there is not a statistically significant relationship (*p*=.60).

But for our purposes, the real question is this: how can we best communicate this pattern visually?

In our first attempt, we’ll specify not one but *two* “over( )” variables. In this case, the first will determine the minor values that appear on the x-axis (i.e., immediately below the axis line), while the second “over( )” variable determines the major values that appear on the x-axis (i.e., farther below the line). In short, each major value will contain all of the minor values.

Let’s try it!

```Stata 
graph bar, over(smoke, gap(*.6)) /// identifies minor over variable; notice this controls gap distance
  over(agecats, gap(*1)) /// identifies major over variable
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// format bar labels
  ylab(, glcolor(gs15) glstyle(solid)) /// add light gray, solid horizontal lines
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// format look of bars
  scheme(white_jet) /// specify scheme
  b1title(Age Group) /// x-axis title
  ytitle(Percent of Sample) /// title y-axis
  graphregion(margin(vsmall)) /// small margin between plot and outer edge of graph
  xsize(6.5) ysize(4.5) // specify graph dimensions in inches
```

Here’s what we get:

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*hFHWcGqCfE8_TiUxKF20qg.png)

Bar graph with two “over( )” variables

At first glance, this seems to have done what we wanted: it shows the percentages of non-smokers and smokers, arrayed across categories of the age variable.

And yet, if you stare long enough, you notice something a bit odd that doesn’t fit so nicely with our cross-table above: *the percentages are out of the total sample, rather than as a share of each age category*. In other words, the percentages of all the bars total up to 100, rather than totaling to 100 within each age category.

This means that, as much as we might be tempted to, we ***cannot\*** say, for example, that 18% of mothers ages 20–23 were non-smokers.

This graph, in short, makes it hard to know what % of mothers in any age group were smokers vs. non-smokers.

Again, the problem is that the percentages are out of the sample as a whole, rather than — as in our cross-table above — out of the total number of observations within each age category. This subtle distinction can create lots of confusion.

A second (though less important) issue is that there is a lot of text on the x-axis, potentially making it more difficult to process for readers.

A simple fix for the latter concern is to add the “asyvars” option. This will generate separate colors for each category of the first “over( )” variable. We can also specify how each of these colors looks by using the “bar(#, …)” options (“1” for first category, “2” for second category, etc.):

```Stata 
*-Using asyvars option (see third line) and specifying colors (lines 7 & 8)

graph bar, over(smoke, gap(*.6)) /// notice this controls gap distance between smoke categories
  over(agecats, gap(*1)) ///  controls gap distance between age categories
  asyvars /// creates legend for each category of first "over()" variable
  legend(pos(3)) /// positions legend at 3 o'clock
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// format bar labels
  ylab(, glcolor(gs15) glstyle(solid)) /// adds gray horizontal solid lines
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// look of first smoke category
  bar(2, fcolor(blue%85) fintensity(inten100) lcolor(midblue) lwidth(vthin)) /// look of second smoke category
  scheme(white_jet) /// specifies scheme
  b1title(Age Group) /// titles x-axis 
  ytitle(Percent of Sample) /// titles y-axis
  graphregion(margin(vsmall)) /// small margin between plot and outer edge of graph
  xsize(6.5) ysize(4.5) // specify graph dimensions in inches
```

We get the following graph:

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*Lh3DTVuIegSbUoTuS3Xe_A.png)

Bar graph of bivariate relationship (with “asyvars” option)

Because it uses different colors for categories of “smoke”, this graph removes a lot of text and is perhaps a bit easier to make sense of than the previous graph.

And yet, the problem with percentages remains. How might we do better?

**Communicating Bivariate Relationships Effectively: Some Better Options**

One way to better visualize our cross-table result is by using the “by( )” option. What this does is break apart each value of the second “over( )” variable — one panel for each value.

Note: we can control how this graph is organized by using the “col( )” option, which indicates how many columns of subgraphs there should be. For example, “col(2)” specifies that the subgraphs should be arrayed in two columns.

Another *very important* tip: so many key options must appear ***within\*** the “by( )” options. In the code below, note that “by( )” begins on the second line but isn’t closed until the end of the third line:

```Stata 
*-Bar Graph with "by( )" option

graph bar, over(smoke, gap(*.2))  ///
  by(agecats, col(2) note("")     ///
  title("Age Group & Smoking Status", box color(white) span fcolor(black) lcolor(black) bexpand) ///
  subtitle(, color(gs4) fcolor(gs15) lcolor(midblue) lwidth(vthin) bexpand)) /// 
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// format bar labels
  asyvars ///
  legend(pos(6) row(1)) /// legend in 6 o'clock position and in one row
  ylab(, glcolor(gs15) glstyle(solid)) /// adds gray horizontal solid lines
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// look of first smoke category
  bar(2, fcolor(blue%85) fintensity(inten100) lcolor(midblue) lwidth(vthin)) /// look of second smoke category
  scheme(white_jet) /// specifies scheme
  ytitle(Percent of Sample) /// titles y-axis
  graphregion(margin(vsmall)) /// small margin between plot and outer edge of graph
  xsize(6.5) ysize(4.5) // specify graph dimensions in inches
```

This gives us the following:

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*W8-p5yJJ32oiMwK1ScuX9Q.png)

Note that we can also add the “asyvars” option if we don’t want all that text and/or would prefer a bit more color:

```Stata 
graph bar, over(smoke, gap(*.2))  ///
  by(agecats, col(2) note("") ///
  title("Age Group & Smoking Status", box color(white) span fcolor(black) lcolor(black) bexpand) ///
  subtitle(, color(gs4) fcolor(gs15) lcolor(midblue) lwidth(vthin) bexpand)) ///
  blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// format bar labels
  asyvars ///
  legend(pos(6) row(1)) /// legend in 6 o'clock position and in one row
  ylab(, glcolor(gs15) glstyle(solid)) /// adds gray horizontal solid lines
  bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// look of first smoke category
  bar(2, fcolor(blue%85) fintensity(inten100) lcolor(midblue) lwidth(vthin)) /// look of second smoke category
  scheme(white_jet) /// specifies scheme
  ytitle(Percent of Sample) /// titles y-axis
  graphregion(margin(vsmall)) /// small margin between plot and outer edge of graph
  xsize(6.5) ysize(4.5) // specify graph dimensions in inches
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*Od5i6ZKyqy13Zikhkdw2JA.png)

On the plus side, using the “by( )” option makes exactly the comparisons from our cross-tab above: we get to see the % of non-smokers vs. % of smokers within each age group. In other words, percentages sum to 100 *within each of the four panels*, rather than across all bars in the graph.

On the negative side, if there are a large number of categories, this can be a difficult graph to read (imagine 8 or 9 or 10 subgraphs instead of the 4 we have here). It can feel a bit like we’re trying to make sense of multiple different graphs instead of just one.

Second, it is slightly annoying to compare across age groups that aren’t featured in the same row (e.g., comparing the % of smokers ages 14–19 vs. % of smokers ages 27–45). We do not, in other words, have the luxury of simply comparing bar heights from left to right.

And this brings us to `catplot`, which is a [package](https://journals.sagepub.com/doi/pdf/10.1177/1536867X0400400209) written by Stata legend, [Nicholas J. Cox](https://www.statalist.org/forums/member/6-nick-cox).

To install, simply execute:

```Stata 
*-Install `catplot`

ssc install catplot, replace
```

The `catplot` package operates very much like the -graph bar- function that we’ve been using thus far. That said, the most important option within `catplot`, for our purposes, is “**percent( )**”. This indicates *which variable’s values should be considered the denominator*. In the language of a cross-tab, this option specifies which variable is arrayed in *columns* when we are asking for column percentages to sum to 100. Below, we specify that this is the “agecats” variable, not the “smoke” variable.

Two other important options are “recast(bar)” and “asyvars”. The first option ensures that the graph displays as a *vertical* bar graph (tip: use “recast(hbar)” for a *horizontal* version). The second option renders the different values of “smoke” as different colors. Here is the annotated code:

```Stata 
*-catplot

catplot smoke agecats, /// makes smoke first category, agecats second
    percent(agecats) /// percentages will be out of all cases within each value of agecats
    recast(bar) asyvars /// make this a bar graph; separate values of smoke using different colors
    legend(pos(3)) /// position the legend at 3 o'clock
    legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) /// add legend title
    bargap(10) /// specify gap between bars
    blabel(bar, color(white) format(%4.0f) size(small) pos(inside)) /// format bar labels
    ylab(, glcolor(gs15) glstyle(solid)) /// add light gray horizontal, solid lines
    b1title(Age Group) /// title the x-axis
    ytitle(Percent of Sample) /// title the y-axis
    graphregion(margin(vsmall)) /// small margin between plot and outer edge of graph
    xsize(6.5) ysize(4.5) /// specify graph dimensions in inches
    scheme(gg_set3) // specify scheme
```

We get the following graph:

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*nVYMjZCfEIAzWG6MKFl10g.png)

Notice: Each age group is considered the denominator, so we can easily see — within each age group — what % are non-smokers and what % are smokers. This is a big improvement over our initial attempts using Stata’s -graph bar- command.

And, in contrast to the “by( )” option above, we can easily just look left to right to examine the age groups with the tallest and shortest smoker/non-smoker bars.

Again, the syntax for `catplot` is otherwise very similar to the -graph bar- syntax. For example, we can easily change the color of the first and second categories of smoke using the “bar(1, …)” and “bar(2, …)” options, respectively. The code below provides an example of this, and also changes the legend position, adds a title, and makes some other minor aesthetic tweaks:

```Stata 
*-catplot with some extra customization

catplot smoke agecats, ///
percent(agecats) ///
recast(bar) asyvars ///
bargap(5) ///
blabel(bar, color(white) format(%4.0f) size(small) pos(inside)) /// made format whole numbers
ylab(, glcolor(gs15) glstyle(solid)) ///
b1title(Age Group) ///
bar(1, fcolor(midblue%85) fintensity(inten100) lcolor(cyan) lwidth(vthin)) ///
bar(2, fcolor(navy%85) fintensity(inten100) lcolor(blue) lwidth(vthin)) ///
ytitle(Percent of Sample) ///
graphregion(margin(vsmall)) xsize(6.5) ysize(4.5) ///
title("Example of How to Use -catplot-", box color(white) span fcolor(black) lcolor(black) bexpand) /// title
subtitle("Smoking Status & Age Group", box color(white) span fcolor(black) lcolor(black) bexpand) ///  subtitle
legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) ///
legend(ring(0) pos(11)) ///
scheme(white_jet)
```

We get the following graph:



![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*oO-x_VDYJmKVXZTL9LHRVw.png)

Done! We now have a graph that serves to visualize the cross-tab above: percentages for “smoke” are out of all observations in a given category of “agecats”.

**Displaying a “Total” Category**

One remaining detail that our cross-tab has, but that our graph above doesn’t, is a column for “total”. That is, our graph does not tell us the overall % of non-smokers and % of smokers in the sample.

How can we make a visualization that would display the total percentages?

One option is very simple. The “by( )” option approach that we used above conveniently includes a “total” suboption (the basic syntax would simply be “by( agecats, total)”). By including this “total” suboption, we get an additional subgraph showing the total % of non-smokers vs. smokers in the sample:



![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*HSrwHeeietePmsEj-NZ3Iw.png)

Using the “by(agecats)” option with the “total” suboption. Note that, coincidentally, the “total” percentages are equal to the percentages in the 27–45 age group.

The upside here is that adding the “total” suboption is extremely simple. It makes the graph asymmetrical in this case, but if you can live with it, this is a very fast and easy way to feature a comparison for the sample as a whole.

But what if we wanted to use `catplot` instead of the “by( )” option?

Fortunately, there is a way, and it involves a very clever trick. It is more complicated than using the “by( , total)” approach, but still isn’t too bad.

Here’s how it works:

1. **First preserve your data** using the -preserve- command. I recommend executing this **manually** in the command window (Stata doesn’t seem to like when I run it out of a .do file). (For those unfamiliar, this command basically lets you mess with the structure of your data set for a while, and then later quickly “restore” the data to how they were before you executed -preserve-.)
2. **Next, expand the data set** using the -expand- command, followed by “2”. This will literally just double the entire data set.
3. **Create a new category** for the variable that has values arrayed along the x-axis.
4. **Add a value label** for your new category
5. **Make the graph** using your revised categorical variable
6. **Execute -restore-** via the Command window (not from the .do file) to return to the dataset as it was before you preserved it (*don’t forget to do this!*)

Here is how we could use this technique with our data set:

```Stata 
*-*First preserve the data

preserve // execute this manually in the Command window

*-Expand the data set and add a new value to agecats

expand 2, gen(new)
replace agecats=5 if new // makes agecats equal 5 if "new" is equal to 1
label def agecats 5 "Total", modify
```

In short, what’s happening is that we literally doubled the data set, and then used the new half of the data to create the “Total” category.

All credit for this ingenious solution to both [Andrew Musau](https://www.statalist.org/forums/member/4687-andrew-musau) (who generously provided me with example code on [Statalist](https://www.statalist.org/forums/)) and Nicholas J. Cox (who provided a link to a [Stata Journal article](https://journals.sagepub.com/doi/pdf/10.1177/1536867X1401400117) he’s published on this technique).

We can then run the following code:

```Stata 
*-catplot with "Total" category

catplot smoke agecats, ///
percent(agecats) ///
recast(bar) ///
asyvars ///
legend(pos(3)) ///
bargap(10) ///
blabel(bar, color(white) format(%4.0f) size(small) pos(inside)) /// made format whole numbers
ylab(, glcolor(gs15) glstyle(solid)) ///
b1title(Age Group) ///
bar(1, fcolor(gs4%85) fintensity(inten100) lcolor(black) lwidth(vthin)) ///
bar(2, fcolor(red%90) fintensity(inten100) lcolor(yellow) lwidth(vthin)) ///
ytitle(Percent of Sample) ///
graphregion(margin(vsmall)) xsize(6.5) ysize(4.5) ///
title("Example of How to Create a 'Total' Category", box color(white) span fcolor(black) lcolor(black) bexpand) /// title
subtitle("Smoking Status & Age Group", box color(white) span fcolor(black) lcolor(black) bexpand) ///  subtitle
legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) ///
legend(ring(0) pos(11)) ///
scheme(white_jet)
```

Which produces the following graph:



![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*0GGJ8f-J7k-oM8a-8sUwhw.png)

Voila! We now have a “Total” category for “Age Group”, allowing us to see the % of non-smokers and % smokers not only within each age group, but also for the sample as a whole.

Just remember to use the Command window and **execute -restore- once you are done** so that you can go back to the original data set!

Finally, here’s another example of how we can use this trick along with the `catplot` package. In this case, we’ll use a recoded version of the doctor visits variable (“ftv”), feature a “Total” category for “smoke” (rather than for “agecats”), and make some other aesthetic changes:

```Stata 
*-Create a new variable (number of doctor visits in first trimester)
fre ftv 
recode ftv (3 4 6=3), gen(ftvcats)
label define ftvcats 0 "Zero" 1 "One" 2 "Two" 3 "Three or More", replace
label values ftvcats ftvcats
fre ftvcats

*-Next preserve the data
preserve // execute this manually in the Command window

*-Now expand the dataset and add another category to "smoke"
expand 2, gen(new)
replace smoke=3 if new
label def smoke 3 "Total", modify

*-Make the catplot
catplot smoke ftvcats, ///
percent(smoke) /// makes percentages within each value of smoke category
recast(hbar) /// makes graph horizontal
asyvars /// colors minor categories
var2opts(label(angle(vertical))) /// controls angle of vertical axis values
blabel(bar, color(white) format(%4.0f) size(medsmall) pos(inside)) /// format bar labels
ylab(0(10)60, glcolor(black) glstyle(solid) glwidth(*.2)) /// add light gray, solid horizontal lines
bar(1, fcolor(midblue%95) fintensity(inten100) lcolor(cyan) lwidth(vthin)) /// format look of bars
bar(2, fcolor(red%95) fintensity(inten100) lcolor(yellow) lwidth(vthin)) ///
bar(3, fcolor(black%95) fintensity(inten100) lcolor(lime) lwidth(vthin)) ///
scheme(black_jet) /// specify scheme
l1title(Number of Doctor Visits in 1st Trimester, margin(medium)) /// title the vertical axis (b1title and ytitle will title the horizontal axis in this case)
ytitle(Percent of Sample) /// title y-axis
graphregion(fcolor(black) margin(vsmall)) /// small margin between plot and outer edge of graph
title("Another Example of Using a 'Total' Category", box color(white) span fcolor(black) lcolor(black) bexpand) /// title
subtitle("Smoking Status & Doctor Visits in First Trimester", box color(white) span fcolor(black) lcolor(black) bexpand) ///  subtitle
legend(title("Smoking Status", size(small) margin(tiny) box fcolor(gs6) color(white) bexpand)) ///
legend(ring(1) pos(3) col(1)) ///
xsize(6.5) ysize(4.5) /// specify graph dimensions in inches
plotregion(fcolor(gs5)) // color the region inside the plot

*-Once satisfied with the graph, restore the data
restore // execute this manually in the Command window
```

Last graph! 



![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/1*l7xzgId5iUlX6Ia9V-hSMQ.png)

A crucial difference here is that we specified “percent(smoke)”, which means we want to treat each category of smoke — not each category of doctor visits — as the denominator. In short, we want to think about nonsmokers as one sample, smokers as another sample, and then both combined as a third (“Total”) sample. Each percentage, therefore, is out of one particular category of “smoke”; thus, each particular bar *color* totals up to 100%.

As this is a bit different from the previous graph, we just need to be careful about what this graph says (it is *very* easy to misinterpret bar graphs like these!).

Knowing all this, we can say, for example, that 48% of nonsmokers had zero doctor visits in their first trimester, but that this percentage is even higher for smokers (61%). But what about the *sample as a whole*? This graph tells us that 53% of the sample as a whole had zero doctor visits in their first trimester. Only 6% of the sample as a whole had three or more doctor visits.

That’s it! I hope this guide has been helpful for you. Good luck and enjoy!