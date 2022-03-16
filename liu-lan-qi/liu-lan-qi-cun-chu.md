# 浏览器存储

## 一、引言

Cookie, LocalStorage, SessionStorage, IndexedDB 都是客户端存储的解决方案。其中 LocalStorage 和SessionStorage 是 **HTML5 Web Storage** API 新加入的特性，这个特性主要是用来作为本地存储来使用的，解决了 cookie 存储空间不足的问题。并且还带来了以下好处：

* 减少网络流量：一旦数据保存在本地后，就可以避免再向服务器请求数据，因此减少不必要的数据请求，减少数据在浏览器和服务器间不必要地来回传递。快速显示数据。
* 性能好：从本地读数据比通过网络从服务器获得数据快得多，本地数据可以即时获得。再加上网页本身也可以有缓存，因此整个页面和数据都在本地的话，可以立即显示。
* 临时存储：很多时候数据只需要在用户浏览一组页面期间使用，关闭窗口后数据就可以丢弃了，这种情况使用SessionStorage 非常方便。

## 二、Cookie <a href="#89cc08ef" id="89cc08ef"></a>

Cookie 是服务器委托浏览器存储的一些数据，它让服务器有了“记忆能力”，它会在浏览器下次向服务器再 发起请求时被携带并发送到服务器上。

### 1. Cookie 的工作过程

会用到两个字段：响应头字段 Set-Cookie 和请求头字段 Cookie。

* 响应报文使用 Set-Cookie 字段发送“key=value”形式的 Cookie 值；
* 请求报文里用 Cookie 字段发送多个 Cookie 值；

### 2. Cookie 的属性

#### Cookie 的生存周期

可以使用 Expires 和 Max-Age 两个属性来设置

* Expires 即过期时间，用的是绝对时间点，可以理解为“截止日期”（deadline）；
* Max-Age 用的是相对时间，单位是秒，浏览器用收到报文的时间点再加上 Max-Age，就可以得到失效的绝对时间；
* Expires 和 Max-Age 可以同时出现，浏览器会优先采用 Max-Age 计算失效期；

#### Cookie 的作用域

让浏览器仅发送给特定的服务器和 URI，避免被其他网站盗用。

* Domain 和 Path 指定了 Cookie 所属的域名和路径，浏览器在发送 Cookie 前会从 URI 中提取出 host 和 path 部分，对比 Cookie 的属性；
* 如果不满足条件，就不会在请求头里发送 Cookie；

#### Cookie 的安全性

尽量不要让服务器以外的人看到。

| Value    | 如果用于保存用户登录态，应该将该值加密，不能使用明文的用户标识                                 |
| -------- | --------------------------------------------------------------- |
| HttpOnly | Cookie 只能通过浏览器 HTTP 协议传输，禁止其他方式访问，比如不能通过 JS 访问 Cookie，减少 XSS 攻击 |
| Secure   | 只能在协议为 HTTPS 的请求中携带                                             |
| SameSite | 规定浏览器不能在跨域请求中携带 Cookie，减少 CSRF 攻击                               |

### 3. Cookie 的应用

* 身份识别：Cookie 最基本的一个用途就是身份识别，保存用户的登录信息，实现会话事务；
* 广告跟踪：广告商网站（例如 Google），它会“偷偷地”给你贴上 Cookie 小纸条，这样你上其他的网站，别的广告就能用 Cookie 读出你的身份，然后做行为分析，再推给你广告；

### 4. Cookie 的特点

* 数据生命周期：一般由服务器生成，可以设置过期时间；
* 数据存储大小：Cookie 的大小受限，一般为 4 KB；
* 与服务端通信：每次发起同域下的 HTTP 请求时，在 header 中都会携带当前域名下的 Cookie ，会影响请求的性能；

> 若不设置 Cookie 的过期时间，则表示这个 Cookie 的生命周期为浏览器会话期间，关闭浏览器窗口，Cookie 就会消失。这种生命周期为浏览器会话期的 Cookie 被称之会话 Cookie。通常会话 Cookie 不存储在硬盘而是保存在内存里。

### 5. Cookie 的使用 <a href="#1b8014fa" id="1b8014fa"></a>

```javascript
// 读取网站下所有的 cookie 信息，获取的结果是一个以分号`;`作为分割的一个字符串
let allCookies = document.cookie;

// 往原来的已经存在的 cookie 中加入新的 cookie
document.cookie ="name=vincent";

// 还可以在后面加上可选择的选项键值对，如 domain、path、expires
document.cookie="name=vincent;domain=.test.com"

// 删除cookie，就是让这个 cookie 的 expires 过期，即设置这个 expires 的值为 0
document.cookie="name=vincent;domain=.test.com;expires=0");
```

## 三、 LocalStorage <a href="#3d2c07d1" id="3d2c07d1"></a>

LocalStorage 主要用于**持久化的本地存储**，它是采用键值对的方式存储数据，按域名将数据分别保存到对应数据库文件里。除非主动删除数据，否则数据是永远不会过期的。

### 1. LocalStorage 的特点

* 通常在浏览器中支持的LocalStorage 的大小是5M。
* 目前所有的浏览器中都会把 localStorage  的值类型限定为 string  类型，这个在对我们日常比较常见的 JSON 对象类型需要一些转换。
* LocalStorage  在浏览器的隐私模式下面是不可读取的。
* LocalStorage  本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡。
* LocalStorage  不能被爬虫抓取到。

### 2. LocalStorage 的使用

```javascript
// setItem() 设置属性
localStorage.setItem('name', 'vincent');

// getItem() 获取数据
let name = localStorage.getItem('name');

// removeItem() 移除某个属性
localStorage.removeItem('name');

// clear() 移除所有数据项
localStorage.clear();
```

## 四、SessionStorage <a href="#d9b769d9" id="d9b769d9"></a>

SessionStorage 与 LocalStorage 的接口类似，但保存数据的生命周期与 LocalStorage 不同。SessionStorage 主要用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此 SessionStorage 不是一种持久化的本地存储，仅仅是**会话级别的存储**。也就是说只要这个浏览器窗口没有关闭，即使刷新页面或进入同源另一页面，数据仍然存在。关闭窗口后，SessionStorage 即被销毁。

### 1. SessionStorage 的特点

* 同源策略限制。若想在不同页面之间对同一个 SessionStorage 进行操作，这些页面必须在同一协议、同一主机名和同一端口下。
* **单标签页**限制。SessionStorage 操作限制在单个标签页中，在此标签页进行同源页面访问都可以共享SessionStorage 数据。
* 只在本地存储。SeesionStorage 的数据不会跟随 HTTP 请求一起发送到服务器，只会在本地生效，并在关闭标签页后清除数据。(若使用 Chrome 的恢复标签页功能，seesionStorage 的数据也会恢复)。
* 存储方式。SeesionStorage 的存储方式采用 key、value 的方式。value 的值必须为字符串类型(传入非字符串，也会在存储时转换为字符串。true 值会转换为 "true")。
* 存储上限限制。不同的浏览器存储的上限也不一样，但大多数浏览器把上限限制在**5MB以下**。

### 2. SessionStorage 的使用

sessionStorage 与 localStorage 拥有统一的 API 接口。

```javascript
// setItem() 设置属性
sessionStorage.setItem('name', 'vincent');

// getItem() 获取数据
let name = sessionStorage.getItem('name');

// removeItem() 移除某个属性
sessionStorage.removeItem('name');

// clear() 移除所有数据项
sessionStorage.clear();
```

## 五、IndexedDB <a href="#ed4474dc" id="ed4474dc"></a>

indexedDB 是 NoSQL 数据库，是一种支持事务的浏览器数据库，用于客户端存储大量结构化数据，包括文件、二进制大型对象。&#x20;

### 1. IndexedDB 的特点

* 数据生命周期：除非被清理，否则会一直存在；
* 数据存储大小：无限制；
* 与服务端通信：不与服务端进行通信；

### 2. IndexedDB 的使用

```javascript
// 使用 indexedDB.open() 方法创建或打开数据库
var request = window.indexedDB.open(dbName, version); // 数据库名 版本号
// error 事件表示打开数据库失败
request.onerror = function (event) {
  console.log('创建或打开数据库失败');
};
// success 事件表示成功打开数据库
var db;
request.onsuccess = function (event) {
  db = request.result;
  console.log('创建或打开数据库成功');
};
```

## 六、比较 <a href="#ed4474dc" id="ed4474dc"></a>

### 1. 相同点 <a href="#6968204c" id="6968204c"></a>

它们三者的共同点是都**保存在客户端**，并且受**同源策略**限制（URL的协议、端口、主机名是相同的）。

### 2. 不同点 <a href="#6373b223" id="6373b223"></a>

| 特性      | Cookie                                 | LocalStorage                          | SessionStorage                        |
| ------- | -------------------------------------- | ------------------------------------- | ------------------------------------- |
| 数据的生命期  | 可设置失效时间，默认是关闭浏览器后失效                    | 除非手动清除，否则永久保存                         | 仅在当前会话下有效，关闭页面或浏览器后被清除                |
| 存放数据大小  | 4KB左右                                  | 一般为5MB                                | 一般为5MB                                |
| 与服务器端通信 | 每次都会携带在HTTP头中，如果使用Cookie 保存过多数据会带来性能问题 | 仅在客户端（即浏览器）中保存，不参与和服务器的通信             | 仅在客户端（即浏览器）中保存，不参与和服务器的通信             |
| 作用域     | 在所有同源窗口中共享                             | 在所有同源窗口中共享                            | 仅在单个标签页内有效                            |
| 易用性     | 源生的Cookie 接口不友好，需要程序员自己封装              | 源生接口可以接受，亦可再次封装来对Object 和Array 有更好的支持 | 源生接口可以接受，亦可再次封装来对Object 和Array 有更好的支持 |

### 3. 应用场景 <a href="#73d82126" id="73d82126"></a>

现在已经不建议使用 Cookie 来存储数据，当没有大量数据存储需求可以用 localStorage 和 sessionStorage，不怎么改变的数据用 localStorage，否则的话用 sessionStorage；而当有大量数据存储需求时可以使用 indexedDB。

* Cookie 一般用来保存用户登录后的信息，例如sessionId等。
* LocalStorage 一般用来在同源策略下的不同标签页之间进行通信。
* SessionStorage 非常适合SPA(单页应用程序)，可以方便在各业务模块进行传值。

## 七、参考 <a href="#01238d4e" id="01238d4e"></a>

* [https://www.cnblogs.com/jerryzou/p/4472670.html](https://www.cnblogs.com/jerryzou/p/4472670.html)
* [https://www.cnblogs.com/ke-nan/p/7092663.html](https://www.cnblogs.com/ke-nan/p/7092663.html)
* [https://blog.csdn.net/lq15310444798/article/details/79109026](https://blog.csdn.net/lq15310444798/article/details/79109026)
* [https://www.cnblogs.com/polk6/p/5512979.html](https://www.cnblogs.com/polk6/p/5512979.html)
* [https://www.cnblogs.com/st-leslie/p/5617130.html](https://www.cnblogs.com/st-leslie/p/5617130.html)
* [https://juejin.cn/post/6974052211395887141](https://juejin.cn/post/6974052211395887141)
