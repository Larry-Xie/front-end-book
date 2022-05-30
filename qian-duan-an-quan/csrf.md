# CSRF

## 一、引言

CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

典型的 CSRF 攻击是这样的：

* 受害者登录`A`网站，并且保留了登录凭证（Cookie）
* 攻击者引诱受害者访问`B`网站
* `B`网站向`A`网站发送了一个请求（这个就是下面将介绍的几种伪造请求的方式），浏览器请求头中会默认携带 A 网站的 Cookie
* `A` 网站服务器收到请求后，经过验证发现用户是登录了的，所以会处理请求

一张图了解原理：

![CSRF 攻击](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/10/11/ea16ef6642f4e35b5beca485a8847781\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

## 二、攻击类型

### **1. GET 类型的 CSRF**

GET 类型的 CSRF 利用非常简单，只需要一个 HTTP 请求，一般会这样利用：

```html
 <img src="http://bank.example/withdraw?amount=10000&for=hacker" > 
```

在受害者访问含有这个 img 的页面后，浏览器会自动向`http://bank.example/withdraw?account=xiaoming&amount=10000&for=hacker`发出一次 HTTP 请求。bank.example 就会收到包含受害者登录信息的一次跨域请求。

### **2. POST 类型的 CSRF**

这种类型的 CSRF 利用起来通常使用的是一个自动提交的表单，如：

```html
 <form action="http://bank.example/withdraw" method=POST>
    <input type="hidden" name="account" value="xiaoming" />
    <input type="hidden" name="amount" value="10000" />
    <input type="hidden" name="for" value="hacker" />
</form>
<script> document.forms[0].submit(); </script> 
```

访问该页面后，表单会自动提交，相当于模拟用户完成了一次 POST 操作。

POST 类型的攻击通常比 GET 要求更加严格一点，但仍并不复杂。任何个人网站、博客，被黑客上传页面的网站都有可能是发起攻击的来源，后端接口不能将安全寄托在仅允许 POST 上面。

### **3. 链接类型的 CSRF**

链接类型的 CSRF 并不常见，比起其他两种用户打开页面就中招的情况，这种需要用户点击链接才会触发。这种类型通常是在论坛中发布的图片中嵌入恶意链接，或者以广告的形式诱导用户中招，攻击者通常会以比较夸张的词语诱骗用户点击。

```html
<a href="http://test.com/csrf/withdraw.php?amount=1000&for=hacker" taget="_blank">
  重磅消息！！
<a/>
```

## 三、特点

* 攻击一般发起在第三方网站，而不是被攻击的网站。被攻击的网站无法防止攻击发生。
* 攻击利用受害者在被攻击网站的登录凭证，冒充受害者提交操作；而不是直接窃取数据。
* 整个过程攻击者并不能获取到受害者的登录凭证，仅仅是“冒用”。
* 跨站请求可以用各种方式：图片 URL、超链接、CORS、Form 提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪。

## 四、防范措施

由上面对 CSRF 的介绍我们知道了，

* CSRF 通常发生在第三方域名
* 并且 CSRF 攻击者不能获取到受害者的 cookie 等信息，只是借用他们的登录状态来伪造请求

所以我们可以针对这两点来制定防范措施：

### **1. 同源检测**

既然`CSRF`大多来自第三方网站，那么我们就直接禁止第三方域名（或者不受信任的域名）对我们发起请求。

在 HTTP 协议中，每一个异步请求都会携带两个 Header，用于标记来源域名：

* Origin Header
* Referer Header

这两个 Header 在浏览器发起请求时，大多数情况会自动带上，并且不能由前端自定义内容。 服务器可以通过解析这两个 Header 中的域名，确定请求的来源域。同时服务器应该优先检测 Origin。为了安全考虑，相比于 Referer，Origin 只包含了域名而不带路径。

### **2. CSRF Token**

* 在浏览器向服务器发起请求时，服务器生成一个 `CSRF Token`。`CSRF Token` 其实就是服务器生成的随机字符串，然后将该字符串植入到返回的页面中，通常是放到表单的隐藏输入框中，这样能够很好的保护 `CSRF Token` 不被泄漏；
* 当浏览器再次发送请求的时候，就需要携带这个 `CSRF Token` 值一起提交；
* 服务器验证 `CSRF Token` 是否一致；从第三方网站发出的请求是无法获取用户页面中的 `CSRF Token` 值的。

### **3. 给 Cookie 设置合适的 SameSite**

当从 A 网站登录后，会从响应头中返回服务器设置的 Cookie 信息，而如果 Cookie 携带了 SameSite=strict 则表示完全禁用第三方站点请求头携带 Cookie，比如当从 B 网站请求 A 网站接口的时候，浏览器的请求头将不会携带该 Cookie。

* Samesite=Strict，这种称为严格模式，表明这个 Cookie 在任何情况下都不可能作为第三方 Cookie
* Samesite=Lax，这种称为宽松模式，比 Strict 放宽了点限制：假如这个请求是这种请求（改变了当前页面或者打开了新页面）且同时是个GET请求，则这个Cookie可以作为第三方Cookie。（默认）
* None 任何情况下都会携带；

### **4. 验证码&密码**

在关键请求时要求再次输入验证码和密码之类的，打断 `csrf` 的进程，简单粗暴且有效。

### **5. 双重 Cookie 验证**

利用 `CSRF` 攻击不能获取到用户 `Cookie` 的特点，我们可以要求 `Ajax` 和表单请求携带一个 `Cookie` 中的值。

1. 在用户访问网站页面时，向请求域名注入一个 `Cookie`，内容为随机字符串（例如 `csrfcookie=v8g9e4ksfhw`）。
2. 在前端向后端发起请求时，取出 `Cookie`，并添加到 `URL` 的参数中（接上例 `POST` `https://www.a.com/comment?csrfcookie=v8g9e4ksfhw`）。
3. 后端接口验证 `Cookie` 中的字段与 `URL` 参数中的字段是否一致，不一致则拒绝。

## 五、考点

### 1. CSRF 和 XSS  的区别

与 xss 区别 通常来说 CSRF 是由 XSS 实现的，CSRF 时常也被称为 XSRF（CSRF 实现的方式还可以是直接通过命令行发起请求等）。

本质上讲，XSS 是代码注入问题，CSRF 是 HTTP 问题。XSS 是内容没有过滤导致浏览器将攻击者的输入当代码执行。CSRF 则是因为浏览器在发送 HTTP 请求时候自动带上 cookie，而一般网站的 session 都存在 cookie里面。&#x20;

## 六、参考

* [前端常见的安全问题及防范措施](https://juejin.cn/post/7067697624626757646)
* [前端安全汇总](https://blog.flqin.com/390.html)
* [如何防止 CSRF 攻击](https://tech.meituan.com/2018/10/11/fe-security-csrf.html)
