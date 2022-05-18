# 性能优化执行篇

## 一、引言

我们都知道大致的网页渲染过程有以下五个步骤为关键渲染路径：

1. HTML -> DOM，将 html 解析为 DOM
2. CSS -> CSSOM，将 CSS 解析为 CSSOM
3. DOM/CSSOM -> Render Tree，将 DOM 与 CSSOM 合并成渲染树
4. RenderTree -> Layout，确定渲染树中每个节点的位置信息
5. Layout -> Paint，将每个节点渲染在浏览器中

对于渲染层面，亦即执行层面的性能优化，很大程度上是对**关键渲染路径进行优化**，其中最重要的理念是**避免过多的重绘和重排**。

又可以分为以下四个部分来讨论。

* 渲染优化
* 样式优化
* 脚本优化

## **二、渲染优化**

### **1**. 图片优化

* 不用图片。很多时候会使用到很多修饰类图片，其实这类修饰图片完全可以用 CSS 去代替。
* 对于移动端来说，屏幕宽度就那么点，完全没有必要去加载原图浪费带宽。一般图片都用 CDN 加载，可以计算出适配屏幕的宽度，然后去请求相应裁剪好的图片。
* 小图使用 base64 格式
* 将多个图标文件整合到一张图片中（雪碧图）
* 选择正确的图片格式：
  * 对于能够显示 WebP 格式的浏览器尽量使用 WebP 格式。因为 WebP 格式具有更好的图像数据压缩算法，能带来更小的图片体积，而且拥有肉眼识别无差异的图像质量，缺点就是兼容性并不好
  * 小图使用 PNG，其实对于大部分图标这类图片，完全可以使用 SVG 代替
  * 照片使用 JPEG

### 2. 无阻塞加载

无阻塞加载即将 CSS 放在文件头部，JavaScript 文件放在底部。

* CSS 执行会阻塞渲染，阻止 JS 执行；
* JS 加载和执行会阻塞 HTML 解析，阻止 CSSOM 构建；

如果这些 CSS、JS 标签放在 HEAD 标签里，并且需要加载和解析很久的话，那么页面就空白了。所以 JS 文件要放在底部（不阻止 DOM 解析，但会阻塞渲染），等 HTML 解析完了再加载 JS 文件，尽早向用户呈现页面的内容。

那为什么 CSS 文件还要放在头部呢？

因为先加载 HTML 再加载 CSS，会让用户第一时间看到的页面是没有样式的、“丑陋”的，为了避免这种情况发生，就要将 CSS 文件放在头部了。

### **3. 避免资源标签 src 为空**

避免 img、iframe 等的 src 为空，空`src`会重新加载当前页面，影响速度和效率。

### **4. 设置 viewport**

HTML的`viewport`可加速页面的渲染

```html
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1, minimum-scale=1, maximum-scale=1">
```

### **5. 减少DOM节点**

DOM 节点太多影响页面的渲染，尽量减少 DOM 节点。

### **6. 优化动画**

* 尽量使用 CSS3 动画
* 合理使用 requestAnimationFrame 动画代替 setTimeout
* 适当使用 Canvas 动画：5个元素以内使用`CSS动画`，5个元素以上使用`Canvas动画`
* 使用 transform 和 opacity 属性更改来实现动画：在 CSS 中，transforms 和 opacity 这两个属性更改不会触发重排与重绘，它们是可以由合成器（composite）单独处理的属性

### **7. GPU 加速**

使用某些 HTML5 标签和 CSS3 属性会触发`GPU渲染`，请合理使用(**过渡使用会引发手机耗电量增加**)

* HTML标签：`video`、`canvas`、`webgl`
* CSS属性：`opacity`、`transform`、`transition`

## **三、样式优化**

### **1. 避免在 HTML 中书写 style**

### **2. 避免 CSS 表达式**

CSS 表达式可以在 CSS 里执行 JavaScript，仅 IE5-IE7 支持，IE8 标准模式已经废弃。 CSS 表达式超出预期的频繁执行，页面滚动、鼠标移动时都会不断执行，带来很大的性能损耗。

### **3. 正确使用 display**

`display`会影响页面的渲染

* `display:inline`后不应该再使用`float`、`margin`、`padding`、`width`和`height`
* `display:inline-block`后不应该再使用`float`
* `display:block`后不应该再使用`vertical-align`
* `display:table-*`后不应该再使用`float`和`margin`

### **4. 不滥用 float**

`float`在渲染时计算量比较大，尽量减少使用

### **5. 不声明过多的 font-size**

过多的`font-size`影响CSS树的效率

### **6. 标准化各种浏览器前缀**

* **无前缀属性应放在最后**
* CSS 动画属性只用-webkit-、无前缀两种
* 其它前缀为-webkit-、-moz-、-ms-、无前缀四种：`Opera`改用`blink`内核，`-o-`已淘汰

### 7. 使用 flexbox 而不是较早的布局模型

在早期的 CSS 布局方式中我们能对元素实行绝对定位、相对定位或浮动定位。而现在，我们有了新的布局方式 [flexbox](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2FCSS\_Flexible\_Box\_Layout%2FBasic\_Concepts\_of\_Flexbox)，它比起早期的布局方式来说有个优势，那就是性能比较好。

下面的截图显示了在 1300 个框上使用浮动的布局开销：

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8171c61cb63f4e599bd8e0144371f2dc\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

然后我们用 flexbox 来重现这个例子：

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e95eca94007949ee8f0b4f5e7306bbf0\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

现在，对于相同数量的元素和相同的视觉外观，布局的时间要少得多（本例中为分别 3.5 毫秒和 14 毫秒）。

## **四、脚本优化**

对于 JavaScript 的优化，主要可以分为 DOM 操作相关和 JS 本身的优化。

### 1. DOM 操作优化 <a href="#s5w4dg" id="s5w4dg"></a>

HTML 集合（HTMLCollections）是包含了 DOM 节点引用的类数组对象。以下方法或属性的返回值就是一个集合：

* document.getElementsByName()
* document.getElementsByClassName()
* document.getElementsByTagName()
* document.images
* document.links
* document.forms

HTML 集合以一种假定实时态实时存在，意味着 HTML 集合一直与文档保持着连接。因此每当底层文档对象更新时它也会自动更新，并且每次需要最新的信息时都会重复执行查询过程，即便只是获取集合里的元素个数也是如此。而这正是低效之源。可以从两方面提高相应性能：

#### 可以将集合的 length 属性缓存到一个变量中，在迭代中使用这个变量。如果经常操作这个集合，可以将集合拷贝到数组中。

```javascript
function slow() {  // 慢
    var coll = document.getElementsByTagName('div'),
        name = '';
    for (var i = 0; i < coll.length; i++) {
        name = coll[i].nodeName;
        name = coll[i].nodeType;
        name = coll[i].tagName;
    }
    return name;
}

function fast() {  // 快
    var coll = document.getElementsByTagName('div'),
        len = coll.length,  // 把HTML集合的长度存在局部变量
        name = '',
        el = null;
    for (var i = 0; i < len; i++) {
        el = coll[i];  // 把集合存储在局部变量中
        name = el.nodeName;
        name = el.nodeType;
        name = el.tagName;
    }
    return name;
}
```

#### 在特定的场合选取最高效的API。

* querySelector() 和querySelectorAll() 方法使用CSS 选择器作为参数并返回一个NodeList，NodeList是包含着匹配节点的类数组对象。该方法不会返回 HTML 集合，因此返回的节点不会对应实时的文档结构，也避免了潜在的性能问题。
* 为了获取某类元素列表，推荐使用 querySelector() 和querySelectorAll() 来代替getElementById()、getElementsByTagName()、getElementsByClassName()。

```javascript
var elements = document.querySelectorAll('#menu a');

var elements = document.getElementById('menu').getElementsByTagName('a');
```

### 2. 重排和重绘优化

浏览器在加载完页面后会解析并生成两个内部数据结构：DOM 树和渲染树。当 DOM 的变化影响了元素的几何属性，并且其他元素也会受到影响，浏览器需要重新计算元素的几何属性，并重新构造渲染树。这个过程称为重排（reflow），完成重排后，浏览器会重新绘制受影响的部分到屏幕中，该过程称为重绘（repaint）。

下述情况中会发生重排：

* 添加或删除可见的DOM 元素。
* 元素位置改变。
* 元素尺寸改变（margin、padding、border、width、height等）。
* 内容改变。
* 页面渲染器初始化。
* 浏览器窗口尺寸改变。
* 滚动条的出现。

所以应该尽量减少重排和重绘的次数，可从三个方面进行优化：

#### 尽量合并多次对DOM 和样式的修改，然后一次处理掉以减少重排和重绘的次数。

```javascript
// 优化前
var el = document.getElementById('mydiv');
el.style.borderLeft = '1px';
el.style.borderRight = '2px';
el.style.padding = '5px';

// 优化后
var el = document.getElementById('mydiv');
el.style.cssText = 'border-left: 1px; border-right: 2px; padding: 5px';
```

#### 当需要对 DOM 元素进行一系列操作时，可以先使元素脱离文档流，再对其应用多重改变，最后把元素带回文档中的方法来减少重排和重绘的次数。有三种基本方法可以使 DOM脱离文档流：

* 隐藏元素，应用修改，重新显示。
* 使用文档片断在DOM 之外构建一个子树，再把它拷贝回文档。
* 将原始元素拷贝到一个脱离文档的节点中，修改副本，完成后再替换原始元素。

#### 当有某个动画（例如页面顶部的展开/折叠）导致一次代价昂贵的大规模重排，会让用户感到页面一顿一顿的，可用以下步骤来避免页面中的大部分重排。

* 使用绝对位置定位页面上的动画元素，将其脱离文档流；
* 让元素动起来，当它扩大时，会临时覆盖部分页面。但这只是页面一个小区域的重绘，不会产生大部分的重排和重绘；
* 当动画结束时恢复定位，从而只会下移一次文档的其他元素。

### 3. 高效的事件处理

* 减少绑定事件监听的节点，如通过**事件委托**；
* 尽早处理事件，在 DOMContentLoaded 即可进行，不用等到 load 以后;

事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。所有用到按钮的事件（多数鼠标事件和键盘事件）都适合采用事件委托技术， 使用事件委托可以节省内存。

```html
<ul>
  <li>苹果</li>
  <li>香蕉</li>
  <li>凤梨</li>
</ul>

// good
document.querySelector('ul').onclick = (event) => {
  const target = event.target
  if (target.nodeName === 'LI') {
    console.log(target.innerHTML)
  }
}

// bad
document.querySelectorAll('li').forEach((e) => {
  e.onclick = function() {
    console.log(this.innerHTML)
  }
}) 
```

### 4. JS 基本优化 <a href="#m0wzew" id="m0wzew"></a>

可从以下几个方面进行基本的优化：

#### 多个变量声明

```javascript
// Bad
let count = 5;
let color = 'blue';
let values = [1, 2, 3];
let now = new Date();

// Good
let count = 5,
    color = 'blue',
    values = [1, 2, 3],
    now = new Date();
```

#### 使用数组和对象字面量

```javascript
// Bad
let values = new Array();
values[0] = 123;
values[1] = 456;
values[2] = 789;
let person = new Object();
person.name = 'Hello';
person.age = 28;

// Good
let values = [123, 456, 789];
let person = {
    name: 'hello',
    age: 28
}
```

#### 避免临时变量

```
// Bad
str += 'one' + 'two';

// Good
str = str + 'one' + 'two';
```

#### 使用原生方法

### 5. 数据存取

在 JavaScript 中数据存储的位置会对代码整体性能产生重大的影响。JavaScript 中有四种基本的数据存取位置：

* 字面量：只代表自身，不存储在特定位置；
* 局部变量：使用关键字var定义的数据存储单元；
* 数组项：存储在JavaScript数组对象内部，以数字作为索引；
* 对象成员：存储在JavaScript对象内部，以字符串作为索引；

存取字面量和局部变量的速度最快，而存取数组元素和对象成员的速度相对较慢。而由于作用域链和原型链的存在，可以做以下优化：

#### 如果某个跨作用域的值在函数中被引用一次以上，就把它存储到局部变量里。

```javascript
// Bad
function initUI() {
    var bd = document.body,
        links = document.getElementsByTagName_r("a"),
        i = 0,
        len = links.length;
    while (i < len) {
        update(links[i++]);
    }
    document.getElementById("go-btn").onclick = function () {
        start();
    };
    bd.className = "active";
}

// Good
function initUI() {
    var doc = document,
        bd = doc.body,
        links = doc.getElementsByTagName_r("a"),
        i = 0,
        len = links.length;
    while (i < len) {
        update(links[i++]);
    }
    doc.getElementById("go-btn").onclick = function () {
        start();
    };
    bd.className = "active";
}
```

#### 如果要多次读取一个对象属性，最好是将属性值保存到局部变量中。这种优化不适用于对象的成员方法，因为 this 的关系。

```javascript
// Bad
function toggle(element) {
    if (YAHOO.util.Dom.hasClass(element, "selected")) {
        YAHOO.util.Dom.removeClass(element, "selected");
        return false;
    } else {
        YAHOO.util.Dom.addClass(element, "selected");
        return true;
    }
}

// Good
function toggle(element) {
    var Dom = YAHOO.util.Dom;
    if (Dom.hasClass(element, "selected")) {
        Dom.removeClass(element, "selected");
        return false;
    } else {
        Dom.addClass(element, "selected");
        return true;
    }
}
```

### 6. 循环优化

对于循环，整体的优化思路是减少每次循环处理的事务和减少循环的次数。当循环复杂度为O(n)时，减少每次迭代的工作量是最有效的方法。当复杂度大于O(n)时，建议着重减少迭代次数。

#### 减少每次循环处理的事务

减少对象成员及数组项的查找次数或者使用倒序循环可以减少每次处理的事务。

```javascript
// Bad
for (let i = 0 ; i < items.length; i++) {
    process(items[i])
}

let j = 0;
while (j < items.length) {
    process(items[j++]);
}

let k = 0;
do {
    process(items[k]);
} while (k < items.length)


// 缓存数组长度
for (let i = 0, len = items.length ; i < len; i++) {
    process(items[i])
}

let j = 0,
    count = items.length;
while (j < count) {
    process(items[j++]);
}

let k = 0,
    num = items.length;
do {
    process(items[k]);
} while (k < num)


// 倒序循环
for (let i = items.length ; i--; ) {
    process(items[i])
}

let j = items.length;
while (j--) {
    process(items[j++]);
}

let k = items.length;
do {
    process(items[k]);
} while (k--)
```

#### 减少循环的次数

使用达夫设备(Duff'sDevice)可以减少循环的次数。

```javascript
// 达夫设备
let i = 0,
    remain = items.length % 8,
    times = Math.floor(items.length / 8);

while (remain--) {
    process(items[i++]);
}

while (times--) {
    process(items[i++]);
    process(items[i++]);
    process(items[i++]);
    process(items[i++]);
    process(items[i++]);
    process(items[i++]);
    process(items[i++]);
    process(items[i++]);
}
```

### 7. 条件优化

对于条件语句的选择：

* 条件数量越大，越倾向于使用 switch 而不是 if-else。
* if-else适用于判断两个离散值或几个不同的值域， switch适用于判断多于两个离散值。
* 当有大量离散值需要测试时，可以使用数组和对象而不是 if-else 和 switch 来构建查找表。

对于条件语句的优化准则：最小化到达正确分之前所需判断的条件数量。

#### 使用 if-else 时应确保最可能出现的条件放在首位。

```javascript
// 确保最可能出现的条件放在首位
if (value < 5) {  //经常出现
    //do something
} else if (value > 5 && value < 10) {
    //do something
} else {
    //do something
}
```

#### 单个的 if-else 过于庞大时可以使用嵌套的 if-else 语句。

```javascript
// Bad
if (value === 0) {
    return result0;
} else if (value === 1) {
    return result1;
} else if (value === 2) {
    return result2;
} else if (value === 3) {
    return result3;
} else if (value === 4) {
    return result4;
} else if (value === 5) {
    return result5;
} else {
    return result6;
}

// Good
if (value < 3) {
    if (value < 2) {
        if (value === 0) {
            return result0;
        } else {
            return result1;
        }
    } else {
        return result2;
    }
} else {
    if (value < 5) {
        if (value === 3) {
            return result3;
        } else {
            return result4;
        }
    } else {
        if (value === 5) {
            return result5;
        } else {
            return result6;
        }
    }
}
```

### 8. 使用位操作

JavaScript 中的数字都使用 IEEE-754 标准以 64 位格式存储。但是在位操作中，数字被转换为有符号的 32 位格式。即使需要转换，位操作也比其他数学运算和布尔操作快得多。

**取模**

由于偶数的最低位为 0，奇数为 1，所以取模运算可以用位操作来代替。

```javascript
if (value % 2) {
	// 奇数
} else {
	// 偶数 
}
// 位操作
if (value & 1) {
	// 奇数
} else {
	// 偶数
}
```

**取整**

```javascript
~~10.12 // 10
~~10 // 10
~~'1.5' // 1
~~undefined // 0
~~null // 0
```

**位掩码**

```javascript
const a = 1
const b = 2
const c = 4
const options = a | b | c
```

通过定义这些选项，可以用按位与操作来判断 a/b/c 是否在 options 中。

```javascript
// 选项 b 是否在选项中
if (b & options) {
	...
}
```

## 五、参考

* [前端性能优化指南](https://juejin.cn/post/6844903844455907336)
* [前端性能优化 24 条建议](https://juejin.cn/post/6892994632968306702)
* [写给中高级前端的性能优化](https://juejin.cn/post/6981673766178783262)
* [工作中如何进行前端性能优化](https://juejin.cn/post/6904517485349830670)
* [写在 2021 的前端性能优化指南](https://juejin.cn/post/7020212914020302856)
* [前端性能优化之雅虎 35 条军规](https://juejin.cn/post/6844903657318645767)
* [高频前端面试题汇总之前端性能优化篇](https://juejin.cn/post/6941278592215515143)
* [前端性能优化三部曲(加载篇)](https://juejin.cn/post/6844903863963631623)
