# 预加载和异步加载



预加载

* 预取 CSS 文件，预渲染整个页面或提前解析域
* 浏览器有一个简单的内置方式来完成所有这些事情。有六个 `<link rel>` 标记指示浏览器预加载内容：

```html
<link rel="prefetch" href="/index.css" as="style" />
<link rel="preload" href="/index.css" as="style" />
<link rel="preconnect" href="https://example.com" />
<link rel="dns-prefetch" href="https://example.com" />
<link rel="prerender" href="https://example.com/about.html" />
<link rel="modulepreload" href="/index.js" />
复制代码
```

**preload**

* 使用 `preload` 作为 `rel` 属性的属性值。还需要通过 `href` 和 `as` 属性指定需要被预加载资源的资源路径及其类型。

```html
<link rel="preload" href="index.css" as="style">
复制代码
```

* 使用 `as` 来指定将要预加载的内容的类型，将使得浏览器能够：
  * 更精确地优化资源加载优先级。
  * 匹配未来的加载需求，在适当的情况下，重复利用同一资源。
  * 为资源应用正确的[内容安全策略](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FHTTP%2FCSP)。
  * 为资源设置正确的 [`Accept`](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FHTTP%2FHeaders%2FAccept) 请求头。
  * [MDN 完整列表](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FHTML%2FPreloading\_content%23what\_types\_of\_content\_can\_be\_preloaded)

**prefetch**

* `<link rel="prefetch">` 要求浏览器在后台下载并缓存资源（例如 JS 或 CSS）。下载的优先级较低，因此不会干扰更重要的资源。当您知道在下一个页面上需要该资源，并且想要提前对其进行缓存时，这将很有帮助。
* 下载资源后，浏览器不执行任何操作。不执行 JS，不应用 CSS。它只是被缓存了，因此当其他需求时，它立即可用。
* 通过 `link` 标签的 `rel` 属性指定为 `"prefetch"`，在 `href` 属性里指定要加载资源的地址

```html
<!-- 预加载整个页面 -->
<link rel="prefetch" href="https://juejin.cn/user/96412754251390" />

<!-- 预加载一个图片 -->
<link rel="prefetch" href="https://images.pexels.com/photos/918281/pexels-photo-918281.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500" />
复制代码
```

**preconnent**

* `<link rel="preconnect">` 要求浏览器提前执行到域的连接。

```html
<link rel="dns-prefetch" href="https://juejin.cn/user/96412754251390"> 
复制代码
```

**dns-prefetch**

* `DNS-prefetch`（DNS 预获取）是尝试在请求资源之前解析域名。这可能是后面要加载的文件，也可能是用户尝试打开的链接目标。

```html
<link rel="dns-prefetch" href="https://juejin.cn/user/96412754251390"> 
复制代码
```

**链接预加载的一些注意事项**

* 预加载可以跨域进行，当然，请求时 cookie 等信息也会被发送。
* 预加载可能破坏网站统计数据，而用户并没有实际访问。
* 浏览器兼容性不是很好

**prerender**

* `<link rel="prerender">` 要求浏览器加载 URL 并将其呈现在不可见的标签中。当用户单击指向该 URL 的链接时，应立即呈现该页面。当您确实确定用户接下来将访问特定页面并且想要更快地呈现它时，这将很有帮助。

```html
<link rel="prerender" href="https://juejin.cn/user/96412754251390" />
复制代码
```

* 当您确定大多数用户将导航到特定页面时，您希望加快速度，那么你可以使用它

**modulepreload**

* `<link rel="modulepreload">` 告诉浏览器尽快下载，缓存和编译 JS 模块脚本。
* 使用它可以更快地加载您的 ES 模块应用程序。此标记仅适用于预加载 ES 模块——即您通过 `import ...` 或导入的模块 `<script type="module">`。

```html
<link rel="modulepreload" href="/static/Header.js" />
复制代码
```

* [Preload, prefetch and other `<link>` tags](https://link.juejin.cn/?target=https%3A%2F%2F3perf.com%2Fblog%2Flink-rels%2F%23preload)

