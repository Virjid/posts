---
title: shell编程
date: 2019-06-25 11:58:58
tags: 
    - shell编程
    - Linux操作系统
---

## 认识shell脚本
### shell 与 shell script
通常，大家会把shell脚本直接简称为shell，将`shell`与`shell script`等同起来，但还是要清楚地认识到严格意义上这是不同的两个概念。

shell指的是一种程序，它为用户访问系统内核服务提供了一种方式（通过命令行）。

shell还允许用户编写由shell命令组成的程序并保存于文件中，shell可以读取、解析并执行该文件，该文件就被称为shell脚本。

### shell的特点
- shell脚本编写起来简单方便
- 具有一定的逻辑行为能力
- 能够轻易实现很多任务的简单化、自动化，尤其是批量处理文件这样的任务
- 不适合字符串操作、数学计算、数据库操作等复杂操作

### First shell script
首先创建一个文本文件，命名为`demo.sh`，然后用文本编辑器（如Vim）直接打开并编辑：
```shell
#!/bin/bash
echo Hello~
echo $PATH
```
其中以`#！`开头的第一行用来标识该脚本由哪一个脚本解释器执行，在这里表示使用bash来运行该脚本。bash是shell的具体实现之一，并且默认安装于大部分的Linux系统上。

### 运行shell脚本
运行的方式有两种：
#### 作为解释器参数运行
```
/bin/bash ./demo.sh

# /bin/bash 是bash的路径
# ./demo.sh 是脚本的路径
# 一般目录/bin都被加入到环境变量PATH中，所以原命令可以简写为 bash ./demo.sh
# bash只是shell的一种，还可以使用其它shell来运行脚本
```
如果使用该方式执行脚本，命令行中就已经明确指明所使用的解释器，所以此时脚本中无需`#!`行。
#### 作为可执行程序
首先要为脚本文件赋予相应的权限
```
chmod +rx ./demo.sh  #赋予脚本可执行、可读取权限
```
注意，使用该方式执行的脚本文件的**第一行**必须是`#!`行。

然后执行脚本即可
```
./demo.sh
```
> 顺便说一下，shell的注释是以`#`标识的，但排除`#!`

## 变量
### 定义变量
shell中定义变量的方式和其他语言中的定义方式是差不多的：
```shell
#!/bin/bash
date = "2019-06-25" # 定义变量
```
### 使用变量
在shell中使用变量时，需要在变量名前添加美元符`$`：
```shell
#!/bin/bash
date="2019-06-25"
echo $date # 使用变量
```
> 注意等号两遍不能有空格！比如`date = "2019-06-25"`是错误的写法。

双引号里的美元符会被视为变量标识符：
```shell
#!/bin/bash
date="2019-06-25"
echo "The date of today is $date" # 使用变量



# 运行结果：
# virjid @(๑•̀ㅂ•́)و✧ :~/code/shellstudy$ ./demo.sh
# The date of today is 2019-06-25
```
使用变量的写法除了直接加美元符，还可以写成`${变量名}`，在一些情况下我们不得不用这种写法：
```shell
#!/bin/bash
year=2019
month=06
day=25

# 为了让解释器正确识别变量的边界而添加花括号
echo "${year}_${month}_${day}" 



# 运行结果：
# virjid @(๑•̀ㅂ•́)و✧ :~/code/shellstudy$ ./demo.sh
# 2019_06_25
```

### 变量类型
按照变量的作用范围，变量可以分为局部变量和环境变量：
- 局部变量只对当前的shell实例有效。
- 环境变量是描述操作系统运行环境的一些变量，对所有shell实例都有效。

### 数据类型
在shell中，只有字符串类型和数字类型可用

## 字符串
在shell中字符串的表示很随意：
- 使用单引号
- 使用双引号
- 不用引号

### 使用单引号
- 单引号中的内容不会被做修改
- 单引号不支持转义字符
- 单引号中不能出现单个单引号，但可成对出现，作为字符串拼接使用

### 使用双引号
- 双引号可以将变量替换为相应的值
- 双引号中可以使用转义字符

### 拼接字符串
```shell
#!/bin/bash
user_name=virjid
greeting1="Hello, $user_name"
greeting2='Hello, '$user_name
echo greeting1
echo greeting2


# 运行结果：
# virjid @(๑•̀ㅂ•́)و✧ :~/code/shellstudy$ ./demo.sh
# Hello, virjid
# Hello, virjid
```
### 获取字符串长度
```shell
user_name="virjid"
echo ${#user_name} #输出 6
```
### 获取子串
```shell
user_name="virjid"
echo ${user_name:0:3}

# 输出 vir，表示从第0个字符开始截取，共截取3个字符
# 注意，这里认为shell是以索引0作为起点的
```

### 获取字符所在位置的索引
获取字符v或字符i的索引**并加1**(即以1作为起点的索引、位置)：
```shell
#!/bin/bash
user_name="virjid"
index=`expr index "$user_name" vi`
echo $index # 输出1

# 表达式会返回最小的索引值
```

## 数组
### 定义数组
在shell中只能定义一维数组：
```
数组名=(值1 值2 ... 值n)
```
### 访问数组元素
访问方式如下：
```
${数组名[索引值n]}
```
使用`@`可以获得所有的元素：
```
${数组名[@]}
```
### 与数组相关的长度
如果要查询数组的元素个数，即数组长度:
```
${#数组名[@]}  或  ${#数组名[*]}
```
如果想知道数组中某一个元素的长度：
```
${#数组名[索引值n]}
```

## expr命令

shell本身并不支持数学运算，但可以借助其它命令来进行计算，如`expr命令`

```shell
#!/bin/bash
a=10
b=20

echo `expr $a + $b`   # 加
echo `expr $a - $b`   # 减
echo `expr $a \* $b`  # 乘
echo `expr $a / $b`   # 除
echo `expr $a % $b`   # 取余
echo `expr $a == $b`  # 判断是否相等，相等返回1，不相等返回0
echo `expr $a != $b`  # 判断是否不等，相等返回0，不相等返回1


# 运行结果：
# virjid @(๑•̀ㅂ•́)و✧ :~/code/shellstudy$ ./demo.sh
# 30
# -10
# 200
# 0
# 10
# 0
# 1
```



## test命令

test命令用于判断某一个条件是否成立，所谓的条件可以分为三类：数值测试、字符串测试、文件测试。另外test命令还有一个等价符号 `[` 。



test命令会对由参数组成的条件进行测试，若为真则返回0，若为假则返回1，但要注意返回的是程序退出码，test并不会将结果打印至终端。因此可以通过`echo $?`来查看测试结果。

### 字符串测试

```shell
#!/bin/bash
# _______字符串测试_______

a='Hello!'
b='hello'
c=''

test $a = $b; echo $?   # 参数'='左右两个字符串若相等，则为真，反之为假

test $a != $b; echo $?  # 参数'!='左右两个字符串若不相等，则为真，反之为假

test -z $c; echo $?     # 参数'-z'后面的字符串若为空，则为真，反之为假

test -n $b; echo $?     # 参数'-n'后面的字符串若不为空，则为真，反之为假
```



### 数值测试

```shell
#!/bin/bash
# _______数值测试_______

a=100
b=10
c=10

test $a -eq $b; echo $?  # 参数'-eq'左右两个数值若相等，则为真，反之为假

test $a -ne $c; echo $?  # 参数'-ne'左右两个数值若不等，则为真，反之为假

test $c -gt $b; echo $?  # 参数'-gt'左边数值若大于右边数值，则为真，反之为假

test $c -ge $b; echo $?  # 参数'-ge'左边数值若大于等于右边数值，则为真，反之为假

test $a -lt $b; echo $?  # 参数'-lt'左边数值若小于右边数值，则为真，反之为假

test $a -le $b; echo $?  # 参数'-le'左边数值若小于等于右边数值，则为真，反之为假
```



### 文件测试

```shell
#!/bin/bash
# _______文件测试_______

file='./demo.sh'

test -e $file; echo $?   # 文件存在则为真，反之为假

test -w $file; echo $?   # 文件可写则为真，反之为假

test -r $file; echo $?   # 文件可读则为真，反之为假

test -x $file; echo $?   # 文件可执行则为真，反之为假

test -s $file; echo $?   # 文件存在且至少有一个字符则为真，反之为假

test -d $file; echo $?   # 该文件是目录则为真，反之为假

test -f $file; echo $?   # 该文件是普通文件则为真，反之为假

test -c $file; echo $?   # 该文件为字符型特殊文件则为真，反之为假

test -b $file; echo $?   # 该文件为特殊块文件则为真，反之为假
```



### test 与 [

`test` 与 `[` 是等价的，但是 `[` 要与 `]`**成对出现**，[ 是要运行的程序名，而 ] 则是用来明确指示条件字符的界限的：

```shell
#!/bin/bash

[ 100 -gt 10 ]; echo $?

# 等价于

test 100 -gt 10; echo $?
```

需要特别注意的是，`[` 的右侧和 `]` 的左侧必须至少有一个空格。以命令行`[ 100 -gt 10 ]`为例，若 `[` 的右侧没有空格，则表示shell去运行一个名为 `[100` 的程序;若 `]` 的左侧没有空格，则 `10]` 就会被视为一个参数。









