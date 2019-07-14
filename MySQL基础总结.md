---
title: MySQL基础总结
date: 2019-06-28 14:34:17
tags: 
    - MySQL
    - 数据库
---

## MySQL命令行

### 连接到MySQL的服务器

当安装MySQL时，通常都会自带一个命令行程序`mysql`，它是一个可以与MySQL服务器进行通信的客户端实现。

mysql命令行的参数有很多，一开始仅需要记住几个有关连接服务器的参数即可：

```
virjid @(๑•̀ㅂ•́)و✧ :~$ mysql -u root -h 127.0.0.1 -P 3306 -p

# -u 即user，表示连接服务器时所使用的用户名
# -h 即host，表示要连接的服务器IP地址，默认为localhost，即127.0.0.1
# -P 即port，表示要连接的服务器端口号，默认为3306
# -p 即password，表示所使用的用户需要密码才能连接
```

参数`-p`后面不需要跟上密码，因为密码是不能外泄的，所以输入mysql连接服务器命令敲击回车后，mysql会以不回显的方式要求你输入密码。

### 选择数据库

一个数据库管理系统一般都会管理多个数据库，所以当连接到MySQL服务器时，还需要明确指出想要操作的数据库。 

我们可以使用`show databases;`来查看可以操作的数据库，使用`use <数据库名>`来选择即将操作的数据库：

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```



### 查看数据库中的表

当使用use选择完数据库时，就可以使用`show tables;`来查看数据库中的所有表：

```
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| component                 |
| db                        |
| default_roles             |
| engine_cost               |
| func                      |
| general_log               |

...

# 这里我并没有把sys数据库的所有表给展示出来
```



如果要查看表的具体结构则可使用`describe <表名>;`，其中describe可以**简写为desc**：

```
mysql> desc time_zone;
+------------------+------------------+------+-----+---------+----------------+
| Field            | Type             | Null | Key | Default | Extra          |
+------------------+------------------+------+-----+---------+----------------+
| Time_zone_id     | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
| Use_leap_seconds | enum('Y','N')    | NO   |     | N       |                |
+------------------+------------------+------+-----+---------+----------------+
2 rows in set (0.01 sec)

```



## 检索数据基础

### 简单检索

检索单个列

```sql
select prod_name from products;
```

检索指定的多个列

```sql
select prod_id, prod_name, prod_price from products;
```

检索所有列

```sql
select * from products;
```



### DISTINCT关键字：检索不同的行

```sql
select distinct vend_id
from products;
```



### LIMIT子句：分页检索

返回检索结果的前5行：

```sql
select prod_name
from products
limit 5;
```

从检索结果的第0行开始（包括第0行），取5行返回

```sql
select prod_name
from products
limit 0, 5;
```

> 上面的两种检索方式，最终返回的结果应该是一样的，毕竟所谓的**从检索结果的第0行开始（包括第0行），取5行返回**就是**返回检索结果的前5行**的另一种表述方式。需要注意的是，表的开始行被认为是第0行而不是第1行。



### ORDER BY子句：排序

按单个列排序

```sql
select prod_name
from products
order by prod_name;
```

按多个列排序

```sql
select prod_name
from products
order by prod_price, prod_name;
```

指定排序方向

```sql
select prod_name
from products
order by prod_price desc, prod_name asc;
```



## 使用WHERE子句进行数据过滤

### WHERE子句常用操作符

| 操作符 | 说明 |
| ---- | ---- |
| = | 等于 |
| <> | 不等于 |
| != | 不等于 |
| < | 小于 |
| <= | 小于等于 |
| > | 大于 |
| >= | 大于等于 |
| BETWEEN ... AND ... | 在指定的两个值之间 |



### 基本的数据过滤

检查单个值

```sql
select prod_name, prod_price
from products
where prod_name='fuses';
```

检查不匹配的值

```sql
select vend_id, prod_name
from products
where vend_id <> 1003;
```

范围值检查

```sql
select prod_name, prod_price
from products
where prod_price between 5 and 10;
```

空值检查

```sql
select prod_name
from products
where prod_price IS NULL;
```



### 组合多个简单条件

AND操作符

```sql
select prod_id, prod_price, prod_name
from products
where vend_id=1003 and prod_price<=10;
```

OR操作符

```sql
select prod_name, prod_price
from products
where vend_id=1002 or vend_id=1003;
```

> 多数SQL实现中，在处理OR操作符前，会优先处理AND操作符，即AND的优先级高于OR。

IN操作符

```sql
select prod_name, prod_price
from products
where vend_id IN (1002, 1003)
order by prod_name;
```

> IN操作符在语法层面上可以认为是多个OR操作符的简写。而且IN操作符具有如下优点：
>
> - IN操作符在很多时候可以让语法变得简单清楚且直观
> - IN操作符一般比OR操作符清单执行更快
> - IN操作符后面可以使用子查询语句，支持更加复杂的查询

NOT操作符

```sql
select prod_name, prod_price
from products
where vend_id not in (1002, 1003)
order by prod_name;
```

> MySQL支持使用NOT对IN、BETWEEN和EXISTS子句取反。

## 计算字段和数据处理函数

计算字段并不实际存在于数据库表中，而是在运行SELECT语句时创建。使用计算字段，通常是因为存储在数据表中的数据并没有满足实际的需求。

### CONCAT函数：拼接字段

```mysql
select concat(vend_name, '(', vend_country, ')')
from vendors
order by vend_name;

```

结果：

```
+-------------------------------------------+
| concat(vend_name, '(', vend_country, ')') |
+-------------------------------------------+
| ACME(USA)                                 |
| Anvils R Us(USA)                          |
| Furball Inc.(USA)                         |
| Jet Set(England)                          |
| Jouets Et Ours(France)                    |
| LT Supplies(USA)                          |
+-------------------------------------------+
6 rows in set (0.00 sec)
```

### 使用别名

`concat(vend_name, '(', vend_country, ')')`这样的字段名既不方便阅读，也不方便客户端应用引用该列，我们需要为计算字段取一个别名：

```sql
select concat(vend_name, '(', vend_country, ')') AS vend_title
from vendors
order by vend_name;
```

结果：

```
+------------------------+
| vend_title             |
+------------------------+
| ACME(USA)              |
| Anvils R Us(USA)       |
| Furball Inc.(USA)      |
| Jet Set(England)       |
| Jouets Et Ours(France) |
| LT Supplies(USA)       |
+------------------------+
6 rows in set (0.00 sec)
```

在字段的后面使用AS关键词即可创建别名，在MySQL中AS可以省略。

### 算术运算

可以将检索表中的各个列进行算术运算，然后将结果作为计算字段返回给客户端。

```sql
select prod_id, quantity, item_price, 
       quantity*item_price AS expanded_price
from orderitems
where order_num=20005;
```

结果：

```
+---------+----------+------------+----------------+
| prod_id | quantity | item_price | expanded_price |
+---------+----------+------------+----------------+
| ANV01   |       10 |       5.99 |          59.90 |
| ANV02   |        3 |       9.99 |          29.97 |
| TNT2    |        5 |      10.00 |          50.00 |
| FB      |        1 |      10.00 |          10.00 |
+---------+----------+------------+----------------+
4 rows in set (0.01 sec)
```

### 文本处理函数

| 函数             | 说明                       |
| ---------------- | -------------------------- |
| Left/Right       | 返回串左边/右边的字符      |
| Length           | 返回串的长度               |
| char_length      | 返回串的字符个数           |
| Locate           | 找出串的一个字串           |
| Lower/Upper      | 将串转换为小写             |
| Trim/LTrim/RTrim | 去掉串两边/左边/右边的空格 |
| Soundex          | 返回串的SOUNDEX值          |
| SubString        | 返回子串的字符             |

### 日期和时间处理函数

| 函数        | 说明                           |
| ----------- | ------------------------------ |
| AddDate     | 增加一个日期（天、周等）       |
| AddTime     | 增加一个时间（时、分等）       |
| CurDate     | 返回当前日期                   |
| CurTime     | 返回当前时间                   |
| Date        | 返回日期时间的日期部分         |
| DateDiff    | 计算两个日期之差               |
| Date_Add    | 高度灵活的日期运算函数         |
| Date_Format | 返回一个格式化的日期或时间串   |
| Day         | 返回一个日期的天数部分         |
| DayOfWeek   | 对于一个日期，返回对应的星期几 |
| Hour        | 返回一个时间的小时部分         |
| Minute      | 返回一个时间的分钟部分         |
| Month       | 返回一个日期的月份部分         |
| Now         | 返回当前日期和时间             |
| Second      | 返回一个时间的秒部分           |
| Time        | 返回一个日期时间的时间部分     |
| Year        | 返回一个日期的分钟部分         |

### 数值处理函数

| 函数 | 说明               |
| ---- | ------------------ |
| Abs  | 返回一个数的绝对值 |
| Cos  | 返回一个角度的余弦 |
| Exp  | 返回一个数的指数值 |
| Mod  | 返回除操作的余数   |
| Pi   | 返回圆周率         |
| Rand | 返回一个随机数     |
| Sin  | 返回一个角度的正弦 |
| Sqrt | 返回一个数的平方根 |
| Tan  | 返回一个角度的正切 |

> 注意，在数值处理函数中，有关角度的运算采用的是**弧度制**。



## 聚集函数

聚集函数（aggregate function）指运行在**行组**上，计算和返回**单个值**的函数。具体来讲，当我们检索数据时，可以将数据分为多个组，而聚集函数可以对每一个组进行相关的运算，且每组仅返回一个值。例如统计每组有多少行数据。

在未分组时，则可认为只有一个组，该组包含了所有的检索数据。

### 常见的聚集函数

| 函数  | 说明             |
| ----- | ---------------- |
| AVG   | 返回某列的平均值 |
| COUNT | 返回某列的行数   |
| MAX   | 返回某列的最大值 |
| MIN   | 返回某列的最小值 |
| SUM   | 返回某列值之和   |

### 使用举例

AVG函数

```sql
select avg(prod_price) as avg_price
from products;
```

COUNT函数

```sql
select count(*) as num_cust
from customers;
```

MAX函数

```sql
select max(prod_price) as max_price
from products;
```

MIN函数

```sql
select min(prod_price) as min_price
from products;
```

SUM函数

```sql
select sum(quantity) as items_ordered
from orderitems
where order_num=20005;
```

## 对数据进行分组

### GROUP BY子句：创建分组

只需要使用`GROUP BY`子句即可对数据进行分组，MySQL会按照group by后面的字段名进行分组：

```sql
select vend_id, COUNT(*) as num_prods
from products
group by vend_id;
```

使用GROUP BY子句应该牢记以下的规定：

- GROUP BY 子句可以包含任意数目的列。这使得能对分组进行嵌套,
  为数据分组提供更细致的控制。
- 如果在 GROUP BY 子句中嵌套了分组,数据将在最后规定的分组上
  进行汇总。换句话说,在建立分组时,指定的所有列都一起计算
  (所以不能从个别的列取回数据)。
- GROUP BY 子句中列出的每个列都必须是检索列或有效的表达式
  (但不能是聚集函数)。如果在 SELECT 中使用表达式,则必须在
  GROUP BY 子句中指定相同的表达式。不能使用别名。
- 除聚集计算语句外, SELECT 语句中的每个列都必须在 GROUP BY 子
  句中给出。
- 如果分组列中具有 NULL 值,则 NULL 将作为一个分组返回。如果列
  中有多行 NULL 值,它们将分为一组。
- GROUP BY 子句必须出现在 WHERE 子句之后, ORDER BY 子句之前。

### HAVING子句：过滤分组

having子句和where子句非常类似，能在where中使用的操作符也可以在having中使用。但是，where是对行进行过滤，而having则是对组进行过滤。具体来讲，where过滤的是某一行或某几行，而having过滤的是某一组或者某几组：

```sql
select cust_id, count(*) as orders
from orders
group by cust_id
having count(*)>=2;
```



## SELECT语句中各个子句的顺序

### 书写顺序

1. select  (distinct)
2. from
3. where
4. group by
5. having
6. order by
7. limit

### 执行顺序

1. from
2. where
3. group by
4. having
5. select
6. distinct
7. order by
8. limit

