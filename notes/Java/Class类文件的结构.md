Class 类文件通常是由 Java 源代码编译而来，不过由于 Java 的动态特性，我们完全可以将思路拓宽，用二进制流的角度来看待 **Class 类文件结构**这个主题。二进制流可以从哪里获取？ `.class` 文件、其它二进制文件、网络、数据库等等。

Class 类文件具有以下特点：

- 是一组以 **8位即1个字节** 为基本单位的二进制流
- 不存在分隔符
- 如果一个数据项占用 8 个字节以上，则按照 **Big-Endian** 存储

一个Class文件的基本结构如下：

| 类型            | 名称                | 数量                  | 说明               |
| --------------- | ------------------- | --------------------- | ------------------ |
| u4              | magic               | 1                     | 魔数               |
| u2              | minor_version       | 1                     | Class 文件次版本号 |
| u2              | major_version       | 1                     | Class 文件主版本号 |
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

u1、u2、u3、u4 分别代表 1 个字节、2 个字节、3 个字节以及 4 个字节，而以 `_info` 结尾的类型则表示表结构。



## 魔数与 Class 文件的版本

最开始的魔数是固定的 `0xCAFEBABE` ，其唯一的作用就是确定该文件是否能被 JVM 所接受，换言之如果文件开头的 4 个字节不是 `0xCAFEBABE` 则 JVM 会拒绝执行该 Class 文件。

接下来的 4 个字节则代表了 Class 文件的版本号（次版本号与主版本号），JVM 同样会根据这 4 个字节决定是否接受该 Class 文件。需要注意的是，高版本的JVM能够向下兼容低版本的 Class 文件，如果但不能运行之后版本的 Class 文件。



## 常量池大小与常量池

常量池相当于 Class 文件的资源仓库，之所以这么说是因为 Class 文件有很多其它项目会指向常量池中的数据。常量池中主要保存着字面量与符号引用。

字面量主要有：

- 文本字符串
- 声明为 final 的常量值
- 其它字面量

符号引用则包括了：

- 类/接口的全限定名
- 字段的名称和描述符
- 方法的名称和描述符

很明显，常量池的大小是不固定的，所以在 Class 文件主版本号的后 **两个字节** 则用来 表示常量池的大小（即常量池中有多少个常量）。

常量池中的常量都是有索引的，并且**以 1 开始**，**以 n-1 结束** （ n 表示常量池的大小）。从 1 到 n-1 ，常量显然一共有 n-1 个，实际上还有一个索引 0 。Class 文件的其它项目可以借助索引来引用常量池中的常量数据，而特殊的索引 0 表示不引用常量池中的任何一个常量。

