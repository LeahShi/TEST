---
title: EditPlus初次使用设置
date: 2016-06-20 23:06:26
tags: [EditPlus,工具]
categories: work
comments: true
---

最近向学习点java语言，所以选用了比较轻量级的编辑器  EditPlus。

<!-- more -->

一、创建一个java文件的时候，会发现花括号的格式和C++格式一样，所以要设置一下

菜单栏 → 工具 → 参数设置 → 文件 → 模板

选择java模板，点击载入，template.java文件就处于被编辑状态了，将其编辑成以下格式：
```
class ^! {
	public static void main(String[] args){
		System.out.println("Hello World!");
	}
}

```

二、在编辑器中敲下关键字再敲下回车自动补充双括号：

菜单栏 → 工具 → 参数设置 → 文件 → 设置 & 语法

选择java类型，点击加载java.acp文件，改文件处于编辑状态，将以下内容替换源文件内容：
```
#TITLE=Java/C#
; EditPlus Auto-completion file v1.0 written by ES-Computing.
; This file is provided as a default auto-completion file for Java and C#.

#CASE=y

#T=if
if (^!) {
}
#T=while
while (^!) {
}
#T=for
for (^!; ; ) {
}
#T=switch
switch (^!) {
case :

}
#T=do
do {
}
while (^!);
#T=class
class ^! {
}
#T=try
try {
	^!
}
catch () {
}
#T=interface
interface ^! {
}
#T=namespace
namespace ^! {
}
#
; C# only
#T=foreach
foreach (^!) {
}
#T=get
get {
	^!
}
#T=set
set {
	^!
}
#T=lock
lock (^!) {
}
#T=struct
struct ^! {
}
#

```


以上两个设置，倘若将EditPlus安装在C盘，会出现无法保存的情况。将其拖到桌面编辑保存完成后，覆盖C盘文件。

三、使用快捷键编译java文件：

菜单栏 → 工具 → 参数设置 → 工具 → 用户工具

设置编译命令：
- 点击组名，修改组名为javac;
- 菜单文字：javac
- 命令：选择安装jdk的项目目录下的bin选中javac.exe
- 参数：选择文件名
- 初始目录：选择文件目录
- 动作：选择捕捉输出
- 点击应用，点击确定

设置输出命令：
- 点击组名，修改组名为java;
- 菜单文字：java
- 命令：选择安装jdk的项目目录下的bin选中java.exe
- 参数：选择文件名（不含扩展名）
- 初始目录：选择文件目录
- 动作：无
- 点击应用，点击确定

以上设置完毕后，可以在菜单中工具栏中看到javac和java命令的快捷键

四、使用EditPlus创建的java文件自动生成备份文件：

菜单栏 → 工具 → 参数设置 → 文件 

将“保存时创建备份文件”取消掉。

