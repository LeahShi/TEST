---
title: React Native之环境搭建
date: 2017-06-19 2:02:22
tags: [React Native,跨平台,移动端]
categories: work
comments: true
---

移动端跨平台问题一直都是大家关注的焦点，是想一套代码，运行在多个平台，可以省下多少事。
目前跨平台的前端框架有很多，React Native 是伟大的facebook公司15年发布，至今更新了很多个版本尽管一直没有发布1.0版本，仍然抵挡不住大家的热情。
之后的一段时间，我也会在这方面总结一系列教程和方法。

第一篇来介绍下如何搭建React Native开发环境。搭建环境有相应的几种平台，在Windows下只能 run-android，在Mac OS X系统可一运行ios&&android双平台。
由于博主有有windows系统电脑，下面主要介绍在windows下搭建React Native开发环境填的坑...

<!-- more -->

## 一、依次安装以下环境：
1、安装Java：
    假如电脑跑起来过项目的话，java环境肯定是有的，可以执行java -version命令来检测

2、安装SDK：
    SDK下载地址：http://tools.android-studio.org/index.php/sdk/
- 设置系统环境变量菜单：控制面板\系统和安全\系统\高级系统设置\环境变量 （window10下）
- 设置环境变量ANDROID_HOME：Android SDK Manager的位置 例如：（ANDROID_HOME=> E:\Android\sdk）
- 设置环境变量PATH 例如：（PATH=> %ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools）

3、下载SDK相关：(重要，不要漏掉任何一项)
![](/images/sdk_install.png)
- 打开Android SDK Manager。
- 选中Android SDK Build-tools version 23.0.1  （其他版本可能会出错）
- Android 6.0 (API 23)  （注意安装Genymotion时要注意模拟器版本不能太低）
- Android Support Repository
- Local Maven repository for Support Libraries
下载以上SDK内容可能会很慢，耐心等待即可。

3、安装node：
下载地址：https://nodejs.org/en/
要求4.1版本以上，通过node -v的命令来测试NodeJS是否安装成功

4、安装git：
下载地址：https://git-for-windows.github.io/

## 二、安装React Native相关脚手架：（重要，要按步骤耐心等待下载）
1、进入想要安装RN环境的目录
2、右键选择git bash 输入git clone https://github.com/facebook/react-native.git，耐心等待下载
3、再命令行中执行cd react-native/react-native-cli进入相应目录，执行npm install -g (目的：执行react-native命令)
4、进入想要创建RN项目的目录，执行react-native init AwesomeProject（项目名）命令，耐心耐心等待
5、项目初始化后可以看到android、ios、node_modules文件夹、index.android.js、index.ios.js文件等...

## 三、安装Genymotion模拟器：
Genymotion官网地址：http://www.genymotion.net/
可以自己在网上下，也可附上百度云下载地址：http://pan.baidu.com/s/1pKH8BnP
注意：
- Genymotion对私人开发者免费开源，注册即可（登录需要填写用户名、密码）。对公账户需要收费。
- 双击安装Genymotion会连带安装Oracle VM VirtualBox，要将VirtualBox升级最新版本，（使用360即可），不然会报错，（unable to start the virtual device.VirtualBox cannot start the virtual device）
错误稍后会以专题总结。

## 四、启动项目：
1、进入项目目录，执行**react-native start**命令，耐心等待。访问http://localhost:8081/index.android.bundle?platform=android，如果可以访问表示服务器端跑起来了。
2、启动Genymotion，或者用数据线连接安卓机真机调试，再另启命令行，输入**react-native run-android**，时间很长，耐心等待。
3、第一次启动会报错，`can't find variable:.....`  解决方法：在模拟器调试状态下按下ctrl+M，（真机调试下摇一摇）调出弹窗，
点击Dev Settings → Debug server host & port for device(设置本机的ip和端口固定8081) → 再次ctrl+M(摇一摇),选择Reload JS
（因为RN报错，很多错误无从找起，稍后会用另起一篇专门总结RN中常见错误）
4、Welcome to React Native！ 当看到这样的字时，恭喜你环境搭建好了，可以下一步熟悉相关api了。
5、之后启动项目直接运行react-native run-android 即可，start命令可以省略，倘若要安装第三方插件时，需要关掉再重启。


## 相关学习资源
github上的地址：https://github.com/facebook/react-native
江清清RN系列教程：http://www.lcode.org/react-native/
React Native中文网：http://reactnative.cn/
资源合集：https://github.com/reactnativecn/react-native-guide
第三方组件合集：http://blog.csdn.net/chichengjunma/article/details/52920137
