# 格式化上下文

## 一、引言 <a href="#yswwx" id="yswwx"></a>

Box 是 CSS 布局的对象和基本单位， 直观点来说，就是一个页面是由很多个 Box 组成的。元素的类型和 display 属性，决定了这个 Box 的类型。 不同类型的 Box，会参与不同的 Formatting Context（一个决定如何渲染文档的容器），因此Box内的元素会以不同的方式渲染。

格式化上下文（Formatting context） 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。

不同类型的盒子有不同格式化上下文，大概有这 4 类：

* BFC (Block Formatting Context) 块级格式化上下文；
* IFC (Inline Formatting Context) 行内格式化上下文；
* FFC (Flex Formatting Context) 弹性格式化上下文；
* GFC (Grid Formatting Context) 格栅格式化上下文；

## 二、**BFC**

### 1. 概念

块格式化上下文，它是一个独立的渲染区域，只有块级盒子参与，它规定了内部的块级盒子如何布局，并且与这个区域外部毫不相干。

![BFC](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4a73e2276d8b41f0a905361f151157e2\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### **2. BFC 渲染规则**

* 内部的盒子会在垂直方向，一个接一个地放置；
* 盒子垂直方向的距离由 margin 决定，属于同一个 BFC 的两个相邻盒子的 margin 会发生重叠；
* 每个元素的 margin 的左边，与包含块 border 的左边相接触(对于从左往右的格式化，否则相反)，即使存在浮动也是如此；
* BFC 的区域不会与 float 盒子重叠；
* BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
* 计算 BFC 的高度时，浮动元素也参与计算。

### **3. 创建 BFC**

创建 BFC 只需要设置特定的样式即能触发  BFC:

* html根元素;
* float 的值不为 none;
* overflow 的值不为visible；
* display 的值为 table-cell、table-caption , inline-block ,flex, inline-flex,grid,inline-grid中的任何一个；
* position 的值不为 relative 和 static。

\
作者：\_烦啦\
链接：https://juejin.cn/post/6941355126191816711\
来源：稀土掘金\
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### **4. BFC 应用场景**

#### 1、自适应两栏布局

应用原理：BFC 的区域不会和浮动区域重叠，所以就可以把侧边栏固定宽度且左浮动，而对右侧内容触发 BFC，使得它的宽度自适应该行剩余宽度。

![两栏布局](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc555b773b304ec2af61a8fcbb9bafcb\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```html
<div class="layout">
    <div class="aside">aside</div>
    <div class="main">main</div>
</div>
```

```css
.aside {
    float: left;
    width: 100px;
}
.main {
    <!-- 触发 BFC -->
    overflow: auto;
}
```

#### 2、清除内部浮动

浮动造成的问题就是父元素高度坍塌，所以清除浮动需要解决的问题就是让父元素的高度恢复正常。而用 BFC 清除浮动的原理就是：计算 BFC 的高度时，浮动元素也参与计算。只要触发父元素的 BFC 即可。

![清除内部浮动](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b1b790fa15ca4e3599aa53a8d1c2e973\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```css
.parent {
    overflow: hidden;
}
```

#### 3、 防止垂直 margin 合并

BFC 渲染原理之一：同一个 BFC 下的垂直 margin 会发生合并。所以如果让 2 个元素不在同一个 BFC 中即可阻止垂直 margin 合并。那如何让 2 个相邻的兄弟元素不在同一个 BFC 中呢？可以给其中一个元素外面包裹一层，然后触发其包裹层的 BFC，这样一来 2 个元素就不会在同一个 BFC 中了。

![防止高度塌陷](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e533424631140d5b8bbb48266303709\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```html
<div class="layout">
    <div class="a">a</div>
    <div class="contain-b">
        <div class="b">b</div>
    </div>
</div>
```

```css
.demo3 .a,
.demo3 .b {
    border: 1px solid #999;
    margin: 10px;
}
.contain-b {
    overflow: hidden;
}
复制代码
```

针对以上 3 个 示例 ，可以结合这个 [BFC 应用示例](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2Fbulandent%2Fpen%2FeYBVpEm) 配合观看更佳。

## 三、**IFC**

IFC 的形成条件非常简单，块级元素中仅包含内联级别元素，需要注意的是当IFC中有块级元素插入时，会产生两个匿名块将父元素分割开来，产生两个 IFC。

![IFC](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5cee1281ae5f44a69abc94fb9fa760fd\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**IFC 渲染规则**

* 子元素在水平方向上一个接一个排列，在垂直方向上将以容器顶部开始向下排列；
* 节点无法声明宽高，其中 margin 和 padding 在水平方向有效在垂直方向无效；
* 节点在垂直方向上以不同形式对齐；
* 能把在一行上的框都完全包含进去的一个矩形区域，被称为该行的线盒（line box）。线盒的宽度是由包含块（containing box）和与其中的浮动来决定；
* IFC 中的 line box 一般左右边贴紧其包含块，但 float 元素会优先排列。
* IFC 中的 line box 高度由 line-height 计算规则来确定，同个 IFC 下的多个 line box 高度可能会不同；
* 当内联级盒子的总宽度少于包含它们的 line box 时，其水平渲染规则由 text-align 属性值来决定；
* 当一个内联盒子超过父元素的宽度时，它会被分割成多盒子，这些盒子分布在多个 line box 中。如果子元素未设置强制换行的情况下，inline box 将不可被分割，将会溢出父元素。

针对如上的 IFC 渲染规则，你是不是可以分析下下面这段代码的 IFC 环境是怎么样的呢？

```html
<p>It can get <strong>very complicated</storng> once you start looking into it.</p>
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d357e140d61c4635a13771067758862b\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

对应上面这样一串 HTML 分析如下：

* p 标签是一个 block container，对内将产生一个 IFC；
* 由于一行没办法显示完全，所以产生了 2 个线盒（line box）；线盒的宽度就继承了 p 的宽度；高度是由里面的内联盒子的 line-height 决定；
* It can get：匿名的内联盒子；
* very complicated：strong 标签产生的内联盒子；
* once you start：匿名的内联盒子；
* looking into it.：匿名的内联盒子。

参考：[Inline formatting contexts](https://link.juejin.cn/?target=https%3A%2F%2Fwww.w3.org%2FTR%2FCSS2%2Fvisuren.html%23inline-formatting)

**IFC 应用场景**

* 水平居中：当一个块要在环境中水平居中时，设置其为 inline-block 则会在外层产生 IFC，通过 text-align 则可以使其水平居中。
* 垂直居中：创建一个 IFC，用其中一个元素撑开父元素的高度，然后设置其 vertical-align: middle，其他行内元素则可以在此父元素下垂直居中。

偷个懒，demo 和图我就不做了。

\
作者：大海我来了\
链接：https://juejin.cn/post/6941206439624966152\
来源：稀土掘金\
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
