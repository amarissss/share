## Less入门学习笔记
### 前言
#### 定义    
  > Less是一种动态样式语言，属于CSS预处理语言的一种。它使用类似CSS的语法，为CSS赋予了动态语言的特性，如变量，运算，继承，函数等，更加方便CSS的编写和维护。
#### 为什么选择Less
  就我所知，CSS的预处理语言有三种：Sass，Less，Stylus。
  相比于Less，Sass和Stylus的语法功能更加全面，但在学习成本和适应时间上，Less占据更大的优势。Less没有去掉CSS的任何功能，而是在现有的语法上，增加许多额外的功能特性。因此，新接触预处理语言，Less将会是不错的选择。
#### 使用Less
1. 直接引入less.js文件
```html
  <link href="css/main.less" rel="stylesheet/less">         // 新建less文件，并引入到html页面中
  <script src="js/less.min.js"></script>                    // 引入下载的less文件
```
2. koala编译器  
  将新建的less文件拖拽到软件中，编译成功会在指定文件夹生成css文件。  
3. 在服务器环境中编译，利用node的包管理器
4. 在编译器中实时编译
### 正文
#### 变量
  变量允许我们单独定义一系列通用的样式，然后在需要的时候去调用。所以在做全局样式调整的时候我们可能只需要修改几行代码就可以了。  
  
**1. 值变量**  
  - 声明方式：@变量名:值  
  - 使用方式：@变量名
```less
/*    Less   */
@style: solid;
@width: 1px;
@color: green;             // 不需加引号
#wrap {
  border: @style @width @color;
}

/* 编译后的css */
#wrap {
  border: solid 1px #008000;
}
```
  在平时的工作中，可以把常用的变量封装到一个文件中，方便代码维护。  
  除了使用变量管理值，变量还可以用在其他地方，比如选择器名称，属性名，URLs以及@import语句中。
  
**2. 选择器变量**    
  - 声明方式：@变量名:值  
  - 使用方式：@{变量名}: {...}  
```less
/*    Less    */
@mySelector: main;
#@{mySelector} {           // 变量名必须用大括号包裹
  color: red;
}

/* 编译后的css */
#main {
  color: red;
}
```
**3. 字符串变量**    
  - 声明方式：@变量名:值  
  - 使用方式：@{变量名}  
```less
/*    Less    */
@base-url: "../img";         // 需加引号
body {
  background: url("@{base-url}/sample.png");
}

/* 编译后的css */
body {
  background: url("../img/sample.png");
}
```
**4. 用变量定义变量**      
```less
/*    Less    */
@fnord:  "Less is more";
@var:    "fnord";          
main::after {
  content: @@var;          // 将@var替换成fnord，即@@var等价于@fnord
}

/* 编译后的css */
main::after {
  content: "Less is more";
}
```
**5. 变量运算**   
  运算提供了加，减，乘，除操作；我们可以做属性值和颜色的运算，这样就可以实现属性值之间的复杂关系。Less中的函数一一映射了JavaScript代码，如果你愿意的话可以操作属性值。      
```less
/*    Less    */
@the-border: 1px;
@base-color: #111;
#header {
  color: @base-color * 3;
  border-right: @the-border * 2;
}

/* 编译后的css */
#header {
  color: #333333;
  border-right: 2px;
}
```
**6. 变量作用域：就近原则**  
```less
/*    Less    */
@var: @a;
@a: 100%;
#wrap {
  width: @var;
  @a: 9%;
}

/* 编译后的css */
#wrap {
  width: 9%;
}
```
- 变量是延迟加载的，在使用前不一定要预先声明。
- 在定义一个变量两次时，只会使用最后定义的变量，Less会从当前作用域中向上搜索。
#### 混合  
  混合可以将一个定义好的class A轻松的引入到另一个class B中，从而简单实现class B继承class A中的所有属性。我们还可以带参数地调用，就像使用函数一样。  

**1. 不带参数的混合方法**  
  .和#均可作为方法前缀。  
```less
/*    Less    */
.background(): {                    // 等价于.background
  background: red;
};
#main {
  .background();                    // 等价于.background
}

/* 编译后的css */
#main {
  background: red;
}
```
**2. 带参数的混合方法**    
```less
/*    Less    */
.border(@border-width){             // 声明方式
  border: solid pink @border-width; 
}
#main{
  .border(2px);                     // 引用方式，必须带着单位
}

/* 编译后的css */
#main {
  border: solid pink 2px;
}
```
**3. 带默认参数的混合方法**     
```less
/*    Less    */
.border-radius(@radius: 5px) { 
  -webkit-border-radius: @radius; 
  -moz-border-radius: @radius; 
  border-radius: @radius; 
}
#main {
  .border-radius();
}

/* 编译后的css */
#main {
  border: solid pink 2px;
}
```
**4. @arguments变量**  
  @arguments包含了所有传递进来的参数. 如果你不想单独处理每一个参数的话就可以像这样写:
```less
/*    Less    */
.box-shadow (@x: 0, @y: 0, @blur: 1px, @color: #000) {
  box-shadow: @arguments;
  -moz-box-shadow: @arguments;
  -webkit-box-shadow: @arguments;
}
.box {
  .box-shadow(2px, 5px);
}

/* 编译后的css */
.box {
  box-shadow: 2px, 5px, 1px, #000;
  -moz-box-shadow: 2px, 5px, 1px, #000;
  -webkit-box-shadow: 2px, 5px, 1px, #000;
}
```
**5. 匹配模式**  
  有些情况下，我们想根据传入的参数来改变混合的默认呈现。类似switch语句。  
```less
/*    Less    */
.triangle(top, @w: 5px, @c: #ccc) {                        // 朝上
  border-width; @w; 
  border-color: transparent transparent @c transparent; 
  border-style: dashed dashed solid dashed; 
}
.triangle(bottom, @w: 5px, @c: #ccc) {                     // 朝下
  border-width; @w; 
  border-color: @c transparent transparent transparent; 
  border-style: solid dashed dashed dashed; 
}
.triangle(left, @w: 5px, @c: #ccc) {                       // 朝左
  border-width; @w; 
  border-color: transparent  @c transparent transparent; 
  border-style: dashed solid dashed dashed; 
}
.triangle(right, @w: 5px, @ c: #ccc) {                     // 朝右
  border-width; @w; 
  border-color: transparent transparent transparent  @c; 
  border-style: dashed dashed dashed solid; 
}
.triangle(@_, @w: 5px, @c: #ccc) {                         // 自带样式
  width: 0; 
  height: 0; 
  overflow: hidden；
}
@switch: right;
#main {
  .triangle(@switch, 100px, red);
}

/* 编译后的css */
#main {
  border-width: 100px;
  border-color: transparent transparent transparent #ff0000;
  border-style: dashed dashed dashed solid;
  width: 0;
  height: 0;
  overflow: hidden;
}
```
**6. 导引表达式**  
  为了尽可能地保留CSS的可声明性，Less通过导引混合来实现条件判断，类似if/else语句。  
  导引中可用的全部比较运算有： > >= = =< <。  
```less
/*    Less    */
#template{
  // and，相当于 与运算 &&，必须条件全部符合才会执行
  .border(@width,@color,@style) when (@width > 100px) and (@width < 500px){
      border:@style @color @width;
  }
  // not，相当于 非运算 !，条件不符合才会执行
  .background(@color) when not (@color >= #666){
      background:@color;
  }
  // 逗号分隔符，相当于 或运算 ||，只要有一个符合条件就会执行
  .height(@h:100px) when (@h>50px) , (@h<500px){
      height: @h;
  }
}
#main{
  #template > .border(200px,#999,solid);
  #template .background(#111);
  #template > .height(200px);
}

/* 编译后的css */
#main{
  border:solid #999 200px;
  background:#111;
  height:200px;
}
```
  如果想基于值的类型进行匹配，我们就可以使用 is* 函式：  
```less
#template (@a, @b: 0) when (isnumber(@b)) { ... }
#template (@a, @b: black) when (iscolor(@b)) { ... }
```
#### 扩展
  > extend是Less的一个伪类，它会合并它所在的选择器和它所匹配的引用。
```less
/*    Less    */
nav ul {
  &:extend(.inline);           // “&”代表所有祖先元素
  background: blue;
}
.inline {
  color: red;
}

/* 编译后的css */
nav ul {
  background: blue;
}
.inline,
nav ul {
  color: red;
}
```  
- 与混合的区别：**CSS尺寸归并**。Mixins会复制所有的属性到选择器中，这可能导致不必要的重复。而Extend是将要用的属性移过去，这样就会生成更少的CSS。
```less
/*    Less    */
.sample() {
  color: red;
}
.demo1 {
  .sample;
}
.demo2 {
  .sample;
}

/* （用Mixins）编译后的css */
.demo1 {
  color: red;
},
.demo2 {
  color: red;
}

/* （用Extend）编译后的css */
.demo1,
.demo2 {
  color: red;
}
```  
- extend可以附加给一个选择器，也可以放入一个规则集中。
```less
// 下面两种写法等价
.a:extend(.b) {}
.a{
  &:extend(.b);
}
```
- 可以使用关键字all选择相邻的选择器。当在extend参数的最后面指定all关键字时，这个选择器会被复制，然后匹配的选择器部分会使用扩展替换，创建一个新的选择器。
```less
/*    Less    */
.c.test,
.test.d {
  color: orange;
}
.replacement:extend(.test all) {}

/* 编译后的css */
.c.test,
.test.d,
.c.replacement,
.replacement.d {
  color: orange;
}
```
- 允许有多个扩展
```less
// 下面两种写法等价
.e:extend(.f, .g){}
.e:extend(.f):extend(.g){}
```
- extend默认会在选择器之间寻找精确匹配。不管选择器是否以“*”开头，伪类的顺序是否相同，nth表达式是否具有相同的意义，它们必须以相同的形式匹配。唯一例外的是属性选择器内的引号。
- 扩展必须放在最后。
#### 嵌套规则  
  可以在一个选择器中嵌套另一个选择器来实现继承，这样很大程度减少了代码量，并且代码看起来更加的清晰。  
  备注：嵌套层数最好不超过三层，否则不利于代码维护。
```less
/*    Less    */
#header() {
  color: black;
  .detail(@w: 300px) {
    width: @w;
    .logo(@h: 300px) {
      height: @h;
      &:hover {
        text-decoration: none;
      }
    }
  }
}
#main{
  #header > .detail > .logo(100px);            // 父元素不能加括号
}
#main{
  #header .d();
}
#wrap{                                         // .detail() 如果前面没有引入命名空间 #header ，将会报错
  #header;                                     // 等价于 #header();
  .detail;                                     // 必须先引入 #header
}

/* 编译后的css */
#main {
  height: 100px;
}
#main:hover {
  text-decoration: none;
}
#main {
  width: 300px;
}
#wrap {
  color: black;
  width: 300px;
}
```
#### 循环
> 在Less中，混合可以调用它自身。这样，当一个混合递归调用自己，再结合Guard表达式和模式匹配这两个特性，就可以写出循环结构。
经典用例就是生成栅格系统的CSS。
```less
/*    Less    */
.generate-columns(@n, @i: 1) when (@i =< @n) {
  .column-@{i} {
    width: (@i * 100% / @n);                // 每次递归时产生的样式代码
  }
  .generate-columns(@n, (@i + 1));          // 递归调用自身
}
.generate-columns(4);

/* 编译后的css */
.column-1 {
  width: 25%;
}
.column-2 {
  width: 50%;
}
.column-3 {
  width: 75%;
}
.column-4 {
  width: 100%;
}
```
#### Importing 
**1. 导入准则**
  - 引入less文件，.less后缀可带可不带。
  - 引入css文件且不想Less对它进行处理，只需要使用.css后缀就可以。  
  - @import的位置可随意放置。
```html
@import 'lib.less';
@import 'lib';
@import (reference) "bootstrap.less"; 
```
**2. 导入选项**
  语法：@import (keyword) "filename";
  - reference：使用Less文件但不输出
  - inline：在输出中包含源文件但不加工它
  - less：将文件作为Less文件对象，无论是什么文件扩展名
  - css：将文件作为CSS文件对象，无论是什么文件扩展名
  - once：只包含文件一次（默认行为）
  - multiple：包含文件多次
#### 其他  
**1. 注释**  
  - /**/ 编译
  - // 不编译  

**2. 避免编译**  
  有时候我们需要输出一些不正确的CSS语法或者使用一些Less不认识的专有语法，要输出这样的值可以在字符串前加~。  
```less
/*    Less    */
#demo {
  width; ~'calc(100% - 30px)'
}

/* 编译后的css */
#demo {
  width: calc(100% - 30px);                 // 让浏览器去计算
}
```
**3. 使用JS表达式**  
  JavaScript表达式也可以在.less文件中使用，可以通过反引号的方式使用。  
```less
/*    Less    */
@content:`"less".toUpperCase()`;
#randomColor{
  @randomColor: ~"rgb(`Math.round(Math.random() * 256)`,`Math.round(Math.random() * 256)`,`Math.round(Math.random() * 256)`)";
}
#wrap{
  &:after{
    content:@content;
  }
  #randomColor;
  background-color: @randomColor;
}

/* 编译后的css */
#wrap {
  background-color: rgb(38,41,197);          // 随机颜色 
}
#wrap:after {
  content: "LESS";
}
```
### Ending...
