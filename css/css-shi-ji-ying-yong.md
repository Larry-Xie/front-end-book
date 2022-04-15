# 实际应用

## 一、引言

跟算法类似，CSS 有一些应用实例需要记住，然后再举一反三。

## 二、长文本处理

**默认：字符太长溢出了容器**

![default](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/40ade871ebd34a53a87b5fdac31190b8\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**字符超出部分换行**

![overflow-wrap: break-word](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c5d1b30df0041258c0ae35a496d5e78\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**字符超出位置使用连字符**

![hyphens: auto](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f85b1c16a5c4dbfa549650794659553\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**单行文本超出省略**

![ellipsis](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb0075e0ceed4533b35b44db7e6a2b08\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**多行文本超出省略**

![line clamp](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/88afb6a8f2824d4487267f6ff7f3e49c\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

查看以上这些方案的示例： [codepen demo](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2Fbulandent%2Fpen%2FabBrNby)

此外需要[区分下 white-space, word-break 和 word-wrap](https://juejin.cn/post/6844903667863126030)：

* white-space，**控制空白字符的显示**，同时还能控制是否自动换行。它有五个值：normal | nowrap | pre | pre-wrap | pre-line
* word-break，**控制单词如何被拆分换行**。它有三个值：normal | break-all | keep-all
* word-wrap（overflow-wrap）**控制长度超过一行的单词是否被拆分换行**，是 word-break 的补充，它有两个值：normal | break-word

## 三、隐藏元素

* **display: none**：渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。
* **visibility: hidden**：元素在页面中仍占据空间，但是不会响应绑定的监听事件。
* **opacity: 0**：将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。
* **position: absolute**：通过使用绝对定位将元素移除可视区域内，以此来实现元素的隐藏。
* **z-index: 负值**：来使其他元素遮盖住该元素，以此来实现隐藏。
* **clip/clip-path** ：使用元素裁剪的方法来实现元素的隐藏，这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。
* **transform: scale(0,0)**：将元素缩放为 0，来实现元素的隐藏。这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件

## 四、绘制三角形

CSS 绘制三角形主要用到的是 border 属性，也就是边框。

平时在给盒子设置边框时，往往都设置很窄，就可能误以为边框是由矩形组成的。实际上，border属性是右三角形组成的，下面看一个例子：

```css
div {
    width: 0;
    height: 0;
    border: 100px solid;
    border-color: orange blue red green;
}
```

将元素的长宽都设置为0，显示出来的效果是这样的：

&#x20;&#x20;

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cba8731fea9842a8b8103c2b387fe64f\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

所以可以根据border这个特性来绘制三角形：&#x20;

#### **三角1**

```css
div {
    width: 0;
    height: 0;
    border-top: 50px solid red;
    border-right: 50px solid transparent;
    border-left: 50px solid transparent;
}
```

&#x20;

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab996951a0cc42cf9e6d9e12eb827f8b\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### **三角2**

```css
div {
    width: 0;
    height: 0;
    border-bottom: 50px solid red;
    border-right: 50px solid transparent;
    border-left: 50px solid transparent;
}
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/531c6c250dd8446fb0f264e7b3df6fba\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### 三角3

```css
div {
    width: 0;
    height: 0;
    border-left: 50px solid red;
    border-top: 50px solid transparent;
    border-bottom: 50px solid transparent;
}
```

&#x20;

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4beaf4e4a0140ad9e7252f8a6e4e8e6\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### **三角4**

```css
div {
    width: 0;
    height: 0;
    border-right: 50px solid red;
    border-top: 50px solid transparent;
    border-bottom: 50px solid transparent;
}
```

&#x20;

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/445f50ad19164b0f863ad8dfef2a29b1\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### **三角5**

```css
div {
    width: 0;
    height: 0;
    border-top: 100px solid red;
    border-right: 100px solid transparent;
}
```

&#x20;

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1ac630463164e42a027b54bb95f56ba\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

还有很多，就不一一实现了，**总体的原则就是通过上下左右边框来控制三角形的方向，用边框的宽度比来控制三角形的角度**。

## 五、绘制扇形

用 CSS 实现扇形的思路和三角形基本一致，就是多了一个圆角的样式，实现一个90°的扇形：

```css
div{
    border: 100px solid transparent;
    width: 0;
    heigt: 0;
    border-radius: 100px;
    border-top-color: red;
}
```

![扇形](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/db5e46aea0ce4805a0c2bbec2743546e\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 六、小于 1px 的边

1px 问题指的是：在一些 `Retina屏幕` 的机型上，移动端页面的 1px 会变得很粗，呈现出不止 1px 的效果。原因很简单——CSS 中的 1px 并不能和移动设备上的 1px 划等号。它们之间的比例关系有一个专门的属性来描述：

```javascript
window.devicePixelRatio = 设备的物理像素 / CSS像素。
```

打开 Chrome 浏览器，启动移动端调试模式，在控制台去输出这个 `devicePixelRatio` 的值。这里选中 iPhone6/7/8 这系列的机型，输出的结果就是2，这就意味着设置的 1px CSS 像素，在这个设备上实际会用 2 个物理像素单元来进行渲染，所以实际看到的一定会比 1px 粗一些。 **解决1px 问题的三种思路：**

#### **直接写 0.5px**

如果之前 1px 的样式这样写：

```css
border:1px solid #333
```

可以先在 JS 中拿到 window.devicePixelRatio 的值，然后把这个值通过 JSX 或者模板语法给到 CSS 的 data 里，达到这样的效果（这里用 JSX 语法做示范）：

```javascript
<div id="container" data-device={{window.devicePixelRatio}}></div>
```

然后就可以在 CSS 中用属性选择器来命中 devicePixelRatio 为某一值的情况，比如说这里尝试命中 devicePixelRatio 为2的情况：

```css
#container[data-device="2"] {
  border:0.5px solid #333
}
```

直接把 1px 改成 1/devicePixelRatio 后的值，这是目前为止最简单的一种方法。这种方法的缺陷在于兼容性不行，IOS 系统需要8及以上的版本，安卓系统则直接不兼容。

#### **伪元素先放大后缩小**

这个方法的可行性会更高，兼容性也更好。唯一的缺点是代码会变多。

思路是**先放大、后缩小：在目标元素的后面追加一个 ::after 伪元素，让这个元素布局为 absolute 之后、整个伸展开铺在目标元素上，然后把它的宽和高都设置为目标元素的两倍，border 值设为 1px。接着借助 CSS 动画特效中的放缩能力，把整个伪元素缩小为原来的 50%。此时，伪元素的宽高刚好可以和原有的目标元素对齐，而 border 也缩小为了 1px 的二分之一，间接地实现了 0.5px 的效果。**

代码如下：

```css
#container[data-device="2"] {
    position: relative;
}
#container[data-device="2"]::after{
      position:absolute;
      top: 0;
      left: 0;
      width: 200%;
      height: 200%;
      content:"";
      transform: scale(0.5);
      transform-origin: left top;
      box-sizing: border-box;
      border: 1px solid #333;
    }
}
```

#### **viewport 缩放**

这个思路就是对 meta 标签里几个关键属性下手：

```html
<meta name="viewport" content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">
```

这里针对像素比为2的页面，把整个页面缩放为了原来的1/2大小。这样，本来占用2个物理像素的 1px 样式，现在占用的就是标准的一个物理像素。根据像素比的不同，这个缩放比例可以被计算为不同的值，用 js 代码实现如下：

```javascript
const scale = 1 / window.devicePixelRatio;
// 这里 metaEl 指的是 meta 标签对应的 Dom
metaEl.setAttribute('content', `width=device-width,user-scalable=no,initial-scale=${scale},maximum-scale=${scale},minimum-scale=${scale}`);
```

这样解决了，但这样做的副作用也很大，整个页面被缩放了。这时 1px 已经被处理成物理像素大小，这样的大小在手机上显示边框很合适。但是，一些原本不需要被缩小的内容，比如文字、图片等，也被无差别缩小掉了。

## 七、暗黑模式

可以通过全局变量，根据用户选择，在 JS 里面动态地去设置，应用特定的全局样式。

## 八、骨架屏

基本就是通过动画的方式当在数据请求时，一直显示该动画，当数据返回时，则去掉该动画对应的样式。
