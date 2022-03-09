# 浏览器前缀

## 一、前言 <a href="#gez4gf" id="gez4gf"></a>

**浏览器前缀**（Vendor Prefix）是一些放在 CSS 属性前的小字符串，用来确保这种属性只在特定的浏览器渲染引擎下才能识别和生效。主要是各种浏览器用来试验或测试新出现的属性特征。可以总结为以下3点：

* 试验一些还未成为标准的的 CSS 属性——也许永远不会成为标准
* 对新出现的标准的 CSS3 属性特征做实验性的实现
* 对 CSS3 中一些新属性做等效语义的个性实现

因为制定 HTML 和 CSS 标准的组织 W3C 动作是很慢的。一个新属性的制定要走很复杂的程序。而浏览器厂商为了抓紧推广，如果一个属性已经够成熟了，就会在浏览器中加入支持。而为避免日后 W3C 公布标准时有所变更，加入一个私有前缀，比如 `-webkit-border-radius`，通过这种方式来提前支持新属性。等到日后 W3C 公布了标准，`border-radius` 的标准写法确立之后，再让新版的浏览器支持 `border-radius` 这种写法。

## 二、浏览器前缀类别

浏览器前缀的种类取决于浏览器渲染引擎（浏览器内核）的类别，所以有以下几种前缀。

| 前缀       | 渲染引擎    | 相应浏览器                      |
| -------- | ------- | -------------------------- |
| -moz-    | Gecco   | Firefox                    |
| -webkit- | Webkit  | Safari，早期Chrome，Andorid浏览器 |
| -o-      | Presto  | 早期Opera                    |
| -ms-     | Trident | IE，Edge                    |

## 三、浏览器前缀用法

当需要使用览器引擎前缀时，需要把带有各种前缀的写法放在前面，然后**把不带前缀的标准的写法放到最后**。

```css
-moz-border-radius: 10px; 
-webkit-border-radius: 10px; 
-o-border-radius: 10px; 
-ms-border-radius: 10px;
border-radius: 10px;  /* 不带前缀的放到最后 */
```

## 四、自动添加浏览器前缀

通常我们不在代码里直接写带有浏览器前缀的属性，因此出现很多工具来把这项工作自动化。

* [Autoprefixer](https://github.com/postcss/autoprefixer)：采用[Can I Use...](http://caniuse.com)的数据库来判断哪些前缀是需要添加的；此外， 它是在本地完成编译的，类似预处理器。
* [CSS3, Please!](http://css3please.com)和[pleeease](http://pleee-ase.io/playground.html)：可以把无前缀的CSS代码粘贴进去，它们会自动帮你把必要的前缀都加好。但这类网站因为使用成本太高所以已经过气了。
* [-prefix-free](http://leaverou.github.io/prefixfree)：在浏览器中进行特性检测以决定哪些前缀是需要的。它的好处在于几乎不需要更新，因为其所有信息都是用一份属性清单在真实的浏览器环境中跑出来的结果。
* 类似[Stylus](http://stylus-lang.com)、[LESS](http://lesscss.org)或[Sass](http://sass-lang.com)的预处理器并不自带任何加前缀的方法， 但很多人开发过一些能为常用属性加前缀的mixin；社区中也有一些库提供了这类mixin。

## 五、需要添加前缀的属性

主要的需要添加浏览器前缀的属性包括：

* @keyframes
* 移动和变换属性(transition-property, transition-duration, transition-timing-function, transition-delay)
* 动画属性 (animation-name, animation-duration, animation-timing-function, animation-delay)
* border-radius
* box-shadow
* backface-visibility
* column属性
* flex属性
* perspective属性

## 六、参考

* \<CSS揭秘>
* [http://www.cnblogs.com/yaotome/p/7192741.html](http://www.cnblogs.com/yaotome/p/7192741.html)
* [https://www.jianshu.com/p/09285b04111a](https://www.jianshu.com/p/09285b04111a)
* [https://www.cnblogs.com/duyingxuan/p/6517933.html](https://www.cnblogs.com/duyingxuan/p/6517933.html)
* [https://www.zhihu.com/question/20597072](https://www.zhihu.com/question/20597072)
