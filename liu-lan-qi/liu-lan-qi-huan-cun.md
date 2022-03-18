# 浏览器缓存

## 一、引言

什么是前端缓存？与之相对的什么又是后端缓存？

基本的网络请求就是三个步骤：请求，处理，响应。

后端缓存主要集中于“处理”步骤，通过保留数据库连接，存储处理结果等方式缩短处理时间，尽快进入“响应”步骤。当然这不在本文的讨论范围之内。

而前端缓存则可以在剩下的两步：“请求”和“响应”中进行。在“请求”步骤中，浏览器也可以通过存储结果的方式直接使用资源，直接省去了发送请求；而“响应”步骤需要浏览器和服务器共同配合，通过减少响应内容来缩短传输时间。这些都会在下面进行讨论。

本文主要包含：

* 按缓存位置分类 (memory cache, disk cache, Service Worker 等)
* 按失效策略分类 (`Cache-Control`, `ETag` 等)
* 缓存的应用场景

## 二、按缓存位置分类

我看过的大部分讨论缓存的文章会直接从 HTTP 协议头中的缓存字段开始，例如 `Cache-Control`, `ETag`, `max-age` 等。但偶尔也会听到别人讨论 memory cache, disk cache 等。**那这两种分类体系究竟有何关联？是否有交叉？**

实际上，HTTP 协议头的那些字段，都属于 disk cache 的范畴，是几个缓存位置的其中之一。因此本着从全局到局部的原则，我们应当先从缓存位置开始讨论。等讲到 disk cache 时，才会详细讲述这些协议头的字段及其作用。

我们可以在 Chrome 开发者工具的 Network 面板中看到一个请求最终的处理方式：如果是大小 (多少 K， 多少 M 等) 就表示是网络请求，否则会列出 `from memory cache`, `from disk cache` 和 `from ServiceWorker`。

它们的优先级是：(由上到下寻找，找到即返回；找不到则继续)

1. Service Worker
2. Memory Cache
3. Disk Cache
4. 网络请求

### 1. Memory Cache

memory cache 是内存中的缓存，(与之相对 disk cache 就是硬盘上的缓存)。按照操作系统的常理：先读内存，再读硬盘。

几乎所有的网络请求资源都会被浏览器自动加入到 memory cache 中。

* 常规情况下，浏览器的 TAB 关闭后该次浏览的 memory cache 便告失效。
* 极端情况下，例如一个页面的缓存已经占用了超级多的内存，那可能在 TAB 没关闭之前，排在前面的缓存就已经失效了。

刚才提过，**几乎所有的请求资源**都能进入 memory cache，这里细分一下主要有两块：

* **preloader：**如果你对这个机制不太了解，这里做一个简单的介绍，详情可以参阅[这篇文章](https://calendar.perfplanet.com/2013/big-bad-preloader/)。熟悉浏览器处理流程的同学们应该了解，在浏览器打开网页的过程中，会先请求 HTML 然后解析。之后如果浏览器发现了 js, css 等需要解析和执行的资源时，它会使用 CPU 资源对它们进行解析和执行。在古老的年代(大约 2007 年以前)，“请求 js/css - 解析执行 - 请求下一个 js/css - 解析执行下一个 js/css” 这样的“串行”操作模式在每次打开页面之前进行着。很明显在解析执行的时候，网络请求是空闲的，这就有了发挥的空间：我们能不能一边解析执行 js/css，一边去请求下一个(或下一批)资源呢？这就是 preloader 要做的事情。不过 preloader 没有一个官方标准，所以每个浏览器的处理都略有区别。例如有些浏览器还会下载 css 中的 `@import` 内容或者 `<video>` 的 `poster`等。而这些被 preloader 请求够来的资源就会被放入 memory cache 中，供之后的解析执行操作使用。
* **preload ：**虽然看上去和刚才的 preloader 就差了俩字母。实际上这个大家应该更加熟悉一些，例如 `<link rel="preload">`。这些显式指定的预加载资源，也会被放入 memory cache 中。

memory cache 机制保证了一个页面中如果有两个相同的请求 (例如两个 `src` 相同的 `<img>`，两个 `href` 相同的 `<link>`)都实际只会被请求最多一次，避免浪费。不过在匹配缓存时，除了匹配完全相同的 URL 之外，还会比对他们的类型，CORS 中的域名等进行检测。因此一个作为脚本 (script) 类型被缓存的资源是不能用在图片 (image) 类型的请求中的，即便他们 `src` 相等。

**在从 memory cache 获取缓存内容时，浏览器会忽视例如 `max-age=0`, `no-cache` 等头部配置**。例如页面上存在几个相同 `src` 的图片，即便它们可能被设置为不缓存，但依然会从 memory cache 中读取。这是因为 memory cache 只是短期使用，大部分情况生命周期只有一次浏览而已。而 `max-age=0` 在语义上普遍被解读为“不要在下次浏览时使用”，所以和 memory cache 并不冲突。

但如果站长是真心不想让一个资源进入缓存，就连短期也不行，**那就需要使用 `no-store`。存在这个头部配置的话，即便是 memory cache 也不会存储**，自然也不会从中读取了。

> Memory Cache 是浏览器自身的优化行为，不受 http header 控制，除非设置为 no-store。

### 2. Disk Cache

**disk cache 也叫 HTTP cache**，顾名思义是存储在硬盘上的缓存，因此它是**持久存储**的，是实际存在于文件系统中的。而且它允许相同的资源在跨会话，甚至跨站点的情况下使用，例如两个站点都使用了同一张图片。

disk cache 会严格根据 HTTP 头信息中的各类字段来判定哪些资源可以缓存，哪些资源不可以缓存；哪些资源是仍然可用的，哪些资源是过时需要重新请求的。当命中缓存之后，浏览器会从硬盘中读取资源，虽然比起从内存中读取慢了一些，但比起网络请求还是快了不少的。绝大部分的缓存都来自 disk cache。

凡是持久性存储都会面临容量增长的问题，disk cache 也不例外。在**浏览器自动清理**时，会有神秘的算法去把“最老的”或者“最可能过时的”资源删除，因此是一个一个删除的。不过每个浏览器识别“最老的”和“最可能过时的”资源的算法不尽相同。

> Disk Cache 也叫 HTTP Cache，是根据 header 信息来进行缓存的。

### 3. Service Worker

上述的缓存策略以及缓存/读取/失效的动作都是由浏览器内部判断和进行的，我们只能设置响应头的某些字段来告诉浏览器，而不能自己操作。但 Service Worker 的出现，给予了我们另外一种更加灵活，更加直接的操作方式。可以配置缓存哪些文件，定义路由匹配规则等。

Service Worker 是运行在浏览器背后的**独立线程**，一般可以用来实现缓存功能。使用 Service Worker 的话，传输协议必须为 **HTTPS**。因为 Service Worker 中涉及到请求拦截，所以必须使用 HTTPS 协议来保障安全。

Service Worker 实现缓存功能一般分为三个步骤：首先需要先注册 Service Worker，然后监听到 `install` 事件以后就可以缓存需要的文件，那么在下次用户访问的时候就可以通过拦截请求的方式查询是否存在缓存，存在缓存的话就可以直接读取缓存文件，否则就去请求数据。

以下是这个步骤的实现：

```javascript
// index.js
if (navigator.serviceWorker) {
  navigator.serviceWorker
    .register('sw.js')
    .then(function(registration) {
      console.log('service worker 注册成功')
    })
    .catch(function(err) {
      console.log('servcie worker 注册失败')
    })
}
// sw.js
// 监听 `install` 事件，回调中缓存所需文件
self.addEventListener('install', e => {
  e.waitUntil(
    caches.open('my-cache').then(function(cache) {
      return cache.addAll(['./index.html', './index.js'])
    })
  )
})

// 拦截所有请求事件
// 如果缓存中已经有请求的数据就直接用缓存，否则去请求数据
self.addEventListener('fetch', e => {
  e.respondWith(
    caches.match(e.request).then(function(response) {
      if (response) {
        return response
      }
      console.log('fetch source')
    })
  )
})
```

打开页面，可以在开发者工具中的 `Application` 看到 Service Worker 已经启动了!

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dacd9c7895d942578c6326dfc3367e22\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

在 Cache 中也可以发现我们所需的文件已被缓存

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/3/28/1626b20dfc4fcd26\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

当我们重新刷新页面可以发现我们缓存的数据是从 Service Worker 中读取的

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d67e7882fef4ab0a22542757b20763e\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)



> 如果 Service Worker 没能命中缓存，一般情况会使用 `fetch()` 方法继续获取资源。这时候，浏览器就去 memory cache 或者 disk cache 进行下一次找缓存的工作了。

> 经过 Service Worker 的 `fetch()` 方法获取的资源，即便它并没有命中 Service Worker 缓存，甚至实际走了网络请求，也会标注为 `from ServiceWorker`。

### 4. 请求网络

如果一个请求在上述 3 个位置都没有找到缓存，那么浏览器会正式发送网络请求去获取内容。之后容易想到，为了提升之后请求的缓存命中率，自然要把这个资源添加到缓存中去。具体来说：

1. 根据 Service Worker 中的 handler 决定是否存入 Cache Storage (额外的缓存位置)。
2. 根据 HTTP 头部的相关字段(`Cache-control`, `Pragma` 等)决定是否存入 disk cache
3. memory cache 保存一份资源 **的引用**，以备下次使用。

## 三、按缓存策略分类

memory cache 是浏览器为了加快读取缓存速度而进行的自身优化行为，不受开发者控制，也不受 HTTP 协议头的约束，算是一个黑盒。Service Worker 是由开发者编写的额外的脚本，且缓存位置独立，出现也较晚，使用还不算太广泛。所以我们平时最为熟悉的其实是 disk cache，也叫 HTTP cache。平时所说的强制缓存，对比缓存，以及 `Cache-Control` 等，也都归于此类。

### 1. 强制缓存 (强缓存)

![强制缓存](<../.gitbook/assets/image (9).png>)

强制缓存的含义是，当客户端请求后，会先访问缓存数据库看缓存是否存在。如果存在则直接返回；不存在则请求真的服务器，响应后再写入缓存数据库。

**强制缓存直接减少请求数，是提升最大的缓存策略。** 它的优化覆盖了文章开头提到过的请求数据的全部三个步骤。如果考虑使用缓存来优化网页性能的话，强制缓存应该是首先被考虑的。

可以设置强制缓存的字段是 `Cache-control` 和 `Expires`。

#### Expires

这是 HTTP 1.0 的字段，表示缓存到期时间，是一个绝对的时间 (当前时间+缓存时间)，如

```
Expires: Thu, 10 Nov 2017 08:45:11 GMT
```

在响应消息头中，设置这个字段之后，就可以告诉浏览器，在未过期之前不需要再次请求。

但是，这个字段设置时有两个缺点：

1. 由于是绝对时间，用户可能会将客户端本地的时间进行修改，而导致浏览器判断缓存失效，重新请求该资源。此外，时差或者误差等因素也可能造成客户端与服务端的时间不一致，致使缓存失效。
2. 写法太复杂了。表示时间的字符串多个空格，少个字母，都会导致非法属性从而设置失效。

#### Cache-control

已知 Expires 的缺点之后，在 HTTP/1.1 中，增加了一个字段 Cache-control，该字段表示资源缓存的最大有效时间，在该时间内，客户端不需要向服务器发送请求。

这两者的区别就是前者是绝对时间，而后者是相对时间。如下：

```
Cache-control: max-age=2592000
```

下面列举一些 `Cache-control` 字段常用的值：(完整的列表可以查看 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control))

* `max-age`：即最大有效时间。
* `must-revalidate`：如果超过了 `max-age` 的时间，浏览器必须向服务器发送请求，验证资源是否还有效。
* `no-cache`：虽然字面意思是“不要缓存”，但实际上还是要求客户端缓存内容的，只是是否使用这个内容由后续的对比来决定。
* `no-store`: 真正意义上的“不要缓存”。所有内容都不走缓存，包括强制和对比。
* `public`：所有的内容都可以被缓存 (包括客户端和代理服务器， 如 CDN)
* `private`：所有的内容只有客户端才可以缓存，代理服务器不能缓存。默认值。

> 这些值可以混合使用，例如 `Cache-control:public, max-age=2592000`。

> `max-age=0` 和 `no-cache` 等价吗？从规范的字面意思来说，`max-age` 到期是 **应该(SHOULD)** 重新验证，而 `no-cache` 是 **必须(MUST)** 重新验证。但实际情况以浏览器实现为准，大部分情况他们俩的行为还是一致的。（如果是 `max-age=0, must-revalidate` 就和 `no-cache` 等价了）

> 在 HTTP/1.1 之前，如果想使用 `no-cache`，通常是使用 `Pragma` 字段，如 `Pragma: no-cache`(这也是 `Pragma` 字段唯一的取值)。但是这个字段只是浏览器约定俗成的实现，并没有确切规范，因此缺乏可靠性。它应该只作为一个兼容字段出现，在当前的网络环境下其实用处已经很小。

**Cache-control 的优先级高于 Expires**，为了兼容 HTTP/1.0 和 HTTP/1.1，实际中两个字段都会设置。

### 2. 对比缓存 (协商缓存)

![协商缓存](<../.gitbook/assets/image (7) (1).png>)

当强制缓存失效(超过规定时间)时，就需要使用对比缓存，由服务器决定缓存内容是否失效。

流程上说，浏览器先请求缓存数据库，返回一个缓存标识。之后浏览器拿这个标识和服务器通讯。

* 如果缓存未失效，则返回 HTTP 状态码 304 表示继续使用，于是客户端继续使用缓存；
* 如果失效，则返回新的数据和缓存规则，浏览器响应数据后，再把规则写入到缓存数据库。

**对比缓存在请求数上和没有缓存是一致的**，但如果是 304 的话，返回的仅仅是一个状态码而已，并没有实际的文件内容，因此 **在响应体体积上的节省是它的优化点**。它的优化覆盖了文章开头提到过的请求数据的三个步骤中的最后一个：“响应”。通过减少响应体体积，来缩短网络传输时间。所以和强制缓存相比提升幅度较小，但总比没有缓存好。

对比缓存是可以和强制缓存一起使用的，作为在强制缓存失效后的一种后备方案。实际项目中他们也的确经常一同出现。

对比缓存有 2 组字段(不是两个)：

#### Last-Modified & If-Modified-Since

1. 服务器通过 `Last-Modified` 字段告知客户端，资源最后一次被修改的时间，例如

```
Last-Modified: Mon, 10 Nov 2018 09:10:11 GMT
```

1. 浏览器将这个值和内容一起记录在缓存数据库中。
2. 下一次请求相同资源时时，浏览器从自己的缓存中找出“不确定是否过期的”缓存。因此在请求头中将上次的 `Last-Modified` 的值写入到请求头的 `If-Modified-Since` 字段
3. 服务器会将 `If-Modified-Since` 的值与 `Last-Modified` 字段进行对比。如果相等，则表示未修改，响应 304；反之，则表示修改了，响应 200 状态码，并返回数据。

但是他还是有一定缺陷的：

* 如果资源更新的速度是秒以下单位，那么该缓存是不能被使用的，因为它的时间单位最低是秒。
* 如果文件是通过服务器动态生成的，那么该方法的更新时间永远是生成的时间，尽管文件可能没有变化，所以起不到缓存的作用。

![基于 Last-Modified 的协商缓存](<../.gitbook/assets/image (8) (1).png>)

#### Etag & If-None-Match

为了解决上述问题，出现了一组新的字段 `Etag` 和 `If-None-Match`

`Etag` 存储的是文件的特殊标识(一般都是 hash 生成的)，服务器存储着文件的 `Etag` 字段。之后的流程和 `Last-Modified` 一致，只是 `Last-Modified` 字段和它所表示的更新时间改变成了 `Etag` 字段和它所表示的文件 hash，把 `If-Modified-Since` 变成了 `If-None-Match`。服务器同样进行比较，命中返回 304, 不命中返回新资源和 200。

![基于 Etag 的协商缓存](<../.gitbook/assets/image (2).png>)

> **Etag 的优先级高于 Last-Modified**

## 四、缓存总结

当浏览器要请求资源时

1. 调用 Service Worker 的 `fetch` 事件响应
2. 查看 memory cache
3. 查看 disk cache。这里又细分：
4.
   1. 如果有强制缓存且未失效，则使用强制缓存，不请求服务器。这时的状态码全部是 200
   2. 如果有强制缓存但已失效，使用对比缓存，比较后确定 304 还是 200
5. 发送网络请求，等待网络响应
6. 把响应内容存入 disk cache (如果 HTTP 头信息配置可以存的话)
7. 把响应内容 **的引用** 存入 memory cache (无视 HTTP 头信息的配置)
8. 把响应内容存入 Service Worker 的 Cache Storage (如果 Service Worker 的脚本调用了 `cache.put()`)\


![浏览器缓存处理流程](<../.gitbook/assets/image (14).png>)

## 五、缓存的应用

了解了缓存的原理，我们可能更加关心如何在实际项目中使用它们，才能更好的让用户缩短加载时间，节约流量等。这里有几个常用的模式，供大家参考

### 1. 不常变化的资源

```
Cache-Control: max-age=31536000
```

通常在处理这类资源资源时，给它们的 `Cache-Control` 配置一个很大的 `max-age=31536000` (一年)，这样浏览器之后请求相同的 URL 会命中强制缓存。而为了解决更新的问题，就需要在文件名(或者路径)中添加 hash， 版本号等动态字符，之后更改动态字符，达到更改引用 URL 的目的，从而让之前的强制缓存失效 (其实并未立即失效，只是不再使用了而已)。

在线提供的类库 (如 jquery-3.3.1.min.js, lodash.min.js 等) 均采用这个模式。如果配置中还增加 `public` 的话，CDN 也可以缓存起来，效果拔群。

这个模式的一个变体是在引用 URL 后面添加参数 (例如 `?v=xxx` 或者 `?_=xxx`)，这样就不必在文件名或者路径中包含动态参数，满足某些完美主义者的喜好。在项目每次构建时，更新额外的参数 (例如设置为构建时的当前时间)，则能保证每次构建后总能让浏览器请求最新的内容。

> **特别注意：** 在处理 Service Worker 时，对待 `sw-register.js`(注册 Service Worker) 和 `serviceWorker.js` (Service Worker 本身) 需要格外的谨慎。如果这两个文件也使用这种模式，你必须多多考虑日后可能的更新及对策。

### 2. 经常变化的资源

```
Cache-Control: no-cache
```

这里的资源不单单指静态资源，也可能是网页资源，例如博客文章。这类资源的特点是：URL 不能变化，但内容可以(且经常)变化。我们可以设置 `Cache-Control: no-cache` 来迫使浏览器每次请求都必须找服务器验证资源是否有效。

既然提到了验证，就必须 `ETag` 或者 `Last-Modified` 出场。这些字段都会由专门处理静态资源的常用类库(例如 `koa-static`)自动添加，无需开发者过多关心。

也正如上文中提到协商缓存那样，这种模式下，节省的并不是请求数，而是请求体的大小。所以它的优化效果不如模式 1 来的显著。

### 3. 非常危险的模式 1 和 2 的结合 （反例）

```
Cache-Control: max-age=600, must-revalidate
```

不知道是否有开发者从模式 1 和 2 获得一些启发：模式 2 中，设置了 `no-cache`，相当于 `max-age=0, must-revalidate`。我的应用时效性没有那么强，但又不想做过于长久的强制缓存，我能不能配置例如 `max-age=600, must-revalidate` 这样折中的设置呢？

表面上看这很美好：资源可以缓存 10 分钟，10 分钟内读取缓存，10 分钟后和服务器进行一次验证，集两种模式之大成，但实际线上暗存风险。因为上面提过，浏览器的缓存有自动清理机制，开发者并不能控制。

举个例子：当我们有 3 种资源： `index.html`, `index.js`, `index.css`。我们对这 3 者进行上述配置之后，假设在某次访问时，`index.js` 已经被缓存清理而不存在，但 `index.html`, `index.css` 仍然存在于缓存中。这时候浏览器会向服务器请求新的 `index.js`，然后配上老的 `index.html`, `index.css` 展现给用户。这其中的风险显而易见：不同版本的资源组合在一起，报错是极有可能的结局。

除了自动清理引发问题，不同资源的请求时间不同也能导致问题。例如 A 页面请求的是 `A.js` 和 `all.css`，而 B 页面是 `B.js` 和 `all.css`。如果我们以 A -> B 的顺序访问页面，势必导致 `all.css` 的缓存时间早于 `B.js`。那么以后访问 B 页面就同样存在资源版本失配的隐患。

## 六、考点

### 1. 三种刷新操作对 http 缓存的影响

通常浏览器的刷新操作有如下三种：

* 正常操作：地址栏输入 url，跳转链接，前进后退等；
* 手动刷新：f5，点击刷新按钮，右键菜单刷新；
* 强制刷新：ctrl + f5，shift+command+r；

对应的对缓存的影响如下：

* 正常操作：强制缓存有效，协商缓存有效；
* 手动刷新：强制缓存失效，协商缓存有效；
* 强制刷新：强制缓存失效，协商缓存失效；

### 2. Cache-Control 字段各个值的含义

![Cache-Control](<../.gitbook/assets/image (11).png>)

### 3. 缓存处理顺序 

![浏览器缓存处理流程](<../.gitbook/assets/image (10) (1).png>)

## 七、参考

* [https://github.com/easonyq/easonyq.github.io/blob/master/%E5%AD%A6%E4%B9%A0%E8%AE%B0%E5%BD%95/others/cache.md](https://github.com/easonyq/easonyq.github.io/blob/master/%E5%AD%A6%E4%B9%A0%E8%AE%B0%E5%BD%95/others/cache.md)
* [https://juejin.cn/post/6992843117963509791](https://juejin.cn/post/6992843117963509791)

\
