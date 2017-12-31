---
title: Java基础知识总结（二）
date: 2016-01-19 10:51:23
tags: [Java]
categories: work
---

上篇总结了java的一些数据类型和发展之类，最后介绍了键盘录入对象，此篇继续总结java中的一些基础知识，接上篇。

<!-- more -->

## 一、使用Random类产生随机数
和上篇结尾介绍的键盘录入对象的格式和步骤类似，随机数的产生也分为三个步骤：
- 导包：import java.util.Random;(位置还是在package和class中间，快捷键ctrl+shift+o)
- 创建对象：Random r = new Random();
- 接收数据：int num = nextInt(a);  此处有一个整数a，表示产生[0,a)的随机数，不包括a
ps:若想产生[60,100]的随机数: int num = nextInt(41)+60;


## 二、数组
和其他语言一样，java中也有数组类型，但是定义数组的格式却不同。并且值得强调的是：在js中对同一个数组元素数据类型没有特殊要求，但在java中，要求同一个数组内的元素必须同一种数据类型。
### 2.1、一维数组:
#### 2.1.1、数组的定义：
- int[] arr 【推荐】
- int arr[]

#### 2.1.2、数组的初始化：
数组必须经过初始化才能使用，初始化的过程其实就是为数组元素分配内存空间，并赋值的过程。
a.动态初始化：只指定数组长度，由系统分配初始值:int[] arr = new int[5]; 即：arr.length=5
该初始值为给该数据类型的默认值:
基本数据类型的默认初始化：
byte short int long → 0 、char → '\u0000'、boolean → false、float double → 0.0						
引用数据类型的默认初始值是：null
b.静态初始化：给定数组元素具体值：int[] arr = new int[]{1,2,3,4,5,6}; 或 int[] arr = {1,2,3,4,5,6};

### 2.2、二维数组
二维数组其实就是元素为一元数组的数组。
A、定义：int[][] arr = new int[m][n]; 或者int[][] arr = { { 1, 2, 3 }, { 4, 5, 6 }, { 7, 8, 9 } };
B、和一维数组的区别：
- arr.length 表示的不在是数组的长度，而是一元数组的长度
- arr[0] 表示的不是第一个元素，而是第一个一维数组的地址值
- arr[0][0] 表示的是第一个一维数组的第一个元素

C、二维数组的便利：使用两个循环嵌套
```
int[][] arr = new int[m][n];
for(int y=0; y < m; y++) {
    for (int x = 0; x < n; x++) {
        System.out.print(arr[y][x] + "  ");
    } 
}
```

### 2.3、数组使用常报的错误：
java.lang.NullPointerException  空指针异常
java.lang.ArrayIndexOutOfBoundsException   数组越界异常


## 三、方法
将实现某一项特定功能的代码封装在一起，方便调用称之为方法，即js中的函数。使用Java方法时,需要明确**返回值类型**以及**参数类型和个数**。
### 3.1、方法定义格式：
A、有明确返回值类型的方法：
```
修饰符 返回值类型 方法名(参数类型 参数名1，参数类型 参数名2…) {
        函数体;
        return 返回值;
}
//求和方法 
public static int sum (int a,int b){
    return a+b;
};
```

B、无明确返回值类型的方法：
```
修饰符 void 方法名(参数类型 参数名1，参数类型 参数名2…) {
    函数体;
}
//打印标注数组格式
public static void print(int[] arr){
    System.out.print("[");
    for (int i = 0; i < arr.length; i++) {
        if(i==arr.length-1){
            System.out.println(arr[i]+"]");
        }else{
            System.out.print(arr[i]+",");
        }
    }
}
```

### 3.2、方法调用：
A、有明确返回值类型（比如返回值为一个数字）的方法的调用有三种方式：直接调用、输出调用、赋值调用;
以上求和方法为例：
- 直接调用：sum(3,4); 无意义，不会有输出
- 输出调用：System.out.println(sum(3,4));会输出7，但是若想对返回值加以处理，此种方法不是最优解
- 赋值调用：int sum = sum(3,4);System.out.println(sum);【推荐】

B、无明确返回值类型的方法只有一种调用方式，直接调用。
以上打印数组为例调用：
```
int[] arr = {1,2,3,4,5};
print(arr);     //直接输出   [1,2,3,4,5]
```

### 3.3、方法重载：
定义：在同一个类中，出现方法名相同，参数类型不同、个数不同、或者参数顺序不同的方法，称为方法重载。
特点：与返回值类型无关，只看方法名和参数列表；在调用时，虚拟机通过参数列表的不同来区分同名方法。

### 3.4、不同类型参数传递的相互影响
Java方法的参数可以为基本数据类型（byte\short\int\long\float\double\boolean\char）也可以为引用数据类型（类\接口\数组）。
A、参数为基本数据类型时，形式参数的改变不影响实际参数的改变。
```
//因为形参和实参都是局部变量，都是存储在栈中。在栈中开辟两块内存存放main方法（以及里面的实参）、baseData方法（以及里面的形参），
//因为两者是独立存在的，所以形参无法影响实参。
public static void main(String[] args) {
    int a = 10;
    int b = 20;
    System.out.println("a:"+a+",b:"+b); // 10 20
    baseData(a,b);   
    System.out.println("a:"+a+",b:"+b); // 10 20
}
public static void baseData(int a,int b){  
    a = b;  //a=20 
    b = a + b; //b=40
    System.out.println("a:"+a+",b:"+b);   // 20 40
}
```
B、参数为引用数据类型时，形式参数的改变会影响实际参数的改变。 
```
//由于new出来的东西存在堆内存中，且有特定的地址值，而形参和实参都指向堆内存该地址值，形参改变后，实参也会发生改变。

public static void main(String[] args) {
    int[] arr = {1,2,3,4,5,6,7};
    for (int i = 0; i < arr.length; i++) {
         System.out.print(arr[i]+" ");      //1 2 3 4 5 6 7  
    }
    changeArr(arr);                           
    for (int i = 0; i < arr.length; i++) {
         System.out.print(arr[i]+" ");      //1 4 3 8 5 12 7  
    }
} 
public static void changeArr(int[] arr){
    for (int i = 0; i < arr.length; i++) {
        if(arr[i]%2==0){
            arr[i]*=2;
        } 
    }
}
```


## 三、JVM的内存分配
Java 程序在运行时，需要在内存中的分配空间。为了提高运算效率，就对空间进行了不同区域的划分，因为每一片区域都有特定的处理数据方式和内存管理方式。
内存分配包括：
- 栈：存储的都是局部变量（在方法中定义的）
- 堆：只要是new后面的东西都存储在堆中（如：数组、对象等）
- 方法区（面向对象）
- 本地方法区（硬盘上的东西）
- 寄存去（给CPU）

注意：放在栈内存里的东西，执行完后马上被垃圾回收；而堆内的东西等到垃圾回收机制空闲时回收。 


## 四、Eclipse断点调试
和浏览器断电调试类似，双击Eclipse相应的行数，打断点 → 右键 → Debug as → Java Application → debug视图 → F6下一步
注意：有方法调用的时候，要想看到完整的流程，每个方法都要加断点，建议方法进入的第一条有效语句加断点

调节字体、主题等个性化设置在windows -- preferences 

