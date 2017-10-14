---
title: Java基础知识总结（三）
date: 2016-01-25 10:51:23
tags: [Java]
categories: work
---

最近在网上刷了一些基础题目，有些题目的思路想记录下来。
可能以后随着经验丰富再回头看，可能那些思路已经是很常规的套路，可对于现阶段的自己还是想多多总结。

<!-- more -->

## 一、寻找数字类逻辑题
### 1.1、判断101-200之间有多少个素数，并输出所有素数。
分析：素数的定义为，除了1和自己，不能被任何数整除。
实现步骤：循环遍历101-200（i），在循环2- i,i取不到（j），两个循环嵌套，如果i%j==0，则该数不为素数。

### 1.2、将一个正整数分解质因数。
例如：输入90,打印出90=2*3*3*5。
分析：用该数n除以最小质数2，并用将商从新赋值给n（递归思想）;最后很重要的一步，将循环变量在变为初始化2，因为分解质因数可以重复。
```
public static void main(String[] args) {
     Scanner sc = new Scanner(System.in);
     int n = sc.nextInt();
     
     System.out.print(n+"=");
     for (int k = 2; k <= n/2; k++) {
         if(n%k==0){
             //输出k
             System.out.print(k+"*");
             //重新赋值n 递归
             n = n/k;
             //k返回初始值
             k = 2;
         }
     }
     
     //最后一次递归的n无法再分解，直接输出
     System.out.println(n);
}
```

### 1.3、一个数如果恰好等于它的因子之和，这个数就称为“完数”,找出1000以内的所有完数。
例如6=1＋2＋3。
分析：首先循环1-1000的数字（i），在嵌套一次循环寻找i的因子（j）,j必须小于i。将i的因子相加如果恰好与i相等,输出i.
```
public static void main(String[] args) {
     for (int i = 1; i <= 1000; i++) {
         //每次循环前要清零
        int sum = 0;
        for (int j = 1; j < i; j++) {
            if(i%j==0){
                sum+=j;
            } 
        }
        if(i==sum){
            System.out.print(i+",");
        }
    }
    
}
```

### 1.4、题目：求s=a+aa+aaa+aaaa+aa...a的值，其中a是一个数字。例如2+22+222+2222+22222(此时 共有5个数相加)，几个数相加有键盘控制。
```
public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    System.out.println("请输入要重复的数字：");
    int a = sc.nextInt();
    System.out.println("请输入相加的个数：");
    int b = sc.nextInt();
    int sum = 0;
    //循环相加的个数
    for (int i = 1; i <= b; i++) {
        //得到每个数字  j取值范围1-i;
        for (int j = 0; j < i; j++) {
            int item = (int) (a*Math.pow(10, j));  //2  2 20
            sum+=item;
        }
    }
    System.out.println("sum:"+sum);
}
```

### 1.5、打印杨辉三角
```
1
1 　1
1 　2 　1
1　 3 　3　 1
1　 4　 6 　4 　1
public static void main(String[] args) {
    System.out.println("输入行数：");
    Scanner sc = new Scanner(System.in);
    int lines = sc.nextInt();
    int[] a = new int[lines + 1];
    int previous = 1;
    for (int i = 1; i <= lines; i ++){
        for (int j = 1; j <= i; j++){
            int current = a[j];
            a[j] = previous + current;
            previous = current;
            System.out.print(a[j] + " ");
        }
        System.out.println();
    }
}
```



































 


## 打印图形类逻辑题