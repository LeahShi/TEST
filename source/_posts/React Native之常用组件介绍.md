---
title: React Native之常用组件介绍
date: 2017-06-30 23:06:26
tags: [React Native,跨平台,移动端]
categories: work
comments: true
---

移动端适配是很重点的问题，接下来会用几篇总结布局相关的组件及技巧方法等；
上篇中环境搭建好了之后，分析代码就可以发现，render(){}内用到的所有标签，（RN中称之为组件）都不是我们熟知的，但因为之前有做过微信小程序的经验，布局的标签有相似的地方：
比如，容器标签在小程序中是view标签，在RN中就是View容器；文本标签前者为text,后者为Text；图片标签前者为image,后者为Image...应该说小程序有借鉴RN的思想。

<!-- more -->


## 一、使用组件前，需要将组件在前面申明定义：
```
import {
    AppRegistry,    //注册程序，通常只需要在index.js引用
    StyleSheet,     //样式
    Text,           //文本组件
    View,           //容器组件
    TextInput       //输入框组件
    ScrollView,     //可滚动的容器 可放不同的组件和视图
    TouchableHighlight,	    //可触摸组件
} from 'react-native';
```

## 二、常用内置组件说明：
### 1、View是容器组件：
   相当于div标签，是所有组件的父组件。在render(){return(  {/* 此处写布局 */}  )}布局元素内，最外层必须要有个大的标签包裹。
   注意不能把文本的样式（比如color、font-size等）卸载View组件上。

### 2、Text是文本组件：
文字只能放在该组件内，放在View内会报错。
文本组件的属性也是多重继承的，但是只适用于 Text组件之间的嵌套，倘若一个View > Text组件，将文本属性设置给View将会报错。
其余属性和css中是一样的，特殊的属性有：
- numberOfLines → 设置Text文本的行数（值为数字类型），若显示的内容超过了行数，其他信息不显示。
- textDecorationLine → 横线位置，相当于网页中的text-decoration ("none", 'underline', 'line-through', 'underline line-through')
- textDecorationStyle → 线的风格，("solid", 'double', 'dotted', 'dashed')
- writingDirection → 文本方向("auto", 'ltr', 'rtl')


### 3、Image是图片组件
i、该组件既可以是以单标签的形式结束，又可以以双标签的形式结束（**可作为背景图片**）。
ii、该组件**必须加上宽高方可显示**，且有**resizeMode**这一属性，同微信小程序。
 Image.resizeMode.cover：图片居中显示，没有被拉伸，超出部分被截断；
 Image.resizeMode.contain：容器完全容纳图片，图片等比例进拉伸；
 Image.resizeMode.stretch： 图片被拉伸适应容器大小，有可能会发生变形。

iii、图片组件加载图片的几种方法： 
- 本地资源：（相对于当前的路径检索， ./ 不可少）
```
<Image source={require('./img/2.png')} style={{width: 50,height:50}}/>
```
- 网络资源：
```
<Image source={uri(https://test.png)} style={{width: 50,height:50}}/>
```
- APP中的资源（适用于已经用原生语言开发过的APP）
```
 <Image source={require('image!icon_homepage_map')} style={{width: 50,height:50}}/>
```
**小demo（点击切换图片）**
```
class ToggleImage extends PureComponent {
    //初始值
    constructor(props:Object) {
        super(props)
        this.state = {
            isRefreshing: false,
            isNormal: true,
            img_change: require('./../../img/Mine/avatar.png')
        }
    }
    render(){
        return(
            <View style={styles.testBox}>
                <TouchableOpacity
                    activeOpacity={0.6}
                    //onPress 绑定在View上无效
                    onPress={() => this.changeImage()}
                >
                    <View style={styles.sonBox}>
                        <Image source={this.state.img_change} style={styles.icon} />
                    </View>
                </TouchableOpacity>
            </View>
        )
    }
    changeImage() {
        this.state.isNormal ?
            this.setState({img_change: require('./../../img/img_normal.png'), isNormal: false}) :
            this.setState({img_change: require('./../../img/img_active.png'), isNormal: true})
    }
}
```

### 4、TextInput

### 5、ScrollView
### 6、ListView
### 7、TouchableHighlight
### 8、自定义组件

## 三、布局相关
### 1、FlexBox布局
在React Native中主要是用来Flex布局，关于flex布局可以参见 [之前总结的文章](https://leahshi.github.io/2017/04/20/flex%E5%B8%83%E5%B1%80/) ,
不同的是，在RN中主轴为column，交叉轴为row，和一般网页相反。使用属性flex-direction

### 2、react-native 单位换算：
设计图采用的是PX，而 RN 默认采用的dp，书写样式的时候不需要带单位。
设计稿原型：基于iphone6（1334 x 750 px）
设置尺寸的时候，可以转换为设计稿标注图的一半，比如一个按钮尺寸为 150 * 80，设置为75 * 40