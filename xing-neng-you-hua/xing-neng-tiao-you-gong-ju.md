# 性能调优工具

## 一、引言

我们需要借助一些工具来分析网站的各方面性能以及性能瓶颈从而针对性的做一些优化措施。

## 二、Network

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62898f76c242450eb318b1816428ff65\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

这里可以看到资源加载详情，初步评估影响`页面性能`的因素。鼠标右键可以自定义选项卡，页面底部是当前加载资源的一个概览。`DOMContentLoaded` DOM渲染完成的时间，`Load`：当前页面所有资源加载完成的时间

**shift + cmd + P 调出控制台的扩展工具，添加规则**&#x20;

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a5141f1221f0494a93ace014e64b20ba\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**扩展工具更多使用姿势**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/578d7c1aec9b4ea2ae1d17e4ceef6857\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**瀑布流waterfall**

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1613fbeaffb64587a36613271ecfcade\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

* `Queueing` 浏览器将资源放入队列时间
* `Stalled` 因放入队列时间而发生的停滞时间
* `DNS Lookup` DNS解析时间
* `Initial connection` 建立HTTP连接的时间
* `SSL` 浏览器与服务器建立安全性连接的时间
* `TTFB` 等待服务端返回数据的时间
* `Content Download` 浏览器下载资源的时间

## 三、Lighthouse

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ce06248190e44dd4b043b168e596251f\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

* `First Contentful Paint` 首屏渲染时间，1s以内绿色
* `Speed Index` 速度指数，4s以内绿色
* `Time to Interactive` 到页面可交换的时间

根据 chrome 的一些策略自动对网站做一个质量评估，并且会给出一些优化的建议。

## 四、Peformance

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/56fbfc64c756487c9ae61c9f59de9add\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

对网站最专业的分析

## 五、webPageTest

可以模拟不同场景下访问的情况，比如模拟不同浏览器、不同国家等等，在线测试地址：[webPageTest](https://link.juejin.cn/?target=https%3A%2F%2Fwww.webpagetest.org%2F)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1da6a84b5944311a4f8c6f78b2f5fa1\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) ![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c2fd7f9c05be47bb80ad67064fc72917\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 六、资源打包分析

**webpack-bundle-analyzer**

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1575bf78f22243bcb393b8dbc23d395b\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```javascript
npm install --save-dev webpack-bundle-analyzer
// webpack.config.js 文件
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
module.exports={
  plugins: [
    new BundleAnalyzerPlugin({
          analyzerMode: 'server',
          analyzerHost: '127.0.0.1',
          analyzerPort: 8889,
          reportFilename: 'report.html',
          defaultSizes: 'parsed',
          openAnalyzer: true,
          generateStatsFile: false,
          statsFilename: 'stats.json',
          statsOptions: null,
          logLevel: 'info'
        }),
  ]
}

// package.json
"analyz": "NODE_ENV=production npm_config_report=true npm run build"

```

**开启source-map**

`webpack.config.js`

```javascript
module.exports = {
    mode: 'production',
    devtool: 'hidden-source-map',
}
```

`package.json`

```json
"analyze": "source-map-explorer 'build/*.js'",
```

`npm run analyze`&#x20;

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2057c48588f942579c235925d943c162\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 七、参考

* [聊一聊前端性能优化](https://juejin.cn/post/6911472693405548557)
