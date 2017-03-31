---
title: AngularJS - 模块（app.module）
date: 2016-09-20 23:53:20
tags: [AngularJS,JS]
categories: work
---

## Module介绍
每一个Angular应用都应该声明一个或多个module，通常一个应用视为一个Module即可。SPA应用的首页中引入所有的资源，DOM结构中放置一个div，给其绑定ng-app。在通过路由，实现页面嵌套。在HTML中用`ng-app`绑定某个模块的父节点上。

<!-- more -->

## 使用语法
```
angular.module(name, [requires]);
```
- 【name】为一个字符串，值为页面中`ng-app="myApp"` 中的值（myApp）
- 【requires】为一个数组，在module声明的时候，后面带一个数组，这个数组里面可以指定它所依赖的module，以供该模块使用。如果没有要依赖的模块可以为空`angular.module(name, []);` 

angular.module('name', [])和angular.module('name') 虽然看起来很相似，但是含义却是不同的。
前者是创建一个新的module，[]表示它没有依赖任何其他模块，如果已经有了一个同名模块，则会覆盖现有的（参照项目根目录中app.module.js文件）；
而后者是查找一个现有module，如果这个module不存在，则返回空值（参照项目各个模块的controller、state、service、js文件）。
如果把前者误用为后者，那么在它的返回值上调用controller等函数会出现空指针错误；
如果把后者误用为前者，则会导致那些依赖注入的module丢掉。

## `angular.module('myModule',[]).`后面常用的方法
一下方法每一个都可以作为一个专题来记述，此篇不祥述。只做简单陈述和列举

### `constant(name, object);`
一个应用中的常量设置文件，比如可以设置版本号、DEBUG_INFO_ENABLED（是否启用调试信息）等。通常一个项目中只有一个app.constant.js文件，放在根目录，用以对整个项目的常量设置。

### `run(initializationFn)`
可以使用此方法注册当注入模块全部加载后执行的初始化函数。

### controller
`controller(name, constructor);`
Angular MVC框架中的C，每个页面都有对应的控制器。

### config
`config(configFn);`
使用此方法来注册需要在模块加载上执行的工作，比如`$stateProvider`路由。关于Angular路由是Angular的核心之一，之后在记述。

### services(为和下面方法做区分加-s)
- `factory(name, function);`
    - 创建services中最简单的方法；
    - 需要一个方法和数据的集合且不需要处理复杂的逻辑的时候使用factory()；
    - 需要使用.config()来配置service的时候不能使用factory()方法；
- `service(name, constructor);`
- `provider(name, providerType);`


以上三种都是Angular注册服务构造函数，services 可以用来永久保存应用的数据，只有在应用生命周期结束的时候（关闭浏览器）才会被清除。而controllers在不需要的时候就会被销毁了。
并且这些数据可以在不同的 controller 之间使用。通常用于页面中的数据请求、通用的方法、页面之间的通信，因为同一个services可以注入到不同的控制器中调用，而控制器只能绑定在一个代码块或一个页面。

 