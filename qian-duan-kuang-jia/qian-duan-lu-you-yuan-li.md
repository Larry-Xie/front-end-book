# 前端路由原理

## 一、引言

路由（routing）就是通过互联的网络把信息从源地址传输到目的地址的活动。&#x20;

对于 Web 开发来说，路由的实质是 URL 到对应的处理程序的映射。Web 路由既可以由服务端，也可以由前端实现。其中前端路由根据实现方式的不同，可以分为 **Hash 路由** 和 **History 路由**。

前端路由对于服务端路由来说，最显著的特点是页面可以在无刷新的情况下进行页面的切换。基于前端路由的这一特点，诞生了一种**无刷新**的单页应用开发模式 **SPA**。SPA 通过前端路由避免了页面的切换打断用户体验，让 Web 应用的体验更接近一个桌面应用程序。

## 二、Hash 路由[​](https://febook.hzfe.org/awesome-interview/book4/browser-router#1-hash-%E8%B7%AF%E7%94%B1) <a href="#1hash-lu-you" id="1hash-lu-you"></a>

一个 URI 的组成如下所示。其中的 fragment 部分就是 Hash 路由所读取的内容。

```
     foo://example.com:8042/over/there?name=ferret#nose
     \_/   \______________/\_________/ \_________/ \__/
      |           |            |            |        |
   scheme     authority       path        query   fragment
      |   _____________________|__
     / \ /                        \
     urn:example:animal:ferret:nose
```

fragment 本质是用来标识次级资源，fragment 有以下特点：

* 修改 fragment 的内容不会触发网页重载。
* 修改 fragment 的内容会改变浏览器的历史记录。
* 修改 fragment 的内容会触发浏览器的 onhashchange 事件。

基于 fragment 的以上特点，可实现基于 Hash 的前端路由。

### **1. 实现原理**[**​**](https://febook.hzfe.org/awesome-interview/book4/browser-router#11-%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86)

我们可以通过监听 hashchange 事件来监听页面 hash 的变化，通过解析 hash 的值来切换页面。示例如下：

```javascript
/**
 * 解析 hash
 * @param hash
 * @returns
 */
function parseHash(hash) {
  // 去除 # 号
  hash = hash.replace(/^#/, "");

  // 简单解析示例
  const parsed = hash.split("?");

  // 返回 hash 的 path 和 query
  return {
    pathname: parsed[0],
    search: parsed[1],
  };
}

/**
 * 监听 hash 变化
 * @returns
 */
function onHashChange() {
  // 解析 hash
  const { pathname, search } = parseHash(location.hash);

  // 切换页面内容
  switch (pathname) {
    case "/home":
      document.body.innerHTML = `Hello ${search}`;
      return;
    default:
      return;
  }
}

window.addEventListener("hashchange", onHashChange);
```

### **2. 示例**

`www.test.com/#/` 就是 Hash URL，当 `#` 后面的哈希值发生变化时，不会向服务器请求数据，可以通过 `hashchange` 事件来监听 URL 的变化，从而进行页面跳转。

![](http://qiuzi-blog.oss-cn-shenzhen.aliyuncs.com/qiuzi-website/2019-06-01-043729.png)

### **3. 优缺点**[**​**](https://febook.hzfe.org/awesome-interview/book4/browser-router#12-%E4%BC%98%E7%BC%BA%E7%82%B9)

Hash 路由由于通过监听 hash 变化实现，所以有以下优势和不足：

**优点**

1. 兼容性最佳。
2. 无需服务端配置。

**缺点**

1. 服务端无法获取 hash 部分内容。
2. 可能和锚点功能冲突。
3. SEO 不友好。

## 三、History 路由[​](https://febook.hzfe.org/awesome-interview/book4/browser-router#2-history-%E8%B7%AF%E7%94%B1) <a href="#2history-lu-you" id="2history-lu-you"></a>

Hash 路由是一个相对“Hack”的方式，利用了 fragment 来实现路由功能。而 History 路由则是通过浏览器原生提供的操作 History 的能力来实现的路由功能。

### **1. 实现原理**[**​**](https://febook.hzfe.org/awesome-interview/book4/browser-router#21-%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86)

History 路由核心主要依赖 History API 里的两个方法和一个事件，其中两个方法用于操作浏览器的历史记录，事件用于监听历史记录的切换：

**方法**

* history.pushState：将给定的 Data 添加到当前标签页的历史记录栈中。
* history.replaceState：将给定的 Data 更新到历史记录栈中最新的一条记录中。

**事件**

* popstate：监听历史记录的变化。

通过以上 API 即可实现一个前端路由，示例如下：

```javascript
/**
 * 监听 history 变化
 * @returns
 */
function onHistoryChange() {
  // 解析 location
  const { pathname, search } = location;

  // 根据页面不同执行不同内容
  switch (pathname) {
    case "/home":
      document.body.innerHTML = `Hello ${search.replace(/^\?/, "")}`;
      return;
    default:
      document.body.innerHTML = `Hello World`;
      return;
  }
}

/**
 * 页面跳转
 * @returns
 */
function pushState(target) {
  history.pushState(null, "", target);
  onHistoryChange();
}

// 3 秒后路由跳转
setTimeout(() => {
  pushState("/home?name=HZFEStudio");
}, 3000);

// 6 秒后返回
setTimeout(() => {
  history.back();
}, 6000);

window.addEventListener("popstate", onHistoryChange);
```

### **2. 示例**

![](http://qiuzi-blog.oss-cn-shenzhen.aliyuncs.com/qiuzi-website/2019-06-01-043731.png)

### **3. 优缺点**[**​**](https://febook.hzfe.org/awesome-interview/book4/browser-router#22-%E4%BC%98%E7%BC%BA%E7%82%B9)

History 路由由于通过 History API 实现，所以有以下优势和不足：

**优点**

1. 服务端可获取完整的链接和参数。
2. 前端监控友好。
3. SEO 相对 Hash 路由友好。

**缺点**

1. 兼容性稍弱。
2. 需要服务端额外配置（各 path 均指向同一个 HTML）。

## 四、前端路由的优缺点[​](https://febook.hzfe.org/awesome-interview/book4/browser-router#3-%E5%89%8D%E7%AB%AF%E8%B7%AF%E7%94%B1%E7%9A%84%E4%BC%98%E7%BC%BA%E7%82%B9) <a href="#3-qian-duan-lu-you-de-you-que-dian" id="3-qian-duan-lu-you-de-you-que-dian"></a>

前端路由是前后端分离的开发模式的产物，对比服务端路由，前端路由的实现方式有以下优势和不足：

### **1. 优点**

1. 无刷新切换内容，用户体验更佳。
2. 减轻服务端压力。

### **2. 缺点**

1. 初次加载耗时长。
2. SEO 效果不佳。

## 五、参考

* [前端路由实现](https://febook.hzfe.org/awesome-interview/book4/browser-router)
* [框架通识 - 路由原理](https://www.qiuzi.fun/front-end/framework.html#%E8%B7%AF%E7%94%B1%E5%8E%9F%E7%90%86)
