# XSS

## 一、引言

Cross-site Scripting（跨站脚本攻击）简称 XSS，是一种代码注入攻击。攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。利用这些恶意脚本，攻击者可获取用户的敏感信息如 Cookie、SessionID 等，进而危害数据安全。

> 为了和 CSS 区分，这里把攻击的第一个字母改成了 X，于是叫做 XSS。

XSS 的本质是：恶意代码未经过滤，与网站正常的代码混在一起；浏览器无法分辨哪些脚本是可信的，导致恶意脚本被执行。

而由于直接在用户的终端执行，恶意代码能够直接获取用户的信息，或者利用这些信息冒充用户向网站发起攻击者定义的请求。

在部分情况下，由于输入的限制，注入的恶意脚本比较短。但可以通过引入外部的脚本，并由浏览器执行，来完成比较复杂的攻击策略。

不仅仅是业务上的“用户的 UGC 内容”可以进行注入，包括 URL 上的参数等都可以是攻击的来源。在处理输入时，以下内容都不可信：

* 来自用户的 UGC 信息
* 来自第三方的链接
* URL 参数
* POST 参数
* Referer （可能来自不可信的来源）
* Cookie （可能来自其他子域注入）

## 二、攻击类型

根据攻击的来源，XSS 攻击可以分为以下几类：

### 1. 反射型 XSS 攻击

顾名思义，恶意 `JavaScript` 脚本属于用户发送给网站请求中的一部分，随后网站又将这部分返回给用户，恶意脚本在页面中被执行。一般发生在前后端一体的应用中，服务端逻辑会改变最终的网页代码。

**攻击步骤：**

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

### 2. 基于 DOM 的 XSS 攻击

目前更流行前后端分离的项目，反射型 XSS 无用武之地。 但这种攻击不需要经过服务器，我们知道，网页本身的 `JavaScript` 也是可以改变 `HTML` 的，黑客正是利用这一点来实现插入恶意脚本。

**攻击步骤：**

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL。
3. 用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

### 3. 存储型 XSS 攻击

又叫持久型 XSS，顾名思义，黑客将恶意 `JavaScript` 脚本长期保存在服务端数据库中，用户一旦访问相关页面数据，恶意脚本就会被执行。常见于搜索、微博、社区贴吧评论等。

**攻击步骤：**

1. 攻击者将恶意代码提交到目标网站的数据库中。
2. 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

### 4. 对比

* `反射型的 XSS` 的恶意脚本存在 `URL` 里，`存储型 XSS` 的恶意代码存在数据库里。
* `反射型 XSS` 攻击常见于通过 URL 传递参数的功能，如网站搜索、跳转等。
* `存储型 XSS`攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。
* 而基于`DOM 的 XSS` 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 `JavaScript` 自身的安全漏洞，其他两种 `XSS` 都属于服务端的安全漏洞。

| 类型       | 存储区            | 插入点           |
| -------- | -------------- | ------------- |
| 存储型 XSS  | 后端数据库          | HTML          |
| 反射型 XSS  | URL            | HTML          |
| DOM 型XSS | 后端数据库/前端存储/URL | 前端 JavaScript |

## 三、防范措施

整体的 XSS 防范是非常复杂和繁琐的，我们不仅需要在全部需要转义的位置，对数据进行对应的转义。而且要防止多余和错误的转义，避免正常的用户输入出现乱码。

虽然很难通过技术手段完全避免 XSS，但我们可以总结以下原则减少漏洞的产生：

* **利用模板引擎** 开启模板引擎自带的 HTML 转义功能。例如： 在 ejs 中，尽量使用 `<%= data %>` 而不是 `<%- data %>`； 在 doT.js 中，尽量使用 `{{! data }` 而不是 `{{= data }`； 在 FreeMarker 中，确保引擎版本高于 2.3.24，并且选择正确的 `freemarker.core.OutputFormat`。
* **避免内联事件** 尽量不要使用 `onLoad="onload('{{data}}')"`、`onClick="go('{{action}}')"` 这种拼接内联事件的写法。在 JavaScript 中通过 `.addEventlistener()` 事件绑定会更安全。
* **避免拼接 HTML** 前端采用拼接 HTML 的方法比较危险，如果框架允许，使用 `createElement`、`setAttribute` 之类的方法实现。或者采用比较成熟的渲染框架，如 Vue/React 等。
* **时刻保持警惕** 在插入位置为 DOM 属性、链接等位置时，要打起精神，严加防范。
* **增加攻击难度，降低攻击后果** 通过 CSP、输入长度配置、接口安全措施等方法，增加攻击的难度，降低攻击的后果。
* **主动检测和发现** 可使用 XSS 攻击字符串和自动扫描工具寻找潜在的 XSS 漏洞。

而 XSS 攻击主要有两大步骤：

* 攻击者提交恶意代码
* 浏览器执行恶意代码

所以我们可以针对这两点来制定防范措施：



### **1. 针对攻击者提交恶意代码**

#### **1.1 输入过滤**

在用户提交时，由前端过滤输入，然后提交到后端，这种方法不可行，因为攻击者可能绕过前端过滤，直接构造请求，提交恶意代码。一般在写入数据库前，后端对输入数据进行过滤。虽然输入侧过滤能够在某些情况下解决特定的 XSS 问题，但会引入很大的不确定性和乱码问题。在防范 XSS 攻击时应避免此类方法。

#### **1.2 输入内容长度控制**

对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 `XSS` 发生，但可以增加 `XSS` 攻击的难度。

#### **1.3 验证码**

防止脚本冒充用户提交危险操作。

### **2. 针对浏览器执行恶意代码**

#### **2.1 纯前端渲染**

在纯前端渲染中，我们会明确的告诉浏览器：下面要设置的内容是文本（`.innerText`），还是属性（`.setAttribute`），还是样式（`.style`）等等。浏览器不会被轻易的被欺骗，执行预期外的代码了。

#### **2.2 转义 HTML**

应当尽量避免转义 `HTML`。但如果不是纯前端渲染，就需要采用合适的转义库，对 `HTML` 模板各处插入点进行充分的转义。

#### **2.3 预防 DOM 型 XSS 攻击**

防范存储型和反射型 `XSS` 是后端 `RD` 的责任。而 `DOM` 型 `XSS` 攻击不发生在后端。`DOM` 型 `XSS` 攻击，实际上就是网站前端 `JavaScript` 代码本身不够严谨，把不可信的数据当作代码执行了。

在使用 `.innerHTML`、`.outerHTML`、`document.write()` 时要特别小心，不要把不可信的数据作为 `HTML` 插到页面上，而应尽量使用 `.textContent`、`.setAttribute()` 等。

如果用 `Vue/React` 技术栈，并且不使用 `v-html/dangerouslySetInnerHTML` 功能，就在前端 `render` 阶段避免 `innerHTML、outerHTML` 的 `XSS` 隐患。

`DOM` 中的内联事件监听器，如 `location、onclick、onerror、onload、onmouseover` 等，`<a>` 标签的 `href` 属性，`JavaScript` 的 `eval()、setTimeout()、setInterval()` 等，都能把字符串作为代码运行。如果不可信的数据拼接到字符串中传递给这些 `API`，很容易产生安全隐患，请务必避免。

```javascript
<!-- 内联事件监听器中包含恶意代码 -->
<img onclick="UNTRUSTED" onerror="UNTRUSTED" src="data:image/png,">

<!-- 链接内包含恶意代码 -->
<a href="UNTRUSTED">1</a>

<script>
// setTimeout()/setInterval() 中调用恶意代码
setTimeout("UNTRUSTED")
setInterval("UNTRUSTED")

// location 调用恶意代码
location.href = 'UNTRUSTED'

// eval() 中调用恶意代码
eval("UNTRUSTED")
</script>
```

#### **2.4 CSP**

[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) (`CSP`) 是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括跨站脚本 (`XSS`) 和数据注入攻击等。

通过 `HTTP Header` 来定义(优先)：

```
"Content-Security-Policy:" 策略集
```

通过 `html meta` 标签使用：

```
<meta http-equiv="content-security-policy" content="策略集" />
```

内容安全策略 (`CSP`) 是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括跨站脚本 (`XSS`) 和数据注入攻击等。

严格的 CSP 在 XSS 的防范中可以起到以下的作用：

* 禁止加载外域代码，防止复杂的攻击逻辑。
* 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
* 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。
* 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
* 合理使用上报可以及时发现 XSS，利于尽快修复问题。

#### **2.5 HttpOnly & Secure**

* `cookie` 中设置了 `HttpOnly` 属性，禁止 `JavaScript` 读取某些敏感 `Cookie`，攻击者完成 `XSS` 注入后也无法窃取此 `Cookie`。
* `cookie` 中设置了 `Secure` 属性，规定 `cookie` 只能在 `https` 协议下才能够发送到服务器。防止信息在传递的过程中被监听捕获后信息泄漏。

```
  // koa
  ctx.cookies.set(name, value, {
      httpOnly: true // 默认为 true
  })
```

## 四、参考

* [前端常见的安全问题及防范措施](https://juejin.cn/post/7067697624626757646)
* [前端安全汇总](https://blog.flqin.com/390.html)
* [如何防止 XSS 攻击](https://tech.meituan.com/2018/09/27/fe-security.html)
