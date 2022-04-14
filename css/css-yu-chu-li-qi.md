# CSS 预处理器

## 一、引言

CSS 本身不属于可编程语言，当前端项目逐渐庞大之后 CSS 的维护也愈加困难。CSS 预处理器所做的本质上是为 CSS 增加一些可编程的特性，通过**变量**、**嵌套**、**简单的程序逻辑**、**计算**、**函数**等特性，通过**工程化**的手段让 CSS 更易维护，提升开发效率。

目前主流的 CSS 预处理器主要有 Sass、Less、Stylus、PostCSS。

## 二、PostCSS

PostCSS 是目前最为流行的 CSS 预/后处理器。PostCSS 本体功能比较单一，它提供一种用 JavaScript 来处理 CSS 的方式。PostCSS 会把 CSS 解析成 AST（Abstract Syntax Tree 抽象语法树），之后由其他插件进行不同的处理。

### **1. 功能**

PostCSS 本体功能比较单一，大多数的 CSS 处理功能都由插件提供，下面是一些常用的插件：

* [Autoprefixer](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fpostcss%2Fautoprefixer) 为 CSS 中的属性添加浏览器特定的前缀。
* [postcss-preset-env](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcsstools%2Fpostcss-preset-env) 根据 `browserslist` 指定的目标浏览器将一些 CSS 的新特性转换为目标浏览器所支持的语法。
* [cssnano](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcssnano%2Fcssnano) 提供 CSS 压缩功能。
* [postcss-nested](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fpostcss%2Fpostcss-nested) 提供 CSS 嵌套功能。
* [postcss-px-to-viewport](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fevrone%2Fpostcss-px-to-viewport) 提供 px 转 vw 功能。
* [postcss-custom-properties](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fpostcss%2Fpostcss-custom-properties) 支持 CSS 的自定义属性。

### **2. 优点**

* 插件系统完善，扩展性强。
* 配合插件功能齐全。
* 生态优秀。

### **3. 缺点**

* 配置相对复杂。

## 三、Sass

Sass 在完全兼容 CSS 语法的前提下，给 CSS 提供了变量、嵌套、混合、操作符、自定义函数等可编程能力。

### **1. 功能**

Sass 常用的有几种功能：

* 变量：变量中可以存储颜色、字体或任何 CSS 值。
* 嵌套：可嵌套 CSS 选择器，提供清晰的层次结构。
* 混合：可以定义&重用代码块。
* 扩展/集成：可以在一个选择器内继承另一个选择器。
* 操作符：可以在 CSS 中使用操作符进行计算。
* 条件/循环语句：可以循环/条件生成 CSS。
* 自定义函数：可以自定义复杂操作的函数。

### **2. 优点**

* 使用广泛。
* 功能支持完善。
* 可编程能力强。

### **3. 缺点**

* CSS 的复杂度不可控。
* node-sass 国内安装不易（非 Sass 本身的缺点，dart-sass 可代替）。

## 四、Less

Less 和 Sass 类似，完全兼容 CSS 语法，并给 CSS 提供了变量、嵌套、混合、运算等可编程能力。Less 通过 JavaScript 实现，可在浏览器端直接使用。

### **1.功能**

Less 常用的有几种功能：

* 变量：变量中可以存储颜色、字体或任何 CSS 值。
* 嵌套：可嵌套 CSS 选择器，提供清晰的层次结构。
* 混合：可以定义&重用的代码块。
* 扩展/集成：可以在一个选择器内继承另一个选择器。
* 运算：可以在 CSS 中进行计算。
* 条件/循环语句：可以循环/条件生成 CSS。

### **2. 优点**

* 使用广泛。
* 可以在浏览器中运行，容易实现主题定制功能。

### **3. 缺点**

* 不支持自定义函数（可通过 mixins 实现简单逻辑）。
* 编程能力相对较弱。

## 五、Stylus

Stylus 基础功能和 Sass / Less 十分类似。Stylus 的特点是冒号、分号、逗号和括号都是可选项，所以可以写出非常简洁的 CSS，示例如下：

```stylus
body {
  background-color: #000
}

body::after {
  content: 'HZFEStudio'
  color: #fff
  font-size: 20px
}
```

## 六、其它

### 1. CSS Modules

CSS Modules 和前文介绍的预处理器不同，不是可编程化的 CSS，而仅是给 CSS 文件加入了作用域和模块依赖，主要是为了解决 CSS 全局污染的痛点以及为了解决全局污染而嵌套过深的问题。使用示例如下：

```css
/* style.css */
.hzfeTitle {
  color: #666;
  font-size: 20px;
}
```

```javascript
import style from "./style.css";

document.body.innerHTML = `<h1 class="${style.hzfeTitle}">HZFEStudio</h1>`;
```

### 2. CSS-in-JS

如名字所见，CSS-in-JS 是一种在 JavaScript 里写 CSS 的方式。利用 JS 的优势可实现非常灵活的可扩展的样式。CSS-in-JS 有很多实现，目前比较流行的是 [Styled-components](https://link.juejin.cn/?target=https%3A%2F%2Fwww.styled-components.com%2F)。

通过 Styled-components 写 CSS 的示例如下：

```javascript
import React from "react";
import styled from "styled-components";

function hzfe() {
  const Title = styled.h1`
    font-size: 1.5em;
    text-align: center;
    color: #666;
  `;
  return <Title>HZFEStudio</Title>;
}
```

## 七、参考

* [面试官让我谈谈 CSS 预处理器](https://juejin.cn/post/7012170928826089479)
* [CSS预处理器，你还是只会嵌套么](https://juejin.cn/post/7001860784586227720)
