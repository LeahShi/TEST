---
title: AngularJS - 简介
date: 2016-09-12 23:53:20
tags: [AngularJS,JS]
categories: work
---

## 写在前面
先来个来个小故事^_^
传说中挺牛逼的产品经理总算是入职了，第二天开早会，就提出对公司ERP进行改版。
一个月后，迭代新版本上线了...
老板挺开心的，PM来了就一个月居然就有这么大的变化，值了。
可不是么，一大片扎眼的blue...

这次迭代之后也带来很多不好维护的问题，于是各种开会讨论后，归结于框架没选好...
PM:我们要换框架，前后台都换。现在前台用的React，虽然很流行，但是毕竟是新东西
各个猿们在底下OS：第一版本用的.Net，结果换个PM了后，说后台语言没选好，第二版本改成Java...这次又换框架，明年又是要重复今年的节奏啊...

<!-- more -->

以上为博一笑而已，请勿当真。手动分割线

在项目之前虽然有接触过一些Angular的基础API，但也只是平常练手，在项目中的经验还是和欠缺的。改变是更多的是机遇，心里还是挺开心的。之后会根据项目中学到的姿势和自我体验先后记述系列学习笔记。欢迎拍砖...

## 框架选型
后台框架选用的是比较新型的[J潮客](https://jhipster.github.io/)；在首页，映入人眼的是这样一句话：JHipster is a Yeoman generator, used to create a Spring Boot + Angular project. 即 JHipster ≈ Yeoman + Spring Boot + Angular。 所以前台框架自然就从React换成了Angular。
原来一片绿，真的很扎眼，所以也在网上搜集了很多有关Angular的UI库，看到[Angulr](http://flatfull.com/themes/angulr/html/index.html)，眼前一亮，一直都很偏爱黑白简单的风格。

## Angular简单介绍
AngularJS 是由 Google 发起的一款开源的前端 MVC（Model-View-Controller） 脚本框架，主要用来构建单页面应用程序，即SPA（simple page application）。

在之后的笔记中，会围绕下面模块简单记述。

- 路由（route）
- 数据绑定
    - 表达式 —— 花括号赋值
    - 作用域（$scope） 
- 数据请求通信
- 事件类型
- 指令（directives）
- 模块（module）
    - 依赖注入
- 控制器（controller）
    - 依赖注入
- 服务（ service、factory）
    - 依赖注入
- 过滤器（filter） 
- 国际化（translate）
