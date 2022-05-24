# DOM

## 一、引言

DOM(文档对象模型)是针对于xml但是扩展用于HTML的应用程序编程接口，定义了访问和操作 HTML 的文档的标准。

W3C文档对象模型是中立于平台和语言之间的接口，它允许程序和脚本动态的访问和更新文档的内容、结构、样式。总之 HTML 是关于如何获取、修改、添加和删除 HTML 元素的标准。

## 二、DOM 分层节点

DOM 的分层节点一般被称作是 DOM 树，树中的所有节点都可以通过脚本语言例如 JS 进行访问，所有HTML 元素节点都可以被创建、添加或者删除。

在 DOM 分层节点中，页面就是用分层节点图表示的。

* 整个文档是一个文档节点，就想是树的根一样。
* 每个 HTML 元素都是元素节点。
* HTML 元素内的文本就是文本节点。
* 每个 HTML 属性时属性节点。

当咱们访问一个 web 页面时，浏览器会解析每个 HTML 元素，创建了 HTML 文档的虚拟结构，并将其保存在内存中。接着，HTML 页面被转换成树状结构，每个 HTML 元素成为一个叶子节点，连接到父分支。 考虑以下 Html 结构：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>A super simple title!</title>
</head>
<body>
<h1>A super simple web page!</h1>
</body>
</html>
```

在这个结构的顶部有一个`document`，也称为根元素，它包含另一个元素：`html`。 `html`元素包含一个`head`，而 `head` 又有一个`title`。 然后`body` 包含一个`h1`。 每个 HTML 元素都由特定类型（也称为接口）表示，并且可能包含文本或其他嵌套元素：

```html
document (HTMLDocument)
  |
  | --> html (HTMLHtmlElement)
          |  
          | --> head (HtmlHeadElement)
          |       |
          |       | --> title (HtmlTitleElement)
          |                | --> text: "A super simple title!"
          |
          | --> body (HtmlBodyElement)
          |       |
          |       | --> h1 (HTMLHeadingElement)
          |              | --> text: "A super simple web page!"
```

每个`HTML`元素都来自`Element`，但其中很大一部分都是专用的。 咱们可以检查原型以查找元素所属的“种类”。 例如，`h1`元素是`HTMLHeadingElement`：

```
document.querySelector('h1').__proto__

// Output: HTMLHeadingElement
复制代码
```

而`HTMLHeadingElement`则是`HTMLElement`的后代:

```javascript
document.querySelector('h1').__proto__.__proto__

// Output: HTMLElement
```

## 三、DOM 常用方法

### **1. 获取节点**

```javascript
// 通过id号来获取元素，返回一个元素对象
document.getElementById(idName) 
      
// 通过name属性获取id号，返回元素对象数组 
document.getElementsByName(name)  
   
// 通过class来获取元素，返回元素对象数组
document.getElementsByClassName(className)   

// 通过标签名获取元素，返回元素对象数组
document.getElementsByTagName(tagName)       
```

### **2. 获取/设置元素的属性值：**

```javascript
// 括号传入属性名，返回对应属性的属性值
element.getAttribute(attributeName)

// 传入属性名及设置的值
element.setAttribute(attributeName,attributeValue)
```

### **3. 创建节点**

```javascript
// 创建一个html元素，这里以创建h3元素为例
document.createElement("h3")

// 创建一个文本节点；
document.createTextNode(String);

// 创建一个属性节点，这里以创建class属性为例
document.createAttribute("class");
```

### **4. 增添节点**

```javascript
// 往element内部最后面添加一个节点，参数是节点类型
element.appendChild(Node);

// 在element内部的中在existingNode前面插入newNode
elelment.insertBefore(newNode,existingNode); 
```

### **5. 删除节点**

```javascript
//删除当前节点下指定的子节点，删除成功返回该被删除的节点，否则返回null
element.removeChild(Node)
```

## 四、DOM 常用属性

### **1. 获取当前元素的父节点**

```javascript
// 返回当前元素的父节点对象
element.parentNode
```

### **2. 获取当前元素的子节点**

```javascript
// 返回当前元素所有子元素节点对象，只返回HTML节点
element.chlidren

// 返回当前元素多有子节点，包括文本，HTML，属性节点。（回车也会当做一个节点）
element.chilidNodes

// 返回当前元素的第一个子节点对象
element.firstChild

// 返回当前元素的最后一个子节点对象
element.lastChild
```

### **3. 获取当前元素的同级元素**

```javascript
// 返回当前元素的下一个同级元素 没有就返回null
element.nextSibling

// 返回当前元素上一个同级元素 没有就返回 null
element.previousSibling
```

### **4. 获取当前元素的文本**

```
// 返回元素的所有文本，包括html代码
element.innerHTML

// 返回当前元素的自身及子代所有文本值，只是文本内容，不包括html代码
element.innerText
```

### **5. 获取当前节点的节点类型**

```
// 返回节点的类型,数字形式（1-12）
// 常见几个1：元素节点，2：属性节点，3：文本节点。
node.nodeType   
```

### **6. 设置样式**

```
// 设置元素的样式时使用style
element.style.color=“#eea”;
```

## 五、DOM 操作

DOM 中的每个 HTML 元素也是一个节点，可以像这样查找节点：

```
document.querySelector('h1').nodeType;
```

上面会返回`1`，它是`Element`类型的节点的标识符，还可以检查节点名称：

```
document.querySelector('h1').nodeName;

"H1"
```

上面的示例返回大写的节点名。但是需要理解的最重要的概念是，咱们主要使用 DOM 中的两种类型的节点：

* 元素节点
* 文本节点

创建元素节点，可以通过 `createElement`方法：

```
var heading = document.createElement('h1');
```

创建文本节点，可能通过 `createTextNode` 方法：

```
var text = document.createTextNode('Hello world');
```

接着将两个节点组合在一起，然后添加到 `body` 上：

```
var heading = document.createElement('h1');
var text = document.createTextNoe('Hello world');
heading.appendChild(text);
document.body.appendChild(heading)
```

在学习 Dom 操作时候，这些方法需要牢记并熟练使用的。

目前像咱们用这种方式创建和操作元素，是属于命令式 DOM 操作。

## 六、考点

### 1. document 和 window 的区别

简单来说，`document`是`window`的一个对象属性。`window` 对象表示浏览器中打开的窗口。如果文档包含框架（`frame` 或 `iframe` 标签），浏览器会为 HTML 文档创建一个 `window` 对象，并为每个框架创建一个额外的 `window` 对象。所有的全局函数和对象都属于 `window` 对象的属性和方法。

**区别:**

1. `window` 指窗体。`document`指页面。`document`是`window`的一个子对象。
2. 用户不能改变 `document.location`(因为这是当前显示文档的位置)。但是,可以改变`window.location` (用其它文档取代当前文档)`window.location`本身也是一个对象,而`document.location`不是对象。

**document** 接口有许多实用方法，比如`querySelector()`，它是用于查找给定页面内**HTML**元素的方法：

```javascript
document.querySelector('h1');
```

`window`表示当前的浏览器，下面代码与上面等价：

```javascript
window.document.querySelector('h1');
```

当然，更常见的是用第一种方式。

**window 是一个全局对象**，可以从浏览器中运行的任何JS代码直接访问。 `window`暴露了很多属性和方法，如：

```javascript
window.alert('Hello world'); // Shows an alert
window.setTimeout(callback, 3000); // Delay execution
window.fetch(someUrl); // make XHR requests
window.open(); // Opens a new tab
window.location; // Browser location
window.history; // Browser history
window.navigator; // The actual user agent
window.document; // The current page
```

因为这些属性和方法也是全局的，所以也可以这样访问它们

```javascript
alert('Hello world'); // Shows an alert
setTimeout(callback, 3000); // Delay execution
fetch(someUrl); // make XHR requests
open(); // Opens a new tab
location; // Browser location
history; // Browser history
navigator; // The actual user agent
document;// The current page
```

其中有些咱们都已经很熟悉了，如`setTimeout()` 的方法。 例如，当咱们想要得知当前用户的浏览器语言时，`window.navigator`就非常有用：

```javascript
if (window.navigator) {
  var lang = window.navigator.language;
  if (lang === "en-US") {
    // show something
  }

  if (lang === "it-IT") {
    // show something else
  }
}
```

## 七、参考

* [什么是 DOM 及 DOM 操作](https://juejin.cn/post/6844904023003234311)
* [网道 JavaScript 教程 - DOM](https://wangdoc.com/javascript/dom/index.html)
