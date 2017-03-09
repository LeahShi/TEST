---
title: compass总结
date: 2016-03-29 11:25:34
tags: [Compass,Sass,CSS]
categories: work
---

[前面](https://leahshi.github.io/2017/02/15/sass%E6%80%BB%E7%BB%93/)已提及sass是css的预处理器开发工具，而compass相当于sass的工具库。想想jQuery与javascript的关系，就明了了。
用compass我们可以很轻松的书写css3属性、制作精灵图等，compass里也设定了很多混合器、模块和函数等供我们调用。
[compass官网](http://compass-style.org/)中提到Compass is an open-source CSS Authoring Framework。总而言之，使用compass会让css更加精简和方便。

<!-- more -->

## 一、安装与创建compass项目
由于compass和sass一样都是基于Ruby开发，倘若电脑里已安装sass则不必在安装Ruby，直接执行以下命令安装：（windows下）
```
gem install compass
compass create compass-test  //初始化compass项目
```
如果你跟我一样在2016年12月之前安装的Ruby，会报如下错误：
```
ERROR:  Could not find a valid gem 'compass' (>= 0), here is why:
          Unable to download data from https://rubygems.org/ - SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (https://rubygems.org/specs.4.8.gz)
```
则需要[版本升级](https://gist.github.com/luislavena/f064211759ee0f806c88)


## 二、compass应用
官网提供了很多compass方法，只总结常用几种。
[compass core](http://compass-style.org/reference/compass/)提供七种，分别是Browser Support、reset、layout、CSS3、helpers、Typography、utilities。
### [浏览器支持](http://compass-style.org/reference/compass/support/)：

### 网页初始化：`@import "compass/reset"`【推荐】

### [layout](http://compass-style.org/reference/compass/layout/):【不建议使用】
`@import "compass/layout"`背景栅格化、footer固定在网页地端、指定子元素占满父元素的空间

### [CSS3](http://compass-style.org/reference/compass/css3/):【推荐】
`@import "compass/css3";`compass内置了许多CSS3混合器，以便使用，不用将统一个属性写多次以兼容各个浏览器

### [Helper函数](http://compass-style.org/reference/compass/helpers/)
Compass还提供一系列非常有用函数，比如image-width()和image-height()返回图片的宽和高等

### [Typography](http://compass-style.org/reference/compass/typography/)
为排版提供许多混合器，如链接颜色，列表板式，文字相关属性（换行，省略。。）等等

### [utilities](http://compass-style.org/reference/compass/utilities/)
提供了很多方法，比如颜色、清除浮动、浏览器兼容、表格、精灵图等等，详细见文档，以下仅总结常用且方便的：
#### 使用compass制作[精灵图](http://compass-style.org/help/tutorials/spriting/)：
1、将所有icon放在同一个文件（假设文件夹名为icons）加下，要求宽高比为1且格式为PNG
2、在.scss文件下加入
```
@import "compass/utilities/sprites";
@import "icons/*.png";
@include all-icons-sprites;   //icons为文件夹名称
```
3、调用
#### 清除浮动
```
import "compass/utilities/";
　　.clearfix {
　　　　@include clearfix;
　　}
```


## 三、编译
compass同sass一样有四种[编译风格](https://leahshi.github.io/2017/02/15/sass%E6%80%BB%E7%BB%93/)，执行一下编译命令后会将sass文件夹的文件编译成css文件，保存于stylesheets目录下。

### 编译方式（两种）
1、使用命令行
- `compass compile`默认编译会有大量注释
- `compass compile --output-style compressed` //使用--output-style参数+编译风格
2、改变配置文件`config.rb`中的编译风格
```
output_style = :compressed
再执行compass compile
```
3、通过指定environment的值（:production或者:development），智能判断编译模式。
```
environment = :development
　　output_style = (environment == :production) ? :compressed : :expanded
```
4、用`compass watch`监控文件改动，自动编译【常用】
