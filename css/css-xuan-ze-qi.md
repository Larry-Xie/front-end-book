# CSS 选择器

## 一、引言 <a href="#cndte" id="cndte"></a>

CSS 选择器对 HTML 页面中的元素实现一对一，一对多或者多对一的控制，从而给指定元素添加样式。同时还要考虑一个元素被赋予多个样式时如何生效的问题，这个就和选择器优先级相关了。

**优先级**是基于不同种类选择器组成的匹配规则。浏览器通过**优先级**来判断哪些样式与一个元素最为相关，从而在该元素上应用这些样式。

CSS 选择器有如下几种：

![CSS 选择器](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/613b29f0bfb74f8e84947e243f865875\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 二、选择器 <a href="#mff3e" id="mff3e"></a>

### 1. 基本选择器 <a href="#uks5d" id="uks5d"></a>

| **选择器**    | **名称**   | **描述**                  |
| ---------- | -------- | ----------------------- |
| \*         | 通配选择器    | 选择所有的元素                 |
| E          | 元素/标签选择器 | 选择指定标签的元素               |
| #idName    | id选择器    | 选择id属性等于idName的元素       |
| .className | 类选择器     | 选择class属性包含className的元素 |

元素选择器只要是写 CSS 都会经常用，这一块的内容非常简单，没什么特别要说的。

### 2. 属性选择器 <a href="#pxhuj" id="pxhuj"></a>

| **选择器**         | **描述**                                                |
| --------------- | ----------------------------------------------------- |
| E\[att]         | 选择具有att属性的E元素                                         |
| E\[att="val"]   | 选择具有att属性且属性值等于val的E元素                                |
| E\[att\~="val"] | 选择具有att属性且属性值其中一个等于val的E元素（包含只有一个值且该值等于val的情况）        |
| E\[att\|="val"] | 选择具有att属性且属性值为以val开头并用连接符-分隔的字符串的E元素，如果属性值仅为val，也将被选择 |
| E\[att^="val"]  | 选择具有att属性且属性值为以val开头的字符串的E元素                          |
| E\[att$="val"]  | 选择具有att属性且属性值为以val结尾的字符串的E元素                          |
| E\[att\*="val"] | 选择具有att属性且属性值为包含val的字符串的E元素                           |

### 3. 组合选择器 <a href="#ehs9g" id="ehs9g"></a>

| **选择器** | **名称** | **描述**           |
| ------- | ------ | ---------------- |
| E F     | 包含选择器  | 选择所有包含在E元素里面的F元素 |
| E>F     | 子选择器   | 选择所有作为E元素的子元素F   |
| E+F     | 相邻选择器  | 选择紧贴在E元素之后的F元素   |
| E\~F    | 兄弟选择器  | 选择E元素所有兄弟元素F     |

这里要注意几点：

* 子选择器只能选中子元素，而不能选中孙辈；而包含选择符将会选中所有符合条件的后代，包括儿子，孙子，孙子的孙子...
* 相邻选择符只会选中符合条件的相邻的兄弟元素；而兄弟选择符会选中所有符合条件的兄弟元素，不强制是紧邻的元素。

### 4. 伪类选择器 <a href="#oi9fy" id="oi9fy"></a>

![伪类选择器](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/310652ad0bf040cda0b17b4054cecaa1\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

| **选择器**               | **描述**                                           |
| --------------------- | ------------------------------------------------ |
| E:link                | 设置超链接a在未被访问前的样式                                  |
| E:visited             | 设置超链接a在其链接地址已被访问过时的样式                            |
| E:hover               | 设置元素鼠标在其悬停时的样式                                   |
| E:active              | 设置元素在被用户激活（在鼠标点击与释放之间发生的事件）时的样式                  |
| E:focus               | 设置元素在成为输入焦点（该元素的onfocus事件发生）时的样式。(一般应用于表单元素)     |
| E:checked             | 匹配用户界面上处于选中状态的元素E。(用于input type为radio与checkbox时) |
| E:enabled             | 匹配用户界面上处于可用状态的元素E。(一般应用于表单元素)                    |
| E:disabled            | 匹配用户界面上处于禁用状态的元素E。(一般应用于表单元素)                    |
| E:empty               | 匹配没有任何子元素（包括text节点）的元素E                          |
| E:root                | 匹配E元素在文档的根元素。在HTML中，根元素永远是HTML                   |
| E:not(s)              | 匹配不含有s选择符的元素E                                    |
| E:first-child         | 匹配父元素的第一个子元素E                                    |
| E:last-child          | 匹配父元素的最后一个子元素E                                   |
| E:only-child          | 匹配父元素仅有的一个子元素E                                   |
| E:nth-child(n)        | 匹配父元素的第n个子元素E                                    |
| E:nth-last-child(n)   | 匹配父元素的倒数第n个子元素E                                  |
| E:first-of-type       | 匹配同类型中的第一个同级兄弟元素E                                |
| E:last-of-type        | 匹配同类型中的最后一个同级兄弟元素E                               |
| E:only-of-type        | 匹配同类型中的唯一的一个同级兄弟元素E                              |
| E:nth-of-type(n)      | 匹配同类型中的第n个同级兄弟元素E                                |
| E:nth-last-of-type(n) | 匹配同类型中的倒数第n个同级兄弟元素E                              |

注意事项：

* 超链接的4种状态(访问前，鼠标悬停，当前被点击，已访问)，需要有特定的书写顺序才能生效；**a:hover 必须位于 a:link 和 a:visited 之后，a:active 必须位于 a:hover 之后**。
* E:first-child 选择符，E 必须是它的兄弟元素中的第一个元素，换言之，E 必须是父元素的第一个子元素。与之类似的伪类还有 E:last-child，只不过情况正好相反，需要它是最后一个子元素。

### 5. 伪元素选择器 <a href="#jdtb4" id="jdtb4"></a>

| **选择器**                        | **描述**                          |
| ------------------------------ | ------------------------------- |
| E:before/E::before             | 在目标元素E的前面插入的内容。用来和content属性一起使用 |
| E:after/E::after               | 在目标元素E的后面插入的内容。用来和content属性一起使用 |
| E:first-letter/E::first-letter | 设置元素内的第一个字符的样式                  |
| E:first-line/E::first-line     | 设置元素内的第一行的样式                    |
| E::placeholder                 | 设置元素文字占位符的样式。(一般用于input输入框)     |
| E::selection                   | 设置元素被选择时的字体颜色和背景颜色              |

注意事项：

* 一个选择器中只能使用一个伪元素；
* CSS3 中**伪元素应该用双冒号，以便区分伪元素和伪类**。但是旧版的规范未做明确区分，所以大多数浏览器中支持部分伪元素使用单双冒号两种写法；

## 三、优先级 <a href="#gigaj" id="gigaj"></a>

### 1. 基本规则 <a href="#brnut" id="brnut"></a>

基本的优先级规则是：

!important > 内联 > ID选择器 > 类选择器 > 标签选择器 > 通用选择器。

### 2. 权重计算 <a href="#ey1fx" id="ey1fx"></a>

但是在实际中不是简单地根据上述规则来比较的，因为一个元素的一个属性可以从不同的选择器计算得到，还能通过继承的方式得到。

所以实际上是通过权重计算得出的，各个选择器的权重值如下表所示：

![各个选择器权重值](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dd9370357f444a719c3e20b4c400d897\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![权重计算](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4b226c55b87c426c840d2c70d51d3511\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

权重是由 A 、B、C、D 的值来决定的，其中它们的值计算规则如下：

* 如果存在内联样式，那么 A = 1, 否则 A = 0;
* B 的值等于 ID选择器 出现的次数;
* C 的值等于 类选择器 和 属性选择器 和 伪类 出现的总次数;
* D 的值等于 标签选择器 和 伪元素 出现的总次数 。

例如：

```
#nav-global > ul > li > a.nav-link
```

套用上面的算法，依次求出 A B C D 的值：

* 因为没有内联样式 ，所以 A = 0;
* ID 选择器总共出现了1次， B = 1;
* 类选择器出现了1次， 属性选择器出现了0次，伪类选择器出现0次，所以 C = (1 + 0 + 0) = 1；
* 标签选择器出现了3次， 伪元素出现了0次，所以 D = (3 + 0) = 3;
* 上面算出的A 、 B、C、D 可以简记作：(0, 1, 1, 3)。

更多的示例如下：

```
li                                  /* (0, 0, 0, 1) */
ul li                               /* (0, 0, 0, 2) */
ul ol+li                            /* (0, 0, 0, 3) */
ul ol+li                            /* (0, 0, 0, 3) */
h1 + *[REL=up]                      /* (0, 0, 1, 1) */
ul ol li.red                        /* (0, 0, 1, 3) */
li.red.level                        /* (0, 0, 2, 1) */
a1.a2.a3.a4.a5.a6.a7.a8.a9.a10.a11  /* (0, 0, 11,0) */
#x34y                               /* (0, 1, 0, 0) */
li:first-child h2 .title            /* (0, 0, 2, 2) */
#nav .selected > a:hover            /* (0, 1, 2, 1) */
html body #nav .selected > a:hover  /* (0, 1, 2, 3) */
```

### 3. 权重比较 <a href="#mmfxp" id="mmfxp"></a>

在计算出权重之后，需要进行比较来选取最终的值，比较的规则是：从左往右依次进行比较 ，较大者胜出，如果相等，则继续往右移动一位进行比较。

来看一下例子：

* html:

```html
<div class="nav-list" id="nav-list">
  <div class="item">nav1</div>
  <div class="item">nav2</div>
</div>
```

* CSS：

```css
#nav-list .item {
  color: #f00;
}

.nav-list .item {
  color: #0f0;
}
```

算出 #nav-list .item 的优先级是 (0, 1, 1, 0)，.nav-list .item 的优先级是 (0, 0, 2, 0)。 左边第一位都是0， 再看看左边第二位，前者是1，后者是0， 所以（0, 1, 1, 0） 的大于 (0, 0, 2, 0) ，即 #nva-list .item 大于 .nav-list .item，所以字体会是红色。

其它注意点：

* 如果权重值相同，则写在后面的样式生效；
* !important 是 CSS 选择器中的一个"流氓"属性，不论选择器的权重或者优先级的高低，只要加上 !important，那么这个样式的优先级就是最高的；
* 如果针对同一元素样式存在冲突且同时存在 !important ，那么选择器总权重值高者生效；
* 通配符、子选择器、兄弟选择器，虽然权重为0，但是优先于继承的样式；
* !important 设置的属性值不是一定生效，比如对子元素设置的 width 取决于父元素设置的 max-width。因为**优先级的比较是针对于相同属性的**。

## 四、考点 <a href="#uia3k" id="uia3k"></a>

### 1. :not() 的应用 <a href="#m2exl" id="m2exl"></a>

* 给一组元素的除了最后一个以外加样式；

```css
// 给该列表中除最后一项外的所有列表项加一条底边线。
li:not(:last-child) { border-bottom: 1px solid #ddd; }
```

* 选择包含子元素的元素；

```css
// 选择包含子元素的div元素
div:not(:empty)
```

### 2. child 和 type 的差异 <a href="#fcxlv" id="fcxlv"></a>

first-of-type vs first-child:

* E:first-of-type 总是能命中父元素的第1个为 E 的子元素，不论父元素第1个子元素是否为 E；
* E:first-child 里的 E 元素必须是它的兄弟元素中的第一个元素，否则匹配失效；

last-of-type vs last-child:

* E:last-of-type 与 E:last-child 也是同理；

nth-of-type(n) vs nth-child(n):

* E:nth-of-type(n) 总是能命中父元素的第 n 个为 E 的子元素，不论父元素第 n 个子元素是否为 E；
* E:nth-child(n) 会选择父元素的第 n 个子元素 E，如果第 n 个子元素不是 E，则是无效选择符，但n会递增。

### 3. !important 设置的样式一定生效吗 <a href="#ekqis" id="ekqis"></a>

* 如果针对同一元素样式存在冲突且同时存在 !important ，那么选择器总权重值高者生效；
* 比如对子元素设置的 width 取决于父元素设置的 max-width。因为**优先级的比较是针对于相同属性的**。

## 五、参考 <a href="#rcmmf" id="rcmmf"></a>

* [CSS 选择器整理](https://segmentfault.com/a/1190000007815822)
* [前端布局必须了解的css选择器](https://juejin.cn/post/6844904147414712334)
* [深入解析 CSS 选择器](https://juejin.cn/post/6953405751104634916)
* [说下CSS选择器优先级](https://juejin.cn/post/6844904159305531406)
* [深入理解CSS选择器优先级](https://juejin.cn/post/6844903709772611592)
