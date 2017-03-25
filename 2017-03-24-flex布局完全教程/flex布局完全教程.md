---
title: flex布局完全教程
date: 2017-03-24 00:02:04
tags:
- Flexbox
- CSS
- CSS布局
- 前端基础
---
> 原文：[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
 作者：[CHRIS COYIER](https://css-tricks.com/author/chriscoyier/)
 译者：Shelley Lee
 本文同时发布于[知乎专栏：前端指南](https://zhuanlan.zhihu.com/fe-guide)
 转载需提前联系译者，未经允许不得转载。
 

## 背景介绍

Flexbox 布局（也叫弹性盒子布局，目前在W3C最后一轮工作草案中）模块目标在于提供一个更有效地布局、对齐方式，并且能够使父元素在子元素的大小未知或动态变化情况下仍然能够分配好子元素之间的间隙。

Flex布局的主要思想是使父元素能够调节子元素的高度、宽度和排布的顺序，从而能够最好地适应可用布局空间（能够适应不同的设备和不同大小的屏幕）。设定为flex布局的父元素（容器）能够放大子元素使之尽可能填充可用空间，也可以收缩子元素使之不溢出。

最重要的是，与传统布局中块状元素按照垂直方向摆放，行内元素按照水平方向摆放相比，flex布局是无方向的。传统布局在应对大型复杂的布局时缺乏灵活性，特别是在改变方向、改变大小、伸展、收缩等等方面。

Note: Flex 布局比较适合小规模的布局，Gird布局面向更大规模的布局。

## 基本概念

Flex布局是一个完整的模块而不是一个单独的属性，它包括了完整的一套属性。其中有的属性是设置在容器（container，也可以叫做父元素，称为`flex container`）上，有的则是设置在容器的子元素上（flex items）上。

<!--more--> 

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/flex-container.svg)
![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/flex-items.svg)

> 译者注：由于item译成项目不够直观和形象，以下统一用父元素指代container，子元素指代item。

如果我们可以说传统布局是建立在块状元素垂直流和行内元素水平流上的，那么flex布局就是建立在"flex-flow方向"上的，通过下图解释flex布局的主要思想。

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/flexbox.png)

在flex布局中，子元素要么按照主轴也就是`main axis`（从`main-start`到`main-end`）排布，要么按照交叉轴，也就是`cross axis`(从`cross-start`到`cross-end`)排布。

下面介绍几个概念：

*  __main axis__: Flex 父元素的主轴是指子元素布局的主要方向轴，注意主轴不一定是水平的，它由属性`flex-direction`来确定主轴是水平还是垂直的（后面会介绍）。
* __main-start|main-end__: 分别表示主轴的开始和结束，子元素在父元素中会沿着主轴从`main-start`到`main-end`排布。
* __main size__: 单个项目占据主轴的长度大小。
* __cross axis__: 交叉轴，与主轴垂直。
* __cross-start|cross-end__: 分别表示交叉轴的开始和结束。子元素在交叉轴的排布从`cross-start`开始到`cross-end`。
* __cross size__:  子元素在交叉轴方向上的大小。

## 属性介绍

属性分作用于父元素的属性和作用于子元素的属性两部分介绍。

### 父元素属性

#### display

用来定义父元素是一个 flex布局容器。如果设置为`flex`则父元素为块状元素，设置为`inline-flex`父元素呈现为行内元素。

```css
.container {
  display: flex; /* or inline-flex */
}
```

#### flex-direction

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/flex-direction2.svg)

`flex-direction`定义flex布局的主轴方向。flex布局是单方向布局，子元素主要沿着水平行或者垂直列布局。

```css
.container {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

* `row`: 行方向，`flex-direction`的默认值，在`ltr`(left to right, 从左到右)排版方式下从左到右排列，在`rtl`(right to left, 从右到左)排版方式下从右到左排列。
* `row-reverse`: 行反方向，在`ltr`中从右向左，在`rtl`中从左到右。
* `column`: 列方向，与`row`相似，只是从上到下。
* `column-reverse`: 列反方向，与`row-reverse`相似，只是从下岛上。

#### flex-wrap

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/flex-wrap.svg)

默认情况下，flex布局中父元素会把子元素尽可能地排在同一行，通过设置`flex-wrap`来决定是否允许子元素这行排列。

```css
.container{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```
* `nowrap`: 不折行，默认值，所有的子元素会排在一行。
* `wrap`: 折行，子元素会从上到下根据需求折成多行。
* `wrap-reverse`: 从下向上折行，子元素会从下岛上根据需求折成多行。

这里有一些可视化的[示例](https://css-tricks.com/almanac/properties/f/flex-wrap/)。

#### flex-flow

`flex-flow`是`flex-direction`和`flex-wrap`属性的缩写形式。默认值是`row,nowrap`。

```css
flex-flow: <‘flex-direction’> || <‘flex-wrap’>
```

#### justify-content

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/justify-content.svg)

`justify-content`属性定义了子元素沿主轴方向的对齐方式，用来当子元素大小最大的时候，分配主轴上的剩余空间。也可以当子元素超出主轴的时候用来控制子元素的对齐方式。

* `flex-start`: 默认值，朝主轴开始的方向对齐。
* `flex-end`: 朝主轴结束的方向对齐。
* `center`: 沿主轴方向居中。
* `space-between`: 沿主轴两端对齐，第一个子元素在主轴起点，最后一个子元素在主轴终点。
* `space-around`: 沿主轴子元素之间均匀分布。要注意的是子元素看起来间隙是不均匀的，第一个子元素和最后一个子元素离父元素的边缘有一个单位的间隙，但两个子元素之间有两个单位的间隙，因为每个子元素的两侧都有一个单位的间隙。

```css
.container {
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

#### align-items

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/align-items.svg)

`align-items`定义了子元素在交叉轴方向的对齐方向，这是在每个子元素仍然在其原来所在行的基础上所说的。可以看作是交叉轴上的`justify-content`属性;

```css
.container {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```
* flex-start: 按照交叉轴的起点对齐。
* flex-end: 按照交叉轴的终点对齐。
* center: 沿交叉轴方向居中。
* baseline: 按照项目的第一行文字的基线对齐。
* stretch: 默认值，在满足子项目所设置的`min-height`、`max-height`、`height`的情况下拉伸子元素使之填充整个父元素。

#### align-content

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/align-content.svg)

`align-content`是当父元素所包含的行在交叉轴方向有空余部分时如何分配空间。与`justify-content`在主轴上如何对单个子元素对齐很相似。

**注意**：当只有一行的时候，该属性并不起作用。

```css
.container {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```
> **译者注**：该属性中的六个属性值与`justify-content`中的六个属性意思相似，不同之处在于`justify-content`沿主轴方向的作用于单个子元素，而`align-content`沿交叉轴方向作用于行。遂不再赘述各属性值含义。

> **译者注**：注意`align-items`和`align-content`的区别，前者是指在单行内的子元素对齐方式，后者是指多行之间的对齐方式。

#### 父元素属性总结

```css
  display: flex|inline-flex;
  flex-direction: row | row-reverse | column | column-reverse;
  flex-wrap: nowrap | wrap | wrap-reverse;
  flex-flow: <‘flex-direction’> || <‘flex-wrap’>;
  justify-content: flex-start | flex-end | center | space-between | space-around;
  align-items: flex-start | flex-end | center | baseline | stretch;
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
```
### 子元素属性

#### order

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/order-2.svg)

默认情况下，子元素按照代码书写的先后顺序布局，但`order`属性可以更改子元素出现的顺序。

```css
.item {
  order: <integer>;
}
```
> **译者注**：`order`的默认值为0;子元素的`order`值越小，布局越排在前面，参考例图理解。

#### flex-grow

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/flex-grow.svg)

`flex-grow`规定在空间允许的情况下，子元素如何按照比例分配可用剩余空间。如果所有的子元素的属性都设定为`1`,则父元素中的剩余空间会等分给所有子元素。如果其中某个子元素的`flex-grow`设定为2，则在分配剩余空间时该子元素将获得其他元素二倍的空间（至少会尽力获得）。

```css
.item {
  flex-grow: <number>; /* default 0 */
}
```
**注**：`flex-grow`不接受负值。
> **译者注**：默认值为0,意味着即使有剩余空间，各子元素也不会放大。

#### flex-shrink

与`flex-grow`属性类似，`flex-shrink`定义了空间不足时项目的缩小比例。

```css
.item {
  flex-shrink: <number>; /* default 1 */
}
```

**注**： `flex-shrink`不接受负值。
>**译者注**：`flex-shrink`默认值为1, 当所有子元素都为默认值时，则空间不足时子元素会同比例缩小。如果其中某个子元素的`flex-shrink`值为0，则空间不足时该子元素并不会缩小。如果其中某个子元素的`flex-shrink`值为2时，则空间不足时该子元素会以二倍速度缩小。

#### flex-basis
`flex-basis`定义了在计算剩余空间之前子元素默认的大小。可以设置为某个长度（e.g. 20%, 5rem, etc.）或者关键字。关键字`auto`意味着子元素会按照其本来的大小显示。关键字`content`意味着根据内容来确定大小——这个关键字到目前没有被很好地支持，所以测试起来比较困难，与`content`的类似的关键字还有`max-content`, `min-content`, `fit-content`。

```css
.item {
  flex-basis: <length> | auto; /* default auto */
}
```
如果设置为0, 则子元素内容周围的空隙不会根据`flex-grow`按比例分配，如果设置为`auto`，则子元素周围额外的空袭会根据`flex-grow`按照比例分配，如下图：

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/rel-vs-abs-flex.svg)

#### flex

`flex`是`flex-grow`、`flex-shrink`、`flex-basis`三个属性的缩写。其中第二个和第三个参数(`flex-grow`,`flex-basis`)是可选的。默认值为`0 1 auto`。

```css
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

推荐使用缩写形式而不是单独地设置每一个属性，缩写形式中会智能地计算出相关值。

#### align-self

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/align-self.svg)

通过设置某个子元素的`align-self`属性，可以覆盖`align-items`所设置的对齐方式。属性值与`align-items`中的意义相同，不再赘述。

```css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

**注**：`float`,`clear`,和`vertical-align`对flex子元素没有任何影响。

## 示例

### 示例一：水平垂直居中

我们从一个非常非常简单的例子开始，解决一个我们经常会遇到的问题：水平垂直居中。如果使用flex布局会非常简单。

```css
.parent {
  display: flex;
  height: 300px; /* Or whatever */
}

.child {
  width: 100px;  /* Or whatever */
  height: 100px; /* Or whatever */
  margin: auto;  /* Magic! */
}
```
这个主要原因是，在flex布局的父元素中设置`margin`为`auto`会自动吸收额外的空间，所以设置水平垂直的`margin`都为`auto`会使子元素在水平垂直方向上都完美居中。

### 示例二：响应式初体验

现在我们考虑用更多的属性。考虑有6个子元素，有固定的大小，但是我们希望他们能够在改变浏览器宽度的时候仍然可以在水平轴上完美地显示（注意在不使用媒体查询的前提下）。

```css
.flex-container {
  /* 首先我们先创建一个flex布局上下文 */
  display: flex;
  
  /* 然后我们定义flex方向和是否允许子元素换行
   * 注意这与以下代码等价：
   * flex-direction: row;
   * flex-wrap: wrap;
   */
  flex-flow: row wrap;
  
  /* 然后我们定义在剩余空间上子元素如何排布 */
  justify-content: space-around;
}
```

完成。剩下的就是一些其他样式如颜色的设置了。

<iframe src="https://codepen.io/css-tricks/embed/EKEYob?height=409&theme-id=1&slug-hash=EKEYob&default-tab=result&user=css-tricks#0" style="height:300px;width:100%;overflow:hidden" seamless></iframe>

改变浏览器大小，看看布局会有什么变化吧！

### 示例三：响应式导航栏
让我们再尝试一些别的东西。假设我们有一个向右对齐的导航栏在我们网页的最上端，但是我们希望它在中屏上显示时为居中，在小屏上显示为单列。同样使用flex布局，实现起来会很简单。

```css
/* 大屏 */
.navigation {
  display: flex;
  flex-flow: row wrap;
  /* 这里设置对齐主轴方向的末端 */
  justify-content: flex-end;
}

/* 中屏 */
@media all and (max-width: 800px) {
  .navigation {
    /* 当在中屏上，设置居中，并设置剩余空间环绕在子元素左右 */
    justify-content: space-around;
  }
}

/* 小屏 */
@media all and (max-width: 500px) {
  .navigation {
    /* 在小屏上，我们不在使用行作为主轴，而以列为主轴 */
    flex-direction: column;
  }
}
```
<iframe src="https://codepen.io/css-tricks/embed/YqaKYR?height=283&theme-id=1&slug-hash=YqaKYR&default-tab=result&user=css-tricks#0" style="height:300px;width:100%;overflow:hidden" seamless></iframe>

改变浏览器大小，看看布局会有什么变化吧！

### 示例四：移动优先的三栏布局

我们通过灵活使用flex布局尝试一些更好玩的布局。来做一个移动优先的3列布局并带有全屏宽的header和footer。

```css
.wrapper {
  display: flex;
  flex-flow: row wrap;
}

/* 我们要告诉所有的子元素宽度 100% */
.header, .main, .nav, .aside, .footer {
  flex: 1 100%;
}

/* 移动优先依赖于源代码默认的渲染顺序
 * in this case:
 * 1. header
 * 2. nav
 * 3. main
 * 4. aside
 * 5. footer
 */

/* 中屏 */
@media all and (min-width: 600px) {
  /* 我们要告诉两边的sidebar共享一个行 */
  .aside { flex: 1 auto; }
}

/* 大屏幕 */
@media all and (min-width: 800px) {
  /* 通过order设定各个面板的渲染顺序
   * 告诉主要面板元素占用侧栏两倍的空间
   */
  .main { flex: 2 0px; }
  
  .aside-1 { order: 1; }
  .main    { order: 2; }
  .aside-2 { order: 3; }
  .footer  { order: 4; }
}
```

<iframe src="https://codepen.io/css-tricks/embed/jqzNZq?height=359&theme-id=1&slug-hash=jqzNZq&default-tab=result&user=css-tricks#0" style="height:300px;width:100%;overflow:hidden" seamless></iframe>

改变浏览器大小，看看布局会有什么变化吧！

## 浏览器前缀

Flex布局需要一些浏览器前缀来最大力度地兼容大多数的浏览器。Flex布局的前缀不只是在属性前面添加浏览器前缀，不同浏览器下的属性名和属性值都不同，这是因为Flexbox布局的标准一直在变，一共有old, tweener, new 三个版本。

可能处理前缀的最好方法是使用新的语法书写CSS并通过[Autoprefixer](https://css-tricks.com/autoprefixer/)运行CSS，能够很好地处理这个问题。

另外，这里有一个Sass中 @mixin 来处理一些前缀，也可以给你一些处理前缀的启发：

```css
@mixin flexbox() {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
}

@mixin flex($values) {
  -webkit-box-flex: $values;
  -moz-box-flex:  $values;
  -webkit-flex:  $values;
  -ms-flex:  $values;
  flex:  $values;
}

@mixin order($val) {
  -webkit-box-ordinal-group: $val;  
  -moz-box-ordinal-group: $val;     
  -ms-flex-order: $val;     
  -webkit-order: $val;  
  order: $val;
}

.wrapper {
  @include flexbox();
}

.item {
  @include flex(1 200px);
  @include order(2);
}
```

## 其他资源

* [Flexbox in the CSS specifications](http://www.w3.org/TR/css3-flexbox/)
* [Flexbox at MDN](https://developer.mozilla.org/en-US/docs/CSS/Tutorials/Using_CSS_flexible_boxes)
* [Flexbox at Opera](http://dev.opera.com/articles/view/flexbox-basics/)
* [Diving into Flexbox by Bocoup](http://weblog.bocoup.com/dive-into-flexbox/)
* [Mixing syntaxes for best browser support on CSS-Tricks](https://css-tricks.com/using-flexbox/)
* [Flexbox by Raphael Goetter (FR)](http://www.alsacreations.com/tuto/lire/1493-css3-flexbox-layout-module.html)
* [Flexplorer by Bennett Feely](http://bennettfeely.com/flexplorer/)

## Bugs

我见过的最棒的flexbox bug总结是Philip Walton 和 Greg Whitworth的[Flexbugs](https://github.com/philipwalton/flexbugs),是开源的，你可以在上面跟踪动态。

## 浏览器支持

首先看一下Flex布局的三个版本

* (new)是指标准中最近的语法(e.g. `display:flex;`)。
* (tweener)是指2011年以后非官方的临时版本(e.g. `display:flexbox;`)。
* (old)是指2009年以后的旧语法(e.g. `display:box;`)

![](http://ojr0cmiri.bkt.clouddn.com/a-complete-guide-to-flexbox/browser-support.png)

Blackberry browser 10+ 支持新语法。

更多混合使用语法达到最佳浏览器兼容，可以参考[ this article (CSS-Tricks)](https://css-tricks.com/using-flexbox/)或者this article (DevOpera)](http://dev.opera.com/articles/view/advanced-cross-browser-flexbox/#fallbacks)



## 译者的话
网上有不少flex相关教程，但当我看到[CHRIS COYIER](https://css-tricks.com/author/chriscoyier/)的这篇文章时，不禁被其详尽所震撼，最近也在撰写布局相关的文章，故产生了翻译此文的想法。翻译过程中尽量保持原文原貌，部分地方做了小幅调整以便更加符合中文思维。文中图片均来源于原文。水平有限，如有误漏之处，还请读者不吝赐教。最后希望此文能给读者带去帮助。

