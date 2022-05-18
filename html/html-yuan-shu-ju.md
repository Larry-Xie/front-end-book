# HTML 元数据

## 一、引言

在 html 顶部我们会定义一些页面的元信息，这部分通常会放在 head 标签中，这些元数据内容是不会被渲染到页面上的，它们可以用来描述页面的相关信息，包括样式、脚本及数据等。

除了 head 标签本身，与元数据相关的标签有下面几个：

## 二、base

`base` 元素为相对链接设置基本URL。

相对链接是省略URL的协议，主机和端口部分的链接并且针对一些其他 URL (由基本元素或由基本元素指定的 URL)进行评估用于加载当前文档的 URL。

`base` 元素也指定如何在用户单击链接时打开链接，以及在表单提交后浏览器的行为。

`base` 元素有两个局部属性

* href
* target

> 一个HTML 文档最多只能有一个 base，设置多个后面的会被忽略。

### 1. href

`href` 属性指定基本 URL 来解析文档中的相对 URL。

举个例子，页面中有一个 a 标签，href 值是相对路径 /page，如果不指定 base，那么点击 a 访问的是相对当前页面的 page，一旦指定了 base 路径就变成了相对 base 的 href 值。

```html
<!DOCTYPE HTML>
<html>
<head>
   <title>Example</title>
   <base href="//www.w3cschool.cn/listings/"/>
</head>
<body>
   <p>This is a test.</p>
   <!-- 以下链接为绝对 URL，所以直接使用 -->
   <a href="https://www.w3cschool.cn">Visit www.w3cschool.cn</a>
   <!-- 以下链接解析为 //www.w3cschool.cn/listings/javascript.html -->
   <a href="/javascript.html">JavaScript</a>
</body>
</html>
```

### 2. target

`target` 属性指示浏览器如何打开网址。这个属性和 a 标签的 target 一样指定打开方式，不过它指定的是**整页面所有内容**的打开方式。

target 有四个取值：

* 默认值是 \_self，在当前页面打开。
* 我们比较熟悉的是 \_blank，在新页面打开。
* 还有两个不常用的值 \_parent 和 \_top，当没有发生页面嵌套时这两个属性没有效果，当发生嵌套时前者表示在父页面打开，后者表示在最顶层打开。

## 三、link

link 可以用来引用外部资源，最常用法是使用 link 引用来自外部的 css 文件资源，**link 是不受浏览器同源策略限制的（即允许访问跨域资源）**，因此我们可以加载部署在 CDN 域名下的外部 css 资源。

除了 css，link 还可以用来加载其他类型资源，这是由它的 rel 属性指定的，link 上两个最重要的属性：

* href 指定资源链接
* rel 指定资源类型，rel 添加多个值时使用空格分隔。

rel 取值有很多，除去已经废弃的取值外主要有下面几个：

* stylesheet：这个是最常见的，加载 css 资源，当不设置 rel 时浏览器也会优先按照 css 处理。
* shortlink：指定短链接，方便用于分享。
* search：可以在浏览器上添加快捷搜索工具（只有 firefox 和 IE 支持）。
* prev、next：在分页关系中指定上一页、下一页内容，方便 SEO。
* canonical：标记相同资源，在 SEO 处理时会用到。
* pingback：这是用来处理引用的一种机制，页面 B 引用页面 A 时，如果 A 添加了 pingback，B 会向此 pingback 链接发通知，pingback 在 WordPress 中有用到。
* manifest：添加 webmanifest 类型文件，主要用于 PWA 中。
* icon：这个也比较常用，添加 favicon 图标，在浏览器 tab 上会显示在 title 左侧。
* help：链接帮助资源。
* license：链接到 license 资源内容。
* author：链接到作者页面。
* alternate：意思是替换，可以结合媒体查询做一些事情，这里有一个一键换肤的[例子](https://link.juejin.cn/?target=https%3A%2F%2Fwww.zhangxinxu.com%2Fwordpress%2F2019%2F02%2Flink-rel-alternate-website-skin%2F)可以参考。
* preload：在页面渲染之前预加载资源，如果预加载资源 3 秒没有使用浏览器会出现警告。
* prefetch：预获取资源，与 preload 的区别在于 prefetch 是在空闲时加载，可以用来加载其他页面所需资源。
* prerender：预渲染，在 prefetch 基础上进一步处理。
* preconnect：提前连接不下载，适用场景较少，更多时候还是需要 preload。
* modulepreload：预加载 js module。
* dns-prefetch：DNS 域解析，可以提前查找 DNS。

上面简单介绍了一些 rel 的含义，其中很大一部分使用场景较少，更多时候根据需要进行查询即可。

除了 rel 和 href 之外，link 还有一些**其他属性**：

* as：用于 preload 或 prefetch 时指定内容的类型。
* crossorigin：请求时添加 cors 请求头，其取值 anonymous（不带认证信息）和 use-credentials（带认证信息）。
* disabled：仅对于 stylesheet 类型，禁用样式表。
* hreflang：指定 href 的语言。
* importance：在 preload 或 prefetch 时自定优先级。
* integrity：用来校验资源完整性，这个属性在对安全要求比较高的场景下还是很有必要的。
* media：值为[媒体查询](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FCSS%2FMedia\_Queries%2FUsing\_media\_queries)，添加后会在符合媒体查询条件下应用资源。
* sizes：指定 favicon 的大小。
* referrerpolicy：指定获取资源时的 Referer 信息。
*   title：link 标签添加 title 后会影响样式加载，这个在前面的换肤例子中有介绍，这里引用一下那篇文章的内容：

    * 没有 title属性，rel 属性值仅仅是 stylesheet的`<link>`无论如何都会加载并渲染。
    * 有 title 属性，rel 属性值仅仅是 stylesheet的`<link>`作为默认样式CSS文件加载并渲染。
    * 有 title 属性，rel 属性值同时包含 alternate stylesheet的`<link>`作为备选样式CSS文件加载，默认不渲染。

    这里有个非常有趣的特性，那就是`rel="stylesheet"`的`<link>`如果有title属性并有值，性质上就变成了一个可以控制其渲染或者不渲染的特殊元素了。
* type：指定 mime 类型。

以上算是 link 标签的一个比较详细的功能介绍，其中很多内容使用的并不多，也有一些与 SEO 或者内容安全 SRI 相关，这里作为储备知识可以在有需要的时候适当选用。还有一些性能优化相关的 preload、prefetch、dns-prefetch 等也可以应用到自己的网站中。

## 四、style

style 标签大家应该都很熟悉了，里面写 CSS 代码，用来给页面添加样式。

## 五、title

title 描述页面标题，这个大家都比较熟悉了，标题信息会显示在浏览器 tab 上，此外一些页面分享等需要使用页面名称的地方也都会用到 title 信息，title 只能有一个，设置多个后面的也不会生效，不设置 title 会显示无标题或者 url 信息（取决于浏览器实现）。title 信息对 SEO 和 a11y 都有重要的意义，通常情况我们都需要为网站添加标题。

## 六、meta

元数据就是描述数据的数据，而 HTML 有一个官方的方式来为一个文档添加元数据——  [`<meta>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta) 元素。

meta 表示不能由其他 HTML 相关元素（`<base>`，`<link>`，`<script>`，`<style>` 或 `<title>`）表示的元数据。因此 meta 的能力很强。

meta 的属性，值和含义如下表所示

| Attribute                                                               | Value                                                                                 | Description                                                                |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| [charset](https://www.w3schools.com/tags/att\_meta\_charset.asp)        | _character\_set_                                                                      | Specifies the character encoding for the HTML document                     |
| [content](https://www.w3schools.com/tags/att\_meta\_content.asp)        | _text_                                                                                | Specifies the value associated with the http-equiv or name attribute       |
| [http-equiv](https://www.w3schools.com/tags/att\_meta\_http\_equiv.asp) | <p>content-security-policy<br>content-type<br>default-style<br>refresh</p>            | Provides an HTTP header for the information/value of the content attribute |
| [name](https://www.w3schools.com/tags/att\_meta\_name.asp)              | <p>application-name<br>author<br>description<br>generator<br>keywords<br>viewport</p> | Specifies a name for the metadata                                          |

> content 属性是用来设置内容，通常不单独使用，而是和 http-equiv 或 name 来一起使用。

### 1. charset

这个属性声明了页面的字符编码。常用的字符编码有如下几种：

* ASCII 是第一个字符编码标准。它定义127个字母数字字符。ASCII 支持的数字（0-9），英文字母（A-Z）和一些特殊字符！ $ + - （）@ <>。
* ANSI（Windows-1252）是原始的Windows字符集。它支持256种不同的字符代码。
* ISO-8859-1是 HTML 4 的默认字符集。它支持256种不同的字符代码。
* UTF-8（Unicode）涵盖了世界上几乎所有的字符和符号。

**HTML 5 的默认字符编码为 UTF-8**。此外在使用该属性时还有以下注意点：

* **不得**使用 `CESU-8`，`UTF-7`，`BOCU-1` 和/或 `SCSU` 作为跨站点脚本，这些编码已被证明可以用来攻击。
* 不应该使用 `UTF-32`，因为不是所有的 HTML5 编码算法都可以将其与 `UTF-16` 区分开来。
* 声明的字符编码必须与页面保存的字符编码相匹配，以避免出现乱码和安全漏洞。
* 声明编码的 `<meta>` 元素必须位于 `<head>` 元素中，并且在 HTML 的**前 1024 个字节内**，因为某些浏览器仅在这些字节来确定编码。
* `<meta>` 元素只是确定页面字符集的算法的一部分。`Content-Type` 头和任何`字节顺序标记`会比这个元素优先级更高。
* 强烈建议您指定字符编码。如果页面的编码未定义，则可能被跨脚本技术，例如 [`UTF-7` 后备跨脚本技术](https://code.google.com/p/doctype-mirror/wiki/ArticleUtf7)来更改网页内容。
* `<meta>` 元素的 `charset` 属性和以下 `HTML5` 内容 `<meta http-equiv="Content-Type" content="text/html; charset=*IANAcharset*">` 等效，其中 `*IANAcharset*` 包含了和 `charset` 属性一样的值。后者的语法仍然是允许的，虽然不再推荐。

### 2. http-equiv

该属性可以包含 HTTP 头的名称。它定义了一条可以改变服务器和用户代理行为的指令，指令的值在 `content` 属性中定义。http-equiv 可以是以下值之一：

#### **content-language**&#x20;

定义页面的默认语言。它可以被任何元素上的 lang 属性覆盖。

> **警告：** 请勿使用该值，因为它已过时。首选是 `<html>` 元素上的 lang 属性。

#### **content-security-policy**

允许页面作者为当前页面定义内容策略。内容策略通常指定允许的服务器源和脚本端点，这有助于防止跨站点脚本攻击。

**content-type**&#x20;

定义文档的 MIME 类型，后跟其字符编码。

> **警告：** 请勿使用该值，因为它已过时。请用 [`<meta>`](https://www.mifengjc.com/html/meta.html) 元素的 [`charset`](https://www.mifengjc.com/html-ref/attr-meta-charset.html) 属性。

#### **refresh**

该值指定了：

* 如果 [`content`](https://www.mifengjc.com/html-ref/attr-meta-content.html) 属性只包含一个正整数，则是重新载入页面的时间间隔（秒）；
* 如果 [`content`](https://www.mifengjc.com/html-ref/attr-meta-content.html) 属性 包含一个正整数并且跟着一个字符串 `;url=`，以及一个有效的 URL，则是重定向到该链接的时间间隔（秒）。

```html
<!-- 当前页每五秒刷新一次 -->
<meta http-equiv="refresh" content="5"/>
<!-- 三秒后跳转到指定URL -->
<meta http-equiv="refresh"  content="3; http://www.www.w3cschool.cn"/>
```

### 3. name

该属性定义了一段文档级元数据的名称。该元数据名称与 [`content`](https://www.mifengjc.com/html-ref/attr-meta-content.html) 属性中包含的值关联。&#x20;

name 属性的可能值是：

#### **application-name**

定义了在网页中运行的应用程序的名称。

> * 浏览器可以使用它来识别应用程序。它与 [`<title>`](https://www.mifengjc.com/html/title.html) 元素不同，后者通常包含应用程序名称，但也可能包含文档名称或状态等信息。
> * 简单的网页不应定义应用程序名称。

#### **author**

定义了文档作者的名称。

#### **description**

包含页面内容的简短和精确的摘要。一些浏览器，如 Firefox 和 Opera，将其用作书签页面的默认描述。

**generator**

包含了生成该页面的软件标识。

#### **keywords**

逗号分隔列表，包含了页面内容相关的关键词。

#### **referrer**

控制附加在从文档发送的请求上的`Referer` HTTP 头，与之对应的 ** `content` ** 属性的值：

| `no-referrer`                     | 不发送 HTTP `Referer` 头。                                                                          |
| --------------------------------- | ---------------------------------------------------------------------------------------------- |
| `origin`                          | 发送文档的 origin。                                                                                  |
| `no-referrer-when-downgrade`      | 当目的页面是安全的（HTTPS->HTTPS），则发送 origin 作为 referrer，当目的链接安全性比当前低时（HTTPS->HTTP），不发送 referrer。这是默认行为。 |
| `origin-when-crossorigin`         | 在同源请求下，发送完整的 URL（不含查询参数），其他情况下则仅发送当前文档的 origin 。                                               |
| `same-origin`                     | 在同源的请求下，将发送 referrer，但跨域请求将不包含 referrer 信息。                                                    |
| `strict-origin`                   | 当目的页面是安全的（HTTPS->HTTPS）才发送文档的 origin 作为 referrer，如果是相比不安全则不发送（HTTPS->HTTP）。                    |
| `strict-origin-when-cross-origin` | 同源的请求下发送完整的 URL，当目的页面是安全的（HTTPS->HTTPS）才发送文档的 origin，如果目的页面相比不安全（HTTPS->HTTP）则不发送任何头。          |
| `unsafe-URL`                      | 在同源和跨域的请求中，都发送完整的 URL（不含查询参数）。                                                                 |

> * 动态插入 `<meta name="referrer">`（通过 `document.write` 或 `appendChild`）会使得 referrer 的行为不可预知。
> * 定义了多个冲突的策略时，将使用 `no-referrer` 策略。

该属性也可能取 [WHATWG Wiki MetaExtensions 页面](https://wiki.whatwg.org/wiki/MetaExtensions) 上定义的扩展列表的值。尽管尚未正式接受，但一些常用的名称是：

#### **creator**

定义文档的创建者的名称，例如组织或机构。如果有多个，应使用多个 [`<meta>`](https://www.mifengjc.com/html/meta.html) 元素。

#### **googlebot**

`robots` 的同义词，后面跟着 Googlebot（Google 的索引抓取工具）。

#### **publisher**

定义了文档发布者的名称。

#### **robots**

定义遵循规则的抓取工具或 “机器人” 应该在页面中使用的行为。它是以下值的逗号分隔列表：

**`<meta name="robots">` 中 `content` 属性的值**

| 值              | 描述                                                       | 使用对象                                                                                                                                                                                                                                                                                             |
| -------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `index`        | 允许机器人索引页面（默认）。                                           | 所有                                                                                                                                                                                                                                                                                               |
| `noindex`      | 要求机器人不索引页面。                                              | 所有                                                                                                                                                                                                                                                                                               |
| `follow`       | 允许机器人跟踪页面上的链接（默认）。                                       | 所有                                                                                                                                                                                                                                                                                               |
| `nofollow`     | 要求机器人不能跟踪页面上的链接。                                         | 所有                                                                                                                                                                                                                                                                                               |
| `none`         | 相当于 `noindex, nofollow`                                  | [Google](https://support.google.com/webmasters/answer/79812)                                                                                                                                                                                                                                     |
| `noodp`        | 阻止将[开放目录项目](https://www.dmoz.org)的描述（如果有）用作搜索引擎结果中的页面描述。 | [Google](https://support.google.com/webmasters/answer/35624#nodmoz)，[Yahoo](https://help.yahoo.com/kb/search-for-desktop/meta-tags-robotstxt-yahoo-search-sln2213.html#cont5)，[Bing](https://www.bing.com/webmaster/help/which-robots-metatags-does-bing-support-5198d240)                       |
| `noarchive`    | 要求搜索引擎不能缓存页面内容。                                          | [Google](https://developers.google.com/webmasters/control-crawl-index/docs/robots\_meta\_tag#valid-indexing--serving-directives)，[Yahoo](https://help.yahoo.com/kb/search-for-desktop/SLN2213.html)，[Bing](https://www.bing.com/webmaster/help/which-robots-metatags-does-bing-support-5198d240) |
| `nosnippet`    | 阻止在搜索引擎结果中显示页面的任何描述。                                     | [Google](https://developers.google.com/webmasters/control-crawl-index/docs/robots\_meta\_tag#valid-indexing--serving-directives)，[Bing](https://www.bing.com/webmaster/help/which-robots-metatags-does-bing-support-5198d240)                                                                    |
| `noimageindex` | 要求该页面不会显示为索引图像的引用页面。                                     | [Google](https://developers.google.com/webmasters/control-crawl-index/docs/robots\_meta\_tag#valid-indexing--serving-directives)                                                                                                                                                                 |
| `nocache`      | `noarchive` 的同义词。                                        | [Bing](https://www.bing.com/webmaster/help/which-robots-metatags-does-bing-support-5198d240)                                                                                                                                                                                                     |

> * 只有一些机器人才遵守这些规则。不要期望能够对电子邮件收割机有效。
> * 机器人仍然需要访问该页面才能阅读这些规则。为了防止带宽消耗，请使用 _robots.txt_ 文件。
> * 如果你想删除一个页面，设置 `noindex` 将会起作用，但是要在机器人再次访问页面之后才能识别到。同时，确保 `robots.txt` 文件不妨碍重访。
> * 有些值是互斥的，比如 `index` 和 `noindex`，或者 `follow` 和 `nofollow`。在这些情况下，机器人的行为是不确定的，它们之间可能有所不同。
> * Google，Yahoo 和 Bing 等一些爬虫机器人支持 HTTP 头 `X-Robot-Tags` 的相同值；这允许诸如图像的非 HTML 文档使用这些规则。

#### **slurp**

`robots` 的同义词，但仅用于 `Slurp` - 雅虎搜索的抓取工具

#### **viewport**

给出了 viewport 的初始大小的提示。仅供移动设备使用。对应的 **** `content` 属性的值**：**

| 值               | 可能的子值                      | 描述                                                                    |
| --------------- | -------------------------- | --------------------------------------------------------------------- |
| `width`         | 一个正整数，或 `device-width` 文本  | 定义您希望网站呈现的视口的像素宽度。                                                    |
| `height`        | 一个正整数，或 `device-height` 文本 | 定义视口的高度。任何浏览器都没有用到。                                                   |
| `initial-scale` | 一个 `0.0` 到 `10.0` 之间的数字    | 定义设备宽度（纵向模式下的 `device-width` 或横向模式下的 `device-height`）与视口大小之间的比率。      |
| `maximum-scale` | 一个 `0.0` 到 `10.0` 之间的数字    | 定义要放大的最大量。它必须大于或等于 `minimum-scale`，否则行为未知。浏览器设置可以忽略这个规则，iOS10+ 默认忽略它。 |
| `minimum-scale` | 一个 `0.0` 到 `10.0` 之间的数字    | 定义最小缩放级别。它必须小于或等于 `maximum-scale`， 否则行为未知。浏览器设置可以忽略这个规则，iOS10+ 默认忽略它。 |
| `user-scalable` | `yes` 或 `no`               | 如果设置为 `yes`，则用户无法放大网页。默认是 `no` 。浏览器设置可以忽略这个规则，iOS10+ 默认忽略它。           |

| 规范                                                                                                                                                  | 状态   | 备注                |
| --------------------------------------------------------------------------------------------------------------------------------------------------- | ---- | ----------------- |
| <p><a href="https://drafts.csswg.org/css-device-adapt/#viewport-meta">CSS Device Adaptation<br><code>&#x3C;meta name="viewport"></code> 的定义</a></p> | 工作草案 | 非规范地描述了视口 META 元素 |

> * 虽然没有标准化，但有着事实上的支配地位，大多数移动浏览器都会遵守这一声明。
> * 设备和浏览器的默认值可能不同。

## 七、参考

* [https://juejin.cn/post/7055560787393249288](https://juejin.cn/post/7055560787393249288)
* [https://developer.mozilla.org/zh-CN/docs/learn/HTML/Introduction\_to\_HTML/The\_head\_metadata\_in\_HTML](https://developer.mozilla.org/zh-CN/docs/learn/HTML/Introduction\_to\_HTML/The\_head\_metadata\_in\_HTML)
* [https://www.w3cschool.cn/html/html-css-meta.html](https://www.w3cschool.cn/html/html-css-meta.html)
* [https://www.mifengjc.com/html/meta.html](https://www.mifengjc.com/html/meta.html)
* [https://www.w3schools.com/tags/tag\_meta.asp](https://www.w3schools.com/tags/tag\_meta.asp)
