# 性能优化加载篇

## 一、引言

对于网络层面，亦即加载层面的性能优化，又可以分为以下三个阶段来讨论。

* 构建阶段
* 部署阶段
* 请求阶段

## 二、构建阶段

该阶段处于代码构建和发布之前。主要围绕 webpack 做相关处理，同时也是接入最普遍的性能优化策略。其他构建工具的处理也是大同小异，可能只是配置上不一致。

### 1. 分割代码

**分割各个模块代码，提取相同部分代码**，好处是`减少重复代码的出现频率`。`webpack v4`使用`splitChunks`替代`CommonsChunksPlugin`实现代码分割。

`splitChunks`配置较多，详情可参考[官网](https://link.juejin.cn/?target=https%3A%2F%2Fwebpack.docschina.org%2Fconfiguration%2Foptimization%2F%23optimizationsplitchunks)，在此笔者贴上常用配置。

```javascript
export default {
    // ...
    optimization: {
        runtimeChunk: { name: "manifest" }, // 抽离WebpackRuntime函数
        splitChunks: {
            cacheGroups: {
                common: {
                    minChunks: 2,
                    name: "common",
                    priority: 5,
                    reuseExistingChunk: true, // 重用已存在代码块
                    test: AbsPath("src")
                },
                vendor: {
                    chunks: "initial", // 代码分割类型
                    name: "vendor", // 代码块名称
                    priority: 10, // 优先级
                    test: /node_modules/ // 校验文件正则表达式
                }
            }, // 缓存组
            chunks: "all" // 代码分割类型：all全部模块，async异步模块，initial入口模块
        } // 代码块分割
    }
};
```

### 2. 摇树优化

**删除项目中未被引用代码**，好处是`移除重复代码和未使用代码`。`摇树优化`首次出现于`rollup`，是`rollup`的核心概念，后来在`webpack v2`里借鉴过来使用。

`摇树优化`只对`ESM规范`生效，对其他模块规范失效。`摇树优化`针对静态结构分析，只有`import/export`才能提供静态的`导入/导出`功能。因此在编写业务代码时必须使用`ESM规范`才能让`摇树优化`移除重复代码和未使用代码。

在`webpack`里只需将打包环境设置成`生产环境`就能让`摇树优化`生效，同时业务代码使用`ESM规范`编写，使用`import`导入模块，使用`export`导出模块。

```javascript
export default {
    // ...
    mode: "production"
};
```

### 3. 动态垫片

**通过垫片服务根据UA返回当前浏览器代码垫片**，好处是`无需将繁重的代码垫片打包进去`。每次构建都配置`@babel/preset-env`和`core-js`根据某些需求将`Polyfill`打包进来，这无疑又为代码体积增加了贡献。

`@babel/preset-env`提供的`useBuiltIns`可按需导入`Polyfill`。

* [x] **false**：无视`target.browsers`将所有`Polyfill`加载进来
* [x] **entry**：根据`target.browsers`将部分`Polyfill`加载进来(仅引入有浏览器不支持的`Polyfill`，需在入口文件`import "core-js/stable"`)
* [x] **usage**：根据`target.browsers`和检测代码里ES6的使用情况将部分`Polyfill`加载进来(无需在入口文件`import "core-js/stable"`)

在此推荐大家使用`动态垫片`。`动态垫片`可根据浏览器`UserAgent`返回当前浏览器`Polyfill`，其思路是根据浏览器的`UserAgent`从`browserlist`查找出当前浏览器哪些特性缺乏支持从而返回这些特性的`Polyfill`。对这方面感兴趣的同学可参考[polyfill-library](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FFinancial-Times%2Fpolyfill-library)和[polyfill-service](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FFinancial-Times%2Fpolyfill-service)的源码。

使用[html-webpack-tags-plugin](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fjharris4%2Fhtml-webpack-tags-plugin)在打包时自动插入`动态垫片`。

```javascript
import HtmlTagsPlugin from "html-webpack-tags-plugin";

export default {
    plugins: [
        new HtmlTagsPlugin({
            append: false, // 在生成资源后插入
            publicPath: false, // 使用公共路径
            tags: ["https://polyfill.alicdn.com/polyfill.min.js"] // 资源路径
        })
    ]
};
```

### 4. 按需加载

**将路由页面/触发性功能单独打包为一个文件，使用时才加载**，好处是`减轻首屏渲染的负担`。因为项目功能越多其打包体积越大，导致首屏渲染速度越慢。

首屏渲染时只需对应`JS代码`而无需其他`JS代码`，所以可使用`按需加载`。`webpack v4`提供模块按需切割加载功能，配合`import()`可做到首屏渲染减包的效果，从而加快首屏渲染速度。只有当触发某些功能时才会加载当前功能的`JS代码`。

`webpack v4`提供魔术注解命名`切割模块`，若无注解则切割出来的模块无法分辨出属于哪个业务模块，所以一般都是一个业务模块共用一个`切割模块`的注解名称。

```javascript
const Login = () => import( /* webpackChunkName: "login" */ "../../views/login");
const Logon = () => import( /* webpackChunkName: "logon" */ "../../views/logon");
```

运行起来控制台可能会报错，在`package.json`的`babel`相关配置里接入[@babel/plugin-syntax-dynamic-import](https://link.juejin.cn/?target=https%3A%2F%2Fbabeljs.io%2Fdocs%2Fen%2Fbabel-plugin-syntax-dynamic-import.html)即可。

```json
{
    // ...
    "babel": {
        // ...
        "plugins": [
            // ...
            "@babel/plugin-syntax-dynamic-import"
        ]
    }
}
```

### 5. 作用提升

**分析模块间依赖关系，把打包好的模块合并到一个函数中**，好处是`减少函数声明和内存花销`。`作用提升`首次出现于`rollup`，是`rollup`的核心概念，后来在`webpack v3`里借鉴过来使用。

在未开启`作用提升`前，构建后的代码会存在大量函数闭包。由于模块依赖，通过`webpack`打包后会转换成`IIFE`，大量函数闭包包裹代码会导致打包体积增大(`模块越多越明显`)。在运行代码时创建的函数作用域变多，从而导致更大的内存开销。

在开启`作用提升`后，构建后的代码会按照引入顺序放到一个函数作用域里，通过适当重命名某些变量以防止变量名冲突，从而减少函数声明和内存花销。

在`webpack`里只需将打包环境设置成`生产环境`就能让`作用提升`生效，或显式设置`concatenateModules`。

```javascript
export default {
    // ...
    mode: "production"
};
// 显式设置
export default {
    // ...
    optimization: {
        // ...
        concatenateModules: true
    }
};
```

### 6. 压缩资源

**压缩 HTML/CSS/JS 代码，压缩字体/图像/音频/视频**，好处是`更有效减少打包体积`。极致地优化代码都有可能不及优化一个资源文件的体积更有效。

针对`HTML`代码，使用[html-webpack-plugin](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fjantimon%2Fhtml-webpack-plugin)开启压缩功能。

```javascript
import HtmlPlugin from "html-webpack-plugin";

export default {
    // ...
    plugins: [
        // ...
        HtmlPlugin({
            // ...
            minify: {
                collapseWhitespace: true,
                removeComments: true
            } // 压缩HTML
        })
    ]
};
```

针对`CSS/JS`代码，分别使用以下插件开启压缩功能。其中`OptimizeCss`基于`cssnano`封装，`Uglifyjs`和`Terser`都是`webpack`官方插件，同时需注意压缩`JS代码`需区分`ES5`和`ES6`。

* [optimize-css-assets-webpack-plugin](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FNMFR%2Foptimize-css-assets-webpack-plugin)：压缩`CSS代码`
* [uglifyjs-webpack-plugin](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fwebpack-contrib%2Fuglifyjs-webpack-plugin)：压缩`ES5`版本的`JS代码`
* [terser-webpack-plugin](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fwebpack-contrib%2Fterser-webpack-plugin)：压缩`ES6`版本的`JS代码`

```javascript
import OptimizeCssAssetsPlugin from "optimize-css-assets-webpack-plugin";
import TerserPlugin from "terser-webpack-plugin";
import UglifyjsPlugin from "uglifyjs-webpack-plugin";

const compressOpts = type => ({
    cache: true, // 缓存文件
    parallel: true, // 并行处理
    [`${type}Options`]: {
        beautify: false,
        compress: { drop_console: true }
    } // 压缩配置
});
const compressCss = new OptimizeCssAssetsPlugin({
    cssProcessorOptions: {
        autoprefixer: { remove: false }, // 设置autoprefixer保留过时样式
        safe: true // 避免cssnano重新计算z-index
    }
});
const compressJs = USE_ES6
    ? new TerserPlugin(compressOpts("terser"))
    : new UglifyjsPlugin(compressOpts("uglify"));

export default {
    // ...
    optimization: {
        // ...
        minimizer: [compressCss, compressJs] // 代码压缩
    }
};
```

## 三、部署阶段

该阶段主要围绕部署和服务器端的优化来考虑。

### 1. 使用 CDN

网站80-90%响应时间消耗在资源下载上，减少资源下载时间是性能优化的黄金法则。相比分布式架构的复杂和巨大投入，静态内容分发网络（CDN）可以以较低的投入，获得加载速度有效提升。

内容分发网络（CDN）是一组分布在多个不同地理位置的 Web 服务器。我们都知道，当服务器离用户越远时，延迟越高。CDN 就是为了解决这一问题，在多个位置部署服务器，让用户离服务器更近，从而缩短请求时间。

当用户访问一个网站时，如果没有 CDN，过程是这样的：

1. 浏览器要将域名解析为 IP 地址，所以需要向本地 DNS 发出请求。
2. 本地 DNS 依次向根服务器、顶级域名服务器、权限服务器发出请求，得到网站服务器的 IP 地址。
3. 本地 DNS 将 IP 地址发回给浏览器，浏览器向网站服务器 IP 地址发出请求并得到资源。

![不使用 CDN](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d25d1b0091b4e00ae51789172a46d2d\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

如果用户访问的网站部署了 CDN，过程是这样的：

1. 浏览器要将域名解析为 IP 地址，所以需要向本地 DNS 发出请求。
2. 本地 DNS 依次向根服务器、顶级域名服务器、权限服务器发出请求，得到全局负载均衡系统（GSLB）的 IP 地址。
3. 本地 DNS 再向 GSLB 发出请求，GSLB 的主要功能是根据本地 DNS 的 IP 地址判断用户的位置，筛选出距离用户较近的本地负载均衡系统（SLB），并将该 SLB 的 IP 地址作为结果返回给本地 DNS。
4. 本地 DNS 将 SLB 的 IP 地址发回给浏览器，浏览器向 SLB 发出请求。
5. SLB 根据浏览器请求的资源和地址，选出最优的缓存服务器发回给浏览器。
6. 浏览器再根据 SLB 发回的地址重定向到缓存服务器。
7. 如果缓存服务器有浏览器需要的资源，就将资源发回给浏览器。如果没有，就向源服务器请求资源，再发给浏览器并缓存在本地。

![使用 CDN](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67c19972e7dd4ae0840a0f838dd6a017\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. 压缩文件

前端工程师可以想办法明显地缩短通过网络传输 HTTP 请求和响应的时间。毫无疑问，终端用户的带宽速度，网络服务商，对等交换点的距离等等，都是开发团队所无法控制的。但还有别的能够影响响应时间的因素，压缩可以通过减少 HTTP 响应的大小来缩短响应时间。

Gzip 压缩通常可以减少70%的响应大小，对某些文件更可能高达90%，比 Deflate 更高效。主流 Web 服务器都有相应模块，而且绝大多数浏览器支持 gzip 解码。所以，应该对 HTML、CSS、JS、XML、JSON等文本类型的内容启用压缩。

> **注意：**图片和 PDF 文件不要使用 gzip。它们本身已经压缩过，再使用 gzip 压缩不仅浪费 CPU 资源，而且还可能增加文件体积。

从 HTTP/1.1 开始，**客户端**就有了支持压缩的Accept-Encoding HTTP请求头。

> Accept-Encoding: gzip, deflate

如果 web 服务器看到这个请求头，它就会用客户端列出的一种方式来压缩响应。**web 服务器**通过 Content-Encoding 响应头来通知客户端。

> Content-Encoding: gzip

在响应之前，服务器本身需要做相应配置来支持对应的压缩。

#### **Node 配置**

```javascript
const compression = require('compression')
// 在其他中间件前使用
app.use(compression())
```

#### Nginx 配置

```javascript
http {
  gzip on;
  gzip_buffers 32 4K;
  gzip_comp_level 6;
  gzip_min_length 100;
  gzip_types application/javascript text/css text/xml;
  gzip_disable "MSIE [1-6]\.";
  gzip_vary on;
}
```

### 3. HTTP 缓存

该策略主要围绕浏览器缓存做相关处理，同时也使接入成本最低的性能优化策略。其显著减少网络传输所带来的损耗，提升网页访问速度，是一种很值得使用的性能优化策略。

通过下图可知，为了让浏览器缓存发挥最大作用，该策略尽量遵循以下五点就能发挥浏览器缓存最大作用。

* **考虑拒绝一切缓存策略**：`Cache-Control:no-store`
* **考虑资源是否每次向服务器请求**：`Cache-Control:no-cache`
* **考虑资源是否被代理服务器缓存**：`Cache-Control:public/private`
* **考虑资源过期时间**：`Expires:t/Cache-Control:max-age=t,s-maxage=t`
* **考虑协商缓存**：`Last-Modified/Etag`

![缓存判断机制](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f245496e62f4d1f96f48363512267cc\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

缓存策略通过设置HTTP报文实现，在形式上分为**强缓存/强制缓存**和**协商缓存/对比缓存**。为了方便对比，将某些细节使用图例展示，相信你有更好的理解。

![强缓存](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e2572dfb1ee4923a0d3e183c63380b2\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![协商缓存](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc66368a78e947058b8d816f92b00607\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

整个`缓存策略`机制很明了，`先走强缓存，若命中失败才走协商缓存`。若命中`强缓存`，直接使用`强缓存`；若未命中`强缓存`，发送请求到服务器检查是否命中`协商缓存`；若命中`协商缓存`，服务器返回304通知浏览器使用`本地缓存`，否则返回`最新资源`。

有两种较常用的应用场景值得使用`缓存策略`一试，当然更多应用场景都可根据项目需求制定。

* **频繁变动资源**：设置`Cache-Control:no-cache`，使浏览器每次都发送请求到服务器，配合`Last-Modified/ETag`验证资源是否有效
* **不常变化资源**：设置`Cache-Control:max-age=31536000`，对文件名哈希处理，当代码修改后生成新的文件名，当HTML文件引入文件名发生改变才会下载最新文件

### 4. 使用 HTTP 2.0

`http2` 的诸多特性决定了它更快的传输速度。

1. 多路复用，在浏览器可并行发送 N 条请求。
2. 首部压缩，更小的负载体积。
3. 请求优先级，更快的关键请求

目前，网站已大多上了 http2，可在控制台面板进行查看。

![h2](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f20a8789c22c41c5aca8028d19d9736a\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

由于 http2 可并行请求，解决了 http1.1 线头阻塞的问题，以下几个性能优化点将会过时

1. 资源合并。如 `https://shanyue.tech/assets??index.js,interview.js,report.js`
2. 域名分片。
3. 雪碧图。将无数小图片合并成单个大图片。

### 5. 服务器端渲染

客户端渲染: 获取 HTML 文件，根据需要下载 JavaScript 文件，运行文件，生成 DOM，再渲染。

服务端渲染：服务端返回 HTML 文件，客户端只需解析 HTML。

* 优点：首屏渲染快，SEO 好。
* 缺点：配置麻烦，增加了服务器的计算压力。

下面用 Vue SSR 做示例，简单的描述一下 SSR 过程。

**客户端渲染过程**

1. 访问客户端渲染的网站。
2. 服务器返回一个包含了引入资源语句和 `<div id="app"></div>` 的 HTML 文件。
3. 客户端通过 HTTP 向服务器请求资源，当必要的资源都加载完毕后，执行 `new Vue()` 开始实例化并渲染页面。

**服务端渲染过程**

1. 访问服务端渲染的网站。
2. 服务器会查看当前路由组件需要哪些资源文件，然后将这些文件的内容填充到 HTML 文件。如果有 ajax 请求，就会执行它进行数据预取并填充到 HTML 文件里，最后返回这个 HTML 页面。
3. 当客户端接收到这个 HTML 页面时，可以马上就开始渲染页面。与此同时，页面也会加载资源，当必要的资源都加载完毕后，开始执行 `new Vue()` 开始实例化并接管页面。

从上述两个过程中可以看出，区别就在于第二步。客户端渲染的网站会直接返回 HTML 文件，而服务端渲染的网站则会渲染完页面再返回这个 HTML 文件。

**这样做的好处是更快的内容到达时间 (time-to-content)**。

假设你的网站需要加载完 abcd 四个文件才能渲染完毕。并且每个文件大小为 1 M。

这样一算：客户端渲染的网站需要加载 4 个文件和 HTML 文件才能完成首页渲染，总计大小为 4M（忽略 HTML 文件大小）。而服务端渲染的网站只需要加载一个渲染完毕的 HTML 文件就能完成首页渲染，总计大小为已经渲染完毕的 HTML 文件（这种文件不会太大，一般为几百K，我的个人博客网站（SSR）加载的 HTML 文件为 400K）。**这就是服务端渲染更快的原因**。

### 6. 避免重定向

HTTP 重定向通过`301/302`状态码实现。下面是一个有 301 状态码的 HTTP 头:

```
 HTTP/1.1 301 Moved Permanently 
 Location: http://example.com/newuri
 Content-Type: text/html
```

浏览器会自动跳转到 Location 域指明的 URL。重定向需要的所有信息都在 HTTP 头部，而响应体一般是空的。除此之外还有别的跳转方式，但如果你必须得做重定向，最好用标准的 3xxHTTP 状态码，主要是为了让返回按钮能正常使用。

客户端收到服务器的重定向响应后，会根据响应头中 Location 的地址再次发送请求。重定向会影响用户体验，尤其是多次重定向时，用户在一段时间内看不到任何内容，只看到浏览器进度条一直在刷新。

* 最浪费的重定向经常发生、而且很容易被忽略：URL 末尾应该添加`/`但未添加。比如，访问`http://astrology.yahoo.com/astrology`将被`301`重定向到 `http://astrology.yahoo.com/astrology/`（注意末尾的 /）。如果使用 Apache，可以通过Alias或mod\_rewrite或DirectorySlash解决这个问题。
* 网站域名变更：CNAME 结合 Alias 或 mod\_rewrite 或者其他服务器类似功能实现跳转。

### 7. 减少 DNS 查询

用户输入 URL 以后，浏览器首先要查询域名（hostname）对应服务器的 IP 地址，一般需要耗费20-120毫秒时间。DNS 查询完成之前，浏览器无法从服务器下载任何数据。

基于性能考虑，ISP、局域网、操作系统、浏览器都会有相应的 DNS 缓存机制。

* IE 缓存30分钟，可以通过注册表中 DnsCacheTimeout 项设置；
* Firefox 缓存1分钟，通过 network.dnsCacheExpiration 配置；

另外减少不同的主机名可减少 DNS 查找，减少不同主机名的数量同时也减少了页面能够并行下载的组件数量，避免 DNS 查找削减了响应时间，而减少并行下载数量却增加了响应时间。原则是把组件分散在2到4个主机名下，这是同时减少 DNS 查找和允许高并发下载的折中方案。

## 四、请求阶段

该阶段主要围绕请求阶段的优化来考虑。

### 1. 减少 HTTP 请求

一个完整的 HTTP 请求需要经历 DNS 查找，TCP 握手，浏览器发出 HTTP 请求，服务器接收请求，服务器处理请求并发回响应，浏览器接收响应等过程。接下来看一个具体的例子帮助理解 HTTP ：

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/05c1c42e60734ecd8dc7db8f4a8443ce\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

这是一个 HTTP 请求，请求的文件大小为 28.4KB。

名词解释：

* Queueing: 在请求队列中的时间。
* Stalled: 从TCP 连接建立完成，到真正可以传输数据之间的时间差，此时间包括代理协商时间。
* Proxy negotiation: 与代理服务器连接进行协商所花费的时间。
* DNS Lookup: 执行DNS查找所花费的时间，页面上的每个不同的域都需要进行DNS查找。
* Initial Connection / Connecting: 建立连接所花费的时间，包括TCP握手/重试和协商SSL。
* SSL: 完成SSL握手所花费的时间。
* Request sent: 发出网络请求所花费的时间，通常为一毫秒的时间。
* Waiting(TFFB): TFFB 是发出页面请求到接收到应答数据第一个字节的时间。
* Content Download: 接收响应数据所花费的时间。

从这个例子可以看出，真正下载数据的时间占比为 `13.05 / 204.16 = 6.39%`，文件越小，这个比例越小，文件越大，比例就越高。这就是为什么要建议将多个小文件合并为一个大文件，从而减少 HTTP 请求次数的原因。

### 2. 减少携带 Cookie

Cookie 被用于身份认证、个性化设置等诸多用途。Cookie 通过 HTTP 头在服务器和浏览器间来回传送，减少 Cookie 大小可以降低其对响应速度的影响。

* 去除不必要的 Cookie；
* 尽量压缩 Cookie 大小；
* 注意设置 Cookie 的 domain 级别，如无必要，不要影响到 sub-domain；
* 设置合适的过期时间。

### 3. 使用外部 JS 和 CSS

外部 JavaScript 和 CSS 文件可以被浏览器缓存，在不同页面间重用，也能降低页面大小。

当然，实际中也需要考虑代码的重用程度。如果仅仅是某个页面使用到的代码，可以考虑内嵌在页面中，减少 HTTP 请求数。

### 4. 异步加载

对于某些外部 JS 文件，在合适的情况下，可以考虑使用异步加载的方式。

* async
* defer

详情参见[这里](../html/yi-bu-jia-zai.md)。

### 5. 预加载

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

根据特定的使用场景来决定是否使用预加载以及何种预加载方式，详情参见[这里](../html/yu-jia-zai.md)。

### 6. 懒加载

懒加载也叫做延迟加载、按需加载，指的是在长网页中延迟加载图片数据，是一种较好的网页性能优化的方式。在比较长的网页或应用中，如果图片很多，所有的图片都被加载出来，而用户只能看到可视窗口的那一部分图片数据，这样就浪费了性能。

如果使用图片的懒加载就可以解决以上问题。在滚动屏幕之前，可视化区域之外的图片不会进行加载，在滚动屏幕时才加载。这样使得网页的加载速度更快，减少了服务器的负载。懒加载适用于图片较多，页面列表较长（长列表）的场景中。

## 五、参考

* [前端性能优化 24 条建议](https://juejin.cn/post/6892994632968306702)
* [写给中高级前端的性能优化](https://juejin.cn/post/6981673766178783262)
* [工作中如何进行前端性能优化](https://juejin.cn/post/6904517485349830670)
* [写在 2021 的前端性能优化指南](https://juejin.cn/post/7020212914020302856)
* [前端性能优化之雅虎 35 条军规](https://juejin.cn/post/6844903657318645767)
* [高频前端面试题汇总之前端性能优化篇](https://juejin.cn/post/6941278592215515143)
* [前端性能优化三部曲(加载篇)](https://juejin.cn/post/6844903863963631623)
