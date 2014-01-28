---
title       : Brief Introduction to R
subtitle    : Why you should learn this "language"?
author      : David Yang
job         : R Enthusiast
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
github      :
  user      : saluteyang
  repo      : slidifyDemo
---

## Agenda

1. Introduce some basic concepts of the statistical computing language
2. Introduce two packages that are powerful and popular
3. Point to further uses of R with a fun example

---

## This is easier than you think:

> 1. R is a high level language that skips over most programming niceties
> 2. R has a big support community and more packages than you have time to explore
> 3. Anything you want to achieve, someone probably has done it in R, and done it beautifully

---

## Let's start with `ggplot2`

> * Contributed by Hadley Wickham of Rice University
> * Some data to start us off
> * Historical price-load relationship during August based on hourly PJM data
![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-11.png) ![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-12.png) 

---

## What happened


```r
require(xlsx)
require(ggplot2)
priceload <- read.csv("C:\\Users\\Yangster\\Dropbox\\Private\\3AugHrlyLdPrice_PJM.csv")
p <- ggplot(priceload, aes(x = E_W_LD, y = Price)) + geom_point() + xlab("Load") + 
    ylab("Price")
p + facet_grid(Date ~ .)
p + facet_grid(Date ~ .) + geom_smooth()
```


With three simple lines of code, we
* Imported data from an Excel file
* Created elegant charts using `ggplot2`'s graphics language
  * using aesthetics (aes) to create mapping of data to graphic elements
  * using geometry (geom) to specify the way we want to present the data including sophisticated "faceting" and non-parametric regression

---

## Data visualization done right

Let's look at another example using historical forward data for Henry Hub natural gas for August 2013 delivery. Trade dates of each month are colored differently, a local regression line is produced as well as it's error bands.

<img src="figure/unnamed-chunk-3.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" style="display: block; margin: auto;" />


---

## Some programming asides


```r
require(xlsx)
require(lubridate)
require(ggplot2)
futNGDel = read.xlsx("C:\\Users\\Yangster\\Dropbox\\Private\\FUTURE_NG_Del.xlsx", 
    2, header = TRUE)
s2 <- ggplot(data = subset(futNGDel, subset = year(DelMo) == 2013 & month(DelMo) == 
    8 & TradeDate > as.Date("4/1/2013", "%m/%d/%Y")), aes(x = TradeDate, y = Price, 
    colour = as.factor(month(TradeDate))))
s2 + geom_point() + geom_smooth() + theme(legend.position = "none")
```


> * We do need to mention some R-related programming knowledge such as the following
    * How to subset data
    * How to use `month()` and `year()` functions in the lubridate package to extract information from dates
    * How to convert dates stored as `factors` to dates stored as `dates` and back

---

## A lot more cool stuff awaits

Once we are more familiar with R, we can do a lot by typing relatively little. What's more important is probably the dynamic and reproducible nature of it.

Any guesses what the code below will do?


```r
require(xlsx)
require(lubridate)
require(ggplot2)
# Importing and processing of data
price.Seg = read.csv("C:\\Users\\Yangster\\Dropbox\\Private\\3AugHrlyLdPrice_PJM_2.csv", 
    header = TRUE)
price.Seg$Date_Long <- as.Date(price.Seg$Date_Long, "%m/%d/%Y")
price.Seg <- price.Seg[, c("Date_Long", "Date", "Price", "SegType")]
aggreg.Seg <- aggregate(price.Seg, list(price.Seg$SegType, price.Seg$Date_Long), 
    mean)
aggreg.Seg <- aggreg.Seg[, c(1, 2, 4, 5)]
names(aggreg.Seg) <- c("peaktype", "date", "year", "price")
```


---

## (continued from above)


```r
aggreg.Seg <- aggreg.Seg[order(aggreg.Seg$peaktype, aggreg.Seg$date), ]
aggreg.Seg$peaktype <- factor(aggreg.Seg$peaktype, levels = c("OnPeak", "OffPeak", 
    "Wknd"))
# Setting up plotting
p_seg <- ggplot(aggreg.Seg, aes(x = date, y = price, colour = factor(year))) + 
    geom_point() + xlab("Date") + ylab("Price")
p_seg + facet_grid(peaktype ~ year, scale = "free_x") + theme(axis.text.x = element_text(angle = 90, 
    vjust = 0.5))
p_seg_hist <- ggplot(aggreg.Seg, aes(x = price, fill = factor(year), colour = factor(year), 
    ..density..)) + geom_density(alpha = 0.5, position = "identity")
p_seg_hist + facet_grid(peaktype ~ ., scale = "free_x")
```


---

## Side-by-side comparison of August power prices by segment and year

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-71.png) ![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-72.png) 


---

## Now let's look at `forecast`

> * Contributed by Rob Hyndman of Monash University, Australia
> * An excellent source of information on time series modeling using R is here: https://www.otexts.org/fpp/resources
> * The package `forecast` enables complex methods to be invoked with just the right function call
> * We'll see an example using a double-seasonality Holt-Winters procedure to forecast hourly power prices using the same hourly PJM data
    * The double seasonality refers to both the daily cycle as well as the weekly (weekday/weekend) cycle
> * The example uses a training set of 600 hourly data; the forecast (blue) is compared to the actual out-of-sample data of hour number 601 to 744 (plotted in black)

---

## Automated time-series forecast

<iframe src='
figure/unnamed-chunk-8.html
' scrolling='no' seamless class='rChart 
morris
 '
id=iframe-
chart26b4724d7947
></iframe>
<style>iframe.rChart{ width: 100%; height: 400px;}</style>


---

## Automated time-series forecast

<iframe src='
figure/unnamed-chunk-9.html
' scrolling='no' seamless class='rChart 
morris
 '
id=iframe-
chart2cdc4c833f65
></iframe>
<style>iframe.rChart{ width: 100%; height: 400px;}</style>


---

## Let's have some fun, finally

Here is a neat way of visualizing 10 paths of random walk. This is realized in R using the googleVis package.



<!-- MotionChart generated in R 3.0.2 by googleVis 0.4.7 package -->
<!-- Tue Jan 28 12:25:26 2014 -->


<!-- jsHeader -->
<script type="text/javascript">
 
// jsData 
function gvisDataMotionChartID26b431cd42f8 () {
var data = new google.visualization.DataTable();
var datajson =
[
 [
 "Path_1",
new Date(2011,0,1),
1,
10 
],
[
 "Path_1",
new Date(2011,0,2),
2,
9.993110482 
],
[
 "Path_1",
new Date(2011,0,3),
3,
10.00879403 
],
[
 "Path_1",
new Date(2011,0,4),
4,
9.995487007 
],
[
 "Path_1",
new Date(2011,0,5),
5,
10.0010814 
],
[
 "Path_1",
new Date(2011,0,6),
6,
9.992493975 
],
[
 "Path_1",
new Date(2011,0,7),
7,
9.995898229 
],
[
 "Path_1",
new Date(2011,0,8),
8,
10.00090033 
],
[
 "Path_1",
new Date(2011,0,9),
9,
9.994889223 
],
[
 "Path_1",
new Date(2011,0,10),
10,
9.995162135 
],
[
 "Path_1",
new Date(2011,0,11),
11,
10.00792908 
],
[
 "Path_1",
new Date(2011,0,12),
12,
9.999143064 
],
[
 "Path_1",
new Date(2011,0,13),
13,
9.992065437 
],
[
 "Path_1",
new Date(2011,0,14),
14,
9.993753562 
],
[
 "Path_1",
new Date(2011,0,15),
15,
10.01222585 
],
[
 "Path_1",
new Date(2011,0,16),
16,
10.02130279 
],
[
 "Path_1",
new Date(2011,0,17),
17,
10.03146699 
],
[
 "Path_1",
new Date(2011,0,18),
18,
10.04498285 
],
[
 "Path_1",
new Date(2011,0,19),
19,
10.04245848 
],
[
 "Path_1",
new Date(2011,0,20),
20,
10.02920835 
],
[
 "Path_1",
new Date(2011,0,21),
21,
10.03212089 
],
[
 "Path_1",
new Date(2011,0,22),
22,
10.06013389 
],
[
 "Path_1",
new Date(2011,0,23),
23,
10.06184223 
],
[
 "Path_1",
new Date(2011,0,24),
24,
10.05478198 
],
[
 "Path_1",
new Date(2011,0,25),
25,
10.07971471 
],
[
 "Path_1",
new Date(2011,0,26),
26,
10.06993934 
],
[
 "Path_1",
new Date(2011,0,27),
27,
10.0653883 
],
[
 "Path_1",
new Date(2011,0,28),
28,
10.05899663 
],
[
 "Path_1",
new Date(2011,0,29),
29,
10.06988529 
],
[
 "Path_1",
new Date(2011,0,30),
30,
10.04899322 
],
[
 "Path_1",
new Date(2011,0,31),
31,
10.06397223 
],
[
 "Path_1",
new Date(2011,1,1),
32,
10.05512506 
],
[
 "Path_1",
new Date(2011,1,2),
33,
10.07178378 
],
[
 "Path_1",
new Date(2011,1,3),
34,
10.07498706 
],
[
 "Path_1",
new Date(2011,1,4),
35,
10.08805827 
],
[
 "Path_1",
new Date(2011,1,5),
36,
10.09615369 
],
[
 "Path_1",
new Date(2011,1,6),
37,
10.10919181 
],
[
 "Path_1",
new Date(2011,1,7),
38,
10.10679496 
],
[
 "Path_1",
new Date(2011,1,8),
39,
10.13756499 
],
[
 "Path_1",
new Date(2011,1,9),
40,
10.13820203 
],
[
 "Path_1",
new Date(2011,1,10),
41,
10.13021748 
],
[
 "Path_1",
new Date(2011,1,11),
42,
10.12948706 
],
[
 "Path_1",
new Date(2011,1,12),
43,
10.13591332 
],
[
 "Path_1",
new Date(2011,1,13),
44,
10.15862245 
],
[
 "Path_1",
new Date(2011,1,14),
45,
10.15641258 
],
[
 "Path_1",
new Date(2011,1,15),
46,
10.14091447 
],
[
 "Path_1",
new Date(2011,1,16),
47,
10.15920779 
],
[
 "Path_1",
new Date(2011,1,17),
48,
10.17043737 
],
[
 "Path_1",
new Date(2011,1,18),
49,
10.19562466 
],
[
 "Path_1",
new Date(2011,1,19),
50,
10.19608335 
],
[
 "Path_2",
new Date(2011,0,1),
1,
10 
],
[
 "Path_2",
new Date(2011,0,2),
2,
9.997288459 
],
[
 "Path_2",
new Date(2011,0,3),
3,
10.00203978 
],
[
 "Path_2",
new Date(2011,0,4),
4,
9.999482596 
],
[
 "Path_2",
new Date(2011,0,5),
5,
9.995950204 
],
[
 "Path_2",
new Date(2011,0,6),
6,
9.993520235 
],
[
 "Path_2",
new Date(2011,0,7),
7,
9.993359144 
],
[
 "Path_2",
new Date(2011,0,8),
8,
9.987205199 
],
[
 "Path_2",
new Date(2011,0,9),
9,
9.958196266 
],
[
 "Path_2",
new Date(2011,0,10),
10,
9.963269721 
],
[
 "Path_2",
new Date(2011,0,11),
11,
9.970406455 
],
[
 "Path_2",
new Date(2011,0,12),
12,
9.973855805 
],
[
 "Path_2",
new Date(2011,0,13),
13,
9.981746013 
],
[
 "Path_2",
new Date(2011,0,14),
14,
9.969961302 
],
[
 "Path_2",
new Date(2011,0,15),
15,
9.975872427 
],
[
 "Path_2",
new Date(2011,0,16),
16,
9.972755862 
],
[
 "Path_2",
new Date(2011,0,17),
17,
9.982683562 
],
[
 "Path_2",
new Date(2011,0,18),
18,
9.969612406 
],
[
 "Path_2",
new Date(2011,0,19),
19,
9.970638305 
],
[
 "Path_2",
new Date(2011,0,20),
20,
9.986816474 
],
[
 "Path_2",
new Date(2011,0,21),
21,
9.982909486 
],
[
 "Path_2",
new Date(2011,0,22),
22,
9.999710343 
],
[
 "Path_2",
new Date(2011,0,23),
23,
10.00299277 
],
[
 "Path_2",
new Date(2011,0,24),
24,
10.01100974 
],
[
 "Path_2",
new Date(2011,0,25),
25,
10.01006728 
],
[
 "Path_2",
new Date(2011,0,26),
26,
10.00391491 
],
[
 "Path_2",
new Date(2011,0,27),
27,
9.997007677 
],
[
 "Path_2",
new Date(2011,0,28),
28,
9.991011251 
],
[
 "Path_2",
new Date(2011,0,29),
29,
10.00338637 
],
[
 "Path_2",
new Date(2011,0,30),
30,
10.01290239 
],
[
 "Path_2",
new Date(2011,0,31),
31,
10.02643685 
],
[
 "Path_2",
new Date(2011,1,1),
32,
10.017116 
],
[
 "Path_2",
new Date(2011,1,2),
33,
10.02688853 
],
[
 "Path_2",
new Date(2011,1,3),
34,
10.04249652 
],
[
 "Path_2",
new Date(2011,1,4),
35,
10.02766388 
],
[
 "Path_2",
new Date(2011,1,5),
36,
10.03999445 
],
[
 "Path_2",
new Date(2011,1,6),
37,
10.01498795 
],
[
 "Path_2",
new Date(2011,1,7),
38,
10.02342497 
],
[
 "Path_2",
new Date(2011,1,8),
39,
10.00274067 
],
[
 "Path_2",
new Date(2011,1,9),
40,
10.00534691 
],
[
 "Path_2",
new Date(2011,1,10),
41,
9.978254606 
],
[
 "Path_2",
new Date(2011,1,11),
42,
9.963672205 
],
[
 "Path_2",
new Date(2011,1,12),
43,
9.96726008 
],
[
 "Path_2",
new Date(2011,1,13),
44,
9.971083094 
],
[
 "Path_2",
new Date(2011,1,14),
45,
9.97606982 
],
[
 "Path_2",
new Date(2011,1,15),
46,
9.982022246 
],
[
 "Path_2",
new Date(2011,1,16),
47,
9.99546117 
],
[
 "Path_2",
new Date(2011,1,17),
48,
9.991253304 
],
[
 "Path_2",
new Date(2011,1,18),
49,
9.987892806 
],
[
 "Path_2",
new Date(2011,1,19),
50,
9.992065356 
],
[
 "Path_3",
new Date(2011,0,1),
1,
10 
],
[
 "Path_3",
new Date(2011,0,2),
2,
10.01991627 
],
[
 "Path_3",
new Date(2011,0,3),
3,
10.02518568 
],
[
 "Path_3",
new Date(2011,0,4),
4,
10.01240763 
],
[
 "Path_3",
new Date(2011,0,5),
5,
10.02393018 
],
[
 "Path_3",
new Date(2011,0,6),
6,
10.00812404 
],
[
 "Path_3",
new Date(2011,0,7),
7,
10.00778176 
],
[
 "Path_3",
new Date(2011,0,8),
8,
10.00376699 
],
[
 "Path_3",
new Date(2011,0,9),
9,
10.01668869 
],
[
 "Path_3",
new Date(2011,0,10),
10,
10.01220017 
],
[
 "Path_3",
new Date(2011,0,11),
11,
10.01541991 
],
[
 "Path_3",
new Date(2011,0,12),
12,
10.01249948 
],
[
 "Path_3",
new Date(2011,0,13),
13,
9.992234699 
],
[
 "Path_3",
new Date(2011,0,14),
14,
9.986229584 
],
[
 "Path_3",
new Date(2011,0,15),
15,
9.986951144 
],
[
 "Path_3",
new Date(2011,0,16),
16,
9.967264918 
],
[
 "Path_3",
new Date(2011,0,17),
17,
9.971666986 
],
[
 "Path_3",
new Date(2011,0,18),
18,
9.955927142 
],
[
 "Path_3",
new Date(2011,0,19),
19,
9.955696222 
],
[
 "Path_3",
new Date(2011,0,20),
20,
9.951473456 
],
[
 "Path_3",
new Date(2011,0,21),
21,
9.952869853 
],
[
 "Path_3",
new Date(2011,0,22),
22,
9.949716004 
],
[
 "Path_3",
new Date(2011,0,23),
23,
9.965505728 
],
[
 "Path_3",
new Date(2011,0,24),
24,
9.97974432 
],
[
 "Path_3",
new Date(2011,0,25),
25,
9.982658045 
],
[
 "Path_3",
new Date(2011,0,26),
26,
10.00178188 
],
[
 "Path_3",
new Date(2011,0,27),
27,
9.997631635 
],
[
 "Path_3",
new Date(2011,0,28),
28,
9.998428096 
],
[
 "Path_3",
new Date(2011,0,29),
29,
10.007284 
],
[
 "Path_3",
new Date(2011,0,30),
30,
10.01236689 
],
[
 "Path_3",
new Date(2011,0,31),
31,
10.02705345 
],
[
 "Path_3",
new Date(2011,1,1),
32,
10.01538177 
],
[
 "Path_3",
new Date(2011,1,2),
33,
9.993740171 
],
[
 "Path_3",
new Date(2011,1,3),
34,
9.977005867 
],
[
 "Path_3",
new Date(2011,1,4),
35,
9.968078212 
],
[
 "Path_3",
new Date(2011,1,5),
36,
9.98941557 
],
[
 "Path_3",
new Date(2011,1,6),
37,
9.984210867 
],
[
 "Path_3",
new Date(2011,1,7),
38,
9.987873769 
],
[
 "Path_3",
new Date(2011,1,8),
39,
9.982204743 
],
[
 "Path_3",
new Date(2011,1,9),
40,
9.984614877 
],
[
 "Path_3",
new Date(2011,1,10),
41,
9.995999863 
],
[
 "Path_3",
new Date(2011,1,11),
42,
9.988170862 
],
[
 "Path_3",
new Date(2011,1,12),
43,
9.996633991 
],
[
 "Path_3",
new Date(2011,1,13),
44,
9.998431774 
],
[
 "Path_3",
new Date(2011,1,14),
45,
10.002427 
],
[
 "Path_3",
new Date(2011,1,15),
46,
10.01097418 
],
[
 "Path_3",
new Date(2011,1,16),
47,
10.01668019 
],
[
 "Path_3",
new Date(2011,1,17),
48,
10.02343881 
],
[
 "Path_3",
new Date(2011,1,18),
49,
10.00690465 
],
[
 "Path_3",
new Date(2011,1,19),
50,
10.02390539 
],
[
 "Path_4",
new Date(2011,0,1),
1,
10 
],
[
 "Path_4",
new Date(2011,0,2),
2,
10.00109187 
],
[
 "Path_4",
new Date(2011,0,3),
3,
10.00269191 
],
[
 "Path_4",
new Date(2011,0,4),
4,
9.993672081 
],
[
 "Path_4",
new Date(2011,0,5),
5,
10.00497969 
],
[
 "Path_4",
new Date(2011,0,6),
6,
10.03261505 
],
[
 "Path_4",
new Date(2011,0,7),
7,
10.05012665 
],
[
 "Path_4",
new Date(2011,0,8),
8,
10.03744258 
],
[
 "Path_4",
new Date(2011,0,9),
9,
10.02867182 
],
[
 "Path_4",
new Date(2011,0,10),
10,
10.03702261 
],
[
 "Path_4",
new Date(2011,0,11),
11,
10.02928016 
],
[
 "Path_4",
new Date(2011,0,12),
12,
10.02508405 
],
[
 "Path_4",
new Date(2011,0,13),
13,
10.02458124 
],
[
 "Path_4",
new Date(2011,0,14),
14,
10.02154191 
],
[
 "Path_4",
new Date(2011,0,15),
15,
10.0163977 
],
[
 "Path_4",
new Date(2011,0,16),
16,
9.997438498 
],
[
 "Path_4",
new Date(2011,0,17),
17,
9.984883431 
],
[
 "Path_4",
new Date(2011,0,18),
18,
10.0260797 
],
[
 "Path_4",
new Date(2011,0,19),
19,
10.05320273 
],
[
 "Path_4",
new Date(2011,0,20),
20,
10.04245476 
],
[
 "Path_4",
new Date(2011,0,21),
21,
10.03132921 
],
[
 "Path_4",
new Date(2011,0,22),
22,
10.03840013 
],
[
 "Path_4",
new Date(2011,0,23),
23,
10.03207238 
],
[
 "Path_4",
new Date(2011,0,24),
24,
10.04122275 
],
[
 "Path_4",
new Date(2011,0,25),
25,
10.03208103 
],
[
 "Path_4",
new Date(2011,0,26),
26,
10.01789515 
],
[
 "Path_4",
new Date(2011,0,27),
27,
10.01923985 
],
[
 "Path_4",
new Date(2011,0,28),
28,
10.03588621 
],
[
 "Path_4",
new Date(2011,0,29),
29,
10.01843723 
],
[
 "Path_4",
new Date(2011,0,30),
30,
10.01527697 
],
[
 "Path_4",
new Date(2011,0,31),
31,
10.00817251 
],
[
 "Path_4",
new Date(2011,1,1),
32,
9.995059157 
],
[
 "Path_4",
new Date(2011,1,2),
33,
9.987650632 
],
[
 "Path_4",
new Date(2011,1,3),
34,
9.996209421 
],
[
 "Path_4",
new Date(2011,1,4),
35,
10.01562617 
],
[
 "Path_4",
new Date(2011,1,5),
36,
10.01653474 
],
[
 "Path_4",
new Date(2011,1,6),
37,
10.01821009 
],
[
 "Path_4",
new Date(2011,1,7),
38,
10.0313713 
],
[
 "Path_4",
new Date(2011,1,8),
39,
10.04201162 
],
[
 "Path_4",
new Date(2011,1,9),
40,
10.02886659 
],
[
 "Path_4",
new Date(2011,1,10),
41,
10.01857444 
],
[
 "Path_4",
new Date(2011,1,11),
42,
10.01840962 
],
[
 "Path_4",
new Date(2011,1,12),
43,
10.01705746 
],
[
 "Path_4",
new Date(2011,1,13),
44,
10.03335051 
],
[
 "Path_4",
new Date(2011,1,14),
45,
10.0208301 
],
[
 "Path_4",
new Date(2011,1,15),
46,
10.01850167 
],
[
 "Path_4",
new Date(2011,1,16),
47,
10.02774804 
],
[
 "Path_4",
new Date(2011,1,17),
48,
10.02300623 
],
[
 "Path_4",
new Date(2011,1,18),
49,
10.02017864 
],
[
 "Path_4",
new Date(2011,1,19),
50,
10.02191321 
],
[
 "Path_5",
new Date(2011,0,1),
1,
10 
],
[
 "Path_5",
new Date(2011,0,2),
2,
10.00183537 
],
[
 "Path_5",
new Date(2011,0,3),
3,
9.995004479 
],
[
 "Path_5",
new Date(2011,0,4),
4,
9.987298288 
],
[
 "Path_5",
new Date(2011,0,5),
5,
9.997890558 
],
[
 "Path_5",
new Date(2011,0,6),
6,
10.0133702 
],
[
 "Path_5",
new Date(2011,0,7),
7,
10.0107144 
],
[
 "Path_5",
new Date(2011,0,8),
8,
9.997357195 
],
[
 "Path_5",
new Date(2011,0,9),
9,
9.988854498 
],
[
 "Path_5",
new Date(2011,0,10),
10,
9.986265539 
],
[
 "Path_5",
new Date(2011,0,11),
11,
10.00367658 
],
[
 "Path_5",
new Date(2011,0,12),
12,
9.991839454 
],
[
 "Path_5",
new Date(2011,0,13),
13,
9.998609493 
],
[
 "Path_5",
new Date(2011,0,14),
14,
10.02213271 
],
[
 "Path_5",
new Date(2011,0,15),
15,
9.996360956 
],
[
 "Path_5",
new Date(2011,0,16),
16,
9.976302948 
],
[
 "Path_5",
new Date(2011,0,17),
17,
9.974991979 
],
[
 "Path_5",
new Date(2011,0,18),
18,
9.969919101 
],
[
 "Path_5",
new Date(2011,0,19),
19,
9.960741859 
],
[
 "Path_5",
new Date(2011,0,20),
20,
9.957953132 
],
[
 "Path_5",
new Date(2011,0,21),
21,
9.941572659 
],
[
 "Path_5",
new Date(2011,0,22),
22,
9.936531627 
],
[
 "Path_5",
new Date(2011,0,23),
23,
9.924177294 
],
[
 "Path_5",
new Date(2011,0,24),
24,
9.919777353 
],
[
 "Path_5",
new Date(2011,0,25),
25,
9.912711847 
],
[
 "Path_5",
new Date(2011,0,26),
26,
9.910647003 
],
[
 "Path_5",
new Date(2011,0,27),
27,
9.931066717 
],
[
 "Path_5",
new Date(2011,0,28),
28,
9.960272127 
],
[
 "Path_5",
new Date(2011,0,29),
29,
9.971219356 
],
[
 "Path_5",
new Date(2011,0,30),
30,
9.972913779 
],
[
 "Path_5",
new Date(2011,0,31),
31,
9.998443405 
],
[
 "Path_5",
new Date(2011,1,1),
32,
9.993113725 
],
[
 "Path_5",
new Date(2011,1,2),
33,
9.988860651 
],
[
 "Path_5",
new Date(2011,1,3),
34,
9.982446274 
],
[
 "Path_5",
new Date(2011,1,4),
35,
9.987419896 
],
[
 "Path_5",
new Date(2011,1,5),
36,
9.986962372 
],
[
 "Path_5",
new Date(2011,1,6),
37,
9.97586409 
],
[
 "Path_5",
new Date(2011,1,7),
38,
9.986406755 
],
[
 "Path_5",
new Date(2011,1,8),
39,
9.993059478 
],
[
 "Path_5",
new Date(2011,1,9),
40,
9.99928214 
],
[
 "Path_5",
new Date(2011,1,10),
41,
9.992227113 
],
[
 "Path_5",
new Date(2011,1,11),
42,
10.00091082 
],
[
 "Path_5",
new Date(2011,1,12),
43,
9.995878181 
],
[
 "Path_5",
new Date(2011,1,13),
44,
9.986990222 
],
[
 "Path_5",
new Date(2011,1,14),
45,
9.987433835 
],
[
 "Path_5",
new Date(2011,1,15),
46,
9.979473717 
],
[
 "Path_5",
new Date(2011,1,16),
47,
9.991275164 
],
[
 "Path_5",
new Date(2011,1,17),
48,
9.977652378 
],
[
 "Path_5",
new Date(2011,1,18),
49,
9.975144946 
],
[
 "Path_5",
new Date(2011,1,19),
50,
9.984361768 
],
[
 "Path_6",
new Date(2011,0,1),
1,
10 
],
[
 "Path_6",
new Date(2011,0,2),
2,
10.02189405 
],
[
 "Path_6",
new Date(2011,0,3),
3,
10.04469691 
],
[
 "Path_6",
new Date(2011,0,4),
4,
10.02356174 
],
[
 "Path_6",
new Date(2011,0,5),
5,
10.03247243 
],
[
 "Path_6",
new Date(2011,0,6),
6,
10.0184661 
],
[
 "Path_6",
new Date(2011,0,7),
7,
10.0378481 
],
[
 "Path_6",
new Date(2011,0,8),
8,
10.04188747 
],
[
 "Path_6",
new Date(2011,0,9),
9,
10.05506004 
],
[
 "Path_6",
new Date(2011,0,10),
10,
10.05945678 
],
[
 "Path_6",
new Date(2011,0,11),
11,
10.05206403 
],
[
 "Path_6",
new Date(2011,0,12),
12,
10.05169447 
],
[
 "Path_6",
new Date(2011,0,13),
13,
10.0557038 
],
[
 "Path_6",
new Date(2011,0,14),
14,
10.04765721 
],
[
 "Path_6",
new Date(2011,0,15),
15,
10.06216762 
],
[
 "Path_6",
new Date(2011,0,16),
16,
10.05565212 
],
[
 "Path_6",
new Date(2011,0,17),
17,
10.05230537 
],
[
 "Path_6",
new Date(2011,0,18),
18,
10.05627769 
],
[
 "Path_6",
new Date(2011,0,19),
19,
10.04261431 
],
[
 "Path_6",
new Date(2011,0,20),
20,
10.04032021 
],
[
 "Path_6",
new Date(2011,0,21),
21,
10.06578318 
],
[
 "Path_6",
new Date(2011,0,22),
22,
10.05995908 
],
[
 "Path_6",
new Date(2011,0,23),
23,
10.08135516 
],
[
 "Path_6",
new Date(2011,0,24),
24,
10.08231094 
],
[
 "Path_6",
new Date(2011,0,25),
25,
10.06585186 
],
[
 "Path_6",
new Date(2011,0,26),
26,
10.09011504 
],
[
 "Path_6",
new Date(2011,0,27),
27,
10.08919477 
],
[
 "Path_6",
new Date(2011,0,28),
28,
10.1089703 
],
[
 "Path_6",
new Date(2011,0,29),
29,
10.10352175 
],
[
 "Path_6",
new Date(2011,0,30),
30,
10.10541722 
],
[
 "Path_6",
new Date(2011,0,31),
31,
10.10646092 
],
[
 "Path_6",
new Date(2011,1,1),
32,
10.11085004 
],
[
 "Path_6",
new Date(2011,1,2),
33,
10.119949 
],
[
 "Path_6",
new Date(2011,1,3),
34,
10.12879777 
],
[
 "Path_6",
new Date(2011,1,4),
35,
10.10303898 
],
[
 "Path_6",
new Date(2011,1,5),
36,
10.08106208 
],
[
 "Path_6",
new Date(2011,1,6),
37,
10.08548566 
],
[
 "Path_6",
new Date(2011,1,7),
38,
10.08303196 
],
[
 "Path_6",
new Date(2011,1,8),
39,
10.07577555 
],
[
 "Path_6",
new Date(2011,1,9),
40,
10.09341324 
],
[
 "Path_6",
new Date(2011,1,10),
41,
10.11263624 
],
[
 "Path_6",
new Date(2011,1,11),
42,
10.0919583 
],
[
 "Path_6",
new Date(2011,1,12),
43,
10.05872481 
],
[
 "Path_6",
new Date(2011,1,13),
44,
10.05322843 
],
[
 "Path_6",
new Date(2011,1,14),
45,
10.03980002 
],
[
 "Path_6",
new Date(2011,1,15),
46,
10.04209118 
],
[
 "Path_6",
new Date(2011,1,16),
47,
10.00863287 
],
[
 "Path_6",
new Date(2011,1,17),
48,
10.02413843 
],
[
 "Path_6",
new Date(2011,1,18),
49,
10.02625705 
],
[
 "Path_6",
new Date(2011,1,19),
50,
10.03630894 
],
[
 "Path_7",
new Date(2011,0,1),
1,
10 
],
[
 "Path_7",
new Date(2011,0,2),
2,
10.00603018 
],
[
 "Path_7",
new Date(2011,0,3),
3,
10.01252755 
],
[
 "Path_7",
new Date(2011,0,4),
4,
10.02332481 
],
[
 "Path_7",
new Date(2011,0,5),
5,
10.03053137 
],
[
 "Path_7",
new Date(2011,0,6),
6,
10.02563524 
],
[
 "Path_7",
new Date(2011,0,7),
7,
10.00624489 
],
[
 "Path_7",
new Date(2011,0,8),
8,
10.01211435 
],
[
 "Path_7",
new Date(2011,0,9),
9,
10.00871222 
],
[
 "Path_7",
new Date(2011,0,10),
10,
10.02278626 
],
[
 "Path_7",
new Date(2011,0,11),
11,
10.05065408 
],
[
 "Path_7",
new Date(2011,0,12),
12,
10.04092573 
],
[
 "Path_7",
new Date(2011,0,13),
13,
10.04246244 
],
[
 "Path_7",
new Date(2011,0,14),
14,
10.04563987 
],
[
 "Path_7",
new Date(2011,0,15),
15,
10.02736408 
],
[
 "Path_7",
new Date(2011,0,16),
16,
10.00907417 
],
[
 "Path_7",
new Date(2011,0,17),
17,
10.01639549 
],
[
 "Path_7",
new Date(2011,0,18),
18,
10.02464753 
],
[
 "Path_7",
new Date(2011,0,19),
19,
10.02532551 
],
[
 "Path_7",
new Date(2011,0,20),
20,
10.03956502 
],
[
 "Path_7",
new Date(2011,0,21),
21,
10.04736345 
],
[
 "Path_7",
new Date(2011,0,22),
22,
10.03758833 
],
[
 "Path_7",
new Date(2011,0,23),
23,
10.03220801 
],
[
 "Path_7",
new Date(2011,0,24),
24,
10.02349549 
],
[
 "Path_7",
new Date(2011,0,25),
25,
10.02138164 
],
[
 "Path_7",
new Date(2011,0,26),
26,
10.0203044 
],
[
 "Path_7",
new Date(2011,0,27),
27,
10.03417555 
],
[
 "Path_7",
new Date(2011,0,28),
28,
10.03269131 
],
[
 "Path_7",
new Date(2011,0,29),
29,
10.03510241 
],
[
 "Path_7",
new Date(2011,0,30),
30,
10.03809811 
],
[
 "Path_7",
new Date(2011,0,31),
31,
10.06191213 
],
[
 "Path_7",
new Date(2011,1,1),
32,
10.03663415 
],
[
 "Path_7",
new Date(2011,1,2),
33,
10.03549751 
],
[
 "Path_7",
new Date(2011,1,3),
34,
10.03492816 
],
[
 "Path_7",
new Date(2011,1,4),
35,
10.0178743 
],
[
 "Path_7",
new Date(2011,1,5),
36,
10.01933071 
],
[
 "Path_7",
new Date(2011,1,6),
37,
10.02473493 
],
[
 "Path_7",
new Date(2011,1,7),
38,
10.02971842 
],
[
 "Path_7",
new Date(2011,1,8),
39,
10.01731003 
],
[
 "Path_7",
new Date(2011,1,9),
40,
10.02328101 
],
[
 "Path_7",
new Date(2011,1,10),
41,
10.01368877 
],
[
 "Path_7",
new Date(2011,1,11),
42,
10.00946469 
],
[
 "Path_7",
new Date(2011,1,12),
43,
10.00848906 
],
[
 "Path_7",
new Date(2011,1,13),
44,
10.03901471 
],
[
 "Path_7",
new Date(2011,1,14),
45,
10.04822977 
],
[
 "Path_7",
new Date(2011,1,15),
46,
10.05398044 
],
[
 "Path_7",
new Date(2011,1,16),
47,
10.06822445 
],
[
 "Path_7",
new Date(2011,1,17),
48,
10.07779618 
],
[
 "Path_7",
new Date(2011,1,18),
49,
10.09965531 
],
[
 "Path_7",
new Date(2011,1,19),
50,
10.11142638 
],
[
 "Path_8",
new Date(2011,0,1),
1,
10 
],
[
 "Path_8",
new Date(2011,0,2),
2,
9.9841981 
],
[
 "Path_8",
new Date(2011,0,3),
3,
9.959522142 
],
[
 "Path_8",
new Date(2011,0,4),
4,
9.961662176 
],
[
 "Path_8",
new Date(2011,0,5),
5,
9.961079048 
],
[
 "Path_8",
new Date(2011,0,6),
6,
9.955376273 
],
[
 "Path_8",
new Date(2011,0,7),
7,
9.962971117 
],
[
 "Path_8",
new Date(2011,0,8),
8,
9.963841573 
],
[
 "Path_8",
new Date(2011,0,9),
9,
9.94860058 
],
[
 "Path_8",
new Date(2011,0,10),
10,
9.954305238 
],
[
 "Path_8",
new Date(2011,0,11),
11,
9.9738914 
],
[
 "Path_8",
new Date(2011,0,12),
12,
9.952993418 
],
[
 "Path_8",
new Date(2011,0,13),
13,
9.945089057 
],
[
 "Path_8",
new Date(2011,0,14),
14,
9.946275192 
],
[
 "Path_8",
new Date(2011,0,15),
15,
9.95583487 
],
[
 "Path_8",
new Date(2011,0,16),
16,
9.964736405 
],
[
 "Path_8",
new Date(2011,0,17),
17,
9.960225386 
],
[
 "Path_8",
new Date(2011,0,18),
18,
9.954305992 
],
[
 "Path_8",
new Date(2011,0,19),
19,
9.958433297 
],
[
 "Path_8",
new Date(2011,0,20),
20,
9.959705148 
],
[
 "Path_8",
new Date(2011,0,21),
21,
9.944077678 
],
[
 "Path_8",
new Date(2011,0,22),
22,
9.936756299 
],
[
 "Path_8",
new Date(2011,0,23),
23,
9.927810157 
],
[
 "Path_8",
new Date(2011,0,24),
24,
9.918937986 
],
[
 "Path_8",
new Date(2011,0,25),
25,
9.92443774 
],
[
 "Path_8",
new Date(2011,0,26),
26,
9.907436965 
],
[
 "Path_8",
new Date(2011,0,27),
27,
9.915615443 
],
[
 "Path_8",
new Date(2011,0,28),
28,
9.893596934 
],
[
 "Path_8",
new Date(2011,0,29),
29,
9.89473994 
],
[
 "Path_8",
new Date(2011,0,30),
30,
9.915695185 
],
[
 "Path_8",
new Date(2011,0,31),
31,
9.898807467 
],
[
 "Path_8",
new Date(2011,1,1),
32,
9.901688831 
],
[
 "Path_8",
new Date(2011,1,2),
33,
9.885968045 
],
[
 "Path_8",
new Date(2011,1,3),
34,
9.894174436 
],
[
 "Path_8",
new Date(2011,1,4),
35,
9.891834734 
],
[
 "Path_8",
new Date(2011,1,5),
36,
9.88480134 
],
[
 "Path_8",
new Date(2011,1,6),
37,
9.884583462 
],
[
 "Path_8",
new Date(2011,1,7),
38,
9.872824881 
],
[
 "Path_8",
new Date(2011,1,8),
39,
9.87485237 
],
[
 "Path_8",
new Date(2011,1,9),
40,
9.860688186 
],
[
 "Path_8",
new Date(2011,1,10),
41,
9.871585937 
],
[
 "Path_8",
new Date(2011,1,11),
42,
9.881353806 
],
[
 "Path_8",
new Date(2011,1,12),
43,
9.886996525 
],
[
 "Path_8",
new Date(2011,1,13),
44,
9.887337299 
],
[
 "Path_8",
new Date(2011,1,14),
45,
9.901258739 
],
[
 "Path_8",
new Date(2011,1,15),
46,
9.912631734 
],
[
 "Path_8",
new Date(2011,1,16),
47,
9.906697073 
],
[
 "Path_8",
new Date(2011,1,17),
48,
9.928707457 
],
[
 "Path_8",
new Date(2011,1,18),
49,
9.924782026 
],
[
 "Path_8",
new Date(2011,1,19),
50,
9.917716446 
],
[
 "Path_9",
new Date(2011,0,1),
1,
10 
],
[
 "Path_9",
new Date(2011,0,2),
2,
9.991511922 
],
[
 "Path_9",
new Date(2011,0,3),
3,
10.00058345 
],
[
 "Path_9",
new Date(2011,0,4),
4,
9.986387031 
],
[
 "Path_9",
new Date(2011,0,5),
5,
9.982716875 
],
[
 "Path_9",
new Date(2011,0,6),
6,
9.99278274 
],
[
 "Path_9",
new Date(2011,0,7),
7,
9.994549386 
],
[
 "Path_9",
new Date(2011,0,8),
8,
10.00641525 
],
[
 "Path_9",
new Date(2011,0,9),
9,
10.00890858 
],
[
 "Path_9",
new Date(2011,0,10),
10,
10.00498583 
],
[
 "Path_9",
new Date(2011,0,11),
11,
10.00220443 
],
[
 "Path_9",
new Date(2011,0,12),
12,
9.997594905 
],
[
 "Path_9",
new Date(2011,0,13),
13,
9.987046901 
],
[
 "Path_9",
new Date(2011,0,14),
14,
9.97508027 
],
[
 "Path_9",
new Date(2011,0,15),
15,
9.999428732 
],
[
 "Path_9",
new Date(2011,0,16),
16,
10.02619324 
],
[
 "Path_9",
new Date(2011,0,17),
17,
10.03875109 
],
[
 "Path_9",
new Date(2011,0,18),
18,
10.04548893 
],
[
 "Path_9",
new Date(2011,0,19),
19,
10.05121057 
],
[
 "Path_9",
new Date(2011,0,20),
20,
10.06094868 
],
[
 "Path_9",
new Date(2011,0,21),
21,
10.05341797 
],
[
 "Path_9",
new Date(2011,0,22),
22,
10.07450048 
],
[
 "Path_9",
new Date(2011,0,23),
23,
10.05906273 
],
[
 "Path_9",
new Date(2011,0,24),
24,
10.07045277 
],
[
 "Path_9",
new Date(2011,0,25),
25,
10.07475493 
],
[
 "Path_9",
new Date(2011,0,26),
26,
10.06654619 
],
[
 "Path_9",
new Date(2011,0,27),
27,
10.06530874 
],
[
 "Path_9",
new Date(2011,0,28),
28,
10.06059102 
],
[
 "Path_9",
new Date(2011,0,29),
29,
10.06620695 
],
[
 "Path_9",
new Date(2011,0,30),
30,
10.06363616 
],
[
 "Path_9",
new Date(2011,0,31),
31,
10.06410158 
],
[
 "Path_9",
new Date(2011,1,1),
32,
10.07994442 
],
[
 "Path_9",
new Date(2011,1,2),
33,
10.10145104 
],
[
 "Path_9",
new Date(2011,1,3),
34,
10.11854415 
],
[
 "Path_9",
new Date(2011,1,4),
35,
10.12969549 
],
[
 "Path_9",
new Date(2011,1,5),
36,
10.11757513 
],
[
 "Path_9",
new Date(2011,1,6),
37,
10.08658371 
],
[
 "Path_9",
new Date(2011,1,7),
38,
10.09762303 
],
[
 "Path_9",
new Date(2011,1,8),
39,
10.09764205 
],
[
 "Path_9",
new Date(2011,1,9),
40,
10.09233152 
],
[
 "Path_9",
new Date(2011,1,10),
41,
10.07658501 
],
[
 "Path_9",
new Date(2011,1,11),
42,
10.06996955 
],
[
 "Path_9",
new Date(2011,1,12),
43,
10.07694187 
],
[
 "Path_9",
new Date(2011,1,13),
44,
10.09780506 
],
[
 "Path_9",
new Date(2011,1,14),
45,
10.06986094 
],
[
 "Path_9",
new Date(2011,1,15),
46,
10.0441007 
],
[
 "Path_9",
new Date(2011,1,16),
47,
10.0472178 
],
[
 "Path_9",
new Date(2011,1,17),
48,
10.04824194 
],
[
 "Path_9",
new Date(2011,1,18),
49,
10.05316012 
],
[
 "Path_9",
new Date(2011,1,19),
50,
10.07393872 
],
[
 "Path_10",
new Date(2011,0,1),
1,
10 
],
[
 "Path_10",
new Date(2011,0,2),
2,
9.994562772 
],
[
 "Path_10",
new Date(2011,0,3),
3,
9.988782382 
],
[
 "Path_10",
new Date(2011,0,4),
4,
10.00484203 
],
[
 "Path_10",
new Date(2011,0,5),
5,
10.00022941 
],
[
 "Path_10",
new Date(2011,0,6),
6,
9.999374861 
],
[
 "Path_10",
new Date(2011,0,7),
7,
10.00230633 
],
[
 "Path_10",
new Date(2011,0,8),
8,
10.02843808 
],
[
 "Path_10",
new Date(2011,0,9),
9,
10.02688123 
],
[
 "Path_10",
new Date(2011,0,10),
10,
10.04161262 
],
[
 "Path_10",
new Date(2011,0,11),
11,
10.02882931 
],
[
 "Path_10",
new Date(2011,0,12),
12,
10.0406538 
],
[
 "Path_10",
new Date(2011,0,13),
13,
10.02789948 
],
[
 "Path_10",
new Date(2011,0,14),
14,
10.0271975 
],
[
 "Path_10",
new Date(2011,0,15),
15,
10.00913353 
],
[
 "Path_10",
new Date(2011,0,16),
16,
9.993053741 
],
[
 "Path_10",
new Date(2011,0,17),
17,
9.988515626 
],
[
 "Path_10",
new Date(2011,0,18),
18,
9.993382699 
],
[
 "Path_10",
new Date(2011,0,19),
19,
9.987784786 
],
[
 "Path_10",
new Date(2011,0,20),
20,
9.981669186 
],
[
 "Path_10",
new Date(2011,0,21),
21,
9.966873917 
],
[
 "Path_10",
new Date(2011,0,22),
22,
9.966390509 
],
[
 "Path_10",
new Date(2011,0,23),
23,
9.950339998 
],
[
 "Path_10",
new Date(2011,0,24),
24,
9.937693655 
],
[
 "Path_10",
new Date(2011,0,25),
25,
9.928007913 
],
[
 "Path_10",
new Date(2011,0,26),
26,
9.93434855 
],
[
 "Path_10",
new Date(2011,0,27),
27,
9.915158703 
],
[
 "Path_10",
new Date(2011,0,28),
28,
9.916487095 
],
[
 "Path_10",
new Date(2011,0,29),
29,
9.916999212 
],
[
 "Path_10",
new Date(2011,0,30),
30,
9.91932509 
],
[
 "Path_10",
new Date(2011,0,31),
31,
9.935335397 
],
[
 "Path_10",
new Date(2011,1,1),
32,
9.961308897 
],
[
 "Path_10",
new Date(2011,1,2),
33,
9.973037186 
],
[
 "Path_10",
new Date(2011,1,3),
34,
9.973329399 
],
[
 "Path_10",
new Date(2011,1,4),
35,
9.972240666 
],
[
 "Path_10",
new Date(2011,1,5),
36,
9.970165013 
],
[
 "Path_10",
new Date(2011,1,6),
37,
10.00289167 
],
[
 "Path_10",
new Date(2011,1,7),
38,
9.997259946 
],
[
 "Path_10",
new Date(2011,1,8),
39,
9.974814481 
],
[
 "Path_10",
new Date(2011,1,9),
40,
9.979392384 
],
[
 "Path_10",
new Date(2011,1,10),
41,
9.975107738 
],
[
 "Path_10",
new Date(2011,1,11),
42,
9.978182778 
],
[
 "Path_10",
new Date(2011,1,12),
43,
9.971359001 
],
[
 "Path_10",
new Date(2011,1,13),
44,
9.953363171 
],
[
 "Path_10",
new Date(2011,1,14),
45,
9.969193595 
],
[
 "Path_10",
new Date(2011,1,15),
46,
9.948694852 
],
[
 "Path_10",
new Date(2011,1,16),
47,
9.945161369 
],
[
 "Path_10",
new Date(2011,1,17),
48,
9.959591653 
],
[
 "Path_10",
new Date(2011,1,18),
49,
9.956911652 
],
[
 "Path_10",
new Date(2011,1,19),
50,
9.952292888 
] 
];
data.addColumn('string','path');
data.addColumn('date','idx');
data.addColumn('number','time');
data.addColumn('number','price');
data.addRows(datajson);
return(data);
}
 
// jsDrawChart
function drawChartMotionChartID26b431cd42f8() {
var data = gvisDataMotionChartID26b431cd42f8();
var options = {};
options["width"] =    600;
options["height"] =    450;

    var chart = new google.visualization.MotionChart(
    document.getElementById('MotionChartID26b431cd42f8')
    );
    chart.draw(data,options);
    

}
  
 
// jsDisplayChart
(function() {
var pkgs = window.__gvisPackages = window.__gvisPackages || [];
var callbacks = window.__gvisCallbacks = window.__gvisCallbacks || [];
var chartid = "motionchart";
  
// Manually see if chartid is in pkgs (not all browsers support Array.indexOf)
var i, newPackage = true;
for (i = 0; newPackage && i < pkgs.length; i++) {
if (pkgs[i] === chartid)
newPackage = false;
}
if (newPackage)
  pkgs.push(chartid);
  
// Add the drawChart function to the global list of callbacks
callbacks.push(drawChartMotionChartID26b431cd42f8);
})();
function displayChartMotionChartID26b431cd42f8() {
  var pkgs = window.__gvisPackages = window.__gvisPackages || [];
  var callbacks = window.__gvisCallbacks = window.__gvisCallbacks || [];
  window.clearTimeout(window.__gvisLoad);
  // The timeout is set to 100 because otherwise the container div we are
  // targeting might not be part of the document yet
  window.__gvisLoad = setTimeout(function() {
  var pkgCount = pkgs.length;
  google.load("visualization", "1", { packages:pkgs, callback: function() {
  if (pkgCount != pkgs.length) {
  // Race condition where another setTimeout call snuck in after us; if
  // that call added a package, we must not shift its callback
  return;
}
while (callbacks.length > 0)
callbacks.shift()();
} });
}, 100);
}
 
// jsFooter
</script>
 
<!-- jsChart -->  
<script type="text/javascript" src="https://www.google.com/jsapi?callback=displayChartMotionChartID26b431cd42f8"></script>
 
<!-- divChart -->
  
<div id="MotionChartID26b431cd42f8"
  style="width: 600px; height: 450px;">
</div>

---
