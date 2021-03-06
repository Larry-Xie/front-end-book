---
description: 基于安全套接层的超文本传输协议（Hyper Text Transfer Protocol over SecureSocket Layer）
---

# HTTPS

## 一、HTTPS

近年来，随着用户和互联网企业安全意识的提高和 HTTPS 成本的下降，HTTPS 已经越来越普及。很多互联网巨头也在力推 HTTPS，比如谷歌的 Chrome 浏览器在访问 HTTP 网站时会在地址栏显示不安全的提醒，微信要求所有的小程序必须使用 HTTPS 传输协议，苹果也要求所有在 App Store 上架的应用必须采用 HTTPS ，国内外的大部分主流网站也都已迁移至 HTTPS，HTTPS 全面取代 HTTP 只是时间问题。

HTTP 之所以被 HTTPS 取代，最大的原因就是不安全，HTTP 在传输数据的过程中，所有的数据都是明文传输，自然没有安全性可言，特别是一些敏感数据，比如用户密码和信用卡信息等，一旦被第三方获取，后果不堪设想。所以要想让 HTTP 更安全，需要使用真正的加密算法，因为加密算法可以用密钥加密或还原数据，只要确保密钥不被第三方获取，那就能确保数据传输的安全了。而这正是 HTTPS 的解决方案。

HTTPS 并非独立的通信协议，是对 HTTP 的扩展，在传输层之上通过 TLS 或者 SSL 来进行加密，从而保证通信安全。可以看作 HTTPS = HTTP + SSL / TLS。

### 1. 为什么需要加密

因为 HTTP 的内容是明文传输的，如果信息在传输过程中被劫持，传输的内容就完全暴露了。劫持者不仅可以读取内容，还可以篡改传输的信息且不被双方察觉，这就是`中间人攻击`。所以我们才需要对信息进行加密来保障传输的可靠性。

> 诸如 MD5，SHA-1 之类的哈希算法是不可逆的哈希算法，不能算是加密算法。

### 2. 对称加密

对称加密就是只有一个私钥，它可以对一段内容加密，加密后只能用它才能解密看到原本的内容，和我们日常生活中用的钥匙作用差不多。

![对称加密](<../.gitbook/assets/image (8) (1) (1) (1) (1).png>)

HTTPS 如果使用对称加密的弊端在于私钥还是需要通过网络从服务器传递给客户端，攻击者同样可以从中劫持来获取私钥信息，这样就算以后的内容进行过加密，但是对已经有私钥的攻击者来说已经是明文的了，同样可以读取和篡改传输内容。

### 3. 非对称加密

非对称加密则是有两把密钥，通常一把叫做公钥、一把叫做私钥，用公钥加密的内容必须用私钥才能解开，同样，用私钥加密的内容只有公钥才能解开。注意用公钥加密的内容用公钥是解不开的，私钥亦是如此。私钥通常存放于服务器端，仅有拥有者可见，而公钥需要传递给客户端。

非对称加密虽然相比对称加密有了很大的安全性提升，但是如果 HTTPS 仅使用非对称加密的话，还是存在被攻击的风险：

* 攻击者在服务器把公钥传输给客户端的过程中进行窃取从而获得公钥，而公钥是可以解开私钥加密的内容的，所以从服务器端经过私钥加密的内容对于拥有公钥的攻击者来说同样的透明的。而从客户端经过公钥加密的内容此时对于攻击者来说是解不开的。
* 更进一步，在服务器把公钥传输给客户端的过程中，攻击者不仅可以窃取公钥，还可以篡改公钥，用自己的公钥替换之，并且自己还有与之对应的私钥。此时攻击者就相当于一个完全的代理，所有内容都经他中转，进而可以随意地获取和篡改。

![中间人攻击](<../.gitbook/assets/image (5) (1) (1).png>)

### 4. 非对称加密+非对称加密

如果在客户端和服务器端都使用非对称加密，这样看上去要安全很多，但是攻击者同样可以作为一个中间代理来进行获取和篡改。而且最主要的原因是非对称加密算法非常耗时，特别是加密解密一些较大数据的时候有些力不从心，而对称加密快很多，所以这种方法实际操作起来不太可行。

### 5. 对称加密+非对称加密

对称加密+非对称加密的过程：

1. 某网站拥有用于非对称加密的公钥A、私钥A’。
2. 客户端向服务器发送请求，服务器把公钥A明文传输给客户端。
3. 客户端随机生成一个用于对称加密的密钥X，用公钥A加密后传给服务器。
4. 服务器拿到后用私钥A’解密得到密钥X。
5. 这样双方就都拥有密钥X了。之后双方所有数据都用密钥X加密解密。

![对称加密+非对称加密](<../.gitbook/assets/image (4) (1).png>)

这其实就是 HTTPS 基本工作流程。这样的好处在于减少了后续非对称加密解密的过程，后面的内容都是通过对称加密来进行的，从而降低了时耗。

但是这种方案同样存在缺陷，攻击者同样可以对服务器传给客户端的公钥进行篡改。

### 6. 公钥的正确性

所以针对上述的攻击，其实只要解决一个问题，那对称加密+非对称加密的方案就可以完美运行。这个问题就是如果确保客户端接收到的公钥是正确的，亦即该公钥确实是所请求的服务器发来的。基于此，引入了CA、数字证书、数字签名的概念。

### 7. 数字证书

在我们日常生活中，身份证包含了一个人的信息，且需要有公信力的机构(公安局)派发，身份证也能篡改和作假，但是当拿去使用时，通过身份证上芯片进行联网校验就能知道该身份证是不是真实有效的。

与此类似，各个网站在使用 HTTPS 前，需要向权威的 **CA 机构**申请颁发一份**数字证书**，数字证书里有网站信息、证书持有者的公钥以及 CA 生成的**数字签名**等信息。

数字签名的制作过程：

1. CA 拥有非对称加密的私钥和公钥。
2. CA 对证书明文信息进行 hash。
3. 对 hash 后的值用私钥加密，得到数字签名。

其中 hash 的目的是为了保证生成的信息是固定长度的，不过多的增加证书内容。其他信息和数字签名共同组成了数字证书，这样一份数字证书就可以颁发给网站了。当客户端拿到服务器传来的数字证书后，需要进行验证以保证没有被篡改过，客户端验证过程：

1. 拿到证书，得到明文 T，数字签名 S。
2. 用 CA 机构的公钥对 S 解密（由于是客户端信任的机构，所以客户端保有它的公钥）得到S’。
3. 用证书里说明的 hash 算法对明文 T 进行 hash 得到 T’。
4. 比较 S’ 是否等于 T’，等于则表明证书可信。

在这其中的最后一个问题就是如何保证客户端持有的 CA 机构的公钥的有效性。这些公钥信息不是明文存储在客户端的，也是以证书的形式存在的。通常操作系统、浏览器本身会预装一些它们信任的根证书，如果在验证过程中客户端本地有该 CA 机构的根证书，那就可以拿到它对应的可信公钥了。

> 实际上证书之间的认证也可以不止一层，可以 A 信任 B，B 信任 C，以此类推，我们把它叫做`信任链`或`数字证书链`，也就是一连串的数字证书，由根证书为起点，透过层层信任，使终端实体证书的持有者可以获得转授的信任，以证明身份。

> 当使用自签名的证书时，亦即网站的证书不是由权威的 CA 机构颁发的，那在客户端访问该网站时，会显示网站访问不了、提示要安装证书的情况。这里安装的就是根证书。说明浏览器不认给这个网站颁发证书的机构，本地也没有该机构的根证书，你就得手动下载安装，同样需要承担风险。安装该机构的根证书后，你就有了它的公钥，就可以用它验证服务器发来的证书是否可信了。

![数字签名的生成和校验](<../.gitbook/assets/image (1) (1) (1).png>)

基于以上策略的传输过程还有被攻击的可能吗？

*   **中间人有可能篡改该证书吗？**

    中间人可以篡改证书的原文信息，但是由于他没有 CA 机构的私钥，所以无法生成新的数字签名。客户端收到该证书后会发现原文和签名解密后的值不一致，则说明证书已被篡改，证书不可信，从而终止向服务器传输信息，防止信息泄露给中间人。
*   **中间人有可能把证书掉包吗？**

    假设有另一个网站 B 也拿到了 CA 机构认证的证书，它想搞垮网站 A，想劫持网站 A 的信息。于是它成为中间人拦截到了 A 传给浏览器的证书，然后替换成自己的证书，传给浏览器，之后浏览器就会错误地拿到B的证书里的公钥了，会导致上文提到的漏洞。其实这并不会发生，因为证书里包含了网站 A 的信息，包括域名，浏览器把证书里的域名与自己请求的域名比对一下就知道有没有被掉包了。

### 8. 完整流程

至此，HTTPS 的整个流程呼之欲出：

1. 客户端访问 HTTPS 网站，向服务器发起建立连接请求；
2. 服务器需要事先从 CA 处申请证书，同时会生成一对非对称加密的私钥和公钥，私钥由服务器自己保存，不能泄露，公钥则在证书里；
3. 服务器响应客户端请求，把证书传输给客户端；
4. 客户端接收到证书后通过 CA 根证书来验证该证书的有效性，并从中提取出该证书中包含的公钥。如果证书已经过期，或者证书不是可信机构颁发，或者证书中域名与实际域名不一致，会显示警告信息；
5. 客户端随机生成一个 KEY 作为对称加密密钥，用上一步得到的公钥来加密该密钥并传输给服务器；
6. 服务器接收到后用自己的私钥来解密得到客户端传来的密钥，至此客户端和服务器都有了对称加密的密钥，也建立了安全的链接；
7. 服务器用对称加密密钥来加密传输的内容，并传输给客户端；
8. 客户端和服务器通过对称加密的方式来进行传输。

![HTTPS 流程](<../.gitbook/assets/image (2) (1) (1).png>)

## 二、SSL & TLS

HTTPS 相比 HTTP 最大的不同就是多了一层 SSL (Secure Sockets Layer 安全套接层)或 TLS (Transport Layer Security 安全传输层协议)。有了这个安全层，就确保了互联网上通信双方的通信安全。

![HTTP vs HTTPS](<../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png>)

SSL 和 TLS 协议通过为通信双方提供识别和认证通道来保证通信的机密性和数据完整性。TLS 协议是从SSL 3.0协议演变而来的，现在 **SSL 已经被 TLS 取代**，所以现在的 HTTPS 可以看作是基于 TLS 的。类似于 TCP 建立连接时的三次握手，HTTPS 的启动过程也需要进行 TLS 握手来进行连接。

### 1. TLS 握手目的

TLS 握手的主要目的是建立安全连接，在这个过程中，需要完成以下内容：

* 商定双方通信所使用的的 TLS 版本 (例如 TLS 1.0, 1.2, 1.3等等)；
* 确定双方所要使用的密码组合；
* 客户端通过服务器的公钥和数字证书上的数字签名验证服务端的身份；
* 生成会话密钥，该密钥将用于握手结束后的对称加密。

### 2. TLS 握手过程

TLS 握手过程如下：

![TLS 握手](<../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1).png>)

具体流程：

1. **"client hello"消息：**客户端通过发送"client hello"消息向服务器发起握手请求，该消息包含了客户端所支持的 TLS 版本和密码组合以供服务器进行选择，还有一个"client random"随机字符串。
2. **"server hello"消息：**服务器发送"server hello"消息对客户端进行回应，该消息包含了数字证书，服务器选择的密码组合和"server random"随机字符串。
3. **验证：**客户端对服务器发来的证书进行验证，确保对方的合法身份，验证过程可以细化为以下几个步骤：检查数字签名；验证证书链；检查证书的有效期；检查证书的撤回状态 (撤回代表证书已失效)。
4. **"premaster secret"字符串：**客户端向服务器发送另一个随机字符串"premaster secret (预主密钥)"，这个字符串是经过服务器的公钥加密过的，只有服务器的私钥才能解密。
5. **使用私钥：**服务器使用私钥解密"premaster secret"。
6. **生成共享密钥：**客户端和服务器均使用 client random，server random 和 premaster secret，并通过相同的算法生成相同的共享密钥 **KEY**。
7. **客户端就绪：**客户端发送经过共享密钥 **KEY**加密过的"finished"信号。
8. **服务器就绪：**服务器发送经过共享密钥 **KEY**加密过的"finished"信号。
9. **达成安全通信：**握手完成，双方使用对称加密进行安全通信。

> 加密加盐算法使用：
>
> 1. 握手期间所使用的的密钥交换和认证算法 (最常用的是 RSA 算法)
> 2. 握手完成之后使用对称加密算法 (常用的有 AES、3DES等)
> 3. 生成数字签名的信息摘要算法 (常用的有 SHA-256、SHA-1 和 MD5 等)

## 三、考点

### **1. HTTPS 和 HTTP 的区别**

* 最重要的区别就是安全性，HTTP 明文传输；而 HTTPS 通过 TLS 进行安全连接并对传输内容进行加密，保证安全性。因为使用了 TLS，使得使用 HTTPS 协议需要申请数字证书。
* HTTP 页面响应速度比 HTTPS 快，因为 HTTPS 多了一层安全层，连接的建立以及数据的加解密都要耗费更长的时间，从而会影响速度。
* 由于 HTTPS 是建构在 SSL / TLS 之上的 HTTP 协议，所以，要比 HTTP 更耗费服务器资源。
* HTTPS 和 HTTP 使用的是完全不同的连接方式，用的端口也不一样，前者是 443，后者是 80。

### **2. HTTPS 的缺点**

* 在相同网络环境中，HTTPS 相比 HTTP 无论是响应时间还是耗电量都有大幅度上升。
* HTTPS 的安全是有范围的，在黑客攻击、服务器劫持等情况下几乎起不到作用。
* 在现有的证书机制下，[中间人攻击](https://link.juejin.cn/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMan-in-the-middle\_attack)依然有可能发生。
* HTTPS 需要更多的服务器资源，也会导致成本的升高。

### 3. HTTPS 工作原理

见上文

### 4. TLS 握手过程

见上文

### 5. 针对现有 HTTPS 机制的中间人攻击

**中间人有可能篡改该证书吗？**

中间人可以篡改证书的原文信息，但是由于他**没有 CA 机构的私钥**，所以无法生成新的数字签名。客户端收到该证书后会发现原文和签名解密后的值不一致，则说明证书已被篡改，证书不可信，从而终止向服务器传输信息，防止信息泄露给中间人。

**中间人有可能把证书掉包吗？**

假设有另一个网站 B 也拿到了 CA 机构认证的证书，它想搞垮网站 A，想劫持网站 A 的信息。于是它成为中间人拦截到了 A 传给浏览器的证书，然后替换成自己的证书，传给浏览器，之后浏览器就会错误地拿到B的证书里的公钥了，会导致上文提到的漏洞。其实这并不会发生，因为**证书里包含了网站 A 的信息**，包括域名，浏览器把证书里的域名与自己请求的域名比对一下就知道有没有被掉包了。

## 四、参考

* [https://juejin.cn/post/6844903670044164109](https://juejin.cn/post/6844903670044164109)
* [https://juejin.cn/post/6844904038253740046](https://juejin.cn/post/6844904038253740046)
* [https://juejin.cn/post/6844904046063517704](https://juejin.cn/post/6844904046063517704)
* [https://juejin.cn/post/6844904127420432391](https://juejin.cn/post/6844904127420432391)
* [https://juejin.cn/post/6844903456554090503](https://juejin.cn/post/6844903456554090503)

