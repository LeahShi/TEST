---
title: HTML5总结
date: 2016-06-08 09:50:49
tags: [HTML5]
categories: work
---

HTML5被定义为新一代开发**Web富客户端应用程序**【具有很强交互性和体验的客户端程序】整体解决方案，并非HTML4的下一版本，其前身为Web Application，在HTML、CSS、JavaScript API方面都有拓展。
主要被应用于：网页应用程序（PC端、WebAPP端、WeChat端等）、混合式本地应用、游戏等...

<!-- more -->

下面主要介绍H5增加部分：

## HTML方面：
### 新增标签
在H5中新增了几种标签增强了其语义化，能够便于开发者阅读和写出更优雅的代码，代码如诗；让浏览器或是网络爬虫可以很好地解析，从而更好分析其中的内容，更好的搜索引擎。
常用的：header、nav、section、aside、article、footer等
应用程序标签：DataList(数据列表)、Progress(进度条)、Meter(数值显示器)、Menu(右键菜单)、Details(明细)  【不太常用，很多浏览器支持这些属性】

### 新增网页多媒体：
1、音频：
```
<audio>
       <source src="img/music.mp3" type="audio/mpeg"/>
 </audio>
```
controls 属性可以去掉；去掉之后音频播放器的默认外观消失可自定义；可通过按钮触发开始和暂停 play()和pause()
2、视频：
```
<video controls="controls">
  <!-- 不同浏览器支持格式不一样 (版权问题)-->
  <source src="fun.ogg" type="video/ogg" />
  <source src="fun.mp4" type="video/mp4" />
  <!-- 当浏览器不兼容video标签，就会将他以div方式解析 -->
  sorry 你的浏览器不支持！
</video>
```
对于不支持radio和audio标签的浏览器解决方法：
**<script src="//api.html5media.info/1.2.1/html5media.min.js"></script>**
还可以为视频添加弹幕
3、Canvas：(后做专题)、2D/3D (WebGL)
4、svg:
svg即：Scalable Vector Graphics 可缩放矢量图形；体积小，质量高，效果好，可控程度高，使用方法如下：
```
//a.使用 "iframe"标签【推荐使用】 svg单独作为一个页面
 <iframe src="1.svg" frameborder="0"></iframe>

//b.使用"object"标签
<object data="data.svg" type=""></object>

//c.使用"embed"标签
<embed src="demo.svg">

//d.Ajax方式加载(自定义样式) 【最推荐使用】
```
<svg data-src="demo.svg"></svg>
<script>
  window.addEventListener('load',function(){
              var svgs=document.getElementByTagName('svg');
              for(var i=0;i<svgs.length;i++){
                          //向服务器发送请求   得到svg
                 }
     })
 </script>
```

### 新增属性：
1、自定义属性:（data-x）【重要】
通过DOM存储与DOM对象强相关的数据。
2、智能表单：【手机端可以使用、pc端有很多兼容性问题】
新表单类型（number、email、date、range、search、tel、color等）；在webapp中应用较多；原生的应用，在浏览器中会有很多兼容性问题。
3、链接关系描述：（用来描述指定链接与当前文档的关系，便于机器理解文档结构）
常见链接关系表：
- stylesheet    文档的外部样式表；（常见）
- nofollow    用于指定 Google 搜索引擎不要跟踪链接（君子协议）
- copyright    包含版权信息的文档
- contents、index、chapter、section、subsection   文档的 目录、索引、节、字段；
- alternate 文档的可选版本（例如打印页、翻译页或镜像）；
- start、next、prev（集合中的第一个文档\下一个\上一个）；
- glossary    文档中所用字词的术语表或解释；
- appendix    文档附录            help    帮助文档
- licence    一般用于文献，表示许可证的含义
- tag    标签集合        friend    友情链接
4、结构数据标记:(很高级的东西，只有Google支持)
```
<div itemscope itemtype="http://example.com/hello">
    <p>我叫<span itemprop="老板">aaa</span>。</p>
    <p>
        我y有个员工叫
        <span itemprop="员工">bbb</span>
        性别<span itemprop="性别">男</span>。
    </p>
</div>
```

5、ARIA属性：
Accessible Rich Internet Application (无障碍富互联网应用程序);
主要针对于屏幕阅读设备(e.g. NVDA)，更快更好地理解网页，更多语义化。


## CSS3【后作专题】


## JavaScriptAPI
### 基础API提升
#### 1.新选择器
JS多了一个原始支持，类似jqueryDOM选择器,可以通过CSS选择器的语法找到DOM元素：
- document.querySelector(selector)      //返回第一个
- document.querySelectorAll(".item")    //返回所有.item元素，数组

#### 2.元素的classList属性
classList返回一个数组，Internet Explorer 9 及更早 IE 版本浏览器不支持 classList 属性
selector.classList.add(className);
add 添加一个新的类名、remove 删除一个的类名、contains 判断是否包含、toggle 切换一个class element.toggle(‘class-name’,[add_or_remove])
toggle函数的第二个参数true为添加 false删除,第二个参数一般为函数

### 访问历史 API
界面上的所有JS操作不会被浏览器记住，就无法回到之前的状态
在HTML5中可以通过window.history操作访问历史状态，让一个页面可以有多个历史状态：
- window.history.forward(); // 前进
- window.history.back(); // 后退
- window.history.go(); // 刷新
- 通过JS可以加入一个访问状态：
 history.pushState(放入历史中的状态数据, 设置title(现在浏览器不支持)， 改变历史状态)

### 全屏 API
JavaScript中可以通过调用requestFullScreen()方式实现指定元素的全屏显示：
```
document.querySelector("").requestFullScreen();
```

### 其他API
- 网页存储：Web Storage、Web SQL、IndexedDB、Application Cache、localStorage、sessionStorage
- 文件系统：File API、FileReader
- 网络访问：XMLHttpRequest、fetch、WebSocket
- 拖放操作：网页内拖放、文件拖入、拖出
- 多线程：桌面通知
- 设备信息访问
- 网络状态
- 硬件访问
- 设备方向
- 地址围栏


