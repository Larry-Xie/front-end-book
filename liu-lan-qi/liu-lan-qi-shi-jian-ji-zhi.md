# 浏览器事件机制

## 一、引言

DOM，即文档对象模型（Document Object Model），W3C 制定的标准接口规范，是一种处理 HTML 和 XML 文件的标准 API。它把文档作为一个树形结构，树的每个结点表示了一个 HTML 标签或标签内的文本项。

DOM 树结构精确地描述了 HTML 文档中标签间的相互关联性。将 HTML 或 XML 文档转化为 DOM 树的过程称为解析（`parse`）。

HTML 文档被解析后，转化为 DOM 树，因此对 HTML 文档的处理可以通过对 DOM 树的操作实现。DOM 模型不仅描述了文档的结构，还定义了结点对象的行为，利用对象的方法和属性，可以方便地访问、修改、添加和删除DOM 树的结点和内容。

可以试着在浏览器把一个 DOM 节点输出来看一下：

```javascript
let body = document.body;
for (var key in body) {
    console.log(`属性:${key},值：${body[key]}`)
}
```

这个节点有很多属性，其中 `onclick` 的默认值是 `null`，当把 `onclick` 赋予一个函数，就可以作为一个事件函数存在。我们可以通过给对应的属性绑定事件来修改、添加和删除DOM 树的结点和内容。

![DOM 元素的属性](<../.gitbook/assets/image (10).png>)

## 二、事件触发三阶段

事件触发有三个阶段：

* **事件捕获阶段**：`window` 往事件触发处传播，遇到注册的捕获事件会触发
* **处于目标阶段**：传播到事件触发处时触发注册的事件
* **事件冒泡阶段**：从事件触发处往 `window` 传播，遇到注册的冒泡事件会触发

首先发生的是事件捕获为截获事件提供机会，然后的是实际的目标接收事件，最后一个阶段是事件冒泡阶段，可以在这个阶段对事件做出响应。 如图：

![事件触发三阶段](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5ad8bd99344d4a1e83eb7cdc56c52804\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

假设在 DOM 结构里面有 `text` 的这样一个标签，给这个标签绑定了一个点击事件，那么在点击这个标签的时候是怎么执行事件的呢？

* 首先是事件捕获阶段，会通过 `window`、`document`、`body`、`div`、`text` 这样的顺序一直往下捕获事件。
* 然后是处于目标阶段，到 `text` 标签处触发绑定的点击事件。
* 最后是事件冒泡阶段，事件是在冒泡阶段做出响应的。冒泡阶段通过 `text` 、`div`、`body`、`document`、`window` 这样的顺序往上冒泡，假如在 `div` 或者 `body` 上面也绑定了对应的 `onclick` 事件，那么会按顺序触发响应。

> 事件触发一般来说会按照上面的顺序进行，但如果给一个元素同时注册冒泡和捕获事件，这个注册顺序的不同在不同的浏览器会有不同的表现，要注意区分。所以建议**注册按照先捕获再冒泡的顺序来**。

## 三、事件注册

我们一般使用 [**EventTarget.addEventListener()**](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener) 来注册事件，当该对象触发指定的事件时，指定的回调函数就会被执行。 事件目标可以是一个文档上的元素 `Element`,`Document`和`Window`或者任何其他支持事件的对象 (比如 `XMLHttpRequest`)`。`

`addEventListener()`的工作原理是将实现`EventListener`的函数或对象添加到调用它的`EventTarget`上的指定事件类型的事件侦听器列表中。语法如下：

```javascript
target.addEventListener(type, listener, options);
target.addEventListener(type, listener, useCapture);
target.addEventListener(type, listener, useCapture, wantsUntrusted );  // Gecko/Mozilla only
```

一般我们使用三个参数：

* **type：**处理的事件名称，如点击事件 `click`；
* **listener：**事件处理程序，即要绑定的函数体；
* **useCapture：**指定是在事件冒泡还是事件捕获阶段处理参数；
  * `true` 则作为捕获事件处理；
  * `false` 则作为冒泡事件处理（默认）。

如果第三个参数用 options 时，可以使用以下几个属性：

* `capture`：布尔值，和第三个参数作为布尔值时作用一样
* `once`：布尔值，值为 `true` 表示该回调只会调用一次，调用后会移除监听
* `passive`：布尔值，表示永远不会调用 `preventDefault`

一般来说，如果我们只希望事件只触发在目标上，这时候可以使用 `stopPropagation` 来阻止事件的进一步传播。

* `stopPropagation` 是用来阻止事件冒泡的，其实该函数也可以阻止捕获事件。
* `stopImmediatePropagation` 也能实现阻止事件，但是**还能阻止该事件目标执行别的注册事件**。

## 四、事件代理

**事件代理是指利用事件冒泡，只指定一个事件处理程序来管理某一类型的所有事件**。

如果一个节点中的子节点是 **动态生成** 的，那么子节点需要注册事件的话可以注册在父节点上。

```html
<ul id="proxy">
  <li>主页</li>
  <li>文章</li>
  <li>公告</li>
  <li>简介</li>
</ul>
```

事件代理：

```javascript
let proxy = document.querySelector('#proxy')
proxy.addEventListener('click', (event) => {
  let target = event.target; // 当前点击的元素
  if (target.nodeName.toLowerCase() == 'li') {
    console.log('click:' + target.innerHTML);
  }
})
```

这种方式相较于直接给目标注册事件来说，有以下优点：

* 可以减少内存占用，减少事件注册
* 不需要给子节点注销事件

## 参考

* [https://juejin.cn/post/6973680109182009374](https://juejin.cn/post/6973680109182009374)
