# 异步加载

## 一、引言

在 HTML 中会遇到以下三类 script：

* `<script src='xxx'></script>`
* `<script src='xxx' async></script>`
* `<script src='xxx' defer></script>`

那么这三类 script 有什么区别呢？

## 二、script

浏览器在解析 HTML 的时候，如果遇到一个没有任何属性的 script 标签，就会暂停解析，先发送网络请求获取该 JS 脚本的代码内容，然后让 JS 引擎执行该代码，当代码执行完毕后恢复解析。整个过程如下图所示：

![script](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/caf2f618530046658ab8e3b4a8699589\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

可以看到，script 阻塞了浏览器对 HTML 的解析，如果获取 JS 脚本的网络请求迟迟得不到响应，或者 JS 脚本执行时间过长，都会导致白屏，用户看不到页面内容。

## 三、async script

async 表示异步，当浏览器遇到带有 async 属性的 script 时，请求该脚本的网络请求是异步的，不会阻塞浏览器解析 HTML，一旦网络请求回来之后，如果此时 HTML 还没有解析完，浏览器会暂停解析，先让 JS 引擎执行代码，执行完毕后再进行解析，图示如下：

![async script](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/021b5dbeddb64db0a7099dc0a4dd076d\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

当然，如果在 JS 脚本请求回来之前，HTML 已经解析完毕了，那就啥事没有，立即执行 JS 代码，如下图所示：

![async script](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4e5a89a4a1fe49ed9d5acaf25ef9aadd\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

所以 async 是不可控的，因为执行时间不确定，你如果在异步 JS 脚本中获取某个 DOM 元素，有可能获取到也有可能获取不到。

当有多个 async 脚本存在时，它们的执行顺序是不确定的。因此确保两者之间互不依赖非常重要。

异步脚本一定会在页面的 **load 事件之前执行**，但可能在 **DOMContentLoaded 事件触发前后执行**。

## 四、defer script

defer 表示延迟，当浏览器遇到带有 defer 属性的 script 时，获取该脚本的网络请求也是异步的，不会阻塞浏览器解析 HTML，一旦网络请求回来之后，如果此时 HTML 还没有解析完，浏览器不会暂停解析并执行 JS 代码，而是等待 HTML 解析完毕再执行 JS 代码，图示如下：

![defer script](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8313e4787f04c79838fec9961bda0fb\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

HTML5 规范**要求**脚本按照他们出现的顺序的先后顺序执行，因此第一个延迟脚本会先于第二个延迟脚本执行，而这两个脚本会**先于DOMContentLoaded事件执行**。

而在现实中，延迟脚本不一定按照顺序执行，也**不一定在 DOMContentLoaded 事件触发前执行**，因此最好只包含一个延迟脚本。

## 五、比较

最后，根据上面的分析，不同类型 script 的执行顺序及其是否阻塞解析 HTML 总结如下：

| script 标签        | JS 执行顺序     | 是否阻塞解析 HTML |
| ---------------- | ----------- | ----------- |
| `<script>`       | 在 HTML 中的顺序 | 阻塞          |
| `<script async>` | 网络请求返回顺序    | 可能阻塞，也可能不阻塞 |
| `<script defer>` | 在 HTML 中的顺序 | 不阻塞         |

> * DOMContentLoaded：在形成完整的DOM树就会触发，不会理会图像、JavaScript文件、css文件或其他资源文件是否已经下载完毕。
> * load：页面中的一切都加载完毕后触发，包括图像、JavaScript文件、css文件及其他资源文件。

## 六、参考

* [https://juejin.cn/post/6894629999215640583](https://juejin.cn/post/6894629999215640583)
* [https://juejin.cn/post/7033422383054585887](https://juejin.cn/post/7033422383054585887)
