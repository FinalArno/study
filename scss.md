# vue 项目中使用 scss 的方法

1. 首先安装依赖

   ```
   npm install node-sass sass-loader --save-dev
   ```

2. 找到 build 中 webpack.base.conf.js 在 rules 中添加 scss 规则

   ```
   {
        test: /\.scss$/,
        loaders: ['style', 'css', 'sass']
   }
   ```

3. 在 Vue 文件中使用

   ```scss
   <style lang='scss'></style>
   ```

# Scss 跟 Sass 的关系

Scss 是 Sass3 引入新的语法,其语法完全兼容 Css3，并且继承了 Sass 的强大功能，也就是说，任何 css3 样式表都是具有相同语义的有效的 Scss 文件。另外 Scss 还能识别大部分 CSS hacks（一些 CSS 小技巧）和特定的浏览器语法。所以所有的 CSS 语法都能在 Scss 中正常运行。

**Scss 与 Sass 唯一的不同是 Scss 需要使用分号和花括号，而 Sass 使用缩进与换行**，例如：
Sass 代码片段：

```css
#sidebar
  width: 30%
  background-color: #faa
```

Scss 代码片段：

```scss
#sidebar {
  width: 30%;
  background-color: #faa;
}
```

另外，scss 对空白符号不敏感，可以不用换行

# Scss 语法

## 1.使用变量

## 1.1 变量申明

变量可以申明在{}之外，也可以申明在其中。如果在{}之内，则只能供此规则块内使用，如果想将规则块中的局部变量变成全局变量，在后面加上!global 即可。

```scss
$hightlight-color: #f90;
```

## 1.2 变量引用

```scss
$hightlight-color: #f90;
.selected {
  color: $hightlight-color;
  $width: 100px !global; //这里就将局部变量变成了全局变量
  width: $width;
  border: 1px solid $hightlight-color;
}
```

变量也可以引用变量
如：

```scss
$hightlight-color: #f90;
$hightlight-border: 1px solid $hightlight-color;
```

## 2.嵌套 css 规则

示例代码：

```scss
#content {
  article {
    h1 {
      color: #333;
    }
    p {
      margin-bottom: 1.4em;
    }
  }
  aside {
    background-color: #eee;
  }
}
```

## 2-1. 父选择器的标识符&;

用于写:hover 这类伪元素或者::after 这些伪类等到

示例代码：

```scss
article {
  color: blue;
  &:hover {
    color: red;
  }
  &::aftter {
    content: "";
    clear: both;
    display: block;
  }
}
```

## 2-2. 群组选择器的嵌套;

代码示例：

```scss
.container {
  h1,
  h2,
  h3 {
    margin-bottom: 0.8em;
  }
}
nav,
aside {
  a {
    color: blue;
  }
}
```

## 2-3. 子组合选择器和同层组合选择器：>、+和~;

> \>表示当前元素的下一级子元素，+表示当前元素后面紧跟的某元素，～表示当前元素的同层相同元素，不管他们之间隔了多少元素

代码示例：

```scss
article {
  ~ article {
    border-top: 1px dashed #ccc;
  }
  > section {
    background: #eee;
  }
  dl > {
    dt {
      color: #333;
    }
    dd {
      color: #555;
    }
  }
  nav + & {
    margin-top: 0;
  }
}
```

## 2-4. 嵌套属性;

注意嵌套属性是在 **:** 后面，即属性名后面加上 **{}**

```scss
nav {
  border: {
    style: solid;
    width: 1px;
    color: #ccc;
  }
}
```

将解析成

```css
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}
```

```scss
nav {
  border: 1px solid #ccc {
    left: 0px;
    right: 0px;
  }
}
```

将解析成

```css
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```

## 3. 导入 SASS 文件;

> css 有一个特别不常用的特性，即@import 规则，它允许在一个 css 文件中导入其他 css 文件。然而，后果是只有执行到@import 时，浏览器才会去下载其他 css 文件，这导致页面加载起来特别慢。

sass 也有一个@import 规则，但不同的是，sass 的@import 规则在生成 css 文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个 css 文件中，而无需发起额外的下载请求。另外，所有在被导入文件中定义的变量和混合器（参见 2.5 节）均可在导入文件中使用。

Scss 使用@import 可以省略.sass 或.scss 文件后缀

## 3-1 使用 SASS 部分文件（加\_的 scss 文件不会被编译成）

> 加\_的 scss 文件不会被编译成 css 文件，只是用来引用，而引用时可以省略\_

当通过@import 把 sass 样式分散到多个文件时，你通常只想生成少数几个 css 文件。那些专门为@import 命令而编写的 sass 文件，并不需要生成对应的独立 css 文件，这样的 sass 文件称为局部文件。对此，sass 有一个特殊的约定来命名这些文件。
此约定即，sass 局部文件的文件名以下划线开头。这样，sass 就不会在编译时单独编译这个文件输出 css，而只把这个文件用作导入。当你@import 一个局部文件时，还可以不写文件的全名，即省略文件名开头的下划线。举例来说，你想导入 themes/\_night-sky.scss 这个局部文件里的变量，你只需在样式表中写 **@import "themes/night-sky";**。

## 3-2. 默认变量值;

!default 标签很像 css 属性中!important 标签的对立面，不同的是!default 用于变量，含义是：如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

```scss
$fancybox-width: 400px !default;
.fancybox {
  width: $fancybox-width;
}
```

## 3-3.嵌套导入

跟原生的 css 不同，sass 允许@import 命令写在 css 规则内。这种导入方式下，生成对应的 css 文件时，局部文件会被直接插入到 css 规则内导入它的地方。举例说明，有一个名为\_blue-theme.scss 的局部文件，内容如下：

```scss
.classA {
  color: red;
  width: 100px;
}
```

可以导入在 css 规则块{}内

```scss
.classB {
  @import "blue-theme";
}
//生成如下代码
.classB {
  .classA {
    color: red;
    width: 100px;
  }
}
```

## 3-4.原生的 CSS 导入；

你不能用 sass 的@import 直接导入一个原始的 css 文件，因为 sass 会认为你想用 css 原生的@import。但是，因为 **sass 的语法完全兼容 css，所以你可以把原始的 css 文件改名为.scss 后缀，即可直接导入了**。

## 4. 静默注释;

> sass 另外提供了一种不同于 css 标准注释格式/_ ... _/的注释语法，即静默注释，其内容不会出现在生成的 css 文件中。静默注释的语法跟 JavaScriptJava 等类 C 的语言中单行注释的语法相同，它们以//开头，注释内容直到行末。

## 5.混合器

主要用来将大段重复的样式封装起来，以便于其他地方引用
使用@mixin xxx 标识符号,用@include xxx;来引用，放在任何想放的地方,

```scss
@mixin classA {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

引用方法

```scss
.classA {
  color: red;
  @include classA;
}
```

## 5-1. 何时使用混合器;

将一组大量重复使用的属性组合成一个混合器

## 5-2. 混合器中的 CSS 规则

混合器中不仅可以包含属性，也可以包含 css 规则，包含选择器和选择器中的属性，如下代码:

```scss
@mixin classA {
  list-style: none;
  li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px;
  }
}
```

引用

```scss
.classB {
  color: #444;
  @include classA;
}

//将解析成
.classB {
  color: #444;
  list-style: none;
}
.classB li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0px;
}
```

混合器中的规则甚至可以使用 sass 的父选择器标识符&。使用起来跟不用混合器时一样，sass 解开嵌套规则时，用父规则中的选择器替代&。

## 5-3. 给混合器传参;

> 混合器并不一定总得生成相同的样式。可以通过在@include 混合器时给混合器传参，来定制混合器生成的精确样式。当@include 混合器时，参数其实就是可以赋值给 css 属性值的变量。如果你写过 JavaScript，这种方式跟 JavaScript 的 function 很像：

```scss
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover {
    color: $hover;
  }
  &:visited {
    color: $visited;
  }
}
```

使用方法

```scss
a {
  @include link-colors(blue, red, green);
}

//Sass最终生成的是：

a {
  color: blue;
}
a:hover {
  color: red;
}
a:visited {
  color: green;
}
```

当你@include 混合器时，有时候可能会很难区分每个参数是什么意思，参数之间是一个什么样的顺序。为了解决这个问题，sass 允许通过语法\$name: value 的形式指定每个参数的值。这种形式的传参，参数顺序就不必再在乎了，只需要保证没有漏掉参数即可：

```scss
a {
  @include link-colors($normal: blue, $visited: green, $hover: red);
}
```

## 5-4. 默认参数值;

为了在@include 混合器时不必传入所有的参数，我们可以给参数指定一个默认值。参数默认值使用\$name: default-value 的声明形式，默认值可以是任何有效的 css 属性值，甚至是其他参数的引用，如下代码：

```scss
@mixin link-colors($normal, $hover: $normal, $visited: $normal) {
  color: $normal;
  &:hover {
    color: $hover;
  }
  &:visited {
    color: $visited;
  }
}
```

如果像下边这样调用：@include link-colors(red) $hover和$visited 也会被自动赋值为 red。

## 6. 使用选择器继承来精简 CSS;

通过@extend 语法实现

```scss
//通过选择器继承继承样式
.error {
  border: 1px solid red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

.seriousError 不仅会继承.error 自身的所有样式，任何跟.error 有关的组合选择器样式也会被.seriousError 以组合选择器的形式继承，如下代码:

```scss
//.seriousError从.error继承样式
.error a {
  //应用到.seriousError a
  color: red;
  font-weight: 100;
}
h1.error {
  //应用到hl.seriousError
  font-size: 1.2rem;
}
```

如上所示，在 class="seriousError"的 html 元素内的超链接也会变成红色和粗体。

## 6-2.继承的高级用法

可以继承所有的 html 元素自带的样式
如继承 a 标签的样式：

```scss
.disabled {
  color: grey;
  @extend a;
}
```

## 6-3 继承的好处

@extend 可以提高站点的速度，让你的 css 美观整洁，但是不要在 css 规则中使用后代选择器（比如 .foo .bar）去继承 css 规则
