# 安装

`npm`默认安装的是`Dart Sass`了。

```go
npm install -g sass
```

# 基础属性

## 1、变量

**变量规则：**

下文的`mixin`和`function`命名也遵循`1234`条规则：

1. 变量以美元符号`$`开头，后面跟变量名；
2. 变量名是不以数字开头的可包含字母、数字、下划线、横线（连接符）；
3. 通过连接符`-`与下划线`_`定义的同名变量为同一变量；
4. 变量一定要先定义，后使用；
5. 写法同`css`，即变量名和值之间用冒号`:`分隔；

**变量作用域：**

- 全局变量：声明在最外层的变量，可在任何地方使用；
- 局部变量：嵌套规则内定义的变量只能在嵌套规则内使用。

**数据类型：**

`scss`支持`7`种主要的数据类型：

1. 数字，`1rem、2vh、13、 10px`；
2. 字符串，分有引号字符串与无引号字符串，`"foo"、 'bar'、baz`；
3. 颜色，`blue, #04a3f9, rgba(255,0,0,0.5)`；
4. 布尔型，`true`和`false`；
5. 空值，`null`是其类型的唯一值。表示缺少值，通常由函数返回以表示缺少结果；
6. 数组 (`list`)，用空格或逗号作分隔符，`1.5em 1em 0 2em,Helvetica,Arial,sans-serif`；
7. `maps`， 相当于 `JavaScript`的 `object`，`(key1: value1, key2: value2)`

`sass`变量的声明和`css`属性的声明很像：

```
$highlight-color: #F90;
```

例如：

```
$nav-color: #F90;
nav {
  $width: 100px;
  width: $width;
  color: $nav-color;
}

//编译后
nav {
  width: 100px;
  color: #F90;
}
```

在这段代码中，`$nav-color`这个变量定义在了规则块外边，所以在这个样式表中都可以像 `nav`规则块那样引用它。`$width`这个变量定义在了`nav`的`{ }`规则块内，所以它只能在`nav`规则块 内使用。这意味着是你可以在样式表的其他地方定义和使用`$width`变量，不会对这里造成影响。

```
$highlight-color: #F90;
$highlight-border: 1px solid $highlight-color;
.selected {
  border: $highlight-border;
}

//编译后
.selected {
  border: 1px solid #F90;
}
```

### !default

可以在变量的结尾添加`!default`来给变量设置默认值，有点类似`Javascript`的逻辑运算符`let content=content || "Second content"`。注意，变量是 `null`时将视为未被`!default`赋值。

```
$content: "First content";
// 如果$content之前没定义就使用如下的默认值
$content: "Second content" !default;
#main {
    content: $content;
}

//编译后
#main {
  content: "First content";
}
```

### 运算符

相等运算`==`和不相等运算`!=`。所有数据类型均支持 `==` 和`!=`，另外，每种数据类型也有其各自支持的运算方式。

```
$theme:"blue";
 
.container {
    @if $theme=="blue" {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}
 
.container {
    @if $theme !="blue" {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}

//编译后
.container {
  background-color: red;
}
 
.container {
  background-color: blue;
}
```

### 关系运算符

四个关系运算符`< > >= <=`

```
$theme:3;
.container {
    @if $theme >= 5 {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}

//编译后
.container {
    background-color: blue;
}
```

### 布尔运行符

三个布尔运算符`and or not`

```
$width: 100;
$height: 200;
$last: false;
div {
  @if $width>50 and $height<300 {
    font-size: 16px;
  } @else {
    font-size: 14px;
  }
  @if not $last {
    border-color: red;
  } @else {
    border-color: blue;
  }
 
  @if $width>500 or $height<300{
    line-height: 20px;
  } @else {
    line-height: 50px;
  }
}

//编译后
div {
    font-size: 16px;
    border-color: red;
    line-height: 20px;
}div {
    font-size: 16px;
    border-color: red;
}
```

### 数字操作符

+ - * / %

```
/* 纯数字与百分号或单位运算时会自动转化成相应的百分比与单位值 */
.container {
    /* ================== + 运算===================== */
    width: 50 + 20;
    width: 50 + 20%;
    width: 50% + 20%;
    width: 10pt + 20px;
    width: 10pt + 20;
 
    /* ================== - 运算===================== */
    height: 50 - 20;
    height: 10 - 20%;
    height: 50pt - 20px;
 
    /* ================== * 运算===================== */
    height: 50 * 30;
    height: 10 * 30%;
    height: 50 * 2px;
    height: 50pt * 4;
 
    /* ==================/ 运算 (除完后最多只能保留一种单位)===================== */
    $width: 100px;
    width: 10/5;
    width: 10px / 5px;
    width: 10px / 20;
    width: ($width/2); // 使用变量与括号
    height: (500px/2); // 使用了括号
 
    /* ==================% 求余运算===================== */
    width: 10 % 3;
    width: 50px % 7;
    width: 50% % 7;
}

//编译后
/* 纯数字与百分号或单位运算时会自动转化成相应的百分比与单位值 */
.container {
    /* ================== + 运算===================== */
    width: 70;
    width: 70%;
    width: 70%;
    width: 25pt;
    width: 30pt;
    /* ================== - 运算===================== */
    height: 30;
    height: -10%;
    height: 35pt;
    /* ================== * 运算===================== */
    height: 1500;
    height: 300%;
    height: 100px;
    height: 200pt;
    /* ==================/ 运算 (除完后最多只能保留一种单位)===================== */
    width: 10/5;
    width: 10px/5px;
    width: 10px/20;
    width: 50px;
    height: 250px;
    /* ==================% 运算===================== */
    width: 1;
    width: 1px;
    width: 1%;
}
```

在`css`中有分隔数字的用途，在`scss`中，以下三种情况会进行除法运算：

1. 如果值或值的一部分，是变量或者函数的返回值；
2. 如果值被圆括号包裹；
3. 如果值是算数表达式的一部分。

```
$width: 1000px;
div {
    font: 16px/30px Arial, Helvetica, sans-serif; // 不运算
    width: ($width/2); // 使用变量与括号
    width: (#{$width}/2); // 使用 #{} 插值语句将变量包裹，避免运算。
    z-index: round(10)/2; // 使用了函数
    height: (500px/2); // 使用了括号
    margin-left: 5px + 8px/2px; // 使用了+表达式
}

/* 编译后 */
div {
    font: 16px/30px Arial, Helvetica, sans-serif;
    width: 500px;
    width: 1000px/2;
    z-index: 5;
    height: 250px;
    margin-left: 9px;
}
```

### 字符串运算

1. `+`可用于连接字符串；
2. 如果有引号字符串（位于 + 左侧）连接无引号字符串，运算结果是有引号的；
3. 无引号字符串（位于 + 左侧）连接有引号字符串，运算结果则没有引号。

```
.container {
    content: "Foo " + Bar;
    font-family: sans- + "serif";
}

/* 编译后 */
.container {
    content: "Foo Bar";
    font-family: sans-serif;
}
```

### 插值语句

文章上面有讲到插值语句，这里来解释一下。

通过 `#{}` 插值语句可以在选择器、属性名、注释中使用变量，使用`#{}`插值语句将变量包裹起来即可，和`js`中的模板字符串很像

```
$font-size: 12px;
$line-height: 30px;
$class-name: danger;
$attr: color;
$author: "福大命大";
 
p {
    font: #{$font-size}/#{$line-height} Arial Helvetica, sans-serif;
}
 
/* 
* 这是文件的说明部分
* @author: #{$author}
*/
 
a.#{$class-name} {
    border-#{$attr}: #f00;
}


/* 编译后 */
p {
    font: 12px/30px Arial Helvetica, sans-serif;
}
 
/* 
* 这是文件的说明部分
* @author: 福大命大
*/
a.danger {
    border-color: #f00;
}
```

## 2、流程控制

`sass`中流程控制包含四类，也是我们在`js`中常见的`@if、@for、@each、@while`。

### @if

`@if`语法和`js`类似，基本格式是`@if...@else if...@else`。

```
$theme:3;
.container {
    @if $theme >= 5 {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}

/* 编译后 */
.container {
    background-color: blue;
}
```

### [@for ](/for ) 

`for`在条件范围内重复操作，这个指令包含两种格式：

1. `@for $var from <start> through <end>`；
2. `@for $var from <start> to <end>`。

两者区别在于 `through` 与 `to`的含义：

1. 使用 `through`时，条件范围包含 `<start>` 与 `<end>`的值；
2. 使用 `to`时条件范围只包含`<start>`的值不包含`<end>`的值；
3. `$var` 可以是任何变量，比如`$i`，`<start>` 和 `<end>` 必须是整数值。

```
@for $i from 1 to 3 {
  #loading span:nth-child(#{$i}) {
      width: 20 * ($i - 1) + px;
  }
}

/* 编译后 */
#loading span:nth-child(1) {
    width: 0px;
}
 
#loading span:nth-child(2) {
    width: 20px;
}
```

### [@each ](/each ) 

`@each`指令的格式是`@each $var in $list` , `$var`可以是任何变量名，比如`$length` 或者`$name`，而`$list`是一连串的值，也就是值列表。

```
$color-list:red green blue turquoise darkmagenta;
@each $color in $color-list {
    $index: index($color-list, $color);
    .p#{$index - 1} {
        background-color: $color;
    }
}

/* 编译后 */
.p0 {
    background-color: red;
}
 
.p1 {
    background-color: green;
}
 
.p2 {
    background-color: blue;
}
 
.p3 {
    background-color: turquoise;
}
 
.p4 {
    background-color: darkmagenta;
}
```

### [@while ](/while ) 

`@while` 指令循环输出直到表达式返回结果为 `false`。这样可以实现比`@for` 更复杂的循环。

比如，可以借此生成栅格化布局。

```
$column:12;
@while $column>0 {
   .col-sm-#{$column} {
      width: $column / 12 * 100%;
   }
    $column:$column - 1;
}

/* 编译后 */
.col-sm-12 {
    width: 100%;
}
 
.col-sm-11 {
    width: 91.6666666667%;
}
 
.col-sm-10 {
    width: 83.3333333333%;
}
 
.col-sm-9 {
    width: 75%;
}
 
.col-sm-8 {
    width: 66.6666666667%;
}
 
.col-sm-7 {
    width: 58.3333333333%;
}
 
.col-sm-6 {
    width: 50%;
}
 
.col-sm-5 {
    width: 41.6666666667%;
}
 
.col-sm-4 {
    width: 33.3333333333%;
}
 
.col-sm-3 {
    width: 25%;
}
 
.col-sm-2 {
    width: 16.6666666667%;
}
 
.col-sm-1 {
    width: 8.3333333333%;
}
```

## 3、嵌套CSS 规则

`css`中重复写选择器是非常恼人的。如果要写一大串指向页面中同一块的样式时，往往需要 一遍又一遍地写同一个`ID`：

```
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

像这种情况，`sass`可以让你只写一遍，且使样式可读性更高。在Sass中，你可以像俄罗斯套娃那样在规则块中嵌套规则块。`sass`在输出`css`时会帮你把这些嵌套规则处理好，避免你的重复书写。

```
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}
/* 编译后 */
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

一个给定的规则块，既可以像普通的CSS那样包含属性，又可以嵌套其他规则块。当你同时要为一个容器元素及其子元素编写特定样式时，这种能力就非常有用了。

```
#content {
  background-color: #f5f5f5;
  aside { background-color: #eee }
}
/* 编译后 */
#content { background-color: #f5f5f5 }
#content aside { background-color: #eee }
```

## 4、父选择器的标识符&

一般情况下，`sass`在解开一个嵌套规则时就会把父选择器（`#content`）通过一个空格连接到子选择器的前边（`article`和`aside`）形成（`#content article`和`#content aside`）。这种在CSS里边被称为后代选择器，因为它选择ID为`content`的元素内所有命中选择器`article`和`aside`的元素。但在有些情况下你却不会希望`sass`使用这种后代选择器的方式生成这种连接。

`sass`选择器，即父选择器。在使用嵌套规则时，父选择器能对于嵌套规则如何解开提供更好的控制。它就是一个简单的`&`符号，且可以放在任何一个选择器可出现的地方，比如`h1`放在哪，它就可以放在哪。

```
article a {
  color: blue;
  &:hover { color: red }
}
/* 编译后 */
article a { color: blue }
article a:hover { color: red }
```

```
#content aside {
  color: red;
  body.ie & { color: green }
}
/* 编译后 */
#content aside {color: red};
body.ie #content aside { color: green }
```

## 5、群组选择器的嵌套

在`CSS`里边，选择器`h1``h2`和`h3`会同时命中h1元素、h2元素和h3元素。与此类似，`.button` `button`会命中button元素和类名为.button的元素。这种选择器称为群组选择器。群组选择器 的规则会对命中群组中任何一个选择器的元素生效。

```
.container h1, .container h2, .container h3 { margin-bottom: .8em }
/* 编译后 */
.container {
  h1, h2, h3 {margin-bottom: .8em}
}
```

```
nav, aside {
  a {color: blue}
}
/* 编译后 */
nav a, aside a {color: blue}
```

## 6、嵌套属性

在`sass`中，除了CSS选择器，属性也可以进行嵌套。尽管编写属性涉及的重复不像编写选择器那么糟糕，但是要反复写`border-style``border-width``border-color`以及`border-*`等也是非常烦人的。在`sass`中，你只需敲写一遍`border`：

```
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
/* 编译后 */
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}
```

## 7、导入文件

`@import`算是一个比较简易的模块系统。`scss`拓展了`@import` 的功能，允许其导入 `scss`或 `sass`文件。被导入的文件将合并编译到同一个 `css`文件中，被导入的文件中所包含的变量或者混合指令 (`mixin`) 都可以在导入的文件中使用。

**_common.scss_**

```go
$color:red;
```

**_index.scss_**

```go
@import "common.scss";
.container {
    border-color: $color;
}

/* 编译后 */
.container {
  border-color: red;
}
```

以下情况下，`@import` 仅作为普通的`css`语句，不会导入`scss`文件：

1. 文件拓展名是`.css`；
2. 文件名以 `http://`开头；
3. 文件名是`url()`；
4. `@import`包含媒体查询。

```
@import "common.css";
@import url(common);
@import "http://xxx.com/xxx";
@import 'landscape' screen and (orientation:landscape);
```

`scss`允许同时导入多个文件，例如同时导入 `rounded-corners` 与`text-shadow` 两个文件，不用再单独写个`import`引入。

```
@import "rounded-corners", "text-shadow";
```

导入文件也可以使用 `#{}` 插值语句（下面有讲，这里把它理解成`js`中模板字符串就行）动态导入，但不是通过变量动态导入 `scss`文件，只能作用于 `css`的 `url()`导入方式

```
$family: unquote("Droid+Sans");
@import url("http://fonts.googleapis.com/css?family=#{$family}");

/* 编译后 */
@import url("http://fonts.googleapis.com/css?family=Droid+Sans");
```

### [@Partials ](/Partials ) 

如果需要导入 `scss`或者 `sass`文件，但又不希望将其编译为 `css`，只需要在文件名前添加下划线，这样会告诉 `scss`不要编译这些文件。注意：

1. 导入语句中却不需要添加下划线；
2. 不可以同时存在添加下划线与未添加下划线的同名文件，添加下划线的文件将会被忽略。

**_common.scss**

```
$color:red;
```

**_index.scss_**

```
@import "common.scss";
.container {
    border-color: $color;
}

/* 编译后 */
.container {
  border-color: red;
}
```

### 嵌套[@import ](/import ) 

大多数情况下，一般在文件的最外层（不在嵌套规则内）使用`@import`，其实，也可以将`@import` 嵌套进内层选择器或者 `@media` 中，与平时的用法效果相同，只是这样导入的样式只能出现在嵌套的层中，存在作用域。

**_common.scss_**

```
.example {
    color: red;
}
```

**_index.scss_**

```
#main {
    @import "example";
}
/* 编译后 */
#main .example {
    color: red;
}
```

## 8、[@mixin ](/mixin ) 

混合指令（`Mixin`）用于定义可重复使用的样式。混合指令可以包含所有的`css`规则，绝大部分 `scss`规则，甚至可以通过参数功能引入变量，输出多样化的样式。

**注意：函数命名和变量命名规则一致。**

### **使用**

```
@mixin mixin-name() {
    /* css 声明 */
}
// 使用
@include mixin-name;
```

### **标准形式**

```
// 定义一个区块基本的样式
@mixin block {
    width: 96%;
    margin-left: 2%;
    border-radius: 8px;
    border: 1px #f6f6f6 solid;
}
// 使用混入 
.container {
    .block {
        @include block;
    }
}

/* 编译后 */
.container .block {
    width: 96%;
    margin-left: 2%;
    border-radius: 8px;
    border: 1px #f6f6f6 solid;
}
```

### 嵌入选择器

`@mixin`里面再嵌套一层

```
@mixin warning-text {
    font-size: 12px;
    .warn-text {
        color: rgb(255, 253, 123);
        line-height: 180%;
    }
}
 
.container {
    @include warning-text;
}

/* 编译后 */
.container {
    font-size: 12px;
}
.container .warn-text {
    color: #fffd7b;
    line-height: 180%;
}
```

### 单参数

```
// 定义flex布局元素纵轴的排列方式
@mixin flex-align($aitem) {
    align-items: $aitem;
}
 
// 只有一个参数，直接传递参数
.container {
    @include flex-align(center);
}

/* 编译后 */
.container {
    align-items: center;
}
```

### 多参数

```
// 定义块元素内边距
@mixin block-padding($top, $right, $bottom, $left) {
    padding-top: $top;
    padding-right: $right;
    padding-bottom: $bottom;
    padding-left: $left;
}
 
// 按照参数顺序赋值
.container1 {
   @include block-padding(10px, 20px, 30px, 40px);
}
 
// 可指定参数赋值
.container2 {
   @include block-padding($left: 20px, $top: 10px, $bottom: 10px, $right: 30px);
}
 
// 可指定参数赋值，但是必须指定4个值，不能缺失
.container3 {
   @include block-padding($left: 10px, $top: 10px, $bottom: 0, $right: 0);
}

/* 编译后 */
.container1 {
    padding-top: 10px;
    padding-right: 20px;
    padding-bottom: 30px;
    padding-left: 40px;
}
 
.container2 {
    padding-top: 10px;
    padding-right: 30px;
    padding-bottom: 10px;
    padding-left: 20px;
}
 
.container3 {
    padding-top: 10px;
    padding-right: 0;
    padding-bottom: 0;
    padding-left: 10px;
}
```

### 指定默认值

```
// 定义块元素内边距，参数指定默认值
@mixin block-padding($top:0, $right:0, $bottom:0, $left:0) {
    padding-top: $top;
    padding-right: $right;
    padding-bottom: $bottom;
    padding-left: $left;
}
 
// 可指定参数赋值
.container {
    /** 不带参数 */
    @include block-padding;
    /** 按顺序指定参数值 */
    @include block-padding(10px,20px);
    /** 给指定参数指定值 */
    @include block-padding($left: 10px, $top: 20px)
}

/* 编译后 */
.container {
    /** 不带参数 */
    padding-top: 0;
    padding-right: 0;
    padding-bottom: 0;
    padding-left: 0;
    /** 按顺序指定参数值 */
    padding-top: 10px;
    padding-right: 20px;
    padding-bottom: 0;
    padding-left: 0;
    /** 给指定参数指定值 */
    padding-top: 20px;
    padding-right: 0;
    padding-bottom: 0;
    padding-left: 10px;
}
```

### 可变参数

使用`...`处理参数不固定的情况，类似于`js`中的函数的剩余参数

```
@mixin linear-gradient($direction, $gradients...) {
    background-color: nth($gradients, 1);
    background-image: linear-gradient($direction, $gradients);
}
 
.table-data {
    @include linear-gradient(to right, #F00, orange, yellow);
}

/* 编译后 */
.table-data {
    background-color: #F00;
    background-image: linear-gradient(to right, #F00, orange, yellow);
}
```

**总结：**

1. `mixin`是可以重复使用的一组`css`声明，有助于减少重复代码，只需声明一次，就可在文件中引用；
2. 混合指令可以包含所有的 `css`规则，绝大部分`scss`规则，可以传递参数，输出多样化的样式；
3. 使用参数时建议加上默认值；
4. `@import`导入局部模块化样式（类似功能、同一组件）；
5. `@minix`定义的是可重复使用的样式

## 9、[@function ](/function ) 

`@function`用于封装复杂的操作，可以很容易地以一种可读的方式抽象出通用公式和行为，函数提供返回值，常用来做计算方面的工作。

### 使用

注意：函数命名和变量命名规则一致。

```
@function square($base) {
    @return $base * $base * 1px;
}
 
.sidebar {
    float: left;
    margin-left: square(4);
}

/* 编译后 */
.sidebar {
    float: left;
    margin-left: 16px;
}
```

### 可选参数

默认值可以是任何表达式，它们甚至可以引用前面的参数！

```
//change-color和hue是内置方法
//hue 返回$color的颜色为0到360度之间的一个数字。
//change-color 用于设置颜色的属性
@function invert($color, $amount: 100%) {
    //@error hue($color); 调试 210deg
    $inverse: change-color($color, $hue: hue($color) + 180);
    @return mix($inverse, $color, $amount);
}
 
$primary-color: #036;
.header {
    background-color: invert($primary-color, 80%);
}

/* 编译后 */
.header {
    background-color: #523314;
}
```

### 指定参数

```go
$primary-color: #036;
.banner {
    //scale-color Fluidly scales one or more properties of .$color
    background-color: $primary-color;
    color: scale-color($primary-color, $lightness: +40%);
}

/* 编译后 */
.banner {
    background-color: #036;
    color: #0a85ff;
}
```

### 可变参数

参数列表还可用于采用任意关键字参数，`meta.keywords()`函数采用参数列表

```
@function sum($numbers...) {
    $sum: 0;
    @each $number in $numbers {
        $sum: $sum + $number;
    }
    @return $sum;
}
 
$widths: 50px, 30px, 100px;
.micro {
    width: sum($widths...);
}

/* 编译后 */
.micro {
    width: 180px;
}
```

### [@return ](/return ) 

`@return`只允许在`@function`内使用，和`js`一样，遇到`return`就会返回。

```
@function red() {
    $is: true;
    @if $is {
        @return 'is';
    }
    @return red;
}
.con{
    color: red();
}

/* 编译后 */
.con {
    color: "is";
}
```

## 10、@extend继承

### 使用

我们以`elementUI`的`el-button`组件为例，可以使用`@extend`继承已经存在的样式，原理是使用逗号选择器。

```
// # id选择器一样的
.button {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}
 
.btn-default {
    @extend .button;
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}
 
.btn-danger {
    @extend .button;
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}

/* 编译后 */
.button, .btn-danger, .btn-default {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}
 
.btn-default {
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}
 
.btn-danger {
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}
```

### 多个[@extend ](/extend ) 

```
.alert {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}
 
.important {
    font-weight: bold;
    font-size: 14px;
}
.alert-danger {
    @extend .alert;
    @extend .important;
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}

/* 编译后 */
.alert, .alert-danger {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}
 
.important, .alert-danger {
    font-weight: bold;
    font-size: 14px;
}
 
.alert-danger {
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}
```

### 多层继承

`@extend`可以多层继承，列如：`.alert-danger`继承自`.important`，`.important`又继承自`.alert`。

```
.alert {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}
 
.important {
    @extend .alert;
    font-weight: bold;
    font-size: 14px;
}
.alert-danger {
    @extend .important;
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}

/* 编译后 */
.alert, .important, .alert-danger {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}
 
.important, .alert-danger {
    font-weight: bold;
    font-size: 14px;
}
 
.alert-danger {
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}
```

### 占位符选择器

占位符选择器`%`，与常用的`id` 与 `class`选择器写法相似，只是 `#` 或 `.` 替换成了`%`，占位符选择器必须通过 `@extend` 指令调用。

还是上面的例子，这里使用占位符选择器操作

```
.button %base {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}
        
.btn-default {
    @extend %base;
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}
 
.btn-danger {
    @extend %base;
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}

/* 编译后 */
.button .btn-danger, .button .btn-default {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}
 
.btn-default {
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}
 
.btn-danger {
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}
```

## 11、[@use ](/use ) 

存在兼容性问题，仅在`Dart Sass 1.23.0`以上有效，官方文档有兼容性介绍。

`scss`真正意义上的模块化，可以从其它 `scss`样式表中加载`mixin`、`function`和`变量`，并将来自多个样式表的 `css`组合在一起。`scss`还提供了很多内置模块，我们可以通过`@use`使用。

### @import缺点

1. 多处导入，存在样式重复加载。
2. 因为没有命名空间，为了避免撞名，不敢使用简写的 `classname`，因此起名总是需要注意。
3. 没有私有函数的概念，样式完全暴露在使用`import`的地方，这对`ui`库不够友好。

### 使用

*src/_corners.scss*

```
$radius: 3px;
@mixin rounded {
    border-radius: $radius;
}

/* 编译后 */
@use "src/corners";
.button {
    @include corners.rounded;
    padding: 5px + corners.$radius;
}
```

### 命名空间

*src/_corners.scss*

```
$radius: 3px;
@mixin rounded {
    border-radius: $radius;
}
```

_index.scss_

```
@use "src/corners" as c;
.button {
    @include c.rounded;
    padding: 5px + c.$radius;
}
```

编译为

```go
.button {
    border-radius: 3px;
    padding: 8px;
}
```

### as*

使用`as*`，那么这一模块就处于全局命名空间。

*src/_corners.scss*

```
$radius: 3px;
@mixin rounded {
    border-radius: $radius;
}
```

_index.scss_

```go
@use "src/corners" as *;
 
.button {
    @include rounded;
    padding: 5px + $radius;
}
```

编译为

```go
.button {
    border-radius: 3px;
    padding: 8px;
}
```

### 私有模块

使用`scss`可以轻松地定义私有成员，私有成员命名以`-`或开头。

*src/_corners.scss*

```
$-radius: 3px;
 
@mixin rounded {
    border-radius: $-radius;
}
```

_index.scss_

```
@use "src/corners";
 
.button {
    @include corners.rounded;
    // Error: Private members can't be accessed from outside their modules
    padding: 5px + corners.$-radius;
}
```

## 12、[@forward ](/forward ) 

`@forward`语句可以引入另一个模块的所有变量、`mixins`和函数，将它们直接作为当前模块的`API`暴露出去，不会真的在当前模块增加代码。不同于 `@use`， `@forward`不能给变量添加命名空间。

### 用法

注意，此时生成的`bootstrap.css`文件中，是不包含`functions`、`variables`、`mixins`代码的，也不能直接在`bootstrap.scss`文件中使用这些模块。而是需要在另一个文件中 `@import 'bootstrap'`或者 `@use bootstrap`模块，再去使用这些方法。`bootstrap.scss`文件类似于一个传输中转站，把上下游的成员变量无缝连接起来。

```go
/* bootstrap.scss */

@forward"functions"
@forward"variables";

@forward"mixins";
```

注意，直接写在上游模块的正常的样式仍然会被`@forward`进来。见下例：

_a.scss_

```go
@mixin rounded {
    border-radius: 100px;
}

footer {
    height: 1px;
}
```

_b.scss_

```go
$radius: 3px;
```

_c.scss_

```go
@forward "a";
@forward "b";
```

_index.scss_

```go
@import "c.scss";
.button {
    @include rounded;
    padding: $radius;
}
```

编译为

```go
footer {
    height: 1px;
}
.button {
    border-radius: 100px;
    padding: 3px;
}
```

### show/ hide

通过控制 `show`和 `hide`，可以决定模块中的哪些成员对引入后的模板可见。对隐藏的变量，在下游文件中不可以使用，相当于模块私有成员。

_c.scss_

```go
@forward "a" show rounded;
@forward "b" hide $radius;
```

_index.css_

```go
@import "c.scss";
.button {
    @include rounded;
    padding: $radius;
}
// Error: Undefined variable. padding: $radius;
```

### 使用as *号为子模块添加前缀

大多数情况下，一个样式库会存在一个入口文件`index.scss`，然后在`index.scss`中引入其他的子文件。这种结构类似于一个多合一模块。那么，如果要在某一文件中 `@forward`多个子模块，就可以使用`as <prefix>-*`语句，为子模块下的成员自动带上前缀以区分。

_c.scss_

```go
@forward "a" as mixin-*;
@forward "b" as var-*;
```

_index.css_

```go
@import "c.scss";
.button {
    @include mixin-rounded;
    padding: $var-radius;
}
```

很多内置的方法就是这样使用的，嘿嘿！

## 13、[@at-root ](/at-root ) 

`@at-root`用来跳出嵌套，在多级嵌套时比较常用，包含`without`和`with`。

### 用法

```go
//没有跳出
.parent-1 {
    color:#f00;
    .child {
        width:100px;
    }
}

//单个选择器跳出
.parent-2 {
    color:#f00;
    @at-root .child {
        width:200px;
    }
}

//多个选择器跳出
.parent-3 {
    background:#f00;
    @at-root {
        .child1 {
            width:300px;
        }
        .child2 {
           width:400px;
        }
    }
}
```

编译为

```go
.parent-1 {
    color: #f00;
}
.parent-1 .child {
    width: 100px;
}
.parent-2 {
    color: #f00;
}
.child {
    width: 200px;
}
.parent-3 {
    background: #f00;
}
.child1 {
    width: 300px;
}
.child2 {
    width: 400px;
}
```

### @without和with

默认`@at-root`只会跳出选择器嵌套，而不能跳出`@media`或`@support`，如果要跳出这两种，则需使用`@at-root (without: media)`或`@at-root (without: support)`，`@at-root`的关键词有四个：

1. `all`表示所有；
2. `rule`表示常规`css`选择器；
3. `media` 表示`media`；
4. `support`表示`support`（`@support`主要是用于检测浏览器是否支持`css`的某个属性）。

我们默认的`@at-root`是`@at-root (without:rule)`

```go
/*跳出父级元素嵌套*/
@media print {
    .parent1{
        color:#f00;
        @at-root .child1 {
            width:200px;
        }
    }
}

/*跳出media嵌套，父级有效*/
@media print {
    .parent2{
        color:#f00;
        @at-root (without: media) {
            .child2 {
                width:200px;
            }
        }
    }
}

/*跳出media和父级*/
@media print {
    .parent3{
        color:#f00;
        @at-root (without: all) {
            .child3 {
                width:200px;
            }
        }
    }
}
```

编译成

```go
/*跳出父级元素嵌套*/
@media print {
    .parent1 {
        color: #f00;
    }
    .child1 {
        width: 200px;
    }
}

/*跳出media嵌套，父级有效*/
@media print {
    .parent2 {
        color: #f00;
    }
}

.parent2 .child2 {
    width: 200px;
}

/*跳出media和父级*/
@media print {
    .parent3 {
        color: #f00;
    }
}
.child3 {
    width: 200px;
}
```

### @at-root与 & 配合使用

```go
.child{
    @at-root .parent &{
        color:#f00;
    }
}
```

编译成

```go
.parent .child {
    color: #f00;
}
```

### 应用于[@keyframe ](/keyframe ) 

```go
.demo {
    animation: motion 3s infinite;
    @at-root {
        @keyframes motion {
        }
    }
}
```

编译成

```go
.demo {
    animation: motion 3s infinite;
}
@keyframes motion {}
```

## 14、Scss内置扩展

`scss`内置扩展分为`color list map math meta selector string`等，扩展也就是`scss`内置的一些`function`，每个模块下内容比较多，这里用一些常用的进行举例。

内置函数可以使用`@use`模块化引入，也可以直接使用他提供的全局函数名调用，以下两种方式是一样的。

```go
@use 'sass:list';
p {
    color: nth($list: red blue green, $n: 2); // blue
    color: list.nth($list: red blue green, $n: 2); // blue
}
```

### color

`scss`包含很多操作颜色的函数。例如`lighten()`与 `darken()`可用于调亮或调暗颜色，`opacify()`使颜色透明度减少，`transparent()`使颜色透明度增加，`mix()`用来混合两种颜色。

```go
.p1 {
    // 让颜色变亮
    color:scale-color(#5c7a29, $lightness: +30%)
}
.p2 {
    // 让颜色变暗
    color:scale-color(#5c7a29, $lightness: -15%);
}
.p3 {
    // 降低颜色透明度
    color:scale-color(#5c7a29, $alpha: -40%);
}
```

编译为

```go
.p1 {
    color: #95c249;
}
.p2 {
    color: #4e6823;
}
.p3 {
    color: rgba(92, 122, 41, 0.6);
}
```

### String

`scss`有许多处理字符串的函数，比如向字符串添加引号的`quote()`、获取字符串长度的`string-length()`和将内容插入字符串给定位置的`string-insert()`。

```go
p {
    &:after {
        content: quote(这是里面的内容);
    }
    background-color: unquote($string: "#F00");
    z-index:str-length("scss学习");
}
```

编译为

```go
p {
    background-color: #F00;
    z-index: 6;
}
p:after {
    content: "这是里面的内容";
}
```

### Math

数值函数处理数值计算，例如：`percentage()`将无单元的数值转换为百分比，`round()`将数字四舍五入为最接近的整数，`min()`和`max()`获取几个数字中的最小值或最大值，`random()`返回一个随机数。

```go
p {
    z-index: abs(-15); // 15
    z-index: ceil(5.8); //6
    z-index: max(5, 1, 6, 8, 3); //8
    opacity: random(); // 随机 0-1
}
```

编译为

```go
p {
    z-index: 15;
    z-index: 6;
    z-index: max(5, 1, 6, 8, 3);
    opacity: 0.8636254167;
}
```

### List

`List`函数操作`List`，`length()`返回列表长度，`nth()`返回列表中的特定项，`join()`将两个列表连接在一起，a`ppend()`在列表末尾添加一个值。

```go
p {
    z-index: length(12px); //1
    z-index: length(12px 5px 8px); //3
    z-index: index(a b c d, c); //3
    padding: append(10px 20px, 30px); // 10px 20px 30px
    color: nth($list: red blue green, $n: 2); // blue
}
```

编译为

```go
p {
    z-index: 1;
    z-index: 3;
    z-index: 3;
    padding: 10px 20px 30px;
    color: blue;
}
```

### Map

`Map`函数操作`Map`，`map-get()`根据键值获取`map`中的对应值，`map-merge()`来将两个`map`合并成一个新的`map`，`map-values()`映射中的所有值。

```go
$font-sizes: ("small": 12px, "normal": 18px, "large": 24px);
$padding:(top:10px, right:20px, bottom:10px, left:30px);
p {
    font-size: map-get($font-sizes, "normal"); //18px
    @if map-has-key($padding, "right") {
        padding-right: map-get($padding, "right");
    }
    &:after {
        content: map-keys($font-sizes) + " "+ map-values($padding) + "";
    }
}
```

编译为

```go
p {
    font-size: 18px;
    padding-right: 20px;
}

p:after {
    content: '"small", "normal", "large" 10px, 20px, 10px, 30px';
}
```

### selector

选择符相关函数可对选择`css`进行一些相应的操作，例如：`selector-append()`可以把一个选择符附加到另一个选择符，`selector-unify()`将两组选择器合成一个复合选择器。

```go
@use 'sass:selector';
@debug selector.is-superselector("a", "a"); // true


// 可以直接使用@forward下的前缀
@debug selector-append("a", ".disabled"); // a.disabled
@debug selector-extend("a.disabled", "a", ".link"); // a.disabled, .link.disabled
.header {
    content: selector-append(".a", ".b", ".c") + '';
    content: selector-unify("a", ".disabled") + '';
}
```

编译为

```go
.header {
    content: ".a.b.c";
    content: "a.disabled";
}
```

### meta

`meta`提供一个`mixin`和一些原子级别的`function`，比如使用`meta.calc-args`获取方法的参数，`meta.calc-name`获取方法名。

#### meta.load-css

`meta.load-css($url，$with:())`该`mixin`可以把`$url`中`css`样式全部包含进来。注意，`$url`引入的函数，变量和`mixin`在 `meta.load-css()`后的`scss`中并不能用，它只会返回编译后的`css`代码。它的第二个参数可以修改使用了`!default`的变量。

_src/corners_

```go
$border-contrast: false !default;
code {
    background-color: #6b717f;
    color: #d2e1dd;
    @if $border-contrast {
        border-color: #dadbdf;
    }
}
```

_index.scss_

```go
@use "sass:meta";
body.dark {
    @include meta.load-css("src/corners", $with: ("border-contrast": true));
}
```

编译为

```go
body.dark code {
    background-color: #6b717f;
    color: #d2e1dd;
    border-color: #dadbdf;

}
```

#### function

```go
@use "sass:meta";
@debug meta.calc-args(calc(100px + 10%)); // unquote("100px + 10%")
@debug meta.calc-args(clamp(50px, var(--width), 1000px)); // 50px, unquote("var(--width)"), 1000px
@debug meta.calc-name(calc(100px + 10%)); // "calc"
@debug meta.calc-name(clamp(50px, var(--width), 1000px)); // "clamp"
```
