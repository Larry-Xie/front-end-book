# CSS 布局

## 一、引言

在我们前端开发时，经常会遇到不同的场景页面布局问题，页面框架搭建是前端开发基础，也是十分重要的一环。本文先是对页面开发基本流程思路进行了总结，在此基础上对 CSS 布局常见方案进行梳理，涉及水平垂直居中、单列布局、两栏布局、圣杯布局、双飞翼布局、等高布局以及粘带布局。

![CSS 布局](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9c8f494e93f94c8a9bf1a3b8c0dbfd9a\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

## 二、布局思路

除了水平垂直居中的布局有些许特殊的方案可以解决之外，其它的整体布局借助的**思路或者方案不外乎以下几种**：

* **display**: 文档流布局，根据 CSS 默认文档流的布局方式，通过改变元素的显示方式（block, inline, inline-block, table）或者生成 BFC，IFC 来达到改变布局的目的。
* **float**: 浮动布局，根据浮动元素能够脱离文档流的特性，来达到改变布局的目的。
* **position**: 定位布局，除了默认的 static 值外，其它值都能改变元素的的位置。
* **flex**: 弹性布局，通过设置 display 为 flex 或 inline-flex，以及借助 flex 相关的其它属性，能够很方便地实现很多复杂的布局，详细的 flex 布局可以参见[这里](flex-bu-ju.md)。
* **grid**: 网格布局，通过设置 display 为 grid 或 inline-grid，以及借助 grid 相关的其它属性，能够很方便地实现很多复杂的布局，详细的 grid 布局可以参见[这里](grid-bu-ju.md)。

以下三、四、五（水平垂直居中）章都是 parent + son 的简单结构，html 代码和效果图就不贴出来了。

## 三、水平居中

### **1. text-align**

#### **原理**

text-align 只控制行内内容(文字、行内元素、行内块级元素)如何相对他的块父元素对齐。

```css
/* 对于本身子元素就是行内元素，直接对父元素设置 */
#parent {
    text-align: center;
}
```

```css
/* 如果子元素是块元素，可以转成行内元素，然后对父元素设置 */
#parent{
    text-align: center;
}
.son{
    display: inline-block; /*改为行内或者行内块级形式，以达到text-align对其生效*/
}
```

#### 优点

* 简单快捷，容易理解，兼容性非常好

#### 缺点

* 只对行内内容有效；
* 属性会继承影响到后代行内内容；
* 如果子元素宽度大于父元素宽度则无效，只有后代行内内容中宽度小于设置 text-align 属性的元素宽度的时候，才会水平居中；
* 块级改为 inline-block，换行、空格会产生元素间隔；

### **2. margin**

#### 原理

根据[规范](https://link.juejin.cn/?target=https%3A%2F%2Fwww.w3.org%2FTR%2FCSS21%2Fvisudet.html%23Computing\_widths\_and\_margins)介绍得很清楚了，有这么一种情况：在 margin 有节余的同时如果左右 margin 设置了 auto，将会均分剩余空间。另外，如果上下的 margin 设置了auto，其计算值为 0。

```css
#son{
    width: 100px; /*必须定宽*/
    margin: 0 auto;
}
```

#### 优点

* 简单；兼容性好

#### 缺点

* 必须定宽，并且值不能为 auto；
* 宽度要小于父元素，否则无效；

### **3. 绝对定位**

#### 原理

子绝父相，top、right、bottom、left 的值是相对于父元素尺寸的，然后 margin 或者 transform 是相对于自身尺寸的，组合使用达到水平居中的目的。

```css
#parent{
    height: 200px;
    width: 200px;  /*定宽*/
    position: relative;  /*父相*/
    background-color: #f00;
}
#son{
    position: absolute;  /*子绝*/
    left: 50%;  /*父元素宽度一半,这里等同于left:100px*/
    transform: translateX(-50%);  /*自身宽度一半,等同于margin-left: -50px;*/
    width: 100px;  /*定宽*/
    height: 100px;
    background-color: #00ff00;
}
```

#### 优点

* 使用 margin-left 兼容性好；
* 不管是块级还是行内元素都可以实现；

#### 缺点

* 代码较多；
* 脱离文档流；
* 使用 margin-left 需要知道宽度值；
* 使用 transform 要考虑兼容性不好；

### **4. flex**

#### 原理

就是设置当前主轴对齐方式为居中。

```css
#parent{
    display: flex;
    justify-content: center;
}
```

#### 优点

* 功能强大；
* 简单方便；
* 容易理解

#### 缺点

* 使用 flex 布局要考虑兼容性；

### 5. 总结对比

* 对于水平居中，我们应该先考虑，哪些元素有自带的居中效果，最先想到的应该就是 `text-align:center` 了，但是这个只对行内内容有效，所以我们要使用 `text-align:center` 就必须将子元素设置为 `display: inline;` 或者 `display: inline-block;` ；
* 其次就是考虑能不能用`margin: 0 auto;` ，因为这都是一两句代码能搞定的事；
* 实在不行就是用绝对定位去实现了；
* 能用 flex 就用 flex，简单方便，灵活并且功能强大；

## 四、垂直居中

### 1. line-height

#### 原理

line-height 的最终表现是通过 inline box 实现的，而无论 inline box 所占据的高度是多少（无论比文字大还是比文字小），其占据的空间都是与文字内容公用水平中垂线的。

```css
/* 对于单行内容 */
#parent{
    height: 150px;
    line-height: 150px;  /*与height等值*/
}

/* 对于多行内容 */
#parent{  /*或者用span把所有文字包裹起来，设置display：inline-block转换成图片的方式解决*/
    height: 150px;
    line-height: 30px;  /*元素在页面呈现为5行,则line-height的值为height/5*/
}
```

#### 优点

* 简单，兼容性好；

#### 缺点

* 只能用于行内内容；
* 对于单行内容要知道高度的值；
* 对于多行的内容需要知道高度和最终呈现多少行来计算出 line-height 的值，建议用 span 包裹多行文本；

### **2. vertical-align**

#### 原理

[vertical-align和line-height的基友关系](https://link.juejin.cn/?target=http%3A%2F%2Fwww.zhangxinxu.com%2Fwordpress%2F2015%2F08%2Fcss-deep-understand-vertical-align-and-line-height%2F)

```css
#parent{
    height: 150px;
    line-height: 150px;
    font-size: 0;
}
img#son{  // 通常作用于图片
    vertical-align: middle;
}
```

#### 优点

* 简单；
* 兼容性好

#### 缺点

* 需要添加 font-size: 0; 才可以完全的垂直居中；
* html#parent 包裹 img 之间需要有换行或空格；

### **3. tabel-cell**

#### 原理

CSS Table，使表格内容对齐方式为 middle

```css
#parent{
    display: table-cell;
    vertical-align: middle;
}
```

#### 优点

* 简单；
* 宽高不定；
* 兼容性好；

#### 缺点

* 设置 tabl-cell 的元素，宽度和高度的值设置百分比无效，需要给它的父元素设置 display: table; 才生效；
* table-cell 不感知margin，在父元素上设置 table-row 等属性，也会使其不感知 height；

### 4. 绝对定位

#### 原理

子绝父相，top、right、bottom、left 的值是相对于父元素尺寸的，然后 margin 或者 transform 是相对于自身尺寸的，组合使用达到水平居中的目的。

```css
#parent{
    height: 150px;
    position: relative;  /*父相*/
}
#son{
    position: absolute;  /*子绝*/
    top: 50%;  /*父元素高度一半,这里等同于top:75px;*/
    transform: translateY(-50%);  /*自身高度一半,这里等同于margin-top:-25px;*/
    height: 50px;
}
```

#### 优点

* 使用 margin 兼容性好；
* 不管是块级还是行内元素都可以实现；

#### 缺点

* 代码较多；
* 脱离文档流；
* 使用 margin 需要知道宽度值；
* 使用 transform 要考虑兼容性不好；

### **5. flex**

#### 原理

flex 设置子元素对齐方式。

```css
/* 主轴为水平方向 */
#parent{
    display: flex;
    align-items: center;
}

/* 主轴为垂直方向 */
#parent{
    display: flex;
    flex-direction: column;
    justify-content: center;
}
```

优点

* 简单灵活；
* 功能强大；

缺点

* 需要考虑兼容性；

### 6. 总结对比

* 对于垂直居中，最先想到的应该就是 `line-height` 了，但是这个只能用于行内内容；
* 其次就是考虑能不能用`vertical-align: middle;` ，不过这个一定要熟知原理才能用得顺手，建议看下[vertical-align和line-height的基友关系](https://link.juejin.cn/?target=http%3A%2F%2Fwww.zhangxinxu.com%2Fwordpress%2F2015%2F08%2Fcss-deep-understand-vertical-align-and-line-height%2F) ；
* 然后便是绝对定位，虽然代码多了点，但是胜在适用于不同情况；
* 移动端兼容性允许的情况下能用 flex 就用 flex；

## 五、两栏布局

对于两栏布局，常见的场景是：

* 一列定宽，一列自适应；
* 一列不定宽（随着内容的增减而宽度变化），一列自适应；

![定宽 + 自适应](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/3/9/1620a136d179e360\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

![不定宽 + 自适应](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/3/9/1620a136d19c5afc\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

对于这两种场景，主要的解决方案是 float，flex 或者 grid，当然也能通过绝对定位和 table 属性来达到效果，但是用的少。

HTML 如下**：**

```html
<body>
    <div id="left">左列定宽/不定宽</div>
    <div id="right">右列自适应</div>
</body>
```

### **1. float + overflow**

对于一列定宽，一列自适应：

```css
#left {
    background-color: #f00;
    float: left;
    width: 100px;
    height: 500px;
}
#right {
    background-color: #0f0;
    height: 500px;
    overflow: hidden; /*触发bfc达到自适应*/
}
```

对于一列不定宽，一列自适应：

```css
left {
    margin-right: 10px;
    float: left;  /*只设置浮动,不设宽度*/
    height: 500px;
    background-color: #f00;
}
#right {
    overflow: hidden;  /*触发bfc*/
    height: 500px;
    background-color: #0f0;
}
```

优缺点：

* 优点：代码简单，容易理解，无需关注定宽的宽度，利用 BFC 达到自适应效果；
* 缺点：浮动脱离文档流，需要手动清除浮动，否则会产生高度塌陷；

### **2. flex**

对于对于一列定宽，一列自适应：

```css
#parent{
    width: 100%;
    height: 500px;
    display: flex;
}
#left {
    width: 100px;
    background-color: #f00;
}
#right {
    flex: 1; /*均分了父元素剩余空间*/
    background-color: #0f0;
}
```

对于一列不定宽，一列自适应：

```css
#parent{
    display: flex;
}
#left { /*不设宽度*/
    margin-right: 10px;
    height: 500px;
    background-color: #f00;
}
#right {
    height: 500px;
    background-color: #0f0;
    flex: 1;  /*均分#parent剩余的部分*/
}
```

### **3. grid**

对于对于一列定宽，一列自适应：

```css
#parent {
    width: 100%;
    height: 500px;
    display: grid;
    grid-template-columns: 100px auto;  /*设定2列就ok了,auto换成1fr也行*/
}
#left {
    background-color: #f00;
}
#right {
    background-color: #0f0;
}
```

对于一列不定宽，一列自适应：

```css
#parent{
    display: grid;
    grid-template-columns: auto 1fr;  /*auto和1fr换一下顺序就是左列自适应,右列不定宽了*/
}
#left {
    margin-right: 10px;
    height: 500px;
    background-color: #f00;
}
#right {
    height: 500px;
    background-color: #0f0;
}
```

## 六、三栏布局

对于**三**栏布局，常见的场景是：

* 两列定宽，一列自适应；（该种场景没有特别讨论的必要，把前两列当作一列，然后按照上述两列布局的方式去做就行）
* 两侧定宽，中间自适应；

![两列定宽，一列自适应](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/3/9/1620a136d1ea53c5\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

![两侧定宽，中间自适应](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/3/9/1620a136d1cc24f8\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

下面就具体讨论两侧定宽，中间自适应的解决方案：

### 1. 圣杯布局

#### **特点**

**比较特殊的三栏布局，同样也是两边固定宽度，中间自适应，唯一区别是 dom 结构必须是先写中间列部分，这样实现中间列可以优先加载**。

```css
.container {
  padding-left: 220px;//为左右栏腾出空间
  padding-right: 220px;
}
.left {
  float: left;
  width: 200px;
  height: 400px;
  background: red;
  margin-left: -100%;
  position: relative;
  left: -220px;
}
.center {
  float: left;
  width: 100%;
  height: 500px;
  background: yellow;
}
.right {
  float: left;
  width: 200px;
  height: 400px;
  background: blue;
  margin-left: -200px;
  position: relative;
  right: -220px;
}
```

```html
<article class="container">
  <div class="center">
    <h2>圣杯布局</h2>
  </div>
  <div class="left"></div>
  <div class="right"></div>
</article>
```

#### **实现步骤**

* 三个部分都设定为左浮动，**否则左右两边内容上不去，就不可能与中间列同一行**。然后设置 center 的宽度为 100%(**实现中间列内容自适应**)，此时，left 和 right 部分会跳到下一行：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/10/18/16682cae82722a6a\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

* 通过设置 margin-left 为负值让 left 和 right 部分回到与 center 部分同一行

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/10/18/16682c1d72a1ea68\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

* 通过设置父容器的 padding-left 和 padding-right，让左右两边留出间隙。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/10/18/16682c473f605745\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

* 通过设置相对定位，让 left 和 right 部分移动到两边。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/10/17/16682bf3615502c2\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

#### **缺点**

* center 部分的最小宽度不能小于 left 部分的宽度，否则会 left 部分掉到下一行
* 如果其中一列内容高度拉长(如下图)，其他两列的背景并不会自动填充。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/8/166f229b862b187f\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. 双飞翼布局

#### **特点**

**同样也是三栏布局，在圣杯布局基础上进一步优化，解决了圣杯布局错乱问题，实现了内容与布局的分离。而且任何一栏都可以是最高栏，不会出问题**。

```css
.container {
    min-width: 600px;//确保中间内容可以显示出来，两倍left宽+right宽
}
.left {
    float: left;
    width: 200px;
    height: 400px;
    background: red;
    margin-left: -100%;
}
.center {
    float: left;
    width: 100%;
    height: 500px;
    background: yellow;
}
.center .inner {
    margin: 0 200px; //新增部分
}
.right {
    float: left;
    width: 200px;
    height: 400px;
    background: blue;
    margin-left: -200px;
}
```

```html
<article class="container">
    <div class="center">
        <div class="inner">双飞翼布局</div>
    </div>
    <div class="left"></div>
    <div class="right"></div>
</article>
```

#### **实现步骤(前两步与圣杯布局一样)**

* 三个部分都设定为左浮动，然后设置 center 的宽度为100%，此时，left 和 right 部分会跳到下一行；
* 通过设置 margin-left 为负值让 left 和 right 部分回到与 center 部分同一行；
* **center 部分增加一个内层 div，并设 margin**: 0 200px；

#### **缺点**

多加一层 dom 树节点，增加渲染树生成的计算量。

**多加一层 dom 树节点，增加渲染树生成的计算量**。

### 3. flex

```html
<body>
<div id="parent">
    <div id="left">左列定宽</div>
    <div id="center">中间自适应</div>
    <div id="right">右列定宽</div>
</div>
</body>
```

```css
#parent {
    height: 500px;
    display: flex;
}
#left {
    width: 100px;
    background-color: #f00;
}
#center {
    flex: 1;  /*均分#parent剩余的部分*/
    background-color: #eeff2b;
}
#right {
    width: 200px;
    background-color: #0f0;
}
```

### 4. grid

```html
<body>
<div id="parent">
    <div id="header"></div>
    <!--#center需要放在前面-->
    <div id="center">中间自适应</div>
    <div id="left">左列定宽</div>
    <div id="right">右列定宽</div>
    <div id="footer"></div>
</div>
</body>
```

```css
#parent {
    height: 500px;
    display: grid;
    grid-template-columns: 100px auto 200px; /*设定3列*/
    grid-template-rows: 60px auto 60px; /*设定3行*/
    /*设置网格区域分布*/
    grid-template-areas: 
        "header header header" 
        "leftside main rightside" 
        "footer footer footer";
}
#header {
    grid-area: header; /*指定在哪个网格区域*/
    background-color: #ccc;
}
#left {
    grid-area: leftside;
    background-color: #f00;
    opacity: 0.5;
}
#center {
    grid-area: main; /*指定在哪个网格区域*/
    margin: 0 15px; /*设置间隔*/
    border: 1px solid #000;
    background-color: #eeff2b;
}
#right {
    grid-area: rightside; /*指定在哪个网格区域*/
    background-color: #0f0;
    opacity: 0.5;
}
#footer {
    grid-area: footer; /*指定在哪个网格区域*/
    background-color: #ccc;
}
```

### 5. table

```html
<body>
<div id="parent">
    <div id="left">左列定宽</div>
    <div id="center">中间自适应</div>
    <div id="right">右列定宽</div>
</div>
</body>
```

```css
#parent {
    width: 100%;
    height: 500px;
    display: table;
}
#left {
    display: table-cell;
    width: 100px;
    background-color: #f00;
}
#center {
    display: table-cell;
    background-color: #eeff2b;
}
#right {
    display: table-cell;
    width: 200px;
    background-color: #0f0;
}

复制代码
```

优缺点：

* 优点：代码简洁，容易理解；
* 缺点：margin 失效，采用 border-spacing 表格两边的间隔无法消除；

## 七、等高布局

等高布局是指**子元素**在**父元素中高度相等**的布局方式。

![等高布局](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4aeff4eef616469ebb5aff59c06323eb\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 1. margin + padding

正值内边距+负值外边距，padding 和 margin 相互抵消的效果

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>等高布局-正值内边距+负值外边距</title>
<style type="text/css">
      *{
            margin:0;
            padding:0;
     }
     .left,
     .right {
            width:50%;
            float:left;
            text-align:center;
            background-color:aquamarine;
            /* 设置正值内边距会把背景颜色拉伸 */
            padding-bottom:9999px;
            /* 设置负值外边距把边框往里推 */
            margin-bottom:-9999px;
     }
     .right{
             background-color: pink;	 
     }
     .container {
            width:1200px;
            margin:0 auto;
            /* 开启BFC限制内容 */
            overflow:hidden;
     }
</style>
</head>
<body>
 <div class="container">
     <div class="left">111111111111</div>
     <div class="right">
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
     </div>
 </div>
</body>
</html>
```

### 2. table

弊端：使用 table 布局对一些属性设置有一定限制。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>等高布局-table布局</title>
<style type="text/css">   
 *{
    margin:0;
    padding:0;
 }
 .center,
 .left,
 .right {
    width:33.3%;
    text-align: center;
    display: table-cell;
    background-color:aquamarine;
 }
.container {
    display:table;
    width:1200px;
    margin:0 auto;
 }    
</style>
</head>
<body>
 <div class="container">
      <div class="left">111111111111</div>
      <div class="center">22222222222222222222222222</div>
      <div class="right">
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
     </div>
  </div>
</body>
</html>
```

### 3. flex

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>等高布局-flex布局</title>
<style type="text/css">   
 *{
    margin:0;
    padding:0;
 }
 .center,
 .left,
 .right {
    text-align: center;
    background-color:aquamarine;
    flex: 1
 }
 .container {
    display: flex;
    /* flex-direction: row; */
    width: 1200px;
    margin:0 auto;
 }
</style>
</head>
<body>
 <div class="container">
    <div class="left">111111111111</div>
    <div class="center">22222222222222222222222222</div>
    <div class="right">
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
 </div>
</div>
</body>
</html>
```

### 4. grid

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>grid布局</title>
<style>   
 *{
    margin:0;
    padding:0;
 }
 .center,
 .left,
 .right {
    text-align:center;
    background-color:aquamarine;
    flex:1
}
 .container {
    display:grid;
    grid-auto-flow: column;
    width:1200px;
    margin:0 auto;
}
</style>
</head>
<body>
<div class="container">
    <div class="left">111111111111</div>
    <div class="center">22222222222222222222222222</div>
    <div class="right">
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
 </div>
</div>
</body>
</html>
```

## 八、粘带布局

粘带布局如下：

* 有一块内容`<main>`，当`<main>`的高度足够长的时候，紧跟在`<main>`后面的元素`<footer>`会跟在`<main>`元素的后面。
* 当`<main>`元素比较短的时候(比如小于屏幕的高度)，我们期望这个`<footer>`元素能够“粘连”在屏幕的底部。

![粘带布局](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/12/21/167ceb06bb1ee763\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 1. margin-bottom

把 footer 以外的内容包起来，设置负的 margin-bottom，他正好等于 footer 的高度。

```html
<html>
<head>
<meta charset="UTF-8">
<title>粘带布局-负margin-bottom</title>
 <style>
/* 用一个元素将footer以外的内容包起来,设置负的margin-bottom,让他正好等于footer的高度 */
html, body {
     margin: 0;
     padding:0;
     text-align:center;
}
#wrap{
      min-height:100%;
      background-color: pink;	
      margin-bottom: -30px;
}
#footer,#main{
      height: 30px;
} 
#footer{
     background-color: aquamarine;
}
</style>
</head>
<body>
    <div id="wrap">
        <div id="main">
            main<br/>
            main<br/>
        </div>
    </div>
    <div id="footer">footer</div>
</body>
</html>
```

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32f0fbfc5fc44fb3821aca0f13cbb263\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. margin-top

在 footer 上使用负的 margin-top 达到把 footer 拉上去的作用，前提是内容区域要预留 footer 的高度。

```html
<html>
<head>
<meta charset="UTF-8">
<title>粘带布局-footer 上用负的 margin-top</title>
<style>
    html, body {
	height: 100%;
	margin: 0;
	text-align:center;
   }
     #wrap{
        width: 100%;
        min-height:100%;
        background-color: pink;	
    }
     /*内容区需要让出一部分区域，防止内容被盖住*/
     #main{
        padding-bottom: 30px;
    }
     /*wrap包裹内容的最小高度是100%，此时将footer的部分通过margin-top拉上去30px。 */
     #footer{
        width: 100%;
        height: 30px;
        background-color: aquamarine;
        margin-top: -30px;
    } 
</style>
</head>
<body>
   <div id="wrap">
       <div id="main">
          <p>main</p> 
          <p>main</p>
      </div>
  </div>
  <div id="footer">footer</div>
</body>
</html>
```

### 3. flex

```html
<html>
<head>
<meta charset="UTF-8">
<title>粘带布局-flex</title>
<style>
 html, body {
    margin: 0;
    padding:0;
    text-align:center;
}
 #wrap{
	height:100%;
	display: flex;
	flex-direction: column;
 }
 #main{
	background-color: pink;	
	flex:1;
 } 
 #footer{
	background-color: aquamarine;
	height: 30px;
 }
</style>
</head>
<body>
   <div id="wrap">
       <div id="main">
          <p>main</p> 
          <p>main</p>
      </div>
      <div id="footer">footer</div>
   </div>
</body>
</html>
```

## 九、考点

### 1. 布局方案的使用准则

* 网页开发基本思路：**确定版心--》分析行模块--》分析列模块**。
* 如果一行当中有**多个模块，一定要放在同一个父模块中**。
* 列模块首先可以先给**宽高背景颜色**，实例化盒子。
* 水平居中推荐使用方法：`justify-content:center`、`absoute+translateX(-50%,0)`。
* 垂直居中最推荐方法：`align-items:center`、`absolute+translateY(0,-50%)`。
* 两栏布局、三栏布局、等高布局、粘带布局优先考虑使用flex。

### 2. 圣杯布局 vs 双飞翼布局

#### 相同点

* 两种布局方式都是把主列放在文档流最前面，使主列优先加载。
* 两种布局方式在实现上也有相同之处，都是让三列浮动，然后通过负外边距形成三列布局。

#### 不同点

* 两种布局方式的不同之处在于如何处理中间主列的位置；
* **圣杯布局是利用父容器的左、右内边距+两个从列相对定位**；
* **双飞翼布局是把主列嵌套在一个新的父级块中利用主列的左、右外边距进行布局调整；**

## 十、参考

* [各种常见布局实现+知名网站实例分析](https://juejin.cn/post/6844903574929932301)
* [CSS布局解决方案](https://juejin.cn/post/6960844183611375630)
* [几种常见的CSS布局](https://juejin.cn/post/6844903710070407182)
* [CSS 常见布局方式](https://juejin.cn/post/6844903491891118087)
