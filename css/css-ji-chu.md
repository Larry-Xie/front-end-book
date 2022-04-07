# CSS 基础

## 一、引言 <a href="#xvpyg" id="xvpyg"></a>

CSS 虽然看上去是个简单的样式，但是本身包含的内容其实很多，也有很多原理和精妙的应用。

## 二、CSS 加载方式 <a href="#hmpfh" id="hmpfh"></a>

有四种 CSS 加载方式：

### 1. 内联样式 <a href="#hdjch" id="hdjch"></a>

内联样式即直接通过 style 属性在元素上设置样式。

* 定义的属性优先级高于所有选择器和其它加载方式；
* 无法复用；
* 覆盖样式只能使用 !important；

### 2. 嵌入样式 <a href="#ckocj" id="ckocj"></a>

嵌入样式即通过 \<style>\</style> 标签写入样式。

* 作用域：只对当前页面有效；
* 单页应用，减少请求，提高速度；
* 支持媒体查询

### 3. 外链样式 <a href="#avaxf" id="avaxf"></a>

外链样式即通过 \<link> 的方式来加载样式文件并应用。

* CSS 和 HTML 分离，便于复用和维护；
* 利于缓存；
* 支持媒体查询；

### 4. 导入样式 <a href="#utqeu" id="utqeu"></a>

导入样式即通过 @import 的方式来引入样式。

* 模块化；
* 利于缓存；
* 支持媒体查询；
* 需先下载并解析引用 CSS，才可以继续下载导入 CSS。现代通常使用模块化工具在编译时合并 CSS；

## 三、文档流 <a href="#vvsay" id="vvsay"></a>

### 1. 标准文档流 <a href="#d7vit" id="d7vit"></a>

在 CSS 的世界中，会把内容按照从左到右、从上到下的顺序进行排列显示。正常情况下会把页面分割成一行一行的显示，而每行又可能由多列组成，所以从视觉上看起来就是从上到下从左到右，而这就是 CSS 中的流式布局，又叫文档流。文档流就像水一样，能够自适应所在的容器，一般它有如下几个特性：

* 块级元素默认会占满整行，所以多个块级盒子之间是从上到下排列的；
* 内联元素默认会在一行里一列一列的排布，当一行放不下的时候，会自动切换到下一行继续按照列排布；

### 2. 脱离文档流 <a href="#bueey" id="bueey"></a>

脱流文档流指节点脱流正常文档流后，在正常文档流中的其他节点将忽略该节点并填补其原先空间。文档一旦脱流，计算其父节点高度时不会将其高度纳入，脱流节点不占据空间。有两种方式可以让元素脱离文档流：浮动和定位。

* 使用浮动（float）会将元素脱离文档流，移动到容器左/右侧边界或者是另一个浮动元素旁边，该浮动元素之前占用的空间将被别的元素填补，另外浮动之后所占用的区域不会和别的元素之间发生重叠；
* 使用绝对定位（position: absolute;）或者固定定位（position: fixed;）也会使得元素脱离文档流，且空出来的位置将自动被后续节点填补。

## 四、盒模型 <a href="#zqwgc" id="zqwgc"></a>

### 1. 盒模型组成 <a href="#yfgs3" id="yfgs3"></a>

在 CSS 中任何元素都可以看成是一个盒子，而一个盒子是由 4 部分组成的：内容（content）、内边距（padding）、边框（border）和外边距（margin）。

盒模型有 2 种：

* 标准盒模型：W3C 制定
* IE 盒模型：IExplore 制定

如果给某个元素设置如下样式：

```
.box {
  width: 200px;
  height: 200px;
  padding: 10px;
  border: 1px solid #eee;
  margin: 10px;
}
```

### 2. 标准盒模型 <a href="#mopxv" id="mopxv"></a>

标准盒模型：box-sizing: content-box;

标准盒模型认为给盒子设置的 width 和 height 只是内容本身，不包含 padding，border 和 margin。

![标准盒模型](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c4b9dddb310540f78a19ea0f7da92938\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

所以 .box 元素内容的宽度就为 200px，而实际的宽度则是 width + padding-left + padding-right + border-left-width + border-right-width = 200 + 10 + 10 + 1 + 1 = 222。

### 3. IE 盒模型 <a href="#tovb8" id="tovb8"></a>

标准盒模型：box-sizing: border-box;

IE 盒模型认为给盒子设置的 width 和 height 包含了 padding 和 border，不包含 margin。

![IE 盒模型](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/302fcd74518b44b4adfe50b02dc3aed3\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

.box 元素所占用的实际宽度为 200px，而内容的真实宽度则是 width - padding-left - padding-right - border-left-width - border-right-width = 200 - 10 - 10 - 1 - 1 = 178。

### 4. 盒模型设置 <a href="#eam3q" id="eam3q"></a>

现在高版本的浏览器**基本上默认都是使用标准盒模型**，而像 IE6 这种老古董才是默认使用 IE 盒模型的。

在 CSS3 中新增了一个属性 box-sizing，允许开发者来指定盒子使用什么标准，它有 2 个值：

* content-box：标准盒模型；
* border-box：IE 盒模型；

## 五、属性继承 <a href="#zri3e" id="zri3e"></a>

### 1. 概念 <a href="#lhjj7" id="lhjj7"></a>

在 CSS 中有一个很重要的特性就是子元素会继承父元素对应属性计算后的值。如果 CSS 中不存在继承性，那我们就需要为每个元素设置一堆属性，带来的后果就是 CSS 的文件大小就会无限增大。

### 2. 可继承属性 <a href="#rcbcj" id="rcbcj"></a>

CSS 属性很多，但并不是所有的属性默认都是能继承父元素对应属性的，常见的继承属性可以分为以下几类：

* 字体相关：font-family、font-style、font-size、font-weight 等；
* 文本相关：text-align、text-indent、text-decoration、text-shadow、letter-spacing、word-spacing、white-space、line-height、color 等；
* 列表相关：list-style、list-style-image、list-style-type、list-style-position 等；
* 其他属性：visibility、cursor 等；

### 3. 继承控制 <a href="#saos2" id="saos2"></a>

对于其他默认不继承的属性也可以通过以下几个属性值来控制继承行为：

* inherit：继承父元素对应属性的计算值；
* initial：应用该属性的默认值，比如 color 的默认值是 #000；
* unset：如果属性是默认可以继承的，则取 inherit 的效果，否则同 initial；
* revert：效果等同于 unset，兼容性差。

## 六、块级元素和行内元素 <a href="#s6ua9" id="s6ua9"></a>

### 1. 行内元素 <a href="#kpsri" id="kpsri"></a>

一个行内元素只占据它对应标签的边框所包含的空间。

下面的元素都是行内元素：

* [b](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/b), [big](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/big), [i](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/i), [small](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/small), [tt](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/tt)
* [abbr](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/abbr), [acronym](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/acronym), [cite](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/cite), [code](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/code), [dfn](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/dfn), [em](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/em), [kbd](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/kbd), [strong](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/strong), [samp](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/samp), [var](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/var)
* [a](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a), [bdo](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/bdo), [br](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/br), [img](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img), [map](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/map), [object](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/object), [q](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/q), [script](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/script), [span](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/span), [sub](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/sub), [sup](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/sup)
* [button](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/button), [input](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input), [label](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/label), [select](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/select), [textarea](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/textarea)

### 2. 块级元素 <a href="#ut1a5" id="ut1a5"></a>

块级元素占据其父元素（容器）的整个空间，因此创建了一个“块”。

常见的块级元素如下：

* \<div>\<p>\<li>\<h1>\<h2>\<h3>\<h4>\<h5>\<h6>
* \<form>\<header>\<hr>\<ul>\<ol>\<address>\<article>\<aside>
* \<audio>\<canvas>\<dd>\<dl>\<fieldset>\<section>\<video>

### 3. 比较 <a href="#brfgv" id="brfgv"></a>

* 默认情况下，行内元素不会以新的一行开始，而块级元素会新起一行。
* 块级元素可以设置 width, height属性，注意：块级元素即使设置了宽度，仍然是独占一行的。而行内元素设置width, height无效。
* 块级元素可以设置 margin 和 padding。而行内元素设置 margin 和 padding 后只能在水平方向有效，竖直方向无效。
* 一般情况下，行内元素只能包含数据和其他行内元素。而块级元素可以包含行内元素和其他块级元素。

## 七、可替换元素和非替换元素 <a href="#dcply" id="dcply"></a>

### 1. 可替换元素 <a href="#emyqb" id="emyqb"></a>

可替换元素（replaced element）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。

典型的可替换元素有：

* \<iframe>
* \<video>
* \<embed>
* \<img>
* \<input>
* \<select>
* \<textarea>

有些元素仅在特定情况下被作为可替换元素处理：

* \<option>
* \<audio>
* \<canvas>
* \<object>
* \<applet>

### 2. 非替换元素 <a href="#dy4jn" id="dy4jn"></a>

非替换元素（non-replaced element） 的内容由CSS渲染直接表现给客户端。和替换元素对应，不是替换元素的元素就是非替换元素。

HTML5 规范中的大多数元素都是不可替换元素，例如：\<p>,\<h1>,\<div> ...

### 3. 比较 <a href="#k74be" id="k74be"></a>

* 所有替换元素都是内联元素，默认 display 属性是 inline 或 inline-block（除了input\[type="hidden"]默认display: none;）。
* 替换元素有自己默认的样式、尺寸（根据浏览器不同而不同），而且其 vertical-align 属性默认是 bottom，非替换元素默认值是 baseline。

## 八、单位 <a href="#vsmyr" id="vsmyr"></a>

![CSS 单位](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e0b942baa35d4951a8378aaaca8de019\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

详细的 CSS 单位的内容可以参见[这篇文章](https://juejin.cn/post/7036900903462371336)。

### 1. 绝对单位 <a href="#dgznn" id="dgznn"></a>

* **px**: Pixel 像素，像素 px 相对于设备显示器屏幕分辨率而言；
* **pt**: Points 磅，1 pt = 1/72 英寸；
* **pc**: Picas 派卡，十二点活字（印刷中使用的），相当于我国新四号铅字的尺寸；
* **in**: Inches 英寸
* **mm**: Millimeter 毫米
* **cm**: Centimeter 厘米
* **q**: Quarter millimeters 1/4毫米

### 2. 相对单位 <a href="#aeljr" id="aeljr"></a>

* **%**: 百分比，相对于父元素宽度；
* **em**: Element meter 根据文档字体计算尺寸，相对于当前文档对象内文本的字体尺寸而言，若未指定字体大小则继承自上级元素，以此类推，直至 body，若 body 未指定则为浏览器默认大小；
* **rem**: Root element meter 根据根文档（ body/html ）字体计算尺寸，相对于根文档对象（ body/html ）内文本的字体尺寸而言，若未指定字体大小则继承为浏览器默认字体大小；
* **ex**: 文档字符“x”的高度，相对于字符“x”的高度，通常为字体高度的一半，若未指定字体尺寸，则相对于浏览器的默认字体尺寸；
* **ch**: 文档数字“0”的的宽度，相对于数字“0”的宽度；
* **vh**: View height 可视范围高度，相对于可视范围的高度；
* **vw**: View width 可视范围宽度，相对于可视范围的宽度；
* **vmin**: View min 可视范围的宽度或高度中较小的那个尺寸；
* **vmax**: View max 可视范围的宽度或高度中较大的那个尺寸；

![vh 和 vw](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8448c193d3bf4fb5b396e884dfedb0fa\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 九、颜色体系 <a href="#ggzmj" id="ggzmj"></a>

CSS 中用于表示颜色的值种类繁多，足够构成一个体系，根据 [CSS 颜色草案](https://link.juejin.cn/?target=https%3A%2F%2Fdrafts.csswg.org%2Fcss-color-3%2F) 中提到的颜色值类型，大概可以把它们分为这几类：

* 颜色关键字
* transparent 关键字
* currentColor 关键字
* RGB 颜色
* HSL 颜色

### 1. 颜色关键字 <a href="#dnjgn" id="dnjgn"></a>

颜色关键字（color keywords）是不区分大小写的标识符，它表示一个具体的颜色，比如 white（白），黑（black）等，截止到目前为止 CSS 颜色关键字总共有 146 个，这里可以查看 [完整的色彩关键字列表](https://link.juejin.cn/?target=https%3A%2F%2Fcodepen.io%2Fbulandent%2Fpen%2FgOLovwL)。

需要注意的是如果声明的时候的颜色关键字是错误的，浏览器会忽略它。

### 2. transparent 关键字 <a href="#badm1" id="badm1"></a>

transparent 关键字表示一个完全透明的颜色，即该颜色看上去将是背景色。从技术上说，它是带有 alpha 通道为最小值的黑色，是 rgba(0,0,0,0) 的简写。

### 3. currentColor 关键字 <a href="#aiqvo" id="aiqvo"></a>

currentColor 会取当前元素继承父级元素的文本颜色值或声明的文本颜色值，即 computed 后的 color 值。

比如，对于如下 CSS，该元素的边框颜色会是 red：

```
.btn {
    color: red;
    border: 1px solid currentColor;
}
```

### 4. RGB 颜色 <a href="#jvthk" id="jvthk"></a>

RGB\[A] 颜色是由 R(red)-G(green)-B(blue)-A(alpha) 组成的色彩空间。

![RGB](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a4f592201b6042d4a0433647babbe62e\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

在 CSS 中，它有两种表示形式：

#### 十六进制符号 <a href="#zmj4g" id="zmj4g"></a>

RGB 中的每种颜色的值范围是 00\~ff，值越大表示颜色越深。所以一个颜色正常是 6 个十六进制字符加上 # 组成，比如红色就是 #ff0000。

如果 RGB 颜色需要加上不透明度，那就需要加上 alpha 通道的值，它的范围也是 00\~ff，比如一个带不透明度为 67% 的红色可以这样写 #ff0000aa。

使用十六进制符号表示颜色的时候，都是用 2 个十六进制表示一个颜色，如果这 2 个字符相同，还可以缩减成只写 1 个，比如，红色 #f00；带 67% 不透明度的红色 #f00a。

#### 函数符 <a href="#q2wab" id="q2wab"></a>

当 RGB 用函数表示的时候，每个值的范围是 0\~255 或者 0%\~100%，所以红色是 rgb(255, 0, 0)， 或者 rgb(100%, 0, 0)。

如果需要使用函数来表示带不透明度的颜色值，值的范围是 0\~1 及其之间的小数或者 0%\~100%，比如带 67% 不透明度的红色是 rgba(255, 0, 0, 0.67) 或者 rgba(100%, 0%, 0%, 67%)。

在第 4 代 CSS 颜色标准中，新增了一种新的函数写法，即可以把 RGB 中值的分隔逗号改成空格，而把 RGB 和 alpha 中的逗号改成 /，比如带 67% 不透明度的红色可以这样写 rgba(255 0 0 / 0.67)。另外还把 rgba 的写法合并到 rgb 函数中了，即 rgb 可以直接写带不透明度的颜色。

需要注意的是 RGB 这 3 个颜色值需要保持一致的写法，要嘛用数字要嘛用百分比，而不透明度的值的可以不用和 RGB 保持一致写法。比如 rgb(100%, 0, 0) 这个写法是无效的；而 rgb(100%, 0%, 0%, 0.67) 是有效的。

### 5. HSL 颜色 <a href="#wqk8b" id="wqk8b"></a>

HSL\[A] 颜色是由色相(hue)-饱和度(saturation)-亮度(lightness)-不透明度组成的颜色体系。

![HSL](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a416a969aacf40aeb6068ff550623555\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

* 色相（H）是色彩的基本属性，值范围是 0 - 360deg， 0 (或 360) 为红色, 120 为绿色, 240 为蓝色；
* 饱和度（S）是指色彩的纯度，越高色彩越纯，低则逐渐变灰，取 0\~100% 的数值；0% 为灰色，100% 全色；
* 亮度（L），取 0\~100%，0% 为暗，100% 为白；
* 不透明度（A），取 0 - 1 及之间的小数；

写法上可以参考 RGB 的写法，只是参数的值不一样。

给一个按钮设置不透明度为 67% 的红色的 color 的写法，以下全部写法效果一致：

```
button {
    color: #ff0000aa;
    color: #f00a;
    color: rgba(255, 0, 0, 0.67);
    color: rgb(100% 0% 0% / 67%);
    color: hsla(0, 100%, 50%, 67%);
    color: hsl(0deg 100% 50% / 67%);
}
```

## 十、视觉格式化模型 <a href="#zena3" id="zena3"></a>

### 1. 概念 <a href="#cxjem" id="cxjem"></a>

视觉格式化模型（Visual formatting model）是用来处理和在视觉媒体上显示文档时使用的计算规则。CSS 中一切皆盒子，而视觉格式化模型简单来理解就是规定这些盒子应该怎么样放置到页面中去，这个模型在计算的时候会依赖到很多的因素，比如：盒子尺寸、盒子类型、定位方案（是浮动还是定位）、兄弟元素或者子元素以及一些别的因素。

![Visual formatting model](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ee9c7946b76427eb6dab179f0520c2f\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

从上图中可以看到视觉格式化模型涉及到的内容很多，有兴趣深入研究的可以结合上图看这个 W3C 的文档 [Visual formatting model](https://link.juejin.cn/?target=https%3A%2F%2Fwww.w3.org%2FTR%2FCSS2%2Fvisuren.html)。所以这里就简单介绍下盒子类型。

![display 各个值对应的完整值](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f659e9524584a42bf22ffbccac8251b\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

盒子类型由 display 决定，同时给一个元素设置 display 后，将会决定这个盒子的 2 个显示类型（display type）：

* outer display type（对外显示）：决定了该元素本身是如何布局的，即参与何种格式化上下文；
* inner display type（对内显示）：其实就相当于把该元素当成了容器，规定了其内部子元素是如何布局的，参与何种格式化上下文；

### 2. 对外显示 <a href="#et7tp" id="et7tp"></a>

对外显示方面，盒子类型可以分成 2 类：block-level box（块级盒子） 和 inline-level box（行内级盒子）。

依据上图可以列出都有哪些块级和行内级盒子：

* 块级盒子：display 为 block、list-item、table、flex、grid、flow-root 等；
* 行内级盒子：display 为 inline、inline-block、inline-table 等；

所有块级盒子都会参与 BFC，呈现垂直排列；而所有行内级盒子都参与 IFC，呈现水平排列。

除此之外，block、inline 和 inline-block 还有什么更具体的区别呢？

#### block <a href="#wi7ta" id="wi7ta"></a>

* 占满一行，默认继承父元素的宽度；多个块元素将从上到下进行排列；
* 设置 width/height 将会生效；
* 设置 padding 和 margin 将会生效；

#### inline <a href="#jhf4a" id="jhf4a"></a>

* 不会占满一行，宽度随着内容而变化；多个 inline 元素将按照从左到右的顺序在一行里排列显示，如果一行显示不下，则自动换行；
* 设置 width/height 将不会生效；
* 设置竖直方向上的 padding 和 margin 将不会生效；

#### inline-block <a href="#taw6g" id="taw6g"></a>

* 是行内块元素，不单独占满一行，可以看成是能够在一行里进行左右排列的块元素；
* 设置 width/height 将会生效；
* 设置 padding 和 margin 将会生效；

### 3. 对内显示 <a href="#qox9u" id="qox9u"></a>

对内方面，其实就是把元素当成了容器，里面包裹着文本或者其他子元素。container box 的类型依据 display 的值不同，分为 4 种：

* block container：建立 BFC 或者 IFC；
* flex container：建立 FFC；
* grid container：建立 GFC;
* ruby container：接触不多，不做介绍。

值得一提的是如果把 img 这种替换元素（replaced element）申明为 block 是不会产生 container box 的，因为替换元素比如 img 设计的初衷就仅仅是通过 src 把内容替换成图片，完全没考虑过会把它当成容器。

## 十一、@ 规则 <a href="#djzwz" id="djzwz"></a>

CSS 规则是样式表的主体，通常样式表会包括大量的规则列表。但有时候也需要在样式表中包括其他的一些信息，比如字符集，导入其它的外部样式表，字体等，这些需要专门的语句表示。

而 @规则 就是这样的语句。CSS 里包含了以下 @规则：

* [@namespace](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40namespace) 告诉 CSS 引擎必须考虑XML命名空间。
* [@media](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40media), 如果满足媒体查询的条件则条件规则组里的规则生效。
* [@page](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40page), 描述打印文档时布局的变化.
* [@font-face](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40font-face), 描述将下载的外部的字体。
* [@keyframes](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40keyframes), 描述 CSS 动画的关键帧。
* [@document](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40document), 如果文档样式表满足给定条件则条件规则组里的规则生效。
* [@charset](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40charset) , 用于定义样式表使用的字符集。
* [@import](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40import) , 用于告诉 CSS 引擎引入一个外部样式表;
* [@supports](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2F%40supports) 用于查询特定的 CSS 是否生效，可以结合 not、and 和 or 操作符进行后续的操作。

## 十二、参考 <a href="#y4qe5" id="y4qe5"></a>

* [前端基础篇之CSS世界](https://juejin.cn/post/6844903894313598989)
* [一次性搞懂行内元素和块级元素的区别](https://juejin.cn/post/6964644611822190622)
* [一文读懂 CSS 单位](https://juejin.cn/post/7036900903462371336)
* [万字 CSS 基础拾遗](https://juejin.cn/post/6941206439624966152)
