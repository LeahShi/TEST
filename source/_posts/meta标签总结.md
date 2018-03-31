---
title: meta标签总结
date: 2015-10-29 11:08:01
tags: [meta,SEO]
categories: work
---

## HTTP 标题信息(http-equiv)
HTTP 标题信息(http-equiv)，它可以向浏览器传回一些有用的信息，以帮助正确和精确地显示网页内容,该枚举的属性定义，可以改变服务器和用户代理行为的编译。合理使用可增加 SEO 收录。编译的值取content 里的内容。简单来说即可以模拟 HTTP 协议响应头。
eg: **<meta http-equiv="参数" content="参数变量值">**

<!-- more -->

1、设置编码类型:
```
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<meta charset="uft-8"/> //在h5中
```

2、设置网页语言【Content-Language】
3、指定时间刷新页面【Refresh】
```
设置网页3s刷新一次：
<meta http-equiv="refresh" content="3" />

3秒钟后跳转页面；见文档3s种后跳转；
<meta http-equiv="refresh" content="3”; url=”http//www.uhoem.com” />
 ```

4、设定页面 cookie 过期时间【set-cookie】
```
<meta http-equiv="Set-Cookie" content="cookievalue=xxx; expires=Friday, 12-Jan-2001 18:18:18 GMT； path=/">
```

5、页面最后生成时间【last-modified】
6、可以用于设定网页的到期时间。一旦网页过期，必须到服务器重新传输。必须使用GMT的时间格式。【expires】
```
<meta http-equiv="expires" content="Fri, 12 Jan 2001 18:18:18 GMT">
```
7、设置文档的缓存机制【cache-control】
8、禁止浏览器从本地计算机的缓存中访问页面内容。【Pragma】
```
<meta http-equiv="Pragma" content="no-cache">
```



## 页面描述信息(name)
用于描述网页，与之对应的属性值为content，content中的内容主要是便于搜索引擎机器人查找信息和分类信息用的
 eg:**<meta name="参数" content="具体的参数值">**

1、Keywords(关键字)　　
```
//keywords用来告诉搜索引擎你网页的关键字是什么。　
<meta name ="keywords" content="science, education,culture,politics,ecnomics，relationships, entertaiment, human">　
```
2、description(网站内容描述)　
```
//description用来告诉搜索引擎你的网站主要内容。
<meta name="description" content="This page is about the meaning of science, education,culture.">
```
3、robots(机器人向导)　
```
//robots用来告诉搜索机器人哪些页面需要索引，哪些页面不需要索引。　　content的参数有all,none,index,noindex,follow,nofollow。默认是all。　　
<meta name="robots" content="none">　
```
4、author(作者)　
```
//标注网页的作者
<meta name="author" content="root,root@21cn.com">
```


## 常用meta标签：
```
<!-- 视图窗口，移动端特属的标签。 -->
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,minimum-scale=1,user-scalable=no" />
<!-- 避免IE使用兼容模式 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<!-- 启用360浏览器的极速模式(webkit) -->
<meta name="renderer" content="webkit">
<!-- 是否启动webapp功能，会删除默认的苹果工具栏和菜单栏。仅在ios有效 -->
<meta name="apple-mobile-web-app-capable" content="yes" />
<!-- 这个主要是根据实际的页面设计的主体色为搭配来进行设置。 -->
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
<!-- 忽略页面中的数字识别为电话号码,email识别 -->
<meta name="format-detection"content="telephone=no, email=no" />
<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true">
<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait">
<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">
<!-- UC强制全屏 -->
<meta name="full-screen" content="yes">
<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">
<!-- UC应用模式 -->
<meta name="browsermode" content="application">
<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">
<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no">
```
