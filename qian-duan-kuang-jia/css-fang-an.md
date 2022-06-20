# CSS 方案

## 一、引言 <a href="#qian-yan" id="qian-yan"></a>

### 1. 内容、样式、行为要分离吗？ <a href="#nei-rong-yang-shi-hang-wei-yao-fen-li-ma" id="nei-rong-yang-shi-hang-wei-yao-fen-li-ma"></a>

内容、样式和行为分离是前现代的前端开发口号，它并没有错误，但是已经过时了：本质上来说，这一口号是适用于**排版时代**的——在那个时期，网页开发被视为是一种排版工作。

在当今这个组件化开发 Web App 的时代，一个组件的内容、样式、行为如果不被放在一起，才是真正的疯狂。这就是为何各种前端程序员的 .js 文件中越来越充斥着 JSX/template 代码、CSS 代码。对于 CSS 而言，其很多功能也是诞生自排版时代的，例如 CSS 选择器、float、文档流等概念。这些功能在现代的 Web App 开发人员看来，便多少有点不合时宜了。

### 2. 传统做法及其问题 <a href="#chuan-tong-zuo-fa-ji-qi-wen-ti" id="chuan-tong-zuo-fa-ji-qi-wen-ti"></a>

传统上，编写 CSS 代码的操作过程是：**纵览文档结构（可选） -> 为元素起名字（可能使用** [**BEM**](https://www.zhihu.com/question/21935157/answer/20116700)**、**[**OOCSS**](https://github.com/stubbornella/oocss/wiki) **等命名规范） -> 用选择器选中 -> 编写 CSS（或者 Sass / Less 等）语句**。这一过程在现代前端项目中，有些不合时宜：

* 由于组件化开发，前端工程师大多数时候面对着的是某一个（作为组件渲染产物的） HTML 片段。因此**传统步骤的前三步，要么已经没有存在的基础，要么纯粹是添麻烦**
* CSS 总是全局的。**JS 代码早已可被封装为模块，CSS 代码却不行**。这导致在大型应用中：
  * CSS 规则很可能互相干扰（选择器权重问题、命名冲突问题），带来混乱
  * 由于不确定会发生什么，无人敢删 CSS 代码。于是项目里的 CSS 代码永远只增不减，成为一个烂摊子
  * CSS 代码不易分割以实现动态加载
  * 组件的 CSS 代码分发不易。编写要分发的 UI 组件时要非常小心，不能对组件用户的页面中的其它部分造成影响

为了克服这些问题，现代的、有技术实现的各类 CSS 工程方案出现了。

## 二、框架生态提供的方案 <a href="#kuang-jia-sheng-tai-ti-gong-de-fang-an" id="kuang-jia-sheng-tai-ti-gong-de-fang-an"></a>

### 1. Vue 官方的 scoped CSS 方案 <a href="#vue-guan-fang-de-scopedcss-fang-an" id="vue-guan-fang-de-scopedcss-fang-an"></a>

Vue 生态通过提供 [Webpack Vue-loader](https://vue-loader.vuejs.org/guide/scoped-css.html#mixing-local-and-global-styles)，向她的用户提供 scoped CSS 方案。其主要原理是，在打包时，对于被标注为`scoped`的 style，其中的所有选择器被额外加上随机字符串`[data-v-xxxxx]`，template 中的所有 HTML 元素和子组件的根 HTML 元素均被加上相应的`data-v-xxxxx` HTML 属性。也就是说**通过为每个选择器附着一个属性选择器来实现模块化。**（但不是彻底的模块化：子组件也会被影响）

从[实现上](https://mp.weixin.qq.com/s/MJScjoqGtKh9IuFpfMbbQg)，是利用了`postcss-selector-parser`这个工具解析 scoped style 中所有的 CSS Selectors，然后逐一加上随机字符串`[data-v-xxxxx]`；template 中元素属性的添加则由`@vue/component-compiler-utils`来完成。

### 2. Angular 官方的 `viewEncapsulation` <a href="#angular-guan-fang-de-viewencapsulation" id="angular-guan-fang-de-viewencapsulation"></a>

编写 Angular 组件时，可以配置[组件的`viewEncapsulation`选项](https://dzone.com/articles/what-is-viewencapsulation-in-angular)。根据配置值的不同，Angular 采用了以下不同的方案：

* Native 方案：Angular 将该组件渲染为浏览器的 shadow dom，样式也置于其中。shadow dom 自带 CSS 隔离的功能
* Emulated 方案（默认方案）：原理同 Vue 的 scoped CSS 方案

### 3. React 社区的各类 CSS-in-JS 方案 <a href="#react-she-qu-de-ge-lei-cssinjs-fang-an" id="react-she-qu-de-ge-lei-cssinjs-fang-an"></a>

CSS-in-JS （后文简称为 CIJ）在 2014 年由 Facebook 的员工 [Vjeux 在 NationJS 会议上](https://blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.html)提出。简单来说，**其形式就是就是将组件应用的 CSS statement 写在 JavaScript 文件里面。**

CIJ 不是某套特定技术方案，恰恰相反，CIJ 的思路和技术实现众多（因为 [React 官方并不关心这个问题，而是交给了社区](https://reactjs.org/docs/faq-styling.html#what-is-css-in-js)）。可以在 [CSS-in-JS Playground](https://www.cssinjsplayground.com/) 上快速尝试不同的 CIJ 实现。

然而，在接口 API 设计、功能或是使用体验上，不同的实现方案越来越接近，其中最受欢迎的两个解决方案是 [Emotion](https://github.com/emotion-js/emotion) 和 [styled-components](https://styled-components.com/docs/basics#motivation)。通过几年间的竞争和更新，它们渐渐具有了几乎相同的 API，只是在内部实现上有所不同。

#### 两种 API 风格 <a href="#liang-zhong-api-feng-ge" id="liang-zhong-api-feng-ge"></a>

各类 CIJ 方案提供的 API 通常是以下两种风格之一（或全部）：

* CSS props：将 CSS 代码以 prop 的形式施加在组件上
  * 优点：很直观。没有作用域问题或是选择器权重问题
  * 缺点：样式代码大量混杂在 JSX 中，可能降低代码可读性
* styled component：用户提供 CSS 代码，CIJ 库返回携带了 CSS 代码的组件供用户使用
  * 缺点：用户得额外和各类纯样式组件打交道
  * 优点：纯样式组件易于分发。适合有统一设计语言的团队

#### 实现细节 <a href="#shi-xian-xi-jie" id="shi-xian-xi-jie"></a>

从实现方法上区分，大体分为两种流派：

* 运行时方案
* 编译期方案

**运行时方案**

**Styled-components 实现赏析**

styled-components 在 API 风格上主要采用 styled component。

通过 styled-components，你可以使用 ES2015 的[标签模板字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template\_literals)语法（Tagged Templates）创建一个 styled component，当该组件的 JS 代码被解析执行的时候，styled-components 会动态生成一个 CSS 选择器，并把对应的 CSS 样式通过 style 标签的形式插入到 head 标签里面。动态生成的 CSS 选择器会有一小段哈希值来保证全局唯一性来避免样式发生冲突。

除了styled-components，采用唯一 CSS 选择器做法的实现还有：[jss](https://github.com/cssinjs/jss)，[emotion](https://github.com/emotion-js/emotion)，[glamorous](https://github.com/paypal/glamorous)等。

[**Radium**](https://github.com/FormidableLabs/radium) **实现赏析**

Radium 在 API 风格上采用的是 CSS props。

Radium 和 styled-components 的最大区别是它生成的是内联样式。由于标签内联样式在处理诸如`media query`以及`:hover`，`:focus`，`:active`等和浏览器状态相关的样式的时候非常不方便，所以 radium 为这些样式封装了一些标准的接口以及抽象。

**编译期方案**

[**linaria**](https://github.com/callstack/linaria) **实现赏析**

暂略。

[**@compiled/css-in-js**](https://compiledcssinjs.com/) **实现赏析**

暂略。

#### 各类 CJS 实现的其它功能 <a href="#ge-lei-cjs-shi-xian-de-qi-ta-gong-neng" id="ge-lei-cjs-shi-xian-de-qi-ta-gong-neng"></a>

除了一些最基本的诸如CSS局部作用域的功能，下面这些功能有的实现会包含而有的却不支持：

* 自动生成浏览器引擎前缀 - built-in vendor prefix
* 支持抽取独立的CSS样式表 - extract css file
* 自带支持动画 - built-in support for animations
* 伪类 - pseudo classes
* 媒体查询 - media query
* 其他

可以看一下这个整理了不同实现的[对比图](https://github.com/michelebertoli/css-in-js#features)来比较它们的功能差异。

#### CJS 的优缺点 <a href="#cjs-de-you-que-dian" id="cjs-de-you-que-dian"></a>

比起前文介绍的各种方案，CJS 是相当激进的一种：它消灭了[传统步骤](https://cyl.moe/post/modern-css-project#%E4%BC%A0%E7%BB%9F%E5%81%9A%E6%B3%95%E5%8F%8A%E5%85%B6%E9%97%AE%E9%A2%98)的前三步。这种激进性带来一些好处和坏处。

**好处**：

* CSS statement 完全是可编程的。其它方案通常需要通过对元素的 className 进行编程来迂回。（ Vue 3.2 起，也支持[可编程的 CSS statement](https://v3.vuejs.org/api/sfc-style.html#state-driven-dynamic-css)）

**坏处**：

* all in js 破坏了前端开发的传统。具有一定的学习成本
* 采用运行时的方案需要在应用中额外加载 CIJ runtime，这带来额外的网络开销；在运行时操作 CSSOM，则会削减了应用的运行时性能
* “唯一 CSS 选择器”的 CIJ 实现，其产物的可读性较差

#### 从 React 社区到其它社区 <a href="#cong-react-she-qu-dao-qi-ta-she-qu" id="cong-react-she-qu-dao-qi-ta-she-qu"></a>

上文提到到很多 CIJ 方案也被移植到 Vue 社区等。例如 [vue-styled-components](https://github.com/styled-components/vue-styled-components) 等。此处不再赘述。

## 三、框架无关的通用方案 <a href="#kuang-jia-wu-guan-de-tong-yong-fang-an" id="kuang-jia-wu-guan-de-tong-yong-fang-an"></a>

### 1. CSS Module <a href="#css-module" id="css-module"></a>

[CSS Module](https://github.com/css-modules/css-modules) 是社区对 CSS 语法和语义对一种扩展，相应的实现有[Webpack CSS-loader](https://github.com/webpack-contrib/css-loader#modules)（需开启 module 模式）、[Postcss-module](https://github.com/css-modules/postcss-modules) ，以及适用于 TS 的 [vanilla-extract](https://github.com/seek-oss/vanilla-extract)等。

在语义上，CSS Module 方案将每个 CSS 文件视为一个模块，默认其中的每一个 class 选择器都是模块级的（并且模块间可以互相导入导出）。在实现上，则是在编译时将每个 CSS module 内的所有 class name 替换成各不重复的随机字符串。对通过`import`语句引用了 CSS module 内的 class name 的 JS module，也会将其中的相关引用替换为这些随机字符串。

对大部分场景而言，该方案没有显著的缺点。 [Vue-loader 也支持直接在 SFC 内写 CSS Module](https://vue-loader.vuejs.org/guide/css-modules.html)。

### 2. 原子类 <a href="#yuan-zi-lei" id="yuan-zi-lei"></a>

原子类思想本身就是一种历史悠久（可追溯到本世纪一零年代以前）的 CSS 方法论，但在现代前端的组件化开发模式流行起来之前，[原子类思想一直被视为反工程的](https://www.zhihu.com/question/22110291)。而伴随着前端 JS 组件化开发的流行，原子类思想的应用条件开始成熟，因此被开发者重新审视。

原子类思想最为激进，是因为[它注意到传统的“在 CSS 层面进行抽象”的必要性是十分可疑的](https://www.zhihu.com/question/337939566/answer/1105939733)。这和 CSS-in-JS 社区的思想有些接近，但做得更进一步：用户连 CSS 都不用写，直接在了 HTML 上堆叠类名即可——不但跳过了[传统步骤](https://cyl.moe/post/modern-css-project#%E4%BC%A0%E7%BB%9F%E5%81%9A%E6%B3%95%E5%8F%8A%E5%85%B6%E9%97%AE%E9%A2%98)的前三步，连第四步都简化了。

现代的原子类解决方案包括：

* [Tailwind.css](https://tailwindcss.com/) ：原子类 CSS 框架的首个成功的主流范例
* [windicss](https://windicss.org/guide/)（[中文文档](https://cn.windicss.org/)同步）：tailwind 的上位替代。在兼容前者的基础上，大大减少了配置项和依赖，增强了易用性
* [unocss](https://github.com/antfu/unocss)：一个元原子类框架，或可称为原子类引擎。该工具可用于生成原子类框架（包括 tailwind 和 windi），适用于需要高度定制原子类的场景

[《重新构想原子化CSS》](https://zhuanlan.zhihu.com/p/425814828)一文中详细对比了这三者，并着重推广了 unocss。

## 四、参考

* [现代 CSS 工程方案](https://cyl.moe/post/modern-css-project)
* [CSS 模块化演进](https://codechina.gitcode.host/programmer/fe/20-CSS-modularization.html)
