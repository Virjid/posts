---
title: Java字符串
date: 2019-07-04 12:56:15
tags: Java
---

## 字符串基本概念

在Java中，字符串的本质是一个Unicode字符序列，类似于一个char类型的数组，但两者的区别还是挺大的。字符串类型并不是Java的原生类型，而**是一个引用类型，叫做String类**。

### 创建字符串

使用**双引号**将字符序列括起来即可创建一个字符串：

```java
String name = "Virjid";
```

因为String属于引用类型，所以也可以使用new运算符来创建字符串：

```java
String name = new String("Virjid");
```

两种方式是等价的，仅使用双引号不过是使用new操作符的简写方式而已。

### 空串

空串是指不含任何字符的串：

```java
String empty = "";
```

### 空白串

空白串不同于空串，由任意多个空白字符(通常指空格、制表符\t、回车换行符\n)的连续组合所得到的串称为空白串。总得来说，空白串中是含有字符的，但空串是不含有任何字符的。

```java
String whitespace = " \t \t    \t";
```

### 单字符串

只含有一个字符的串称为单字符串，但是需要注意单字符串不等同于字符：

```
String singleString = "a";
char singleChar = 'a';
// "a" ≠ 'a'
```

### 子串

串中任意个连续的字符组成的子序列称为该串的**子串**，Java中可以使用成员方法`substring`来提取一个子串：

```java
String sayHello = "Hello World!";
String world = sayHello.substring(6, 11);

System.out.print(world); // world
```

> substring 方法的第一个参数表示从串的哪个位置**开始**提取子串，而第二个参数则表示**不想**提取的第一个字符位置。
>
> 对于 `s.substring(a, b)` 而言，**(b-a) 表示子串的长度**。

### 串的拼接

Java中可以使用加号(+) 来拼接两个字符串：

```java
String sayHello = "Hello" + " " + "world" + "!";

System.out.print(sayHello); // Hello world!
```



## 从内存模型中看字符串

### 关于Java字符串的不可修改

程序运行时，一个字符串就会占据一定的内存。所谓字符串不可被修改的意思是指：当分配一块存储空间用于存放某一个字符串后，Java便不允许我们去重写该存储空间的值了。

如果我们想要对字符串s1进行修改，我们只能重新开辟一块存储空间（定义一个变量s2）用来存放修改的结果，而s1的值并没有发生任何改变：

```java
String s1 = "hello";
String s2 = s1+" world";

System.out.println(s1); // hello
System.out.println(s2); // hello world

// 只能将拼接的结果存放在一个新的内存空间
```

### 字符串常量池



### 空串与NULL



### 判断两个字符串是否相等



## 码点与代码单元



## 构建字符串



## String API