---
title: 判断UA
date: 2016-05-04 09:18:19
tags: [移动端,UA]
categories: work
---

UA即用户代理（User Agent），代表用户行为的软件（软件代理程序）所提供的对自己的一个标识符，被广泛用来标识浏览器客户端信息。
想想项目测试阶段，测试拿着两补手机跑过来问你这两种设备为什么UI表现不一样时，我们首要要做的就是查到这两部设备的UA，才能做兼容。

<!-- more -->


## 判断各个平台的设备【推荐】

```
<script>
    //判断
   var browser={
      versions:function(){
         var u = navigator.userAgent, app = navigator.appVersion;
         return {         //移动终端浏览器版本信息
            trident: u.indexOf('Trident') > -1, //IE内核
            presto: u.indexOf('Presto') > -1, //opera内核
            webKit: u.indexOf('AppleWebKit') > -1, //苹果、谷歌内核
            gecko: u.indexOf('Gecko') > -1 && u.indexOf('KHTML') == -1, //火狐内核
            mobile: !!u.match(/AppleWebKit.*Mobile.*/), //是否为移动终端
            ios: !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/), //ios终端
            android: u.indexOf('Android') > -1 || u.indexOf('Linux') > -1, //android终端或uc浏览器
            iPhone: u.indexOf('iPhone') > -1 , //是否为iPhone或者QQHD浏览器
            iPad: u.indexOf('iPad') > -1, //是否iPad
            webApp: u.indexOf('Safari') == -1 //是否web应该程序，没有头部与底部
         };
      }(),
      language:(navigator.browserLanguage || navigator.language).toLowerCase()
   }
    //调用
//document.writeln("语言版本: "+browser.language);
//document.writeln(" 是否为移动终端: "+browser.versions.mobile);
//document.writeln(" ios终端: "+browser.versions.ios);
//document.writeln(" android终端: "+browser.versions.android);
//document.writeln(" 是否为iPhone: "+browser.versions.iPhone);
//document.writeln(" 是否iPad: "+browser.versions.iPad);
//document.writeln(navigator.userAgent);
    //实际调用小例子
   var intro=document.getElementById('intro');
   if(browser.versions.mobile){
      if(browser.versions.android){
         intro.style.padding="10% 20% 0 20%";
      }else if(browser.versions.ios){
         intro.style.padding="30% 20% 0 20%";
      }
   }else{
      intro.style.padding="3% 20% 0 20%";
   }
</script>
```

## 判断手机横竖屏：
```
<style>
/*横屏时字体红色*/
@media all and (orientation : landscape) {
    h2{color:red;}
}
/*竖屏时字体绿色*/
@media all and (orientation : portrait){
    h2{color:green;}
}

</style>
```

## 判断移动端还是pc端【另种方法】
```
<script type="text/javascript">
      if(/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
              window.location.href ="mobile.html"; // 如果是苹果机，就打开苹果的页面
          } else if (/(Android)/i.test(navigator.userAgent)) {
             //如果是安卓机，就打开安卓页面
              window.location.href ="mobile.html";
          } else {
              //不是移动设备，就是pc端的
                window.location.href ="pc.html";
      };
</script>
```