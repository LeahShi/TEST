---
title: String对象常用API
date: 2016-02-08 10:51:23
tags: [Java]
categories: work
---

这几天开始学习Java，整个人有点分裂，因为JavaScript的一些知识先入为主，两种语言有相似的地方，也有截然不同的东西。
Java是强类型语言，JavaScript是弱类型语言,前者数据类型分为：基本数据类型（四类八种）、和引用数据类型（类、接口、数组）。
后者分为简单数据类型（也称为基本数据类型）：Boolean、Number、String、Null、Undefined、和复杂数据类型。
今天介绍的String字符串，在JS中将String划归为简单数据类型，在Java中，String是一个类，属于引用数据类型。

<!-- more -->

## 一、String对象的构造方法：
### 1.1、把字符串数据封装成字符串对象：
```
String s = new String("HelloWorld");
String s1 = "HelloWorld";
```

以上两者都可以创建一个字符串对象，但是s不等于s1，因为两者的地址值不同。前者是new出来的东西在内存空间中存储在堆中，后者直接存储在方法区的常量池中。
字符串内容是储存在方法区的常量池中的，为了方便重复使用。new出来的也是，只不过从堆再指向方法区的常量池而已。

### 1.2、将字符数组，封装成字符串对象：
```
String s = new String(char[] 字符数组名);
String s = new String(char[] 字符数组名,int index,int count);
```

以上都是讲字符数组封装成字符串对象，后者试讲将字符数组中，索引为index的元素开始数count个，封装成字符串对象。 

## 二、String判断功能;  【返回boolean类型】
- s1.equals(s2)  	   //字符串s1和字符串s2是否完全相同  【账户名密码登录】
- s1.equalsIgnoreCase(s2)  //忽略大小写 是否相同         【验证码】
- s1.starWith(str)	   //字符串s1是否以字符串str开头       
- s1.endWith(str)	   //是否以str 结尾		

## 三、String获取功能：
- s1.length()			//获取字符串对象的长度
- s1.charAt(int index)		//获取字符串对象的某个字符【用于遍历字符串】	
- s1.indexOf(String str)   	   //字符串str在字符串s1中的位置  s1不存在str则返回-1
- s1.subString(int start)		//截取索引从start开始到最后一位的字符串对象
- s1.subString(int start,int end)	//包括start不包括end

## 四、String转换功能
- s1.toUpperCase()	//全都转换为大写
- s1.toLowerCase()	//全都转换为小写
- s1.toCharArray()	//将字符串对象转换为char数组【可用于字符串遍历】

## 五、String去除左右两边的空格以及分割功能
- s1.trim()  //只能去除左右两边的空格  不能去中间的
- String s = "11,ss,dd,ff";
  String[] arr = s.split(","); //在遍历arr即可得到分割后的字符串

## 六、StringBuilder对象
由于字符串拼接会产生很多内存垃圾，而StringBuilder对象无论追加多少个，都存在同一个地方，所以引入StringBuilder对象。
### 6.1、StringBuilder构造方法：
StringBuilder sb = StringBuilder();
- 容量：sb.capacity()	  //理论值
- 长度：sb.length()   //实际值

### 6.2、StringBuilder的添加功能和反转功能：
- 添加功能  StringBuilder s = sb.append("aa").append("bb").append("vv);
无论append多少次，返回的都是对象，输出s==sb  返回true  地址相同 
- 反转功能    System.out.print(s.reverse())   //vvbbaa
	
### 6.3、StringBuilder和String相互转换：
```
StringBuilder → String:
	StringBuilder sb = StringBuilder();
	String s = sb.toString();

String → StringBuilder:
	 String str = "aa";
	 StringBuilder sb = StringBuilder(str);
```

StringBuilder和String都有各自强大的功能，做字符串拼接和反转时，可以转换为StringBuilder来实现。操作字符串的获取、判断、转换功能时，转换为String来实现。

## 七、其他
### 7.1、键盘录入String
```
Scanner sc = new Scanner(System.in);
String str = sc.nextLine();
```

### 7.2、判断字符是大小写或者数字的方法
在ASC码表中，可以查到‘A’= 65;'a'=97;'0'=48,且26个大小写字母，0-9都是连续的。所以有以下规律：
大写   str>= 'A'  65    && str<='Z'
小写   str>='a'   97   &&  str<='z'
数字   str>='0'   48   && str<='9'
 
### 7.3、字符串的反转：
思路一： 【推荐】
    转化为StringBuilder，使用reverse()方法
思路二：
    String s = "";
    将字符串对象转换为字符数组
    char chs[] = str.toCharArray();
    再倒着遍历数组即可
s+=chs[i];
思路三：
  将chs数组反转，索引start end思想  在遍历chs  最后字符串拼接 




	

	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	/n  换行   
	 *  // 一个斜杠/
	 *  /t 相当于tab键
	 *  /"  "
	
	
	
	
	
	
















