# 预加载

## 一、引言

HTML 本身提供了一些预加载的机制，方便根据不同场景设置不同的预加载方式。

有六个 `<link rel>` 标记指示浏览器预加载内容，其中最常见的就是 preload 和 prefetch。

```html
<link rel="prefetch" href="/index.css" as="style" />
<link rel="preload" href="/index.css" as="style" />
<link rel="preconnect" href="https://example.com" />
<link rel="dns-prefetch" href="https://example.com" />
<link rel="prerender" href="https://example.com/about.html" />
<link rel="modulepreload" href="/index.js" />
```

## **二、preload**

`preload` (预加载) 提升了资源加载的优先级，使得它提前开始加载，在需要用的时候能够更快的使用上。另外 `onload` 事件必须等页面所有资源都加载完成才触发，而当给某个资源加上 `preload` 后，该资源将不会阻塞 `onload`。

```html
<link rel="preload" href="index.css" as="style">
```

例如当某个页面加载了两个个脚本 `jquery.min.js` 和 `main.js`：

```html
<script src="https://cdn.bootcss.com/jquery/2.1.4/jquery.min.js"></script>
<script src="./main.js"></script>
```

此时该页面的资源加载 `Waterfall` 长这样：

![默认加载流](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ac1e90790924628b16109323aff5be4\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

当在 `<head>` 里通过 `<link>` 标签给 `main.js` 配置 `preload` 预加载后：

```html
<link rel="preload" as="script" href="./main.js">
```

此时的 `main.js` 加载顺序出现在了 `jquery.min.js` 的前面，这就是 `preload` 提升资源加载优先级的效果。

![preload main.js](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc980cd276aa49909f46a11270a965db\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

通过 `<link rel="preload">` 只是预加载了资源，但是资源加载完成后并不会执行，所以需要在想要执行的地方通过 `<script>` 来引入它：

```html
<script src="./main.js"></script>
```

如果通过 `preload` 加载了资源，但是又没有使用它，则浏览器会报一个警告：

![未使用的 preload 资源浏览器警告](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dff590e644504f41a8f9e8d373c7f008\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

`preload` 除了能够预加载脚本之外，还可以通过 `as` 指定别的资源类型，比如：

* `style` 样式表；
* `font`：字体文件；
* `image`：图片文件；
* `audio`：音频文件；
* `video`：视频文件；
* `document`：文档。

## **三、prefetch**

prefetch (预获取）是一种浏览器机制，其利用浏览器**空闲时间**来下载或预取用户在不久的将来可能访问的文档。网页向浏览器提供一组预取提示，并在浏览器完成当前页面的加载后开始静默地拉取指定的文档并将其存储在缓存中。当用户访问其中一个预取文档时，便可以快速的从**浏览器缓存中**得到。

![prefetch 示例](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2ec60352f9274499b757a373d258800d\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

比如在首页配置如下代码：

```html
<link rel="prefetch" as="script" href="https://cdn.bootcss.com/jquery/2.1.4/jquery.min.js">
```

我们会在页面中看到该脚本的下载优先级已经被降低为 `Lowest`：

![prefetch 时机](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/524a2a1d11a24ffab1e43b234c65aa14\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

当资源被下载完成后，会被存到浏览器缓存中，当从首页跳转到页面 A 的时候，假如页面 A 中引入了该脚本，那么浏览器会直接从 `prefetch cache` 中读取该资源，从而实现资源加载优化。

![从缓存中提取 prefetch 的资源](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84cf282615dc4bdfa52d6f895b38b409\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## **四、preconnent**

当我们的站点需要对别的域下的资源进行请求的时候，就需要和那个域建立连接，然后才能开始下载资源，如果我都已经知道了是和哪个域进行通信，那不就可以先建立连接，然后等需要进行资源请求的时候就可以直接进行下载了。

假设当前站点是 `https://a.com`，这个站点的主页需要请求 `https://b.com/b.js` 这个资源。对比正常请求和配置了 `preconnect` 时候的请求，它们在请求时间轴上看到的表现是不一样的：

![preconnect 使用前后对比](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1f8743ba6b6411998d44c8ff0f82728\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

通过如下配置可以提前建立和 `https://b.com` 这个域的连接：

```html
<link rel="preconnect" href="https://b.com">
```

通过 `preconnect` 提早建立和第三方源的连接，可以将资源的加载时间缩短 100ms \~ 500ms，这个时间虽然看起来微不足道，但是它是实实在在的优化了页面的性能，提升了用户的体验。

> 通过 preconnect 和别的域建立连接后，应该尽快的使用它，因为浏览器会关闭所有在 10 秒内未使用的连接。不必要的预连接会延迟其他重要资源，因此要限制 preconnect 连接域的数量。

## **五、dns-prefetch**

`DNS-prefetch`（DNS 预获取）是尝试在请求资源之前解析域名。这可能是后面要加载的文件，也可能是用户尝试打开的链接目标。

通常我们记住一个网站都是通过它的域名，但是对于服务器来说，它是通过 IP 来记住它们的。浏览器使用 `DNS` 来将站点转成 IP 地址，这个是建立连接的第一步，而这一步骤通常需要花费的时间大概是 20ms \~ 120ms。因此，可以通过 `dns-prefetch` 来节省这一步骤的时间。

既然能通过 `preconnect` 来减少整个建立连接的时间，那为什么还需要 `dns-prefetch` 来减少建立连接中第一步 DNS 查找解析的时间呢？

假如页面引入了许多第三方域下的资源，而如果它们都通过 `preconnect` 来预建立连接，其实这样的优化效果反而不好，甚至可能变差，所以这个时候就有另外一个方案，那就是对于最关键的连接使用 `preconnect`，而其他的则可以用 `dns-prefetch`。

可以按照如下方式配置 `dns-prefetch`：

```html
<link rel="dns-prefetch" href="https://cdn.bootcss.com">
```

另外由于 `preconnect` 的浏览器兼容稍微比 `dns-prefetch` 低，因此 `dns-prefetch` 可以作为不支持 `preconnect` 的浏览器的后备选择，同时配置它们两即可：

```html
<link rel="preconnect" href="https://cdn.bootcss.com">
<link rel="dns-prefetch" href="https://cdn.bootcss.com">
```

## **六、prerender**

`<link rel="prerender">` 要求浏览器加载 URL 并将其呈现在不可见的标签中。当用户单击指向该 URL 的链接时，应立即呈现该页面。当您确实确定用户接下来将访问特定页面并且想要更快地呈现它时，这将很有帮助。

```html
<link rel="prerender" href="https://juejin.cn/user/96412754251390" />
```

当您确定大多数用户将导航到特定页面时，您希望加快速度，那么你可以使用它。

## **七、modulepreload**

`<link rel="modulepreload">` 告诉浏览器尽快下载，缓存和编译 JS 模块脚本。

使用它可以更快地加载您的 ES 模块应用程序。此标记仅适用于预加载 ES 模块——即您通过 `import ...` 或导入的模块 `<script type="module">`。

```html
<link rel="modulepreload" href="/static/Header.js" />
```

## 八、考点

### 1. preload 应用实例

`preload` 主要用于提升当前页面某些阻塞资源的下载优先级，使得页面能够尽快渲染显示出来。

#### **案例一：预加载定义在 CSS 中资源的下载，比如自定义字体**

当页面中使用了自定义字体的时候，就必须在 `CSS` 中引入该字体，而由于字体必须要等到浏览器下载完且解析该 `CSS` 文件的时候才开始下载，所以对应页面上该字体处可能会出现闪动的现象，为了避免这种现象的出现，就可以使用 `preload` 来提前加载字体，`type` 可以用来指定具体的字体类型，**加载字体必须指定 `crossorigin` 属性，否则会导致字体被加载两次**。

```html
<link rel="preload" as="font" crossorigin type="font/woff2" href="myfont.woff2">
```

以上这种写法和指定 `crossorigin="anonymous"` 是等同的效果。

#### **案例二：预加载 CSS 文件**

在首屏加载优化中一直存在一种技术，叫做抽取关键 `CSS`，意思就是把页面中在视口中出现的样式抽出一个独立的 `CSS` 文件出来 `critical.css`，然后剩余的样式在放到另外一个文件上 `non-critical.css`：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b70b68e903594d529e9678da7917a21f\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

由于 `CSS` 会阻塞页面的渲染，当同时去加载这 2 部分样式的时候，只要 `non-critical.css` 还没加载完成，那么页面就显示不了，而实际上只需要显示出视口下的界面即可，所以期待的结果是：当加载完成 `critical.css` 的时候马上显示出视口下的界面，不让 `non-critical.css` 阻塞渲染，则需要给 `non-critical.css` 加上预加载：

```html
<link rel="preload" as="style" href="https://bubuzou.com/non-critical.css">
<link rel="stylesheet" href="https://bubuzou.com/critical.css">
<link rel="stylesheet" href="https://bubuzou.com/non-critical.css">
```

#### **案例三：创建动态的预加载资源**

当需要预先加载的时候调用 `downloadScript`，而希望执行的时候则调用 `runScript` 函数。

```javascript
function downloadScript(src) {
    var el = document.createElement("link")
    el.as = "script"
    el.rel = "preload"
    el.href = src
    document.body.appendChild(el)
}
    
function runScript(src) {
    var el = document.createElement("script")
    el.src = src
}
```

#### **案例四：结合媒体查询预加载响应式图片**

`preload` 甚至还可以结合媒体查询加载对应尺寸下的资源，对于以下代码当可视区域尺寸小于 `600px` 的时候会提前加载这张图片。

```html
<link rel="preload" as="image" href="someimage.jpg" media="(max-width: 600px)">
```

#### **案例五：结合 `Webpack` 预加载 `JS` 模块**

`Webpack` 从 `4.6.0` 版本开始支持在魔术注释中配置预加载模块：

```js
import(_/* webpackPreload: true */_ "CriticalChunk")
```

如果是版本比较老的，则可以使用 `preload-webpack-plugin` 进行处理。

### 2. preconnect 应用实例

#### **场景一**

当知道资源是来源于哪个源下，但是对于加载哪个资源不是很明确的时候，它们要么是动态的，要么是根据不同环境携带不同参数，所以它们很适合用 `preconnect` 进行加载。

#### **场景二**

如果页面上有流媒体，但是没那么快播放，又希望当按下播放按钮的时候可以越快开始越好，此时就可以使用 `preconnect` 预建立连接，节省一段时间。

如果用 `preconnect` 预建立连接的资源是一个字体文件，那么也是需要加上 `crossorigin` 属性。

### 3. preload 和 prefetch 比较

* preload 和 prefetch 的本质都是预加载，即先加载、后执行，加载与执行解耦；
* preload 和 prefetch 不会阻塞页面的onload；
* preload 用来声明当前页面的关键资源，强制浏览器尽快加载；
* prefetch 用来声明将来可能用到的资源，在浏览器空闲时进行加载。

### 4. preload 和 prefetch 使用准则

* 目前浏览器基本上都具备预测解析能力，可以提前解析入口 html 中外链的资源，因此**入口脚本文件、样式文件等不需要特意进行 preload；**
* 类似字体文件这种隐藏在脚本、样式中的首屏关键资源，建议使用 preload 尽早进行资源加载，避免页面渲染延迟；
* prefetch 声明的是将来可能访问的资源，因此适合对异步加载的模块、可能跳转到的其他路由页面进行资源缓存；对于一些将来大概率会访问的资源，如背景图、常见的加载失败 icon 等，也较为适用；
* 不要滥用 preload 和 prefetch，需要在合适的场景中使用；
* preload 的字体资源必须设置 crossorigin 属性，否则会导致重复加载；
* 没有合法 https 证书的站点无法使用 prefetch，预提取的资源不会被缓存；

### 5. 浏览器加载不同资源的优先级规则

我们先来看一张图：

![资源优先级](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/2/11/16182c9d3ff9f3c2\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

> 这张表详见：Chrome Resource Priorities and Scheduling

这张图表示的是，在 Chrome 46 以后的版本中，不同的资源在浏览器渲染的不同阶段进行加载的优先级。 在这里，我们只需要关注 DevTools Priority 体现的优先级，一共分成五个级别，具体解析参见[这里](https://juejin.cn/post/6844903562070196237#heading-9)：

* Highest 最高
* Hight 高
* Medium 中等
* Low 低
* Lowest 最低

## 九、参考

* [https://juejin.cn/post/6893681741240909832](https://juejin.cn/post/6893681741240909832)
* [https://juejin.cn/post/6915204591730556935](https://juejin.cn/post/6915204591730556935)
* [https://juejin.cn/post/6844903562070196237](https://juejin.cn/post/6844903562070196237)

