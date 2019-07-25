---
title: Class类文件结构
date: 2019-07-25 11:23:10
tags: Java
---

Class类文件通常是由Java源代码编译而来，不过由于Java的动态特性，我们完全可以将思路拓宽，用二进制流的角度来看待**Class类文件结构**这个主题。二进制流可以从哪里获取？`.class`文件、其它二进制文件、网络、数据库等等。

Class类文件具有以下特点：

- 是一组以**8位即1个字节**为基本单位的二进制流
- 不存在分隔符
- 如果一个数据项占用8个字节以上，则按照**Big-Endian**存储

一个Class文件的基本结构如下：

| 类型            | 名称                | 数量                  | 说明               |
| --------------- | ------------------- | --------------------- | ------------------ |
| u4              | magic               | 1                     | 魔数               |
| u2              | minor_version       | 1                     | Class文件次版本号  |
| u2              | major_version       | 1                     | Class文件主版本号  |
| u2              | constant_pool_count | 1                     | 常量池的大小       |
| cp_info         | constant_pool       | constant_pool_count-1 | 常量池             |
| u2              | access_flag         | 1                     | 访问标志           |
| u2              | this_class          | 1                     | 类索引             |
| u2              | super_class         | 1                     | 父类索引           |
| u2              | interfaces_count    | 1                     | 接口索引集合的大小 |
| u2              | inferfaces          | interfaces_count      | 接口索引集合       |
| u2              | fields_count        | 1                     | 字段表集合的大小   |
| field_info      | fields              | fields_count          | 字段表集合         |
| u2              | methods_count       | 1                     | 方法表集合的大小   |
| method_info     | methods             | methods_count         | 方法表集合         |
| u2              | attributes_count    | 1                     | 属性表集合的大小   |
| attributes_info | attributes          | attributes_count      | 属性表集合         |

