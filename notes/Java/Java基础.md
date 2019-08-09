# 数据类型

## 基本类型

基本类型又称为**原生类型 (primitive) **，它们在内存中直接保存相应的值。

Java 中的基本类型共有八种：

| 基本类型 | 大小    | 最小值           | 最大值                    |
| -------- | ------- | ---------------- | ------------------------- |
| boolean  | —       | —                | —                         |
| char     | 16 bits | Unicode 0        | Unicode 2<sup>16</sup> -1 |
| byte     | 8 bits  | -128             | +127                      |
| short    | 16 bits | - 2<sup>15</sup> | + 2<sup>15</sup> -1       |
| int      | 32 bits | - 2<sup>31</sup> | + 2<sup>31</sup> -1       |
| long     | 64 bits | - 2<sup>63</sup> | + 2<sup>63</sup> -1       |
| float    | 32 bits | IEEE754          | IEEE754                   |
| double   | 64 bits | IEEE754          | IEEE754                   |



## 引用类型



## 字面量



## 包装类型

有时候我们希望基本类型也具备对象的特性，因此就出现了包装类型，并且每一个基本类型都对应有一个包装类：

| 基本类型 | 包装类    |
| -------- | ------- |
| boolean  | Boolean |
| char     | Character |
| byte     | Byte |
| short    | Short     |
| int      | Integer |
| long   | Long      |
| float    | Float |
| double   | Double |



```java
Integer integer = new Integer(100);
```

### 自动装箱与自动拆箱

将基本类型自动转为包装类型的过程称为**自动装箱**：

```java
Character character = 'a';
```

将包装类型自动转为基本类型的过程称为**自动拆箱**：

```java
Integer integer = 100; // 装箱
int num = integer;     //拆箱
```

### 注意空指针异常

一旦使用了包装类，那就意味着



## 高精度数值



## 数组



