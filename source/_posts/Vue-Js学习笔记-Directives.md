---
title: Vue.js学习笔记 - Directives
date: 2017-02-28 16:16:36
tags: [Vue,js框架]
categories: work
---
!()[/images/vue-logo.png]

<!-- more -->

[Vue.js中文网](https://cn.vuejs.org/v2/guide/syntax.html#指令)中描述道：
指令是带有 v- 前缀的特殊属性。指令属性的值预期是单一 JavaScript 表达式。指令的职责就是当其表达式的值改变时相应地将某些行为应用到 DOM 上。
指令在javascript各种框架中，担当很重要的角色，此次总结的是Vue学习笔记中的指令篇。

## v-bind
v-bind用来绑定元素的属性，比如class、title、id、自定义属性等等。常用的是class与style绑定。
官网中提供了许多