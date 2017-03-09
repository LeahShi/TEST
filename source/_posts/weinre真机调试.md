---
title: 远程真机调试 - weinre
date: 2016-05-27 10:26:24
tags: [Debug,移动端]
categories: work
---

移动端前端开发过程中在各种机型会遇到各种奇怪的问题，这时就用到真机调试，针对性的解决了。
真机调试的方式有很多，后续会相继做出总结，今天总结的是上个WebApp项目中用到的weinre真机调试。

<!-- more -->

## Weinre介绍
Weinre(Web Inspector Remote)是一款基于Web Inspector(Webkit)的远程调试工具， 它使用JS编写， 可以让我们在电脑上直接调试运行在手机上的远程页面。
Weinre的使用场景调试的页面在手机上， 调试工具在PC的chrome， 二者通过网络连接通信。

## 安装环境
**weinre是基于nodejs实现的， 使用它必须先安装node运行环境。**
```
[sudo]  npm -g install weinre  （是Mac/Linux用户， 还需要在前面加入"sudo":）
```
安装成功，会得到下面的输出：
![](/images/weinre_1.png)

## 运行
1、启动weinre的监控服务来进行远程调试：
```
weinre --boundHost -all-
若命令行中输出`weinre:starting server at http://localhost:8080`则成功运行
```
2、以上命令默认端口号为8080，若与本地项目端口冲突要改端口：
```
weinre --httpPort 8082 --boundHost -all-
```
3、启动后，就可用用本地电脑的ip和端口号访问weinre服务页面

## 调试
weinre服务页面中有两个地址值得我们注意：
第一部分是Access Points，如下图：红框中的地址是Debug Client(Weinre调试工具)的用户访问接口，可以通过这个地址进入Debug Client。
![](/images/weinre_3.png)
第二个部分是Target Script，如下图：
![](/images/weinre_4.png)
这个地址是系统根据我们启动Weinre服务时的参数设置生成的target-script.js文件的链接地址。我们需要将这个js文件引入到待测试的页面中。
要注意的是不要使用localhost:8080打开Weinre服务，否则生成的TargetScript链接也以localhost开头，这样直接复制到手机，就无法获取到文件了。

## 小结
```
//安装
npm -g install weinre
//默认端口8080启动
weinre --boundHost -all-
//更改端口为8082
weinre --httpPort 8082 --boundHost -all-
//在调试页面引入文件
<script src="http://172.16.0.140:8082/target/target-script-min.js#anonymous"></script>
//使用本地ip+端口号调试，在公司一般为局域网，如：
http://172.16.0.140:8082
注意输入命令行后直至调试完后才能关闭 否则会断线
```