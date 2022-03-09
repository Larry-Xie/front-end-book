# 浏览器兼容

## 一、前言 <a href="#bbgeig" id="bbgeig"></a>

\
现在的市场上有很多种类的浏览器，不同种类的浏览器的内核也不尽相同，所以不同浏览器对代码的解析会存在差异，这就导致对页面渲染效果不统一的问题。

常见的浏览器兼容性可分为三类：&#x20;

* HTML 兼容&#x20;
* CSS 兼容&#x20;
* JavaScript 兼容

针对浏览器的兼容性问题，也引申出两个概念：

* **渐进增强**：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。
* **优雅式降级**：一开始就构建完整的功能，然后再针对低版本浏览器进行兼容。

> 查看兼容性网站：[https://caniuse.com/](https://caniuse.com)

## 二、HTML 兼容

HTML 兼容问题涉及到的主要是不同的浏览器能识别和支持的标签不同，有些高级的标签，主要是 HTML 5的一些新标签没办法在低级的浏览器中使用。

* 可以通过 Can I Use 去查看下对应的浏览器版本对某个标签是否支持来决定要不要使用该标签；
* 或者可以通过在 head 中引入 **html5shiv** 插件包来解决 IE 9 以下版本不识别 HTML 5 标签的问题；

```html
<!--[if lt IE 9]>
  <script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"></script>
<![endif]-->
```

> 引用 shiv 代码的链接必须位于\<head>元素中，因为 IE 需要通过该文件来识别所有新元素。

> IE 条件注释示例：
>
> \<!–\[if IE 6]> 仅IE6可识别 \<!\[endif]>
>
> \<!–\[if lte IE 6]> IE6及其以下版本可识别 \<!\[endif]>
>
> \<!–\[if lt IE 6]> IE6以下版本可识别 \<!\[endif]>
>
> \<!–\[if gte IE 6]> IE6及其以上版本可识别 \<!\[endif]>
>
> \<!–\[if gt IE 6]> IE6以上版本可识别 \<!\[endif]>
>
> \<!–\[if IE]> 所有的IE可识别 \<!\[endif]>
>
> \<!–\[if !IE]>\<!–> 除IE外都可识别 \<!–\<!\[endif]>

## 三、CSS 兼容

### **1. CSS Hack**

使用 hacker 可以把浏览器**分为3类**：IE6；IE7；其他（IE8 Chrome ff Safari opera等）

* **IE6 认识的 hacker 是下划线 \_ 和星号 \***&#x20;
* **IE7 认识的 hacker 是 星号 \*+**

> 例如对某元素设置有如下样式： height:300px; \*height:200px; \*+height:100px;&#x20;
>
> * IE6 浏览器在读到 height:300px 的时候会认为高是 300px，继续往下读，他也认识 \*heihgt ，所以当 IE6 读到 \*height:200px 的时候会覆盖掉前一条的相冲突设置，认为高度是 200px 。&#x20;
> * IE7 也是一样的从高度 300px 的设置往下读。当它们读到 \*+height:200px 的时候就停下了，因为它们不认识 \*height 。 所以它会把高度解析为 100px 。
> * 剩下的浏览器只认识第一个 height:300px ; 所以他们会把高度解析为 300px 。
> * 因为优先级相同且相冲突的属性设置后一个会覆盖掉前一个，所以书写的次序是很重要的。

写法示例：

```css
/* 注意顺序 */
.box { width: 120px; } /* others */
*html .box { width: 80px;} /* ie6 fixed */
*+html .box { width: 60px;} /* ie7 fixed */
```

### **2. 加上浏览器前缀**

针对不同浏览器加上不同前缀来区别。

| 前缀       | 渲染引擎    | 相应浏览器                      |
| -------- | ------- | -------------------------- |
| -moz-    | Gecco   | Firefox                    |
| -webkit- | Webkit  | Safari，早期Chrome，Andorid浏览器 |
| -o-      | Presto  | 早期Opera                    |
| -ms-     | Trident | IE，Edge                    |

正常的写法如下，把不带前缀的放最后，但通常不用手动写带前缀的，而是编译时通过插件来解决。

```css
-o-transform:rotate(7deg); // Opera

-ms-transform:rotate(7deg); // IE

-moz-transform:rotate(7deg); // Firefox

-webkit-transform:rotate(7deg); // Chrome

transform:rotate(7deg); // 统一标识语句
```

### **3. 不同浏览器的标签默认的 margin 和 padding 不同**

因为历史原因，不同的浏览器样式存在差异，可以通过 **Normalize.css** 抹平差异，也可以定制自己的 reset.css，例如通过通配符选择器，**全局重置样式。**。c

```css
* {
    margin: 0;
    padding: 0;
}
```

> 全局重置样式虽然能够一定程度解决浏览器默认样式的差异性，但是也会带来一定的性能损失，所以也应谨慎使用。

### 4. a 标签的几种状态的顺序 <a href="#user-content-a-css" id="user-content-a-css"></a>

很多人在写 `a` 标签的样式，会疑惑为什么写的样式没有效果，或者点击超链接后，`hover`、`active` 样式没有效果，其实只是写的样式被覆盖了。

**正确的 a 标签的状态样式顺序应该是：**

* link: 平常的状态
* visited: 被访问过之后
* hover: 鼠标放到链接上的时候
* active: 链接被按下的时候

### **5. IE 中宽度和高度的问题**

IE不认得 `min-` 这个定义，但实际上它把正常的 `width` 和 `height` 当作有 `min` 的情况来使。这样问题就大了，如果只用宽度和高度，正常的浏览器里这两个值就不会变，如果只用 `min-width` 和 `min-height` 的话，IE下面根本等于没有设置宽度和高度。比如要设置背景图片，这个宽度是比较重要的。

解决方案：要解决这个问题，可以这样：

```css
#box { 
  width: 80px; 
  height: 35px;
}
html>body #box {
  width: auto; 
  height: auto; 
  min-width: 80px; 
  min-height: 35px;
}
```

### **6. 图片默认有间距**

当有几个 `img` 标签放在一起的时候，有些浏览器会有默认的间距，加了通配符也不起作用。

这个时候只能使用 `float` 属性为 `img` 布局。

### 7. IE 9 以下不能使用 Media Query

通过引入 **respond.js** 垫片来提供支持。

```
<!--[if lt IE 9]>
    <script src="https://cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
<![endif]-->
```

### 8. ul 和 ol 列表缩进问题

为了消除 ul、ol 等列表的缩进时，样式应写成：

```css
list-style: none;
margin: 0;
padding: 0;
```

在 IE 中仅仅设置 margin: 0; 即可达到最终效果；而在Firefox中必须同时设置这三项才能达到最终效果。





\


## 四JavaScript 兼容

\


\


* 事件兼容的问题，我们通常需要会封装一个适配器的方法，过滤事件句柄绑定、移除、冒泡阻止以及默认事件行为处理

\


```
 var  helper = {}

 //绑定事件
 helper.on = function(target, type, handler) {
 	if(target.addEventListener) {
 		target.addEventListener(type, handler, false);
 	} else {
 		target.attachEvent("on" + type,
 			function(event) {
 				return handler.call(target, event);
 		    }, false);
 	}
 };

 //取消事件监听
 helper.remove = function(target, type, handler) {
 	if(target.removeEventListener) {
 		target.removeEventListener(type, handler);
 	} else {
 		target.detachEvent("on" + type,
 	    function(event) {
 			return handler.call(target, event);
 		}, true);
     }
 };
```

\


\


* new Date()构造函数使用，'2018-07-05'是无法被各个浏览器中，使用new Date(str)来正确生成日期对象的。 正确的用法是'2018/07/05'.

\


* 获取 scrollTop 通过 document.documentElement.scrollTop 兼容非chrome浏览器

\


```
var scrollTop = document.documentElement.scrollTop||document.body.scrollTop;
```

\


## 4. 浏览器 hack

\


\
\


## 5. 参考

* [https://juejin.cn/post/6844903633708908557](https://juejin.cn/post/6844903633708908557)
* [https://juejin.cn/post/6971312765356998687](https://juejin.cn/post/6971312765356998687#heading-8)
* [https://juejin.cn/post/6844903493161975822](https://juejin.cn/post/6844903493161975822#heading-0)
* [https://juejin.cn/post/6972937716660961317](https://juejin.cn/post/6972937716660961317)
