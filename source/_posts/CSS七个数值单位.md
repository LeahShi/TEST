---
title: CSS七个数值单位
date: 2015-12-10 11:08:01
tags: [css,layout]
categories: work
---

CSS的度量单位其实有很多，常见的也是用的最多的是px，随着流式布局和自适应布局的流行，一些测量单位也在增加。比如今天介绍的rem、em、vh、vw、vmin、vmax、ex、ch刚开始出来时，很多浏览器不支持这种属性。时至今日，主流的浏览器也在支持这类度量单位。今天借以总结：

<!-- more -->

## rem & em
这两个度量单位我们都很熟悉了，基本思想都是以父元素为标准，增加或减少一定比例。前者是以根节点（root即html或者body）为标准，后者是以直系父节点为标准。

两者有很大的区别，如果body的字体大小为14px，结构为 `body>div>div>div` 三个嵌套的div元素中的字体大小均为1.2em，结果会逐步叠加，第三个div的大小为24.192px。如果为1.2rem，则三个div的字体大小均为16.8px。

用rem用做响应式布局中，要在公共样式文件中使用媒体查询来设置不同分辨率下body的字体大小，以达到效果。假如设计师提供的UI图为750*1334，我们可以以此为标准，设置为50px或者100px（主要为方便计算，以及转化后数值不至于太大或大小）。其他分辨率下，按比例设置。

```
/*media Query*/
@media only screen and (min-width: 320px) and (max-width: 359px) {
  html{font-size:22px;}
}
@media only screen and (min-width: 360px) and (max-width: 374px) {
  html{font-size:25px;}
}
@media only screen and (min-width: 375px) and (max-width: 479px) {
  html{font-size:26px;}
}
@media only screen and (min-width: 480px) and (max-width: 639px) {
  html{font-size:33px;}
}
@media only screen and (min-width: 640px) and (max-width: 719px) {
  html{font-size:44px;}
}
/*以此为标准*/
@media only screen and (min-width: 720px) and (max-width: 749px) {
    html{font-size:50px;}
}
@media only screen and (min-width: 750px) and (max-width: 769px) {
  html{font-size:52px;}
}
/*最大适配尺寸*/
@media only screen and (min-width: 770px) {
  html{font-size:55px;}
  html,body{width:770px;}
}
```

## vh & vw【推荐使用】
vh和vw这两个单位是实际用途中还是比较多的，之前虽然有接触过，但是因为很多浏览器不支持，也就没有过多的关注。但现在，很多浏览器已经支持了，IE9+、Firefox19以上、Chrome20以上...

此处安利一个网站，可以查询CSS属性各大浏览器的兼容情况。[戳这里](http://caniuse.com)

vh和vw定义：1%的视口高度、宽度。传统的响应式做法中宽度可以用百分比去表示，但是仅仅是继承直系父节点的百分比。而且高度时没办法继承的。vh和vw就很好的解决了这个问题。两者都是以视口即窗口的物理宽高为参照。
假设浏览器高度950px, 1 vh = 9.5 px。设置为100vh、100vw则为满屏的宽高。

## vmin & vmax
vmin和vmax是关于视口高度和宽度两者的最小或者最大值。例如，如果浏览器的宽高分别为1100px和700px，则1vmin=7px，1vmax=11px。具体应用场景很少。

## ex & ch
ex和ch是基于字体的度量单位，依赖于设定的字体。
单位ch通常被定义为数字0的宽度。单位ex定义为当前字体的小写x的高度或者1/2的em。很多时候，它是字体的中间标志。很少用到，主要用于板式微调，比如上标下标（sup,sub）精确位置。不推荐使用，px更精确。
