---
title: Linux基础命令整理
date: 2019-07-08 13:10:45
tags: Linux操作系统
---

## 文件管理

### cat：查看文件内容

`cat`命令能够读取多个文件的内容并依次复制到标准输出，我们也可以从标准输入中获取内容：

```
$ cat customer.sql
CREATE TABLE customer(
    id                                VARCHAR(50)             PRIMARY KEY,
    mobile                            VARCHAR(50)             NOT NULL,
    email                             VARCHAR(50)             NOT NULL,
    wechart                           VARCHAR(50)             NOT NULL,
    qq                                VARCHAR(50)             NOT NULL,
    mobile_login                      INT,
    email_login                       INT,
    height                            INT,
    weight                            INT                     NOT NULL,
    birthday                          DATE                    NOT NULL,
    male                              INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

```

通过重定向，可以达到将多个文件合并的目的：

```
$ cat a.txt b.txt c.md > d.txt

# 将a.txt、b.txt、c.md这三个文件依次复制到文件d.txt
```

如果没有指定文件或者文件名为"-"，则从标准输入中读取内容。

### cd：切换工作目录

```
$ cd /home/virjid
```

### chgrp：切换文件所属组

```
$ chgrp root ./customer.sql
```

### chmod：切换文件执行权限

```
chmod +x ./
```



