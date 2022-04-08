# CSS 层叠

## 一、引言

在这个世界上，凡事都有个先后顺序，凡物都有个论资排辈。在 CSS 届，也是如此。只是，一般情况下，大家歌舞升平，看不出什么差异，即所谓的众生平等。但是，当发生冲突发生纠葛的时候，显然，是不可能做到完全等同的，先后顺序，身份差异就显现出来了。对于 CSS 世界中的元素而言，所谓的“冲突”指什么呢，其中，很重要的一个层面就是“层叠显示冲突”。

默认情况下，网页内容是没有偏移角的垂直视觉呈现，当内容发生层叠的时候，一定会有一个前后的层叠顺序产生，有点类似于真实世界中论资排辈的感觉。

而要理解网页中元素是如何“论资排辈”的，就需要深入理解CSS中的层叠上下文和层叠顺序。我们可能都熟悉 CSS 中的 `z-index`属性，但是 `z-index`实际上只是 CSS 层叠上下文和层叠顺序中的一叶小舟。

## 二、层叠上下文

### 1. 概念

**层叠上下文** (堆叠上下文, Stacking Context)，是 HTML 中一个三维的概念。屏幕是一个二维平面，然而在 CSS 规范中每个 HTML 元素的位置是三维的，排列在三维坐标系中，x 为水平位置，y 为垂直位置，z 为屏幕由内向外方向的位置。

我们在看屏幕的时候是沿着 z 轴方向从外向内的；由此，元素在用户视角就形成了层叠的关系，某个元素可能覆盖了其他元素也可能被其他元素覆盖。当元素发生层叠，排在 z 轴越靠上的元素，距离屏幕观察者越近，也即该元素能够遮挡比它 z 轴方向低的元素。

![层叠上下文](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/21/165fc5323852bd61\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. 示例

#### 示例1

**有两个 div，p.a、p.b 被包裹在一个 div 里，p.c 被包裹在另一个盒子里，只为 .a、.b、.c 设置`position`和`z-index`属性:**

```html
<style>
  div {  
    position: relative;  
    width: 100px;  
    height: 100px;  
  }  c
  p {  
    position: absolute;  
    font-size: 20px;  
    width: 100px;  
    height: 100px;  
  }  
  .a {  
    background-color: blue;  
    z-index: 1;  
  }  
  .b {  
    background-color: green;  
    z-index: 2;  
    top: 20px;  
    left: 20px;  
  }  
  .c {  
    background-color: red;  
    z-index: 3;  
    top: -20px;  
    left: 40px;  
  }
</style>

<body>  
  <div>  
    <p class="a">a</p>  
    <p class="b">b</p>  
  </div> 

  <div>  
    <p class="c">c</p>  
  </div>  
</body> 
```

效果：

![层叠上下文示例1](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/8/30/165890e9dcadf485\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

因为 p.a、p.b、p.c 三个的父元素 div 都没有设置`z-index`，所以不会产生层叠上下文，所以 .a、.b、.c都处于由`<html></html>`标签产生的“根层叠上下文”中，属于同一个层叠上下文，此时谁的`z-index`值大，谁在上面。

#### 示例2

**有两个 div，p.a、p.b 被包裹在一个div里，p.c 被包裹在另一个盒子里，同时为两个 div和 .a、.b、.c 设置`position`和`z-index`属性:**

```html
<style>
  div {
    width: 100px;
    height: 100px;
    position: relative;
  }
  .box1 {
    z-index: 2;
  }
  .box2 {
    z-index: 1;
  }
  p {
    position: absolute;
    font-size: 20px;
    width: 100px;
    height: 100px;
  }
  .a {
    background-color: blue;
    z-index: 100;
  }
  .b {
    background-color: green;
    top: 20px;
    left: 20px;
    z-index: 200;
  }
  .c {
    background-color: red;
    top: -20px;
    left: 40px;
    z-index: 9999;
  }
</style>

<body>
  <div class="box1">
    <p class="a">a</p>
    <p class="b">b</p>
  </div>

  <div class="box2">
    <p class="c">c</p>
  </div>
</body>
```

效果：

![层叠上下文示例2](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/8/30/165890ecb78125b2\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

我们发现，虽然`p.c`元素的`z-index`值为9999，远大于`p.a`和`p.b`的`z-index`值，但是由于`p.a`、`p.b`的父元素`div.box1`产生的层叠上下文的`z-index`的值为2，`p.c`的父元素`div.box2`所产生的层叠上下文的`z-index`值为1，所以`p.c`永远在`p.a`和`p.b`下面。

同时，如果我们只更改`p.a`和`p.b`的`z-index`值，由于这两个元素都在父元素`div.box1`产生的层叠上下文中，所以，谁的`z-index`值大，谁在上面。

### 3. 创建层叠上下文

层叠上下文本身只是个逻辑的概念，和格式化上下文类似，它的产生也是有一些特定的 CSS 属性来触发的，一般有三类方法：

1. `HTML`中的根元素`<html></html>`本身就具有层叠上下文，称为“根层叠上下文”。
2. `position`值为`absolute | relative`，且`z-index`值不为 `auto 的普通元素`，产生层叠上下文。
3. CSS3 中的新属性也可以产生层叠上下文。

CSS3 中的能够产生层叠上下文的新属性如下：

* `position` 值为 `fixed | sticky`
* `z-index` 值不为 `auto` 的flex元素，即：父元素`display: flex | inline-flex`
* `opacity` 属性值小于 `1` 的元素
* `transform` 属性值不为 `none`的元素
* `mix-blend-mode` 属性值不为 `normal` 的元素
* `filter`、`perspective`、`clip-path`、`mask`、`mask-image`、`mask-border`、`motion-path` 值不为 `none` 的元素
* `isolation` 属性被设置为 `isolate` 的元素
* `will-change` 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值
* `-webkit-overflow-scrolling` 属性被设置 `touch`的元素

### 4. 层叠上下文特性

层叠上下文元素有如下特性：

* 层叠上下文的层叠水平要比没有层叠上下文的普通元素高；
* 层叠上下文可以阻断元素的混合模式；
* 层叠上下文可以嵌套，内部层叠上下文及其所有子元素均受制于外部的层叠上下文；
* 每个层叠上下文和兄弟元素独立，也就是当进行层叠变化或渲染的时候，只需要考虑后代元素；
* 每个层叠上下文是自成体系的，当元素发生层叠的时候，整个元素被认为是在父层叠上下文的层叠顺序中；

## **三、层叠等级**

### **1. 概念**

**层叠等级** (层叠水平, Stacking Level) 决定了同一个层叠上下文中元素在 z 轴上的显示顺序的概念；

* 普通元素的层叠等级优先由其所在的层叠上下文决定；
* **层叠等级的比较只有在同一个层叠上下文**元素中才有意义；
* 在同一个层叠上下文中，**层叠等级描述定义的是该层叠上下文中的元素在 Z 轴上的上下顺序**；

> 层叠等级并不一定由 z-index 决定。

### 2. z-index

z-index 只适用于定位的元素以及 flex 盒子的孩子元素，对其它元素无效，它可以被设置为正整数、负整数、0、auto，默认值为 auto。

元素的 z-index 值只在同一个层叠上下文中有意义。如果父级层叠上下文的层叠等级低于另一个层叠上下文的，那么它 z-index 设的再高也没用。所以如果你遇到 z-index 值设了很大，但是不起作用的话，就去看看它的父级层叠上下文是否被其他层叠上下文盖住了。

## **四、层叠顺序**

### **1. 概念**

**层叠顺序** (层叠次序, 堆叠顺序, Stacking Order) 描述的是元素在同一个层叠上下文中的顺序**规则**，由此可见，前面所说的“层叠上下文”和“层叠等级”是一种概念，而这里的“层叠顺序”是一种规则。

从层叠的底部开始，共有七种层叠顺序：

1. **背景和边框**：形成层叠上下文的元素的背景和边框。
2. **负 z-index 值**：层叠上下文内有着负 z-index 值的定位子元素，负的越大层叠等级越低；
3. **块级盒**：文档流中块级、非定位子元素；
4. **浮动盒**：非定位浮动元素；
5. **行内盒**：文档流中行内、非定位子元素；
6. **z-index: 0**：z-index 为 0 或 auto 的定位元素， 这些元素形成了新的层叠上下文；
7. **正 z-index 值**：z-index 为正的定位元素，正的越大层叠等级越高；

同一个层叠顺序的元素按照在 HTML 里出现的顺序层叠；第 7 级顺序的元素会显示在之前顺序元素的上方，也就是看起来覆盖了更低级的元素：

![层叠顺序](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/8/30/1658910c5cb364b6\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

![层叠顺序](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/21/165fc538e321d802\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

![层叠顺序](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/21043848687d42c6b46d6cf9c59c17ff\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. 层叠准则

在实际中，如果要比较两个元素的层叠关系，可通过如下准则来进行比较：

* 如果在同一个层叠上下文中，比较两个元素就是按照上图的介绍的层叠顺序进行比较。
* 如果不在同一个层叠上下文中，那就需要比较两个元素分别所处的层叠上下文的等级。
* 如果两个元素都在同一个层叠上下文，且层叠顺序相同，则在 HTML 中定义越后面的层叠等级越高。

## 五、实例

### 1. 普通情况

三个`relative`定位的`div`块中各有`absolute`的不同颜色的`span.red`、`span.green`、`span.blue`，它们都设置了`position: absolute`；

[参见Codepen - 普通情况](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2FSHERlocked93%2Fpen%2FaaPord)

那么当没有元素包含 z-index 属性时，这个例子中的元素按照如下顺序层叠（从底到顶顺序）：

1. 根元素的背景和边界
2. 块级非定位元素按HTML中的出现顺序层叠
3. 行内非定位元素按HTML中的出现顺序层叠
4. 定位元素按HTML中的出现顺序层叠

红绿蓝都属于 z-index 为 auto 的定位元素，因此按照7层层叠顺序规则来说同属于层叠顺序第6级，所以按HTML中的出现顺序层叠：`红->绿->蓝`

![实例1](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/31/16c46d6cdddf1555\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. 在相同层叠上下文的父元素内的情况

红绿位于一个`div.first-box`下，蓝位于`div.second-box`下，红绿蓝都设置了`position: absolute`，`first-box`与`second-box`都设置了`position: relative`；

[参见Codepen - 父元素不同但都位于根元素下](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2FSHERlocked93%2Fpen%2FRYENBw)

这个例子中，红蓝绿元素的父元素`first-box`与`second-box`都没有生成新的层叠上下文，都属于根层叠上下文中的元素，且都是层叠顺序第6级，所以按HTML中的出现顺序层叠：`红->绿->蓝`

![实例2](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/31/16c46d625f5b0ad3\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 3. 给子元素增加 z-index

红绿位于一个`div.first-box`下，蓝黄位于`div.second-box`下，红绿蓝都设置了`position: absolute`，如果这时给绿加一个属性`z-index: 1`，那么此时`.green`位于最上面；

如果再在`.second-box`下`.green`后加一个绝对定位的 `span.gold`，设置`z-index: -1`，那么它将位于红绿蓝的下面；

[参见Codepen - 设置了z-index](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2FSHERlocked93%2Fpen%2FgdZOrK)

这个例子中，红蓝绿黄元素的父元素中都没有生成新的层叠上下文，都属于根层叠上下文中的元素

1. 红蓝都没有设置 z-index，同属于层叠顺序中的第6级，按 HTML 中的出现顺序层叠；
2. 绿设置了正的 z-index，属于第7级；
3. 黄设置了负的 z-index，属于第2级；

所以这个例子中的从底到高显示的顺序就是：`黄->红->蓝->绿`

![实例3](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/31/16c46d4fad8dba67\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 4. 在不同层叠上下文的父元素内的情况

红绿位于一个`div.first-box`下，蓝位于`div.second-box`下，红绿蓝都设置了`position: absolute`，如果`first-box`的 z-index 设置的比`second-box`的大，那么此时无论蓝的 z-index 设置的多大`z-index: 999`，蓝都位于红绿的下面；如果我们只更改红绿的 z-index 值，由于这两个元素都在父元素`first-box`产生的层叠上下文中，此时谁的z-index值大，谁在上面；

[参见Codepen - 不同层叠上下文的父元素](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2FSHERlocked93%2Fpen%2FgdZbOJ)

这个例子中，红绿蓝都属于设置了 z-index 的定位元素，不过他们的父元素创建了新的层叠上下文；

1. 红绿的父元素`first-box`是设置了正 z-index 的定位元素，因此创建了一个层叠上下文，属于层叠顺序中的第 7 级；
2. 蓝的父元素`second-box`也同样创建了一个层叠上下文，属于层叠顺序中的第 6 级；
3. 按照层叠顺序，`first-box`中所有元素都排在`second-box`上；
4. 红绿都属于层叠上下文`first-box`中且设置了不同的正 z-index，都属于层叠顺序中第 7 级；
5. 蓝属于层叠上下文`second-box`，且设置了一个很大的正 z-index，属于层叠元素中第 7 级；
6. 虽然蓝的 z-index 很大，但是因为`second-box`的层叠等级比`first-box`小，因此位于红绿之下；

所以这个例子中从低到到显示的顺序：`蓝->红->绿`

![实例4](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/31/16c46d5ad6860327\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 5. 给子元素设置 opacity

红绿位于`div.first-box`下，蓝位于`div.second-box`下，红绿蓝都设置了`position: absolute`，绿设置了`z-index: 1`，那么此时绿位于红蓝的最上面；

如果此时给`first-box`设置`opacity: .99`，这时无论红绿的 z-index 设置的多大`z-index: 999`，蓝都位于红绿的上面；

如果再在`.second-box`下`.green`后加一个`span.gold`，设置`z-index: -1`，那么它将位于红绿蓝的下面；

[参见Codepen - opacity的影响](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2FSHERlocked93%2Fpen%2FGXPRWB)

之前已经介绍了，设置`opacity`也可以形成层叠上下文，因此：

1. `first-box`设置了`opacity`，`first-box`成为了一个新的层叠上下文；
2. `second-box`没有形成新的层叠上下文，因此其中的元素都属于根层叠上下文；
3. 黄属于层叠顺序中第 2 级，红绿属于第 7 级，`first-box`属于第 6 级，蓝属于层叠顺序中第 6 级且按 HTML 出现顺序位于`first-box`之上；

所以这个例子中从低到到显示的顺序：`黄->红->绿->蓝`

![实例5](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/31/16c46d625f98c04f\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

## 六、参考

* [深入理解CSS中的层叠上下文和层叠顺序](https://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)
* [彻底搞懂CSS层叠](https://juejin.cn/post/6844903667175260174)
* [CSS 中重要的层叠概念](https://juejin.cn/post/6844904145766334472)
