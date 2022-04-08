# CSS 层叠

## 一、引言

在这个世界上，凡事都有个先后顺序，凡物都有个论资排辈。在 CSS 届，也是如此。只是，一般情况下，大家歌舞升平，看不出什么差异，即所谓的众生平等。但是，当发生冲突发生纠葛的时候，显然，是不可能做到完全等同的，先后顺序，身份差异就显现出来了。对于 CSS 世界中的元素而言，所谓的“冲突”指什么呢，其中，很重要的一个层面就是“层叠显示冲突”。

默认情况下，网页内容是没有偏移角的垂直视觉呈现，当内容发生层叠的时候，一定会有一个前后的层叠顺序产生，有点类似于真实世界中论资排辈的感觉。

而要理解网页中元素是如何“论资排辈”的，就需要深入理解CSS中的层叠上下文和层叠顺序。我们可能都熟悉 CSS 中的 `z-index`属性，但是 `z-index`实际上只是 CSS 层叠上下文和层叠顺序中的一叶小舟。

## 二、层叠上下文

屏幕是一个二维平面，然而 HTML 元素却是排列在三维坐标系中，x 为水平位置，y 为垂直位置，z 为屏幕由内向外方向的位置，我们在看屏幕的时候是沿着 z 轴方向从外向内的；由此，元素在用户视角就形成了层叠的关系，某个元素可能覆盖了其他元素也可能被其他元素覆盖。而这其实就是层叠上下文所要描述的东西。

![ceng层叠](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/21/165fc5323852bd61\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

**层叠上下文** (堆叠上下文, Stacking Context)，是HTML中一个三维的概念。在CSS2.1规范中，每个元素的位置是三维的，当元素发生层叠，这时它可能覆盖了其他元素或者被其他元素覆盖；排在z轴越靠上的位置，距离屏幕观察者越近



![-w566](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9fe12ffcbbe547dbbabc0c74488c30c9\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

我们对层叠上下文的第一印象可能要来源于 z-index，认为它的值越大，距离屏幕观察者就越近，那么层叠等级就越高，事实确实是这样的，但层叠上下文的内容远非仅仅如此：

* z-index 能够在层叠上下文中对元素的堆叠顺序其作用是必须配合定位才可以；
* 除了 z-index 之外，一个元素在 Z 轴上的显示顺序还受层叠等级和层叠顺序影响；

在看层叠等级和层叠顺序之前，我们先来看下如何产生一个层叠上下文，特定的 HTML 元素或者 CSS 属性产生层叠上下文，MDN 中给出了这么一个列表，符合以下任一条件的元素都会产生层叠上下文：

* html 文档根元素
* 声明 position: absolute/relative 且 z-index 值不为 auto 的元素；
* 声明 position: fixed/sticky 的元素；
* flex 容器的子元素，且 z-index 值不为 auto；
* grid 容器的子元素，且 z-index 值不为 auto；
* opacity 属性值小于 1 的元素；
* mix-blend-mode 属性值不为 normal 的元素；
* 以下任意属性值不为 none 的元素：
  * transform
  * filter
  * perspective
  * clip-path
  * mask / mask-image / mask-border
* isolation 属性值为 isolate 的元素；
* \-webkit-overflow-scrolling 属性值为 touch 的元素；
* will-change 值设定了任一属性而该属性在 non-initial 值时会创建层叠上下文的元素；
* contain 属性值为 layout、paint 或包含它们其中之一的合成值（比如 contain: strict、contain: content）的元素。

## **层叠等级**

层叠等级指节点在三维空间 Z 轴上的上下顺序。它分两种情况：

* 在同一个层叠上下文中，它描述定义的是该层叠上下文中的层叠上下文元素在 Z 轴上的上下顺序；
* 在其他普通元素中，它描述定义的是这些普通元素在 Z 轴上的上下顺序；

普通节点的层叠等级优先由其所在的层叠上下文决定，层叠等级的比较只有在当前层叠上下文中才有意义，脱离当前层叠上下文的比较就变得无意义了。

## **层叠顺序**

在同一个层叠上下文中如果有多个元素，那么他们之间的层叠顺序是怎么样的呢？

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/21043848687d42c6b46d6cf9c59c17ff\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

以下这个列表越往下层叠优先级越高，视觉上的效果就是越容易被用户看到（不会被其他元素覆盖）：

* 层叠上下文的 border 和 background
* z-index < 0 的子节点
* 标准流内块级非定位的子节点
* 浮动非定位的子节点
* 标准流内行内非定位的子节点
* z-index: auto/0 的子节点
* z-index > 0的子节点

**如何比较两个元素的层叠等级？**

* 在同一个层叠上下文中，比较两个元素就是按照上图的介绍的层叠顺序进行比较。
* 如果不在同一个层叠上下文中的时候，那就需要比较两个元素分别所处的层叠上下文的等级。
* 如果两个元素都在同一个层叠上下文，且层叠顺序相同，则在 HTML 中定义越后面的层叠等级越高。

## 参考

* [深入理解CSS中的层叠上下文和层叠顺序](https://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)
* [彻底搞懂CSS层叠](https://juejin.cn/post/6844903667175260174)
* [CSS 中重要的层叠概念](https://juejin.cn/post/6844904145766334472)