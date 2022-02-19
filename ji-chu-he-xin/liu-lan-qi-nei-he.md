# 浏览器内核

## 1. 什么是浏览器内核

浏览器的内核其实应该分为两部分：**渲染引擎**（Rendering Engine）和**JavaScript引擎**（JS Engine）。因为JS引擎的逐渐独立，故**通常我们所说的浏览器内核是指渲染引擎**。

## 2. 渲染引擎 <a href="#dfe55f2f" id="dfe55f2f"></a>

### 2.1 作用 <a href="#1754a6f8" id="1754a6f8"></a>

* 渲染引擎负责取得网页的内容，整理讯息，以及计算网页的显示方式，然后会输出至显示器或打印机。
* 渲染引擎决定了浏览器如何显示网页的内容以及页面的格式信息。
* 不同的渲染引擎对网页编写语法的解释会有不同，因此同一网页在不同内核的浏览器里的渲染（显示）效果也可能不同，这也是网页编写者需要在不同内核的浏览器中测试网页显示效果的原因。

### 2.2 类别 <a href="#47c41556" id="47c41556"></a>

* **Trident**

代表作是**IE**，此内核只能用于Windows平台，且不是开源的。同时IE版本比较多，存在很多的兼容性问题。

代表作品还有腾讯TT、Maxthon（遨游）、360浏览器、搜狗浏览器等。

* **Gecko**

代表作是**Firefox**、Netscape6及以上版本，此内核是跨平台的，且是开源的。

* **Presto（废弃）**

代表作是Opera，Opera 12.17及更早版本曾经采用的内核，它是世界公认最快的渲染速度的引擎，自2013年Opera宣布加入谷歌阵营之后，Presto便停止开发并废弃。

* **Webkit**

代表作是**Safari**和曾经的Chrome。是苹果开发的开源项目。代表作品还有Ios和Android默认浏览器。

* **Blink（新）**

代表作是**Chrome**和**Opera**。由Google和Opera Software开发并于2013年4月发布。可看做是Webkit的精简高效强化版。Chrome 28.0.1469.0及更高版本均采用Blink。

## 3. JavaScript引擎 <a href="#87480e1c" id="87480e1c"></a>

### 3.1 作用 <a href="#94891793" id="94891793"></a>

* JavaScript引擎是一个专门处理JavaScript脚本的虚拟机，一般会附带在网页浏览器之中。
* JavaScript引擎负责对JavaScript进行解释、编译和执行，以使网页达到一些动态的效果。
* 每个 JavaScript 引擎都实现了一个版本的 ECMAScript，JavaScript 是它的一个分支。随着 ECMAScript 的不断发展，JavaScript 引擎也不断改进。之所以有这么多不同的引擎，是因为它们每个都被设计运行在不同的 web 浏览器、headless 浏览器、或者像 Node.js 那样的运行时环境中。
* JavaScript 引擎会加载你的源代码，把它分解成字符串（又叫做分词），再把这些字符串转换成编译器可以理解的字节码，然后执行这些字节码。
* 最初的JavaScript引擎只负责对JavaScript进行解释执行，所以执行效率很差。直到2008年Chrome的出现，它的代号为V8的JS引擎是一款为JavaScript打造的实时编译（JIT）引擎，它把JavaScript代码转化为机器码来执行，大大提高了JavaScript的执行速度，此后的JS引擎都加入了编译的功能，并且不断优化升级以提高性能。

### 3.2 类别 <a href="#119f2ddb" id="119f2ddb"></a>

有很多的JS引擎，常见的有以下几种：

* **SpiderMonkey**

第一款JavaScript引擎，早期用于Netscape Navigator，现时用于Mozilla Firefox。

* **V8**

开放源代码，由Google丹麦开发，用于Chrome、NodeJS。

* **JavaScriptCore**

开放源代码，用于Safari。

* **Chakra**

中文译为查克拉，用于Internet Explorer和Edge。

* **Carakan**

由Opera软件公司编写，自Opera10.50版本开始使用。



| **Browser Name** | **Rendering Engine** | **JavaScript Engine** |
| ---------------- | -------------------- | --------------------- |
| IE               | Trident              | Chakra                |
| Edge             | EdgeHTML             | Chakra                |
| FireFox          | Gecko                | SpiderMonkey          |
| Safari           | WebKit               | JavaScriptCore        |
| Chrome           | Blink                | V8                    |
| Opera            | Blink                | Carakan               |

## 4. 国产浏览器 <a href="#f1168df5" id="f1168df5"></a>

国内浏览器例如360安全、360极速、猎豹、傲游、UC等浏览器。一般这些浏览器其中一个内核是Trident，然后再增加一个其他内核。国内的产商一般把其他内核（如Webkit）叫做高速浏览模式，而Trident则是兼容浏览模式，可自由切换。但在兼容浏览模式下经常会出现样式混乱，是因为Trident(IE内核)不兼容的原因。

## 5. 参考 <a href="#01238d4e" id="01238d4e"></a>

* [https://www.jianshu.com/p/e22cbcc357c2](https://www.jianshu.com/p/e22cbcc357c2)
* [https://blog.csdn.net/DarinZanya/article/details/81028562](https://blog.csdn.net/DarinZanya/article/details/81028562)
* [https://zh.wikipedia.org/wiki/JavaScript%E5%BC%95%E6%93%8E](https://zh.wikipedia.org/wiki/JavaScript%E5%BC%95%E6%93%8E)
* [http://web.jobbole.com/84351/](http://web.jobbole.com/84351/)
