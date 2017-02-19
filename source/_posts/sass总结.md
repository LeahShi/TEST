---
title: sass总结
date: 2017-02-15 21:32:03
tags: [sass,css]
categories: work
---

## 写在前面
去年用了一些新东西，自后会相继更新几篇文章总结回顾，这是一篇有关sass的总结。

sass是css的预处理器开发工具。
基本思想是，用一种专门的编程语言，进行网页样式设计，然后再编译成正常的CSS文件。
提供了许多便利的写法，大大节省了设计者的时间，使得CSS的开发，变得简单和可维护。

<!-- more -->

## 安装sass
Sass是基于ruby语言写的。必须要先安装ruby,然后再安装sass。

### 安装ruby方法：
[Window 及Linux平台安装方法](http://www.runoob.com/ruby/ruby-installation-windows.html)

安装完后，在开始菜单中，找到刚才我们安装的ruby，打开【Start Command Prompt with Ruby】
然后再命令行中输入：
```
gem install sass
```
### 其他安装方法：
[戳这里](http://www.w3cplus.com/sassguide/install.html)

## 使用sass
### 文件后缀名
- 后缀名为sass: 语法中不能含有大括号和分号；
- 后缀名为scss:（推荐使用） 与css文件格式差不多；使用大括号和分号；

### 基本用法
#### 常用命令行：
```
1.安装：
gem install sass

2.运行：
sass input.scss output.css    //编译成.css文件
sass input.scss             //在命令行中输出css格式代码

3.编译成css的四种风格：
nested  （正常有缩进有嵌套的css文件）
expanded（没有缩进的、扩展的css代码。）
compact（简洁格式的css代码。）
compressed  （压缩后的css代码。）

　sass --style compressed test.sass test.css
　
4.监听改动（部分）：
sass --watch input.scss:output.css

5.监听文件夹：
sass --watch app/sass:public/stylesheets

6.sass、scss互换：
# 将 Sass 转换为 SCSS：
$ sass-convert style.sass style.scss

# 将 SCSS 转换为 Sass：
$ sass-convert style.scss style.sass
```

#### 变量
SASS允许使用变量，所有变量以$开头。
如果变量需要镶嵌在字符串之中，就必须需要写在#{}之中。
如：
```
$side : left;
.rounded {
border-#{$side}-radius: 5px;
}
```
变量名用下划线和中划线均可，两种用法互相兼容，
用中划线声明的变量用下划线也可引用；
申明多个变量时  用 **nth(变量名，排名)**  来引用

#### 计算
变量与变量之间的运算只有乘除 * /
```
body {
    margin: (14px/2);
    top: 50px + 100px;
    right: $var * 10%;
}
```

#### 嵌套
- SASS允许选择器嵌套
```
div {
    hi {color:red;}   →  div h1{color:red;}
}
```
- 属性也可以嵌套
比如border-color属性，可以写成
(**注意，border后面必须加上冒号。**)
```
p {
    border: {
        color: red;width:1px;
    }
}
```
- 父选择器的标识符&
在使用a:hover伪类时若按照以下方式，会出现空格 （sass在解开一个嵌套规则时会把.father通过一个空格		链接到子选择器的前边）；
所以向下面这种方式.father里的所有元素hover时都变色；
```
.father a {
    :hover {color: #ffb3ff;} → .father a :hover{color:#ffb3ff;}
}
```
解决方法：使用特殊选择器**&**
```
.father a{&:hover{color:#ffb3ff;}}
```
Tips:&的位置很灵活，可以选择.father前后的元素：
```
#content aside {
    color: red;
    body.ie & { color: green }
}
/*编译后*/
#content aside {color: red};
body.ie #content aside { color: green }
```
- 群组选择器的嵌套
```
.container{
    h1,h2,h3{font-weight:normal;}
}
```

#### 注释方式：(使用英文注释否则会报错)
- 标准注释 /* comment */ ，会保留到编译后的文件。（常用）
- 单行注释 // comment，只保留在SASS源文件中，编译后被省略。
- 在/*!重要注释!*/，表示这是"重要注释"。
即使是压缩模式编译，也会保留这行注释，通常可以用于声明版权信息。

#### 导入sass文件
@import “文件名” （不需要写后缀名）；

有些sass文件用于导入，不希望为每个这样的局部文件单独地生成一个css文件。对此，sass用一个特殊的约定来解决，即以“_”开头，当导入该局部文件时，可以省略开头下划线；
默认变量值：
反复声明一个变量，只有最后一处声明有效且它会覆盖前边的值；若没有被重新定义，则使用默认变量值
（eg:$fancybox-width: 400px !default;）

### 代码重用
#### 继承：(@extend)
SASS允许一个选择器，使用@extend命令继承另一个选择器。
```
.class1 {
    border: 1px solid #ddd;
}
.class2{
    @extend .class1;font-size:120%;
}
```
- 跟混合器相比，继承生成的css代码相对更少。因为继承仅仅是重复选择器，而不会重复属性；生成css体积更小；
- **当给同个元素添加相同属性的样式时，继承遵从权重规则，若权重相同定义在后的规则胜出。**
- 继承只会在生成css时复制选择器；而不会复制属性值；但是如果你不小心，可能会让生成的css中包含大量的选择器复制。
避免这种情况出现的最好方法就是不要在css规则中使用后代选择器（比如.foo .bar）去继承css规则。如果你这么做，同时被继承的css规则有通过后代选择器修饰的样式，	生成css中的选择器的数量很快就会失控：
```
.foo .bar { @extend .baz; }
.bip .baz { a: b; }
```

在上边的例子中，sass必须保证应用到.baz的样式同时也要应用到.foo .bar（位于class="foo"的元素内的class="bar"的元素）。

例子中有一条应用到.bip .baz（位于class="bip"的元素内的class="baz"的元素）的css规则。

当这条规则应用到.foo .bar时，可能存在三种情况，如下代码:
```
<!-- 继承可能迅速变复杂 -->
<!-- Case 1 -->
<div class="foo">
    <div class="bip">
        <div class="bar">...</div>
    </div>
</div>
<!-- Case 2 -->
<div class="bip">
    <div class="foo">
        <div class="bar">...</div>
    </div>
</div>
<!-- Case 3 -->
<div class="foo bip">
    <div class="bar">...</div>
</div>
```

为了应付这些情况，sass必须生成三种选择器组合（仅仅是.bip .foo .bar不能覆盖所有情况）。

如果任何一条规则里边的后代选择器再长一点，sass需要考虑的情况就会更多。

实际上sass并不总是会生成所有可能的选择器组合，即使是这样，选择器的个数依然可能会变得相当大，所以如果允许，尽可能避免这种用法。

可以放心地继承有后代选择器修饰规则的选择器（不管后代选择器多长），但有一个前提就是，不要继承有后代选择器。


#### 定义混合器(@Mixin)
- 混合器
Mixin有点像C语言的宏（macro），是可以**重用的代码块**。
使用@mixin命令，定义一个代码块。使用@include命令，调用这个mixin。
```
@mixin rounded-corners {
-moz-border-radius: 5px;
-webkit-border-radius: 5px;
border-radius: 5px;
}
notice {
background-color: green;
border: 2px solid #00aa00;
@include rounded-corners;
}
//sass最终生成：
.notice {
background-color: green;
border: 2px solid #00aa00;
-moz-border-radius: 5px;
-webkit-border-radius: 5px;
border-radius: 5px;
}
```
- 混合器传参
```
@mixin link-colors($normal,$hover,$active){
a{color:$normal;}
a:hover{color:$hover;}
a:active{color:$active;}
}
//调用
a{@include link-colors(blue, red, green);}
```
当定义了混合器后调用时可能会忘记参数的意义和顺序，可以不按照参数排列的顺利，只要一个都不少即可。

#### 颜色函数
SASS提供了一些内置的颜色函数，以便生成系列颜色。
```
lighten(#cc3, 10%) // #d6d65c      //变亮
darken(#cc3, 10%) // #a3a329      //变暗
grayscale(#cc3) // #808080        //灰度级
complement(#cc3) // #33c        //补充
```

## 高级用法
### if条件语句
```
@if lightness($color) > 30% {
background-color: #000;
} @else {
background-color: #fff;
}
```

### 循环语句
```
@for $i from 1 to 10 {
.border-#{$i} {
border: #{$i}px solid blue;
　　}
}
```
### 自定义函数
```
@function double($n) {
@return $n * 2;
}
#sidebar {
width: double(5px);
}
```
