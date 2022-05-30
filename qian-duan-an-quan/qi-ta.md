# 其它

## 一、URL 跳转漏洞 <a href="#url-tiao-zhuan-lou-dong" id="url-tiao-zhuan-lou-dong"></a>

借助未验证的 URL 跳转，将应用程序引导到不安全的第三方区域，从而导致的安全问题。

由于是从可信的站点跳转出去的，用户会比较信任，所以跳转漏洞一般用于钓鱼攻击，通过转到恶意网站欺骗用户输入用户名和密码盗取用户信息，或欺骗用户进行金钱交易；

也可能引发的 XSS 漏洞（主要是跳转常常使用302跳转，即设置 HTTP 响应头，Locatioin: url，如果 url 包含了 CRLF，则可能隔断了 http 响应头，使得后面部分落到了 http body，从而导致 xss 漏洞）。

**修复方法：**

* 在控制页面转向的地方校验传入的 URL 是否为可信域名。
* 接入防御JS代码

**漏洞检测：**\
修改参数中的合法URL为非法URL，然后查看是否能正常跳转或者响应包是否包含了任意的构造URL

## 二、target="\_blank" 存在跳转风险 <a href="#targetblank-cun-zai-tiao-zhuan-feng-xian" id="targetblank-cun-zai-tiao-zhuan-feng-xian"></a>

带有 target="\_blank" 跳转的网页拥有了浏览器 window.opener 对象赋予的对原网页的跳转权限，这可能会被恶意网站利用，例如一个恶意网站在某 UGC 网站 Po 了其恶意网址，该 UGC 网站用户在新窗口打开页面时，恶意网站利用该漏洞将原 UGC 网站跳转到伪造的钓鱼页面，用户返回到原窗口时可能会忽视浏览器 URL 已发生了变化，伪造页面即可进一步进行钓鱼或其他恶意行为。

**修复方法：** 为 target="\_blank" 加上 rel="noopener noreferrer" 属性。
