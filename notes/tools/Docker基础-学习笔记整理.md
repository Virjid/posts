## 安装Docker（Ubuntu）

### 使用脚本安装

输入如下命令并执行：

```
virjid @(๑•̀ㅂ•́)و✧ :~$ wget -qO- https://get.docker.com/ | sh
```

该条命令将从docker官网上下载安装脚本。本来会将该脚本内容输出至终端屏幕（标准输出）上，但通过管道符 `|` ，将脚本内容直接交给shell去执行，所以上面的这一条命令直接完成了下载脚本和执行脚本两个步骤。

### 让非root用户执行Docker

在默认情况下，执行Docker相关的很多操作都需要root权限，所以执行以下命令为非root用户赋予执行docker的权限：

```
virjid @(๑•̀ㅂ•́)و✧ :~$ sudo usermod -aG docker virjid
```

### 启动/关闭/重启Docker

使用以下命令可以启动/关闭/重启dcoker的后台服务：

```
$ sudo service docker start      # 启动docker服务
$ sudo service docker stop       # 关闭docker服务
$ sudo service docker restart    # 重启docker服务
```

### Docker的Hello-World

测试docker是否正在运行：

```
$ docker run hello-world
```

### 更换镜像

Docker使用`/etc/docker/daemon.json`作为配置文件，在配置文件中添加一下内容：

```json
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```





## 使用Docker运行应用程序

```
virjid @(๑•̀ㅂ•́)و✧ :~$ docker run ubuntu:15.10 /bin/echo "Hello world"
Hello world
```



- docker 显然表示Docker的可执行文件
- run 是docker的一个命令（command），用来运行一个容器实例
- ubuntu:15.10 是容器实例所使用镜像的名称
- /bin/echo "Hello world" 是在容器实例中执行的命令

> Docker首先会在本地查找指定的镜像是否存在，如果存在就直接加载，如果不存在就会去远程服务器上下载镜像再加载。

上面这条命令表示：

1. Docker 以 ubuntu15.10 镜像创建一个新容器
2. 在容器里执行 bin/echo "Hello world"
3. 输出结果
4. 销毁容器实例

## 与容器内的终端进行交互

我们可以像前面那样运行一个容器并启动该容器中的shell（比如bash）：

```
$ docker run ubuntu:15.10 /bin/bash
```

docker确实这么做了，但很快该容器实例就被销毁了，根本无法与容器内的shell进行交互。



可以使用 **`-t`** 和 **`-i`** 参数来与容器内的shell进行交互：

- -t ：在新容器内指定一个终端
- -i ：允许通过**容器内的标准输入**进行交互

```
$ docker run -i -t ubuntu:15.10 /bin/bash
```

>若只有 -t ，则会指定一个终端，容器实例不会自动销毁，但用户无法与容器内的shell进行交互。若只有 -i ，则仅仅是通过容器内的标准输入与指定的程序进行交互。

## 让容器运行在后台

### 使用-d参数以后台模式运行容器

比如当我们在一个容器中运行Nginx服务器时，往往希望容器运行于后台当中。要以后台模式启动容器，使用 **`-d`** 参数即可。



```
$ docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

输入上面这条命令后，原本终端屏幕上应该会不断的输出`hello world`，但由于使用了 -d 参数，容器被运行在了后台。而屏幕上出现的是一长串没有规律的字符串，该字符串是**所运行容器的ID**：

```
7599b6466de4a6a33f4dafb851d6a9484b6e0f44337a1a67db716dc4c929fbc3
```

### 确认容器的运行

使用`docker ps`可以查看所有正在运行的容器：

```
$ docker ps
```

在返回于屏幕上的结果中，有几个字段的解释如下：

- CONTAINER ID：容器ID
- NAMES：自动分配给容器的名称
- IMAGE：容器所使用的镜像名

> 容器ID很长，所以在后面要用到容器ID的时候，并不需要全部输入，只需要ID的开头一部分即可，不过要保证这开头一部分在所有ID中是唯一的。

### 查看容器的标准输出

由于容器运行于后台，所以标准输出并不会显示在屏幕上，通过`docker logs <容器ID/容器名>`就可以查看指定容器的标准输出：

```
docker logs 7599b64
```

注意容器ID要根据实际的情况传入，返回的结果应该为一大批`hello world`。

除了传入容器ID以外，还可以传入容器名来查看内容。

### 为Docker容器指定容器名

只要在启动容器时，在命令中添加 **`--name`** 参数并指定容器名即可。

## 端口映射

当我们在服务器中运行着一个WEB应用时，就需要将该应用在容器内的端口号映射至宿主机上。

### 将容器内所有暴露的端口号随机地映射到宿主机端口

使用 **`-P`** 参数即可达到端口映射的目的：

```
$ docker run -d -P --name web nginx
```

使用`docker ps`查看端口：

```
$ docker ps
```

结果中有一个PORTS字段，在本次运行中，该字段的值为`0.0.0.0:32768->80/tcp`，表示将容器的80端口映射为宿主机的32768端口。现在打开浏览器输入URL**http://127.0.0.1:32768**即可访问Docker容器，Docker容器会自动帮你将请求转发至容器内的80端口。

### 将指定的容器端口映射为指定的宿主机端口

使用 **`-p`** 参数来设置指定的端口映射：

```
$ docker run -d -p 8888:80 --name web2 nginx
```

该命令表示将容器内的80端口映射为宿主机的8888端口。

### 查看指定容器的端口映射

使用命令`docker port <容器ID/容器名>`

```
$ docker port web1
```

