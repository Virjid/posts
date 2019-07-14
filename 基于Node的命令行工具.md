---
title: 基于Node的命令行工具
date: 2019-06-13 00:32:25
tags: Node.js
---
Node.js常用来开发后端应用，尤其是Web应用，但这并不代表它除此之外就无能为力了。比如命令行工具（比如当今的各种前端构建工具）、桌面应用（比如VSCode编辑器）都可以使用Node结合其它框架进行开发，下面简单介绍一下如何使用Node来创建一个命令行工具。
<!-- more -->
## Simple Demo
创建一个名为`cli`的文件，该文件的内容为下：
```javascript
#!/usr/bin/env node
const [nodePath, scriptPath, name] = process.argv;
console.log('Hello', name);
```

接下来设置文件的权限为可执行：
```
chmod +x cli
```

最后我们使用cd命令切换至cli脚本所在目录并运行该脚本：
```
./cli Virjid

# 输出：
# Hello Virjid
```
因为PATH环境变量中还没有包含cli脚本所在的目录，所以要使用`./cli`命令而不是`cli`命令。

process.argv是Node.js的内置变量，通过该变量可以接受命令行参数。

## 使用命令行参数
### 关于命令行参数
一般命令行都会允许传递参数来控制程序的动作，并且参数名都会以`-`或者`--`开头，参数名后面跟上空格再紧跟参数值：
```
node install --save express
# --save是
```