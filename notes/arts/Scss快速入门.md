CSS在控制样式上虽然十分强大，但它所编写出来的文件实在是难以维护。为了解决这个痛点，有一些CSS预处理器相继出现，其中Scss就是一个强大的CSS预处理器，赋予了CSS变量、继承等一定的编程特性。
<!-- more -->

## Scss中的变量
### 定义变量
```scss
$default-fm: Helvetica, arial, freesans, clean, sans-serif;
```
Scss使用$来标识变量，并且Scss是不会区分变量名的中划线（-）与下划线（_）的。

### 引用变量
如果要引用之前定义的`$default-fm`，就需要这么做：
```scss
$default-fm: Helvetica, arial, freesans, clean, sans-serif;

body {
    font-family: $default-fm;
}
```
变量既可以定义在规则块之外，也可以直接定义于规则块之内。不过，规则块之内的变量，其作用域也仅限于当前规则块：
```scss
code {
    $code-font-fm: Consolas, "Andale Mono WT", "Andale Mono", monospace;
    font-family: $code-font-fm;
}

p {
    font-family: $code-font-fm; // <- Error
}
```
### 变量的覆盖
在正常的情况下，后定义的变量会覆盖掉之前定义的变量：
```scss
$default-col: #333;
$default-col: #3F3F3F;

p {
    color: $default-col;
}
```
上述的Scss代码经处理后的结果为：
```css
p {color:#3F3F3F;}
```

可以使用!default关键词来降低变量的优先级，它的含义和CSS中的!import恰好相反（不过!default只能用在变量上）：
```scss
$default-col: #333;
$default-col: #3F3F3F ！default;

p {
    color: $default-col;
}

// 处理后的样式为： p{color:#333;}
```

## 规则块的嵌套
### 后代选择器
当使用CSS的后代选择器时，我们的编写方式大概是这样的：
```css
nav {
    /*...nav的样式...*/
}
nav ul {
    /*...ul的样式...*/
}
nav ul li {
    /*...li的样式...*/
}
```
通过Scss嵌套可以重写上面的样式：
```scss
nav {
    /*...nav的样式...*/
    ul {
        /*...ul的样式...*/
        li {
            /*...li的样式...*/
        }
    }
}
```
Scss嵌套中的外层是内层的父代，实际上Scss所做的工作是这样滴：**将外层选择器后面加一个空格，然后将内层选择器拼接在后面**，举一个例子：
```scss
a {
    :hover {
        color: blue;
    }
}
```
上述规则块的编译结果为：
```css
a :hover { color: blue; } /* 注意a和:hover之间有一个空格 */
```

### &标识符
前面说过Scss嵌套处理在拼接的过程中会加一个空格，但有时我们并不希望这样，比如：
```scss
.content a {
    :hover { color: blue; }
}
```
处理结果为：
```css
.content a :hover { color: blue; }
```
对于上面的例子，原本我们仅仅希望超链接被hover时字体会变为蓝色。然而实际结果是：超链接始终为蓝色，而.content块中的其它内容被hover时会变为蓝色。

我们可以使用&标识符来告诉scss不要添加空格：
```scss
.content a {
    &:hover { color: blue; }
}
```
&就相当于**父选择器的标识符**，其处理结果为：
```css
.content a:hover { color: blue; }
```

对于&我们可以这样理解：如果出现了标识符&，Scss就不会采取拼接的解析方式，而是将父代选择器直接替换掉&标识符。

### 与嵌套相关的其它选择器
如果明白了Scss处理选择器的基本原理，很快就能自己使用嵌套机制来做更多的事情：
```scss
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  nav + & { margin-top: 0 }
}
```
根据 **外层选择器后面加空格再拼接内层选择器** 以及 **&是外层（父）选择器的标识** ，就可以推出上述规则块的处理结果为：
```css
article ~ article { border-top: 1px dashed #ccc }
article > footer { background: #eee }
article dl > dt { color: #333 }
article dl > dd { color: #555 }
nav + article { margin-top: 0 }
```

### 属性的嵌套
前面所说的嵌套都是选择器的嵌套，不过在Scss中属性也是可以嵌套的。
```css
div {
    border-style: solid;
    border-width: 1px;
    border-color: #ccc;
}
```
上面的规则块可以用Scss改写如下：
```scss
div {
    border {
        style: solid;
        width: 1px;
        color: #ccc;
    }
}
```

我们还可以这么写：
```scss
div {
    border: 1px solid #ccc {
        left: 0px;
        right: 0px;
    }
}
```

## 引入
SASS能够将代码分割为多个片段，并以underscore风格的下划线作为其命名前缀（_partial.scss），SASS会通过这些下划线来辨别哪些文件是SASS片段，并且不让片段内容直接生成为CSS文件，从而只是在使用@import指令的位置被导入。CSS原生的@import会通过额外的HTTP请求获取引入的样式片段，而SASS的@import则会直接将这些引入的片段合并至当前CSS文件，并且不会产生新的HTTP请求。下面例子中的代码，将会在base.scss文件当中引入_reset.scss片断。
```scss
// _reset.scss
html, body, ul, ol {
  margin:  0;
  padding: 0;
}

// base.scss
@import 'reset';
body {
  font: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```

SASS中引入片断时，可以缺省使用文件扩展名，因此上面代码中直接通过@import 'reset'引入，编译后生成的代码如下：
```css
html, body, ul, ol {
  margin: 0;
  padding: 0; }

body {
  font: 100% Helvetica, sans-serif;
  background-color: #efefef; }
```

## 混合
混合（Mixin）用来分组那些需要在页面中复用的CSS声明，开发人员可以通过向Mixin传递变量参数来让代码更加灵活，该特性在添加浏览器兼容性前缀的时候非常有用，SASS目前使用@mixin name指令来进行混合操作。
```scss
@mixin border-radius($radius) {
          border-radius: $radius;
      -ms-border-radius: $radius;
     -moz-border-radius: $radius;
  -webkit-border-radius: $radius;
}

.box {
  @include border-radius(10px);
}
```

上面的代码建立了一个名为border-radius的Mixin，并传递了一个变量$radius作为参数，然后在后续代码中通过@include border-radius(10px)使用该Mixin，最终编译的结果如下：
```css
.box {
  border-radius: 10px;
  -ms-border-radius: 10px;
  -moz-border-radius: 10px;
  -webkit-border-radius: 10px; }
```

## 继承
继承是SASS中非常重要的一个特性，可以通过@extend指令在选择器之间复用CSS属性，并且不会产生冗余的代码，下面例子将会通过SASS提供的继承机制建立一系列样式：
```scss
// 这段代码不会被输出到最终生成的CSS文件，因为它没有被任何代码所继承。
%other-styles {
  display: flex;
  flex-wrap: wrap;
}

// 下面代码会正常输出到生成的CSS文件，因为它被其接下来的代码所继承。
%message-common {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.message {
  @extend %message-common;
}

.success {
  @extend %message-common;
  border-color: green;
}

.error {
  @extend %message-common;
  border-color: red;
}

.warning {
  @extend %message-common;
  border-color: yellow;
}
```

上面代码将.message中的CSS属性应用到了.success、.error、.warning上面，魔法将会发生在最终生成的CSS当中。这种方式能够避免在HTML元素上书写多个class选择器，最终生成的CSS样式是下面这样的：
```css
.message, .success, .error, .warning {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333; }

.success {
  border-color: green; }

.error {
  border-color: red; }

.warning {
  border-color: yellow; }
```

## 操作符
SASS提供了标准的算术运算符，例如+、-、*、/、%。在接下来的例子里，我们尝试在aside和article选择器当中对宽度进行简单的计算。
```scss
.container { width: 100%; }

article[role="main"] {
  float: left;
  width: 600px / 960px * 100%;
}

aside[role="complementary"] {
  float: right;
  width: 300px / 960px * 100%;
}
```

上面代码以960px为基准建立了简单的流式网格布局，SASS提供的算术运算符让开发人员可以更容易的将像素值转换为百分比，最终生成的CSS样式如下所示：
```css
.container {
  width: 100%; }

article[role="main"] {
  float: left;
  width: 62.5%; }

aside[role="complementary"] {
  float: right;
  width: 31.25%; }
```