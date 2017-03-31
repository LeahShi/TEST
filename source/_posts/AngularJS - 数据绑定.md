---
title: AngularJS - 数据绑定 
date: 2016-09-25 22:53:20
tags: [AngularJS,JS]
categories: work
---

操作DOM是实际项目中大量使用的，如果没有接触到Angular，或许觉得JQuery用起来还算顺手。对Angular稍微了解的，就会知道数据绑定在Angular有多简便。

<!-- more -->

## 双向绑定
Angular的数据双向绑定指的是 model和view之间的双向绑定。在用户点击元素、填写表单等view上的行为中用的最多。
### 基础用法(ng-module)
```
<input type="text" ng-model="iptValue"/>
<p>你输入的是：{{iptValue}}</p>
```
以上花括号称为**插值表达式**。在控制器中也可以获取到用户输入的内容
`console.log($scope.iptValue)`
一旦用户输入改变了会改变传输到后台或者后台返回的内容改变了绑定到界面中，故称之为双向。

### 绑定表达式，二次计算
常见的例子，用以界面中带出用户性别，在数据库中存储可能为0和1，这时我们就需要对数据加以处理转换（格式化format）
```
<div>{{formatGender(a.gender)}}</div>
$scope.formatGender = function(gender) {
    if (gender == 0)
        return "女";

    if (gender == 1)
        return "男";
    }
};
```
在绑定表达式时，只能使用自定义函数，不能使用原生函数。若需要用到原生函数是，可以用一个自定义函数作包装，在自定义函数里面可以随意使用各种原生对象
```
<div>{{abs(-1)}}</div>
$scope.abs = function(number) {
    return Math.abs(number);    
};
```

### 绑定数组对象
常用语列表遍历循环 `ng-repeat` ；在指令篇详细介绍。  

Angular双向数据绑定的功能强大的远不止以上列举的三种，还有很多场景：比如操作样式高亮（ng-class）、状态控制（ng-show、ng-if）等。在指令篇详细说明。


## 作用域（$scope）
上面数据绑定中有个参数`$scope`经常出现，在其内可以声明一个作用域。
Angular 作用域是一个指向应用模型的对象，它是表达式的执行环境、能监控表达式和传递事件。一旦一个 ng-app、ng-controller、ng-repeat 等指令被定义，那么一个作用域就产生了。
### 作用域基本功能
- 提供观察者以监视数据模型的变化；
- 可以将数据模型的变化通知给整个应用，甚至是系统外的组件；
- 可以进行嵌套，隔离业务功能和数据；
- 给表达式提供运算时所需的执行环境。

### $rootScope
由 ng-app 所生成的作用域是一个根作用域（$rootScope），它是其他所有$scope 的最顶层。
在js中变量不声明就会被绑定到window上，在Angular中不声明就会绑定到$rootScope上。
 
### $scope 的继承
在Angular中，如果两个控制器所对应的视图存在上下级关系，它们的作用域就自动产生继承关系。
如果里面控制器继承的变量发生改变了，外面的控制器却不会同步变化
```
<div ng-controller="OuterCtrl" ng-app="myApp">
    <span>{{a}}</span>
    <div ng-controller="InnerCtrl">
        <span>{{a}}</span> 
        <button ng-click="a=a*2">点击</button>
    </div>
</div>
 
var app = angular.module('myApp',[]);
app.controller("OuterCtrl",function($scope){
	$scope.a = 1;
})	
app.controller("InnerCtrl",function($scope){
	 
})
```
以上 界面显示两个1；如果InnerCtrl不声明会把表达式的内容打印出来。

### $scope 的生命周期
创建 → 链接 → 更新 → 销毁


