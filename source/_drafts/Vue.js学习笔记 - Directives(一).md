---
title: Vue.js学习笔记 - Directives(一)
date: 2017-02-28 16:16:36
tags: [Vue,js框架]
categories: work
---
![vue-logo](/images/vue-logo.png)

<!-- more -->

[Vue.js中文网](https://cn.vuejs.org/v2/guide/syntax.html#指令)中描述道：
指令是带有 v- 前缀的特殊属性。指令属性的值预期是单一 JavaScript 表达式。指令的职责就是当其表达式的值改变时相应地将某些行为应用到 DOM 上。
指令在javascript各种框架中，担当很重要的角色，此次总结的是Vue学习笔记中的指令篇。

## v-bind
v-bind用来绑定元素的属性，比如class、title、id、自定义属性等等。常用的是class与style绑定。
数据绑定最常见的形式就是使用 “Mustache” 语法（双大括号）的文本插值，但在绑定元素属性方面不能使用Mustache，v-bind是最好的选择。

### 绑定元素class语法
`v-bind:class=""` 中的值可以是一个对象、数组、甚至是三元表达式。
#### 1、对象：
```
<div class="static"
     v-bind:class="{ 'active': isActive, 'text-danger': hasError }">
</div>

data: {
  isActive: true,
  hasError: false
}
```
以上渲染为：`<div class="static active"></div>`

```
//computed属性加控制
<div v-bind:class="classObject"></div>

data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal',
    }
  }
}
```
#### 2、三元表达式
```
<div id="test" v-bind:class="[isActive ? 'active' : '', 'static']">
data: {
    isActive: false
}
```
始终添加 active ，但是只有在 isActive 是 true 时添加 static


### 元素内联样式
当 v-bind:style 使用需要特定前缀的 CSS 属性时，如 transform ，Vue.js 会自动侦测并添加相应的前缀，无需做兼容性处理。
```
<div v-bind:style="styleObject"></div>
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

## v-if
条件渲染指令，通常和v-else配合使用，需要将它添加到一个元素上。若渲染代码块则需要用template包装起来。
条件渲染在实际项目中运用很广泛，比如在展示一些东西时，并没有查询到，我们就需要给用户一些友好的提示。
```
<div class="test">
    <template v-if="length > 0">
        <ul>
            <li v-for="item in items">{{item.message}}</li>
        </ul>
    </template>
    <template v-else>
        <p>暂无数据</p>
    </template>
</div>

<script>
    var app = new Vue({
        el : ".test",
        data : {
            length : null,
            items : [
               {
                   "message":"aaa"
               },
               {
                   "message":"bbb"
               }
           ]
        },
    })
    app.length = app.items.length
</script>
```
若涉及多重条件渲染，则使用v-else-if，与js中if语句类似。
v-else-if 必须跟在 v-if 或者 v-else-if之后，v-else 元素必须紧跟在 v-if 元素或者 v-else-if的后面，否则它们不能被识别

**v-show**指令和**v-if**作用上大体相同，但v-show不能在template代码块中使用，v-show和v-if对于元素的控制可以理解成在css中display和visibility。即v-show 的元素会始终渲染并保持在 DOM 中。
关于更多v-show和v-if的区别，可以在[官网](https://cn.vuejs.org/v2/guide/conditional.html#v-if-vs-v-show)中了解，总的来说，v-if 有更高的切换消耗而 v-show 有更高的初始渲染消耗。因此，如果需要频繁切换使用 v-show 较好，如果在运行时条件不大可能改变则使用 v-if 较好
