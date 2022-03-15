# 浏览器存储

## 一、引言

Cookie, LocalStorage, SessionStorage 都是客户端存储的解决方案。其中 LocalStorage 和SessionStorage 是 **HTML5 Web Storage** API 新加入的特性，这个特性主要是用来作为本地存储来使用的，解决了 cookie 存储空间不足的问题。并且还带来了以下好处：

* 减少网络流量：一旦数据保存在本地后，就可以避免再向服务器请求数据，因此减少不必要的数据请求，减少数据在浏览器和服务器间不必要地来回传递。快速显示数据。
* 性能好：从本地读数据比通过网络从服务器获得数据快得多，本地数据可以即时获得。再加上网页本身也可以有缓存，因此整个页面和数据都在本地的话，可以立即显示。
* 临时存储：很多时候数据只需要在用户浏览一组页面期间使用，关闭窗口后数据就可以丢弃了，这种情况使用SessionStorage 非常方便。

## 二、基本概念 <a href="#89cc08ef" id="89cc08ef"></a>

### 1. Cookie <a href="#d7e4e433" id="d7e4e433"></a>

Cookie 是小甜饼的意思。通常它的大小被限制为4KB左右，是Netscape（网景）的前雇员 Lou Montulli 在1993年3月发明的。它的主要用途是**保存用户登录信息。**例如我们在某个网站登录后，用来辨别我们用户身份的数据通常就会被保存在 Cookie 里，而经常看到的“记住密码”则会转换成 Cookie 存储的数据的有效期。

* Cookie 的主要内容包括：名字、值、过期时间、路径和域。路径与域一起构成Cookie 的作用范围。
* 若不设置 Cookie 的过期时间，则表示这个 Cookie 的生命周期为浏览器会话期间，关闭浏览器窗口，Cookie 就会消失。这种生命周期为浏览器会话期的 Cookie 被称之会话 Cookie。通常会话 Cookie 不存储在硬盘而是保存在内存里。
* 若设置了 Cookie 的过期时间，浏览器就会把 Cookie 保存到硬盘上，关闭后再打开浏览器这些 Cookie 仍然有效直到超过设定的过期时间。

对于 `cookie`，我们还需要注意安全性。

| 属性        | 作用                                |
| --------- | --------------------------------- |
| value     | 如果用于保存用户登录态，应该将该值加密，不能使用明文的用户标识   |
| http-only | 不能通过 JS 访问 Cookie，减少 XSS 攻击       |
| secure    | 只能在协议为 HTTPS 的请求中携带               |
| same-site | 规定浏览器不能在跨域请求中携带 Cookie，减少 CSRF 攻击 |

### 2. LocalStorage <a href="#3d2c07d1" id="3d2c07d1"></a>

LocalStorage 主要用于**持久化的本地存储**，除非主动删除数据，否则数据是永远不会过期的。虽然LocalStorage 是HTML5 新加入的特性，但其实早在 IE 6 时代就有一个叫 userData 的东西用于本地存储，而当时考虑到浏览器兼容性，更通用的方案是使用 Flash。而如今，LocalStorage 被大多数浏览器所支持，如果你的网站需要支持 IE6+，那以 userData 作为你的 polyfill 的方案是种不错的选择。

LocalStorage 具有如下特点：

* 通常在浏览器中支持的LocalStorage 的大小是5M。
* 目前所有的浏览器中都会把 localStorage  的值类型限定为 string  类型，这个在对我们日常比较常见的 JSON 对象类型需要一些转换。
* LocalStorage  在浏览器的隐私模式下面是不可读取的。
* LocalStorage  本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡。
* LocalStorage  不能被爬虫抓取到。

### 3. SessionStorage <a href="#d9b769d9" id="d9b769d9"></a>

SessionStorage 与 LocalStorage 的接口类似，但保存数据的生命周期与 LocalStorage 不同。SessionStorage 主要用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此 SessionStorage 不是一种持久化的本地存储，仅仅是**会话级别的存储**。也就是说只要这个浏览器窗口没有关闭，即使刷新页面或进入同源另一页面，数据仍然存在。关闭窗口后，SessionStorage 即被销毁。

SessionStorage 具有如下特点：

* 同源策略限制。若想在不同页面之间对同一个 SessionStorage 进行操作，这些页面必须在同一协议、同一主机名和同一端口下。
* **单标签页**限制。SessionStorage 操作限制在单个标签页中，在此标签页进行同源页面访问都可以共享SessionStorage 数据。
* 只在本地存储。SeesionStorage 的数据不会跟随 HTTP 请求一起发送到服务器，只会在本地生效，并在关闭标签页后清除数据。(若使用 Chrome 的恢复标签页功能，seesionStorage 的数据也会恢复)。
* 存储方式。SeesionStorage 的存储方式采用 key、value 的方式。value 的值必须为字符串类型(传入非字符串，也会在存储时转换为字符串。true 值会转换为 "true")。
* 存储上限限制。不同的浏览器存储的上限也不一样，但大多数浏览器把上限限制在**5MB以下**。

## 三、三者异同 <a href="#ed4474dc" id="ed4474dc"></a>

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

## 四、使用 <a href="#e1ce7125" id="e1ce7125"></a>

### 1. Cookie <a href="#1b8014fa" id="1b8014fa"></a>

* 存储（同时也能达到更新的目的）

```javascript
function setCookie(name, value, hours) {
    let expires = '';
    if (hours) {
        const date = new Date();
        date.setTime(date.getTime() + (hours * 60 * 60 * 1000));
        expires = '; expires=' + date.toUTCString();
    }
    document.cookie = name + '=' + value + ';' + expires + ';path=/';
}
```

* 读取

```javascript
function getCookie(name) {
    const key = name + '=';
    const value = document.cookie.split(';');
    for (let cookie of value) {
        while (cookie.charAt(0) === ' ') {
            cookie = cookie.substring(1, cookie.length);
        }

        if (cookie.indexOf(key) === 0) {
            return cookie.substring(key.length, cookie.length);
        }
    }
    return null;
}
```

* 删除：要删除Cookie 只能将其设置过期即可

```javascript
function deleteCookie(name) {
    const expireDate = new Date();
    expireDate.setTime(expireDate.getTime() - 1);
    const cval = this.getCookie(name);
    if (cval) {
        document.cookie = name + '=' + cval + ';expires=' + expireDate.toUTCString();
    }
}
```

### 2. LocalStorage <a href="#6e7598d7" id="6e7598d7"></a>

* string localStorage.key(int index) ：返回当前 lessionStorage 对象的第index 序号的key 名称。若没有返回null。
* string localStorage.getItem(string key) ：返回键名(key)对应的值(value)。若没有返回null。
* void localStorage.setItem(string key, string value) ：该方法接受一个键名(key)和值(value)作为参数，将键值对添加到存储中，如果键名存在，则**更新**其对应的值。
* void localStorage.removeItem(string key) ：将指定的键名(key)从 lessionStorage 对象中移除。
* void localStorage.clear() ：清除 lessionStorage 对象所有的项。

### 3. SessionStorage <a href="#d0c3b6f0" id="d0c3b6f0"></a>

* string sessionStorage.key(int index) ：返回当前 sessionStorage 对象的第index 序号的key 名称。若没有返回null。
* string sessionStorage.getItem(string key) ：返回键名(key)对应的值(value)。若没有返回null。
* void sessionStorage.setItem(string key, string value) ：该方法接受一个键名(key)和值(value)作为参数，将键值对添加到存储中，如果键名存在，则**更新**其对应的值。
* void sessionStorage.removeItem(string key) ：将指定的键名(key)从 sessionStorage 对象中移除。
* void sessionStorage.clear() ：清除 sessionStorage 对象所有的项。

## 五、应用场景 <a href="#73d82126" id="73d82126"></a>

* Cookie 一般用来保存用户登录后的信息，例如sessionId等。
* LocalStorage 一般用来在同源策略下的不同标签页之间进行通信。
* SessionStorage 非常适合SPA(单页应用程序)，可以方便在各业务模块进行传值。

## 六、参考 <a href="#01238d4e" id="01238d4e"></a>

* [https://www.cnblogs.com/jerryzou/p/4472670.html](https://www.cnblogs.com/jerryzou/p/4472670.html)
* [https://www.cnblogs.com/ke-nan/p/7092663.html](https://www.cnblogs.com/ke-nan/p/7092663.html)
* [https://blog.csdn.net/lq15310444798/article/details/79109026](https://blog.csdn.net/lq15310444798/article/details/79109026)
* [https://www.cnblogs.com/polk6/p/5512979.html](https://www.cnblogs.com/polk6/p/5512979.html)
* [https://www.cnblogs.com/st-leslie/p/5617130.html](https://www.cnblogs.com/st-leslie/p/5617130.html)
