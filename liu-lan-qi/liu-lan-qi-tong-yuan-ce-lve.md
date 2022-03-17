# 浏览器同源策略

## 一、引言 <a href="#sn0atr" id="sn0atr"></a>

1995年，同源政策由 Netscape 公司引入浏览器。目前，所有浏览器都实行这个政策。它是浏览器最核心也是最基本的安全功能，如果缺少了同源策略，浏览器的正常功能可能都会受到影响。可以说 Web 是构建在同源策略的基础之上的，浏览器只是针对同源策略的一种实现。

浏览器的**同源策略**，限制了来自不同源的"document"或脚本，对当前"document"读取或设置某些属性。从一个域上加载的脚本不允许访问另外一个域的文档属性。

同源策略的"**同源**"指的是"三个相同"：

* 协议相同
* 域名相同
* 端口相同

在浏览器中，\<script>、\<img>、\<iframe>、\<link>等标签都可以加载跨域资源，而不受同源限制，但浏览器限制了 JavaScript 的权限使其不能读、写加载的内容。

## **二、跨域场景**

常见的跨域场景如下：

| URL                                                              | 说明          | 是否允许通信                 |
| ---------------------------------------------------------------- | ----------- | ---------------------- |
| <p>http://www.a.com/a.js<br>http://www.a.com/b.js</p>            | 同一域名下，不同文件  | 允许                     |
| <p>http://www.a.com/lab/a.js<br>http://www.a.com/script/b.js</p> | 同一域名下，不同文件夹 | 允许                     |
| <p>http://www.a.com:8000/a.js<br>http://www.a.com/b.js</p>       | 同一域名，不同端口   | 不允许                    |
| <p>http://www.a.com/a.js<br>https://www.a.com/b.js</p>           | 同一域名，不同协议   | 不允许                    |
| <p>http://www.a.com/a.js<br>http://70.32.92.74/b.js</p>          | 域名和域名对应ip   | 不允许                    |
| <p>http://www.a.com/a.js<br>http://script.a.com/b.js</p>         | 主域相同，子域不同   | 不允许（cookie这种情况下也不允许访问） |
| <p>http://www.cnblogs.com/a.js<br>http://www.a.com/b.js</p>      | 不同域名        | 不允许                    |

## 三、跨域限制

随着互联网的发展，"同源政策"越来越严格。目前，如果非同源，共有三种行为受到限制。

* **Cookie 限制**：不同源的两个网站无法拿到对方的 Cookie、LocalStorage 和 IndexDB 的内容。
* **DOM 限制**：不同源的两个网页无法拿到对方的 DOM。
* **AJAX 限制**：AJAX 请求只能发给同源的网址，否则就报错。

虽然这些限·1制是必要的，但是有时很不方便，合理的用途也受到影响。所以就会需要合适的跨域方法。

广义的跨域是指一个域下的文档或脚本试图去请求另一个域下的资源，而我们通常所说的跨域是狭义的，是指由**浏览器同源策略限制的一类请求场景**。 跨域的目的是为了突破同源策略的限制，所以可以根据同源策略的三种限制把跨域方法分为三大类。

## 四、Cookie 跨域

两个不同源的网站是无法拿到对方的 Cookie 和 LocalStorage 的内容，但是可以通过如下方法来突破限制：

* 对于**Cookie**，**只有在两个网页的一级域名相同**的情况下才能通过 **`document.domain` ** 来共享Cookie；
* 对于**LocalStorage** 和 IndexDB，无法通过 `document.domain` 来规避同源政策，需要**PostMessage** API；

### 1. Document.domain

Cookie 是服务器写入浏览器的一小段信息，只有同源的网页才能共享。但是，两个网页一级域名相同，只是二级域名不同，浏览器允许通过设置`document.domain`共享 Cookie。

举例来说，A网页是`http://w1.example.com/a.html`，B网页是`http://w2.example.com/b.html`，那么只要设置相同的`document.domain`，两个网页就可以共享Cookie。

```javascript
document.domain = 'example.com';
```

现在，A网页通过脚本设置一个 Cookie。

```javascript
document.cookie = "test1=hello";
```

B网页就可以读到这个 Cookie。

```javascript
var allCookie = document.cookie;
```

> 注意，**这种方法只适用于 Cookie 和 iframe 窗口**，**LocalStorage 和 IndexDB 无法通过这种方法规避同源政策，而要使用PostMessage API**。

> 这种解决跨域的方式即将[被 Chrome 给禁用](https://developer.chrome.com/blog/immutable-document-domain/)，以后也**避免使用**。

另外，**服务器也可以在设置Cookie的时候，指定Cookie的所属域名为一级域名**，比如`.example.com`。

```javascript
Set-Cookie: key=value; domain=.example.com; path=/
```

这样的话，二级域名和三级域名不用做任何设置，都可以读取这个Cookie。

### 2. Window.postMessage

通过`window.postMessage`，读写其他窗口的 LocalStorage 也成为了可能。

下面是一个例子，主窗口写入iframe子窗口的`localStorage`。

```javascript
window.onmessage = function(e) {
  if (e.origin !== 'http://bbb.com') {
    return;
  }
  var payload = JSON.parse(e.data);
  localStorage.setItem(payload.key, JSON.stringify(payload.data));
};
```

上面代码中，子窗口将父窗口发来的消息，写入自己的LocalStorage。

父窗口发送消息的代码如下。

```javascript
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
win.postMessage(JSON.stringify({key: 'storage', data: obj}), 'http://bbb.com');
```

加强版的子窗口接收消息的代码如下。

```javascript
window.onmessage = function(e) {
  if (e.origin !== 'http://bbb.com') return;
  var payload = JSON.parse(e.data);
  switch (payload.method) {
    case 'set':
      localStorage.setItem(payload.key, JSON.stringify(payload.data));
      break;
    case 'get':
      var parent = window.parent;
      var data = localStorage.getItem(payload.key);
      parent.postMessage(data, 'http://aaa.com');
      break;
    case 'remove':
      localStorage.removeItem(payload.key);
      break;
  }
};
```

加强版的父窗口发送消息代码如下。

```javascript
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
// 存入对象
win.postMessage(JSON.stringify({key: 'storage', method: 'set', data: obj}), 'http://bbb.com');
// 读取对象
win.postMessage(JSON.stringify({key: 'storage', method: "get"}), "*");
window.onmessage = function(e) {
  if (e.origin != 'http://aaa.com') return;
  // "Jack"
  console.log(JSON.parse(e.data).name);
};
```

## 五、DOM 跨域

如果两个网页不同源，就无法拿到对方的DOM。典型的例子是`iframe`窗口和`window.open`方法打开的窗口，它们与父窗口无法通信。

比如，父窗口运行下面的命令，如果`iframe`窗口不是同源，就会报错。

```javascript
document.getElementById("myIFrame").contentWindow.document
// Uncaught DOMException: Blocked a frame from accessing a cross-origin frame.
```

上面命令中，父窗口想获取子窗口的DOM，因为跨域导致报错。

反之亦然，子窗口获取主窗口的DOM也会报错。

```javascript
window.parent.document.body
// 报错
```

如果两个窗口一级域名相同，只是二级域名不同，那么设置上一节介绍的`document.domain`属性，就可以规避同源政策，拿到DOM。

对于完全不同源的网站，目前有三种方法，可以解决跨域窗口的通信问题。

* Location.hash
* Window.name
* Window.postMessage

### 1. Location.hash

片段标识符（fragment identifier）指的是，URL的`#`号后面的部分，比如`http://example.com/x.html#fragment`的`#fragment`。如果只是改变片段标识符，页面不会重新刷新。而片段标识符可以通过Location.hash来获取和修改。

父窗口可以把信息，写入子窗口的片段标识符。

```javascript
var src = originURL + '#' + data;
document.getElementById('myIFrame').src = src;
```

子窗口通过监听`hashchange`事件得到通知。

```javascript
window.onhashchange = checkMessage;

function checkMessage() {
  var message = window.location.hash;
  // ...
}
```

同样的，子窗口也可以改变父窗口的片段标识符。

```javascript
parent.location.href= target + "#" + hash;
```

在 iframe 里通过互相修改对方的片段标志符来实现跨域通信。

### 2. Window.name

浏览器窗口有`window.name`属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。

父窗口先打开一个子窗口，载入一个不同源的网页，该网页将信息写入`window.name`属性。

```javascript
window.name = data;
```

接着，子窗口跳回一个与主窗口同域的网址。

```javascript
location = 'http://parent.url.com/xxx.html';
```

然后，主窗口就可以读取子窗口的`window.name`了。

```javascript
var data = document.getElementById('myFrame').contentWindow.name;
```

这种方法的优点是，`window.name`容量很大，可以放置非常长的字符串；缺点是必须监听子窗口`window.name`属性的变化，影响网页性能。

### 3. Window.postMessage

上面两种方法都属于破解，HTML5 为了解决这个问题，引入了一个全新的API：**跨文档通信 API**（Cross-document messaging）。这个API为 `window`对象新增了一个`window.postMessage`方法，允许跨窗口通信，不论这两个窗口是否同源。它可用于解决以下方面的问题：

* 页面和其打开的新窗口的数据传递
* 多窗口之间消息传递
* 页面与嵌套的 iframe 消息传递
* 上面三个场景的跨域数据传递

用法：postMessage(data,origin) 方法接受两个参数

* data： html5规范支持任意基本类型或可复制的对象，但部分浏览器只支持字符串，所以传参时最好用JSON.stringify()序列化。
* origin： 协议+主机+端口号，也可以设置为"\*"，表示可以传递给任意窗口，如果要指定和当前窗口同源的话设置为"/"。

举例来说，父窗口`http://aaa.com`向子窗口`http://bbb.com`发消息，调用`postMessage`方法就可以了。

```javascript
var popup = window.open('http://bbb.com', 'title');
popup.postMessage('Hello World!', 'http://bbb.com');
```

`postMessage` 方法的第一个参数是具体的信息内容，第二个参数是接收消息的窗口的源（origin），即"协议 + 域名 + 端口"。也可以设为`*`，表示不限制域名，向所有窗口发送。

子窗口向父窗口发送消息的写法类似。

```javascript
window.opener.postMessage('Nice to see you', 'http://aaa.com');
```

父窗口和子窗口都可以通过`message`事件，监听对方的消息。

```javascript
window.addEventListener('message', function(e) {
  console.log(e.data);
},false);
```

`message`事件的事件对象`event`，提供以下三个属性。

* `event.source`：发送消息的窗口
* `event.origin`:  消息发向的网址
* `event.data`:  消息内容

下面的例子是，子窗口通过`event.source`属性引用父窗口，然后发送消息。

```javascript
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  event.source.postMessage('Nice to see you!', '*');
}
```

`event.origin` 属性可以过滤不是发给本窗口的消息。

```javascript
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  if (event.origin !== 'http://aaa.com') return;
  if (event.data === 'Hello World') {
      event.source.postMessage('Hello', event.origin);
  } else {
    console.log(event.data);
  }
}
```

## 六、AJAX 跨域

同源政策规定，AJAX 请求只能发给同源的网址，否则就报错。

除了可以架设**服务器代理**（浏览器请求同源服务器，再由后者请求外部服务），还有以下三种方法：

* JSONP
* Web Socket
* CORS

### 1. JSONP

JSONP是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。

它的基本思想是，网页通过动态添加一个`<script>`元素，向服务器请求JSON数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。

首先，网页动态插入`<script>`元素，由它向跨源网址发出请求。

```javascript
function addScriptTag(src) {
  var script = document.createElement('script');
  script.setAttribute("type","text/javascript");
  script.src = src;
  document.body.appendChild(script);
}

window.onload = function () {
  addScriptTag('http://example.com/ip?callback=foo');
}

function foo(data) {
  console.log('Your public IP address is: ' + data.ip);
};
```

上面代码通过动态添加`<script>`元素，向服务器`example.com`发出请求。注意，该请求的查询字符串有一个`callback`参数，用来指定回调函数的名字，这对于JSONP是必需的。

服务器收到这个请求以后，会将数据放在回调函数的参数位置返回。后端 NodeJS 代码示例：

```javascript
var querystring = require('querystring');
var http = require('http');
var server = http.createServer();

server.on('request', function(req, res) {
    var params = qs.parse(req.url.split('?')[1]);
    var fn = params.callback;

    // jsonp返回设置
    res.writeHead(200, { 'Content-Type': 'text/javascript' });
    res.write(fn + '(' + JSON.stringify(params) + ')');

    res.end();
});

server.listen('8080');
console.log('Server is running at port 8080...');
```

由于`<script>`元素请求的脚本，直接作为代码运行。这时，只要浏览器定义了`foo`函数，该函数就会立即调用。作为参数的 JSON 数据被视为 JavaScript 对象，而不是字符串，因此避免了使用`JSON.parse`的步骤。

> JSONP 的**缺点是只能实现get一种请求**。

### 2. Web Socket

WebSocket是一种通信协议，使用`ws://`（非加密）和`wss://`（加密）作为协议前缀。该协议不实行同源政策，只要服务器支持，就可以通过它进行跨源通信。

下面是一个例子，浏览器发出的 WebSocket 请求的头信息（摘自[维基百科](https://en.wikipedia.org/wiki/WebSocket)）。

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```

上面代码中，有一个字段是`Origin`，表示该请求的请求源（origin），即发自哪个域名。

正是因为有了`Origin`这个字段，所以 WebSocket 才没有实行同源政策。因为服务器可以根据这个字段，判断是否许可本次通信。如果该域名在白名单内，服务器就会做出如下回应。

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```

### 3. 代理服务器

![反向代理](<../.gitbook/assets/image (10) (1) (1).png>)

增加代理服务器，和 H5 资源服务器放在同一个域名下，接口请求全走代理服务器，这样就变成了同源访问，不存在跨域访问，因此就不会存在跨域的问题。

该方式中，所有发往目标服务器的数据，都会经过代理服务器，适用于同一个公司内部不同域名之间相互访问的情况。

使用此方式还需注意一点，应关注代理服务器的性能，代理服务器的性能应与后端的目标服务器的性能相匹配，否则代理服务器会成为整个系统的性能瓶颈。

### 4. CORS

CORS 是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，是跨源 AJAX 请求的根本解决方法。相比 JSONP 只能发`GET`请求，CORS 允许任何类型的请求。

**CORS 需要浏览器和服务器同时支持**。目前，所有浏览器都支持该功能，IE 浏览器不能低于 IE10。

整个 CORS 通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS 通信与同源的AJAX 通信没有差别，代码完全一样。浏览器一旦发现 AJAX 请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。

因此，实现CORS通信的**关键是服务器**。只要服务器实现了CORS接口，就可以跨域通信。

CORS 要分成简单请求和非简单请求来处理。

> 详情参阅阮一峰：[http://www.ruanyifeng.com/blog/2016/04/cors.html](http://www.ruanyifeng.com/blog/2016/04/cors.html)

> CORS 与 JSONP 的使用目的相同，但是比JSONP更强大。JSONP 只支持`GET`请求，CORS 支持所有类型的HTTP请求。JSONP 的优势在于支持老式浏览器，以及可以向不支持 CORS 的网站请求数据。

## 七、参考

* [http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)
* [https://segmentfault.com/a/1190000011145364](https://segmentfault.com/a/1190000011145364)
* [http://www.ruanyifeng.com/blog/2016/04/cors.html](http://www.ruanyifeng.com/blog/2016/04/cors.html)
