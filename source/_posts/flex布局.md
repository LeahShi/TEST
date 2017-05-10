---
title: Flex布局
date: 2017-04-20 14:55:21
tags: [Layout,CSS]
categories: work
---
相关资料http://www.techug.com/post/flex-examples.html

Flex布局自2009年提出，就一直受到关注。它可以简单、完整、响应式的实现各种页面布局，它的提出意味着打破了标准流中盒子的排列顺序。然后一直以来因为[兼容性问题](http://caniuse.com/#search=display%3Aflex)，IE对此兼容极不友好，因此对于flex布局，题主很少使用在项目中。然而此次学习的小程序，对flex布局的兼容性还是很好的。借以总结相关姿势：

<!-- more -->

采用Flex布局的元素，称为 flex container（父元素）。它的所有子元素自动成为容器成员，称为 flex item。
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。
主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；
交叉轴的开始位置叫做cross start，结束位置叫做cross end。

## 一、Flex container 的相关属性：
- **flex-direction**: row | row-reverse | column | column-reverse;  决定主轴是水平方向还是垂直方向
- **flex-wrap**: nowrap （不换行）| wrap（换行） | wrap-reverse（反向换行）; 定义如果一条轴线排不下，如何换行。默认为不换行
- **justify-content**: flex-start | flex-end | center | space-between（两端对齐） | space-around（四周都有间距）;定义了项目在主轴上的对齐方式，不改变主轴的指定下，默认是水平方向
- **align-items**: flex-start（交叉轴的起点对齐） | flex-end | center | baseline （项目的第一行文字的基线对齐。）| stretch（默认值：如果项目未设置高度或设为auto，将占满整个容器的高度。）;定义了项目在交叉轴上的对齐方式，不改变主轴的指定下，默认是垂直方向

## 二、Flex item 的相关属性：
- **order**的值为一个整数定义项目的排列顺序。数值越小，排列越靠前，默认为0。
- flex-grow的值为Number类型，定义项目占据剩余空间的放大比例，默认为0，如一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
- **flex**属性是flex-grow, flex-shrink（项目的缩小比例）和 flex-basis的简写，默认值为0 1 auto。后两个属性可选
- **align-self**可定义不同的对齐方式，比如斜项对齐。

## 三、布局
容器包含一个项目时，子元素可以位于父元素的九个点的任意一点（参照骰子的布局）。
若希望项目在容器主轴的中间，交叉轴的结束点，则给容器设置一下属性：
```
.box{
    display:flex;
    justify-content:center;
    align-items:flex-end;
}
```
容器包含多个项目的布局方式参照http://www.techug.com/post/flex-examples.html
