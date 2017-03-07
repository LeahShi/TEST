---
title: Hexo_NexT主题添加访问量统计
date: 2016-02-27 15:38:11
tags: [Hexo,NexT,stylus]
categories: work
---

> 本文主要为如何为用Hexo的NexT主题的网站添加访问量和文章阅读量的总结。注意其他主题不适用。

突发奇想为个站添加访问量的功能，在网上查阅了很多资料后，发现大多数用的LeanCloud+不蒜子。故本站也已此来实现。

<!-- more -->

## LeanCloud和不蒜子介绍
### [LeanCloud](https://leancloud.cn/):
主要为移动应用提供云服务，包括存储、账号管理、社交分享、推送、应用统计等以及相关的技术支持和服务。此处，我们只用到的应用统计功能。
### [不蒜子](http://busuanzi.ibruce.info/):
官方介绍：只需两行代码，搞定计数。

## 添加文章阅读量
### 配置
#### LeanCloud上配置：
- 打开官网，注册登录，点击右上【访问控制台】
- 创建新应用（名称随意，其他选择默认即可）
- 进入应用，点击【存储】下的【创建class】， 创建名为Counter
- 在【设置】下【应用key】拿到app_id和app_key
- 在【设置】->【安全中心】的【Web安全域名】中加入自己的博客域名（安全性考虑）

以后可以在【存储】下的【Counter】看到网站访问情况：
![网站访问数据](/images/see_visitor.jpg)


#### 在NexT下：
[NexT](http://theme-next.iissnan.com/getting-started.html)

- 1、在NexT主题的 _config.yml 文件，在相应位置添加：
```
# add post views
leancloud_visitors:
  enable: true
  app_id: **你的app_id**
  app_key: **你的app_key**   //上步已拿到
```

- 2、在`next\layout\ _scripts`路径下，新建lean-analytics.swig文件，并添加以下内容：
```
<!-- custom analytics part create by xiamo -->
<script src="https://cdn1.lncld.net/static/js/av-core-mini-0.6.1.js"></script>
<script>AV.initialize("{{theme.leancloud_visitors.app_id}}", "{{theme.leancloud_visitors.app_key}}");</script>
<script>
function showTime(Counter) {
	var query = new AV.Query(Counter);
	$(".leancloud_visitors").each(function() {
		var url = $(this).attr("id").trim();
		query.equalTo("url", url);
		query.find({
			success: function(results) {
				if (results.length == 0) {
					var content = '0 ' + $(document.getElementById(url)).text();
					$(document.getElementById(url)).text(content);
					return;
				}
				for (var i = 0; i < results.length; i++) {
					var object = results[i];
					var content = object.get('time') + ' ' + $(document.getElementById(url)).text();
					$(document.getElementById(url)).text(content);
				}
			},
			error: function(object, error) {
				console.log("Error: " + error.code + " " + error.message);
			}
		});

	});
}

function addCount(Counter) {
	var Counter = AV.Object.extend("Counter");
	url = $(".leancloud_visitors").attr('id').trim();
	title = $(".leancloud_visitors").attr('data-flag-title').trim();
	var query = new AV.Query(Counter);
	query.equalTo("url", url);
	query.find({
		success: function(results) {
			if (results.length > 0) {
				var counter = results[0];
				counter.fetchWhenSave(true);
				counter.increment("time");
				counter.save(null, {
					success: function(counter) {
						var content =  counter.get('time') + ' ' + $(document.getElementById(url)).text();
						$(document.getElementById(url)).text(content);
					},
					error: function(counter, error) {
						console.log('Failed to save Visitor num, with error message: ' + error.message);
					}
				});
			} else {
				var newcounter = new Counter();
				newcounter.set("title", title);
				newcounter.set("url", url);
				newcounter.set("time", 1);
				newcounter.save(null, {
					success: function(newcounter) {
					    console.log("newcounter.get('time')="+newcounter.get('time'));
						var content = newcounter.get('time') + ' ' + $(document.getElementById(url)).text();
						$(document.getElementById(url)).text(content);
					},
					error: function(newcounter, error) {
						console.log('Failed to create');
					}
				});
			}
		},
		error: function(error) {
			console.log('Error:' + error.code + " " + error.message);
		}
	});
}
$(function() {
	var Counter = AV.Object.extend("Counter");
	if ($('.leancloud_visitors').length == 1) {
		addCount(Counter);
	} else if ($('.post-title-link').length > 1) {
		showTime(Counter);
	}
});
</script>
```

- 3、在next/layout/_macro路径下，编辑post.swig文件，找到相应的插入位置:（我该文件下默认有，就省略）
```
{% if theme.leancloud_visitors.enable %}
&nbsp; | &nbsp;
<span id="{{ url_for(post.path) }}"class="leancloud_visitors" data-flag-title="{{ post.title }}">
         &nbsp;{{__('post.visitors')}}
        </span>
{% endif %}
```
- 4、`next\layout`目录下，编辑_layout.swig文件，在</body>的上方，插入如下代码:
```
{% if theme.leancloud_visitors.enable %}
{% include '_scripts/lean-analytics.swig' %}
{% endif %}
```

- 5、在`next/languages/网站使用的配置文件`的post里添加语言字段posted、sticky、visitors.以英文为例（其他语言，自行翻译）
```
post:
  sticky: Sticky
  posted: Posted on
  visitors: Views // 增加的字段
  ...
```

## 添加网站访问量：
### 两个概念：
- pv：网站的浏览次数。如：单个用户连续点击n篇文章，记录n次访问量
- uv：网站的访客数。如：单个用户连续点击n篇文章，只记录1次访客数。你可以根据需要添加相应的统计功能。

### 安装不蒜子
- 打开并编辑`next/layout/_partial/footer.swig`文件，拷贝下面的代码至文件的开头
```
<script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js">
</script>
```

- 编辑该文件，在`div.theme-info`元素后添加一下代码： （任选一个）
```
//显示pv统计量：【多】
<span id="busuanzi_container_site_pv">
  Total visited  <span id="busuanzi_value_site_pv"></span>  times
</span>

//或显示pv统计量：【少】
<span id="busuanzi_container_site_uv">
  Total visited  <span id="busuanzi_value_site_uv"></span>  times
</span>
```

### 自定义样式：（推荐方法）
- 在`next/source/css/`目录下新建文件夹并新建.styl文件，并以stylus语法编辑样式
- 在同级打开`main.styl`文件，并将新建文件引入
```
//My Layer
    //--------------------------------------------------
    @import "_my/mycss";
```
- stylus语法，之后总结