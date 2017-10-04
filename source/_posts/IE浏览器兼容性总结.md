---
title: IE浏览器兼容性总结
date: 2016-02-01 17:17:09
tags: [Hack,CSS,IE]
categories: work
---

## 常见问题：
### 1、margin加倍的问题：【margin双边距问题】
当元素有**float**属性，又有**margin**属性时，在IE6下面显示的margin的值是设置值的两倍。这个是IE6比较著名的BUG。
解决方法： _display:inline。

<!-- more -->

### 2、IE6图片底侧会有像素间隙问题 ：
解决方法：
a. 将img标记与/div标记放在同一行
```
<div><img src="images/jd.gif" /></div>
```
但是这样写不太方便阅读，我们知道代码的可读性是最为重要的。
b. 在CSS样式中给img上设置
```
       img{display:block;}
```

### IE6下元素最小高度的问题：
IE6浏览器里面有个默认的高度（行高）；19px以下的高度均不可设置；
解决方法：
overflow:hidden；font-size:0;

### IE6显示多余字符问题：
在IE6下，当在浮动元素之间增加HTML注释时就会产生多余字符bug.
解决办法：
- 可以考虑去掉HTML注释。
- 不设置浮动div的宽度。(如果可以不用加宽度)
- 在产生多余字符的那个元素CSS样式加position:relative;

### IE6下文字混排浮动3px间距BUG：
在IE6中，当文本(或无浮动元素)跟在一个浮动的元素之后，文本和这个浮动元素之间会多出3px的间隔。
如果元素左浮动或右浮动，则后面的行内文本环绕该元素时浮动与环绕文本之间会增加3px外边距，其他浏览器正常。
解决办法：外边距可以是负值，所以给这个浮动的盒子加margin-right：-3像素的距离。

### IE6下li里底部3px间距BUG：
如果li里面内容过于复杂，那么li和li之间就出现白空隙。
解决方法：vertical-align: middle  bottom等

### IE6中**奇数**宽高的BUG ：
IE6还有奇数宽高的bug，
解决方案:就是将外部相对定位的div宽度改成偶数。

### 了解ie6盒子会撑高的特性 ：
内容有多大，盒子就撑多大【设置高度无用】
解决方法：父标签使用overflow:hidden

### 左浮元素margin-bottom失效 ：
解决方法：margin-bottom与float不同时作用于一个标签；

### 块元素中有文字及右浮动的行元素，行元素换行 ：
解决方法：将行元素置于块元素内的文字前

### position下的left，bottom错位
解决方法：为父级(relative层)设置宽高或添加*zoom:1

### IE6背景闪烁
如果你给链接、按钮用CSS sprites作为背景，你可能会发现在IE6下会有背景图闪烁的现象。造成这个的原因是由于IE6没有将背景图缓存，每次触发hover的时候都会重新加载，可以用JavaScript设置IE6缓存这些图片：
```
document.execCommand("BackgroundImageCache",false,true);
```
### IE6 不支持min-height属性，认为height就是最小高度
解决方法：使用ie6不支持但其余浏览器支持的属性!important。
```
#container {min-height:200px; height:auto !important; height:200px;}
```

## 针对IE各个版本解决方案——hacker前缀：
### ie6以下：
    语法：_selector:{属性：值；}
### ie7以下：
    语法：+selector:{属性：值；}
### ie7专属：
    语法：*+selector:{属性：值；}
### CSS后缀hack        针对的浏览器
```
color:red\9;        IE6/IE7/IE8/IE9/IE10版本
color:red\0;        IE8/IE9/IE10版本
color:red\9\0;        IE9/IE10
    /*mediaQuery*/
@media screen\9{body { background: red; }}    只对IE6/7生效
@media \0screen {body { background: red; }}    只对IE8生效
@media \0screen\,screen\9{body { background: blue; }}    只对IE6/7/8有效
@media screen\0 {body { background: green; }}    只对IE8/9/10有效
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {body { background: orange; }}    只对IE10有效
```
### 条件注释：【缺点是在IE浏览器下可能会增加额外的HTTP请求数。】
1.判断等于某个IE浏览器版本的语法：
```
语法：<!--[if IE 7]>只能被 IE7 识别;<![endif]-->
例如：   <!--[if ie 5]><link rel="stylesheet" type="text/css" href="css/c.css"><![endif]-->
```
2.判断IE浏览器的范围：
gte,gt,lte,lt
其中： gte:表示高于或等于某个IE浏览的版本 gt:表示高于某个IE浏览器的版本
                    lte:表示低于或等于某个IE浏览器的版本 lt:表示低于某个IE浏览器的版本
```
语法：<!--[if gte ie 版本号]>要判断的内容<![endif]-->
例如：<!--[if gte ie 5]><link rel="stylesheet" type="text/css" href="css/c.css"><![endif]-->
```
3.判断非IE浏览器：
```
语法： <!--[if ! ie]><!-->要判断的内容<!--<![endif]-->
例如：<!--[if ! ie]><!--><link rel="stylesheet" type="text/css" href="css/c.css"><!--<![endif]-->
```
4.判断是IE浏览器 语法：
```
<!--[if ie]>要判断的内容<![endif]-->
例如： <!--[if ie]><link rel="stylesheet" type="text/css" href="css/c.css"><![endif]-->
```
5.其他ie判断 ：
```
<!--[if lt IE 7]> 内容<![endif]-->: 小于 IE7 的版本 ( Less than )；
<!--[if lte IE 7]> 内容<![endif]-->: 小于或等于 IE7 的版本 ( Less than or equal )；
<!--[if gt IE 7]> 内容<![endif]-->: 大于 IE7 的版本 ( Greater than )；
<!--[if gte IE 7]> 内容<![endif]-->: 大于或等于 IE7 的版本 ( Greater than or equal )；
<!--[if !IE 7]>内容<![endif]--> : 不等于 IE7 的版本 ( Not )；
<!--[if !IE]><!-->您使用不是 Internet Explorer<!--<![endif]-->  不是IE浏览器
gt:大于不包括；lt:小于不包括；gte：大于包括；lte:小于包括；！除此之外；
```

**注意：顺序千万不要乱，因为当出现重复定义时，浏览器默认按最后一下渲染，所以一定要先正常，再*，最后_。**
