---
title: Quartz任务调度.md
date: 2018-03-17 19:15:56
tags: [Java,Quartz]
categories: work
---

Quarz任务调度主要用于一些有时效性的场景，希望由系统自动去处理，而不是由人工去处理，在很多地方都会使用到，比如：商城中限时商品秒杀、注册时邮箱三天内激活、产品到期盈利等等

<!-- more-->

## Quartz相关概念
在Quartz中，有三个重要对象Job、Trigger、Schedule，分别对应着任务、触发器、调度器。


