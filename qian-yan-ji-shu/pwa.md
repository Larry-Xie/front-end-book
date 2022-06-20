# PWA

## 一、引言

在手机上浏览网页时，页面偶尔会出现弹窗，询问是否要将此网页保存到本地；如果选择了确定，在手机的主屏上就会出现一个新的 App，它就是刚才浏览的网页；这种 App 并非来自应用商店，却可以像使用 App 一样去使用或卸载它，它就是这篇文章的主角 ——— PWA。

## 二、PWA 介绍

### 1. 什么是 PWA？ <a href="#1.1-20-e4-bb-80-e4-b9-88-e6-98-af-20pwa-ef-bc-9f" id="1.1-20-e4-bb-80-e4-b9-88-e6-98-af-20pwa-ef-bc-9f"></a>

* 渐进式 web 应用（Progressive Web App），是 Google 2016 年提出概念、2017 年落地的 web 技术，渐进式 web 应用 就是 实现了和原生应用相近的用户体验的网页应用
* PWA 不单指一种技术，也是一种思想和概念，它的目的是 —— 通过一系列的 Web 技术去优化网页应用；提升其安全性，性能，流畅性，用户体验等指标；最后达到用户使用网页应用，就像在用 原生App 一样的效果

### 2. 为什么会出现 PWA？ <a href="#1.2-c2-a0-e4-b8-ba-e4-bb-80-e4-b9-88-e4-bc-9a-e5-87-ba-e7-8e-b0-20pwa-ef-bc-9f" id="1.2-c2-a0-e4-b8-ba-e4-bb-80-e4-b9-88-e4-bc-9a-e5-87-ba-e7-8e-b0-20pwa-ef-bc-9f"></a>

原生App 使用起来很流畅，性能好，安全性也可以很高，这是它很显著的优势。但是缺点呢，也很明显，比如

1. 开发成本很昂贵
2. 软件上线，版本更新都需要发布到不同的商店，并通过审核，对开发很不友好
3. 对于用户来说，有些 APP 使用频率很低，但还是得去商店中下载大安装包；或者随着版本的更新，得卸载并更新

PWA 完美地避免了这些问题。

### 3. 如何判断一个 web 应用是 PWA？ <a href="#1.3-c2-a0-e5-a6-82-e4-bd-95-e5-88-a4-e6-96-ad-e4-b8-80-e4-b8-aa-20web-20-e5-ba-94-e7-94-a8-e6-98-af" id="1.3-c2-a0-e5-a6-82-e4-bd-95-e5-88-a4-e6-96-ad-e4-b8-80-e4-b8-aa-20web-20-e5-ba-94-e7-94-a8-e6-98-af"></a>

在安装方式上，PWA 与 原生 App 有很大的不同。在实际使用上，PWA 与 原生 App 的差距非常小。

判断是否为 PWA 的思考方向如下：

1. 可发现 —— 内容可以通过搜索引擎发现
2. 可安装 —— 可以出现在设备的主屏幕
3. 可链接 —— 可以通过 URL 分享它
4. 独立于网络 —— 可以在 离线状态 或 网速很差 的情况下运行
5. 渐进式 —— 在老版浏览器中仍可使用，在新版浏览器中可使用全部功能
6. 可重用 —— 无论何时有更新的内容，它都可以发送通知
7. 响应性 —— 在任何具有屏幕和浏览器的设备上，都可以正常使用（包括手机，平板电脑，笔记本，电视，冰箱等）
8. 安全 —— 可以阻止第三方应用访问用户敏感数据

## 三、PWA 核心功能

### 1. 何谓 Service Worker <a href="#2.1-20-e4-bd-95-e8-b0-93-c2-a0service-20worker" id="2.1-20-e4-bd-95-e8-b0-93-c2-a0service-20worker"></a>

* Service Worker 是浏览器和网络之间的虚拟代理
* Service Workes 运行在一个与 页面的 JavaScript 主线程 独立的线程上，它没有对 DOM 结构的访问权限，可以在不同上下文间 发送/接收 信息
* 可分配给 Servic Worker 一些任务，使用基于 Promise 的方法在任务完成时收到结果
* Service Worker 不仅提供离线功能，还提供包括处理通知，在单独的线程上执行繁重的计算等功能
* Service Worker 可以控制网络请求，修改网络请求，返回缓存的自定义响应，或合成响应；因为强大，所以 Service Workers 只能在安全的上下文中执行（即 HTTPS ）

### 2. 注册 Service Worker <a href="#2.2-20-e6-b3-a8-e5-86-8c-20service-20worker" id="2.2-20-e6-b3-a8-e5-86-8c-20service-20worker"></a>

* 浏览器支持 Service Worker
* Service Worker 的 注册路径 决定了它的 默认作用页面的范围，也就是 scope
* 如果希望改变它的作用域，可在 第二个参数 设置 scope 范围
* 如果存放在网站根路径下，将会收到该网站的所有 fetch 事件

```
  if ("serviceWorker" in navigator) {
    // 浏览器支持 Service Worker
    navigator.serviceWorker
      .register("serviceWorker.js") // 这里可以接受第二个参数，用于设置 scope 范围
      .then(function (registration) {
        // 如果存放在网站根路径下，将会收到该网站的所有 fetch 事件
        console.log("ServiceWorker注册成功: ", registration.scope);
      })
      .catch(function (err) {
        console.log("ServiceWorker注册失败: ", err);
      });
  }1234567891011
```

* 注册完成后，serviceWorker.js 文件会自动下载、安装、激活

### 3. Service Worker 常用事件 <a href="#2.3-c2-a0service-20worker-20-e5-b8-b8-e7-94-a8-e4-ba-8b-e4-bb-b6" id="2.3-c2-a0service-20worker-20-e5-b8-b8-e7-94-a8-e4-ba-8b-e4-bb-b6"></a>

#### 3.1 install <a href="#2.3.1-20install" id="2.3.1-20install"></a>

* install 事件发生在：浏览器安装并注册 Service Worker 时
* event.waitUtil 用于在安装成功之前执行一些预装逻辑，建议只做一些轻量级和非常重要资源的缓存，以减少安装失败的概率，
* Service Worker 会等到 waitUntil 内的代码执行完毕后，才开始安装，它返回一个 promise
* 安装成功后，ServiceWorker 状态会从 installing 变为 installed
* caches 是一个特殊的 CacheStorage 对象，它能在 Service Worker 指定的范围内提供数据存储的能力，
* 如果所有的文件都成功缓存了，便会安装完成；如果任何文件下载失败了，那么安装过程也会随之失败

```
<script>
  var cacheName = "hello-pwa";

  // install 事件发生在: 浏览器安装并注册 Service Worker 时
  self.addEventListener("install", (event) => {
    // event.waitUtil 用于在安装成功之前执行一些预装逻辑
    // 建议只做一些轻量级和非常重要资源的缓存，以减少安装失败的概率
    // 安装成功后 ServiceWorker 状态会从 installing 变为 installed
    event.waitUntil(
      // caches 是一个特殊的 CacheStorage 对象
      // 它能在 Service Worker 指定的范围内， 提供数据存储的能力
      caches
        .open(cacheName)
        .then((cache) =>
          cache.addAll(
            [
              "/", // 一定要包含整个目录，不然无法离线浏览
              "./images/cat2.jpg",
              "./index.html",
              "./style.css",
            ]
            // 如果所有的文件都成功缓存了，便会安装完成
            // 如果任何文件下载失败了，那么安装过程也会随之失败
          )
        )
        .then(() => self.skipWaiting())
    );
  });
</script>
```

#### 3.2 fetch  <a href="#2.3.2-20fetch-c2-a0" id="2.3.2-20fetch-c2-a0"></a>

* Service Worker 会从缓存中请求所需数据，从而提供离线应用功能
* 当应用发起一个 http 请求时，可以使用 fetch 事件
* fetch 事件用于：拦截请求，并对请求作出响应
* caches.match() 函数，用来检查 请求URL 是否匹配 缓存中已存在的内容；如果存在，就返回缓存资源；如果不存在，就通过网络来获取资源，并将资源存储到缓存中

```
  // Service Worker 会从缓存中请求所需数据，从而提供离线应用功能
  self.addEventListener("fetch", function (e) {
    event.respondWith(
      // caches.match() 函数，用来检查 请求URL 是否匹配 缓存中已存在的内容；
      // 如果存在，就返回缓存资源；
      // 如果不存在，就通过网络来获取资源，并将资源存储到缓存中
      caches.match(e.request).then(function (r) {
        return (
          r ||
          fetch(e.request).then(function (response) {
            return caches.open(cacheName).then(function (cache) {
              cache.put(e.request, response.clone());
              return response;
            });
          })
        );
      })
    );
  });
```

### **4. Service Worker 生命周期**

以上我们简单完成了一个静态文件缓存，对于fetch的缓存、读取和删除的操作，接下来我们把目光聚集在最复杂的一环 Service Worker 的生命周期上，我们了解它的生命周期就可以更好的知道它是如何工作，这将为用户提供更好的服务。

我们了解生命周期的目的：

* 实现离线优先。
* 允许新 Service Worker 自行做好运行准备，无需中断当前的 Service Worker。
* 确保整个过程中作用域页面由同一个 Service Worker（或者没有 Service Worker）控制。
* 确保每次只运行一个版本 Service Worker 。

_**install 事件**_

Service Worker 的第一个事件是 install 。它会在 Service Worker 执行时立即触发，并且每个 Service Worker 只会调用一次。如果更改了 Service Worker 脚本，浏览器就会将其视为另一个 Service Worker，并且它将获得自己的 install 事件。 通常，install 事件用于缓存应用运行时所需的全部内容。

_**activate 事件**_

Service Worker 每次启动时都会收到 activate 事件。 activate 事件的主要目的是配置 Service Worker 的行为，清除以前运行中遗留的任何资源（例如旧缓存），并让 Service Worker 准备好处理网络请求（例如下面要讲的 fetch 事件）。

#### _**fetch 事件**_

fetch 事件允许 Service Worker 拦截并处理任何网络请求。它可以通过网络获取资源、可以从自己的缓存中提取资源、生成自定义响应，以及很多种不同的选择。

_**更新 Service Worker**_

浏览器会检查每个页面加载时是否有新版本的 Service Worker 。如果找到新版本，则会下载这个新版本并在后台安装，但不会激活它。它会处于等待状态，直到不再打开任何使用旧 Service Worker 的页面。一旦关闭了使用旧 Service Worker 的所有窗口，新的 Service Worker 就会被激活并获得控制权。

_**整体流程**_

> 注：Service Worker 的生命周期完全独立于网页。

当我们要为网站安装Service Worker时，需要先在页面的 JavaScript 中注册，注册成功后会进入安装过程中，通常这一过程需要缓存某些静态资源。如果所有文件均已成功缓存，那么 Service Worker 就安装完毕。如果任何文件下载失败或缓存失败，那么安装步骤将会失败，Service Worker 就无法激活（也就是不会安装）。如果发生这种情况，不必担心，它下次会再试一次。安装之后，接下来就是激活步骤，这是管理旧缓存的时机，激活之后，Service Worker 将会对其作用域内的所有页面实施控制，不过首次注册该 Service Worker 的页面需要再次加载才会受其控制。Service Worker开始控制后，就可以开始执行我们缓存策略了。

以下是 Service Worker 初始安装时的简化生命周期。

![](https://pic1.zhimg.com/80/v2-1d49eede76c14aada50e839296506488\_720w.jpg)

### 4. Manifest <a href="#2.4-c2-a0manifest" id="2.4-c2-a0manifest"></a>

想成为可安装网站，需要的准备工作：

1. 一份网页清单，填好正确的字段
2. 网站的域必须是安全（HTTPS）的
3. 一个本设备上代表应用的图标
4. 一个注册好的 Service Worker，可以让应用离线工作（这仅对于安卓设备上的 Chrome 浏览器是必需的）

### 5. 清单文件 <a href="#2.5-20-e6-b8-85-e5-8d-95-e6-96-87-e4-bb-b6" id="2.5-20-e6-b8-85-e5-8d-95-e6-96-87-e4-bb-b6"></a>

* 清单文件通常位于网页应用的根目录，包含很多有用的信息，比如：应用标题、应用图标路径、加载/启动画面的背景颜色等等
* 这些信息在 浏览器安装 web 应用时、在主屏上显示应用时 需要的
* 这些信息会以 JSON 的形式列出
* 一份网页清单，最少需要 name、icon (icon 需要带有 src, size 和 type)、description、short\_name、start\_url
* manifest.webapp，它是以前网页清单中常用的扩展名，在 Firefox OS 应用清单中很流行
* 许多人使用 manifest.json 作为网页清单，因为内容是 JSON 格式的
* 但是 .webmanifest 扩展名是在 W3C 清单规范中显示指定的，建议清单文件使用 .webmanifest 作为后缀

```
{
  "name": "Minimal PWA", //网站应用的全名
  "short_name": "PWA Demo", // 显示在主屏上的短名
  "description": "The app that helps you understand PWA",  // 应用描述
  "display": "standalone",  // 应用的显示方式：可以是全屏，独立，最小ui或者浏览器
  "start_url": "/",  // 应用启动的index文档
  "theme_color": "#313131", // ui的主题色，这是操作系统使用的
  "background_color": "#313131", // 背景色，用于安装程序时和启动应用时
  "icons": [
    {
      "src": "icon/lowres.webp",
      "sizes": "48x48",
      "type": "image/webp"
    },
    {
      "src": "icon/lowres",
      "sizes": "48x48"
    },
    {
      "src": "icon/hd_hi.ico",
      "sizes": "72x72 96x96 128x128 256x256"
    },
    {
      "src": "icon/hd_hi.svg",
      "sizes": "72x72"
    }
  ] // 确保有一个最适合用户设备的图标可以被选中
}
12345678910111213141516171819202122232425262728
```

### 6. 添加到主屏 <a href="#2.6-20-e6-b7-bb-e5-8a-a0-e5-88-b0-e4-b8-bb-e5-b1-8f" id="2.6-20-e6-b7-bb-e5-8a-a0-e5-88-b0-e4-b8-bb-e5-b1-8f"></a>

* “添加到主屏” 是移动端浏览器才有的，它利用网页清单中的信息，在设备主屏上显示应用图标和文字
* 用户访问 PWA 时，会出现一个弹框提示用户 “是否安装此应用”，用户确认之后，应用就被安装到主屏了
* 在某些移动端浏览器中，可以通过清单信息产生启动画面（在 PWA 启动时显示），图标、主题和背景色，用于创建这个启动画面
* 在 Ios 上和 Android 手机上打开 Vue 官网，可以将其添加到设备主屏：

![](https://img-blog.csdnimg.cn/20210927100126716.png)

### 7. Push & Notification <a href="#2.7-c2-a0push-20-26-20notification" id="2.7-c2-a0push-20-26-20notification"></a>

#### 7.1 何谓 推送/通知？ <a href="#2.7.1-20-e4-bd-95-e8-b0-93-20-e6-8e-a8-e9-80-81-2f-e9-80-9a-e7-9f-a5-ef-bc-9f" id="2.7.1-20-e4-bd-95-e8-b0-93-20-e6-8e-a8-e9-80-81-2f-e9-80-9a-e7-9f-a5-ef-bc-9f"></a>

Push & Notification（推送和通知）通过推送API 和 通知API 来实现

1. 推送 —— 实现从服务端推送新的内容，而不需要客户端发起请求，它由应用的 Service Worker 实现
2. 通知 —— 通过 Service Worker 向用户展示新信息，比如提醒用户应用更新了功能

* 推送和通知，是在浏览器外部实现的，跟 Service Worker 一样；因此，即使应用被隐藏到后台，甚至应用关闭，我们仍能 推送/通知 到用户
* 推送API 和 通知API 可以独立工作，也可以结合使用

#### 7.2 推送/通知需要的授权 <a href="#2.7.2-20-e6-8e-a8-e9-80-81-2f-e9-80-9a-e7-9f-a5-20-e9-9c-80-e8-a6-81-e7-9a-84-e6-8e-88-e6-9d-83" id="2.7.2-20-e6-8e-a8-e9-80-81-2f-e9-80-9a-e7-9f-a5-20-e9-9c-80-e8-a6-81-e7-9a-84-e6-8e-88-e6-9d-83"></a>

* 当用户同意授权，PWA 就可以实现 推送 + 通知，也就是说授权同时控制这两个
* 用户授权的结果有三种，default（默认），granted（同意） 或者 denied（拒绝）

```
  var button = document.getElementById("notifications");
  console.log(button);
  button.addEventListener("click", function (e) {
    Notification.requestPermission().then(function (result) {
      if (result === "granted") {
        // randomNotification();
        console.log("授权");
      }
    });
  });
```

#### 7.3 关于推送 <a href="#2.7.3-20-e5-85-b3-e4-ba-8e-e6-8e-a8-e9-80-81" id="2.7.3-20-e5-85-b3-e4-ba-8e-e6-8e-a8-e9-80-81"></a>

推送比通知要复杂，他的流程是这样的：

1. 首先需要从服务端订阅一个服务；
2. 之后服务端会推送数据到客户端应用；
3. 接着应用的 Service Worker 负责接收到从服务端推送的数据，这些数据可以用来做通知推送，或者实现其他的需求

* Service Worker 内存在 订阅服务器消息 的机制：registration.pushManager.getSubscription().then(/\* ... \*/);
* 用户订阅服务之后，为了让 PWA 接收到服务器推送的消息，我们需要在 Service Worker 文件里面监听 push 事件
* self.addEventListener("push", function (e) {  /\* ... \*/ });
* 这个技术还处在非常初级的阶段
* 从服务端角度来看，出于安全的目的，整个过程必须使用非对称加密技术进行加密
* VAPID 可以给应用提供一层额外的安全保护

## 四、Service Worker 缓存策略

我们接下来了解一下关于 Service Worker 处理请求的缓存策略，选择正确的缓存策略取决于尝试缓存的资源类型以及以后对于资源的应用。

### 1. 仅缓存

![](https://pic3.zhimg.com/80/v2-8cb5d2c0eb9def55ded8c345c47131c6\_720w.jpg)

直接使用 Cache 缓存的结果，并将结果返回给客户端，这种策略比较适合一上线就不会变的静态资源请求

```js
self.addEventListener('fetch', function(event) {
  // 如果在缓存中没有找到这个资源，将会报连接错误
  event.respondWith(caches.match(event.request));
});
```

### 2. 仅网络

![](https://pic3.zhimg.com/80/v2-4853e24fdebc62533ac1fe20913b3f66\_720w.jpg)

直接强制使用正常的网络请求，并将结果返回给客户端，这种策略比较适合对实时性要求非常高的请求。

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(fetch(event.request));
  // 直接请求网络
});
```

### 3. 缓存优先、使用缓存、失败回退到网络

![](https://pic1.zhimg.com/80/v2-5fd0cc2028b0b5962e6f2ee4ac0b3e98\_720w.jpg)

如果您以离线优先的方式进行构建，这将是您处理大多数请求的方式。

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request);
    })
  );
});
```

### 4. 网络优先、使用网络、失败回退到缓存

![](https://pic3.zhimg.com/80/v2-03cf3c058e1ba5b86ab3aa0543c665a2\_720w.jpg)

适用于频繁更新的资源。

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    fetch(event.request).catch(function() {
      return caches.match(event.request);
    })
  );
});
```

### 5. Stale-while-revalidate 有可用的缓存版本，使用该版本，并且更新缓存。

![](https://pic1.zhimg.com/80/v2-c7a683dbffddf284c260265caf03ed88\_720w.jpg)

适合频繁更新最新版本并非必需的资源，例如头像。

### 6. 缓存和网络竞态

![](https://pic3.zhimg.com/80/v2-d501482a93416643a6b8802ef02294ee\_720w.jpg)

适合小型资源，可用于改善磁盘访问缓慢的设备的性能。

```js
function promiseAny(promises) {
  return new Promise((resolve, reject) => {
    promises = promises.map(p => Promise.resolve(p));
    promises.forEach(p => p.then(resolve));
    promises.reduce((a, b) => a.catch(() => b))
      .catch(() => reject(Error("All failed")));
  });
};

self.addEventListener('fetch', function(event) {
  event.respondWith(
    promiseAny([
      caches.match(event.request),
      fetch(event.request)
    ])
  );
});
```

### 7. 优先使用缓存然后访问网络

![](https://pic3.zhimg.com/80/v2-f5b2a442c9b909ce3a7c9a74b9b94f1e\_720w.jpg)

适用频繁更新的内容,例如，文章、社交媒体、游戏排行榜。这需要页面进行两次请求，一次是请求缓存，另一次是请求访问网络。该方法是首先显示缓存的数据，然后在网络数据到达时更新页面。 ServiceWorker 中的代码：

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.open('cache-v1').then(function(cache) {
      return fetch(event.request).then(function(response) {
        cache.put(event.request, response.clone());
        return response;
      });
    })
  );
});
```

页面中的代码：

```js
let networkDataReceived = false;

// 获取最新数据
let networkUpdate = fetch('/data.json').then(function(response) {
  return response.json();
}).then(function(data) {
  networkDataReceived = true;
  updatePage();
});

// 获取缓存数据
caches.match('/data.json').then(function(response) {
  if (!response) throw Error("No data");
  return response.json();
}).then(function(data) {
  // 如果网络数据已经返回则不会用缓存数据覆盖
  if (!networkDataReceived) {
    updatePage(data);
  }
}).catch(function() {
  return networkUpdate;
}).catch(showErrorMessage).then(stopSpinner);
```

## 五、PWA 发展趋势

* PWA 作为一个 2016 年才落地的新技术，经过四年的发展，基于 Chromium 的浏览器 Chrome 和 Opera 已经完全支持 PWA 了
* 随着 iOS 11.3 的发布，iOS 正式开始支持 PWA
* Windows Edge 也支持 PWA 了
* 越来越多的游览器大厂，对 PWA 做出了支持和优化

### 1. PWA 优势 <a href="#3.1-20pwa-20-e4-bc-98-e5-8a-bf" id="3.1-20pwa-20-e4-bc-98-e5-8a-bf"></a>

* 无需安装下载，只要访问一次他的网址，将其添加到设备桌面，就可持续使用
* 发布不需要提交到 app 商店审核，更新迭代版本也不需要审核
* 现有的 web 网页都能改进为 PWA， 能很快上线，实现业务、获取流量
* 不需要开发 Android 和 IOS 两套不同的版本

### 2. PWA 劣势 <a href="#3.2-20pwa-20-e5-8a-a3-e5-8a-bf" id="3.2-20pwa-20-e5-8a-a3-e5-8a-bf"></a>

* 浏览器对 PWA 的技术支持还不够全面， 没有任何览器能 100% 支持所有 PWA
* 需要通过第三方库才能调用底层硬件（如摄像头）
* 国内某些手机在 Android 系统上屏蔽了 PWA

## 六、参考

* [PWA 概念及核心功能的基本介绍](https://www.dounaite.com/article/627386b2ac359fc9131d9b6b.html)
* [渐进式网页应用(pwa)介绍](https://zhuanlan.zhihu.com/p/96934736)
* [渐进式 Web 应用（PWA）- MDN](https://developer.mozilla.org/zh-CN/docs/Web/Progressive\_web\_apps)
* [请你实现一个 PWA](https://juejin.cn/post/6844904052166230030)
