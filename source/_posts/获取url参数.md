---
title: 获取url参数
date: 2016-08-30 22:23:24
tags: [JS,Skill]
categories: work
---
前几天，营销部在做一个活动时，需要一个H5的活动页，需要用到WebApp以及内嵌原生app中，此时遇到这样的一个场景：在WebApp中需要头部，但是在原生app中只需要除头部的内容即可。如果放在后台，获取地址栏上的参数很容易，可是这是静态页面耶，当然最容易解决的方法固然是做成完全一样的两个页面，只是有无头部的区别，这样做对于一个追求代码简洁的人(^_^)来说， absolutely no way。后来网上找资料找到了解决方案，介意总结

<!-- more -->
 

## 需求
同一个页面，通过向url传递一个参数，js获取参数来控制页面。
WebApp的页面地址假设为`activity.html`，显示完整页面
而App的地址则也` activity.html?isApp=1`，不需要显示头部

## 方法一：
### 正则表达式
一下代码采用正则表达式方法，将url的参数拆分开。
也可以在后面传递多个参数 `activity.html?isApp=1&id=001`
调用时直接是用函数名`console.log(getQueryString(参数名))`即可

```
function getQueryString(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    var r = window.location.search.substr(1).match(reg);
    if (r != null) return unescape(r[2]);
    return null;
}

if(getQueryString("isApp")==1){
    var header=document.getElementsByTagName('header')[0];
    head.style.display="none";
}
```

### 逐步分析
- 首先需要获取到地址栏中的url值
- 再根据js方法将url和参数分离开 url?参数1&参数2
- 将多个参数逐个切分至一个数组中
- 将各个参数的参数名和参数值切分
- 调用函数
```
function getQueryString() {
   var name,value; 
   //1、获取到url的值
   var str=location.href; 

   //2、分割url内的多个参数
   //indexOf() 方法可以返回括号内字符串值在字符串中首次出现的位置 
   var num=str.indexOf("?")
   //substr() 方法语法stringObject.substr(start,length)
   //可在字符串中抽取从 start 下标开始的指定数目的字符
   str=str.substr(num+1);

  //3、各个参数放到数组里
   var arr=str.split("&"); 
   for(var i=0;i < arr.length;i++){ 
        num=arr[i].indexOf("="); 
        if(num>0){ 
            name=arr[i].substring(0,num);
            value=arr[i].substr(num+1);
            this[name]=value;
        } 
    } 
} 
var Request=new getQueryString(); //实例化 
if(Request.isApp==1){
    var header=document.getElementsByTagName('header')[0];
    head.style.display="none";
}
```

## 方法二：
js中提供了location对象，并且提供了location.search方法，比如在浏览器中访问 http://ip:port/demo.html?id=123
调用location.search方法就可以返回 "?id=123" ，可以说很强大了