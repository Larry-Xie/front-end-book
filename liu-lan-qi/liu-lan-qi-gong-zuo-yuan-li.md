# 浏览器工作原理

这一博客系列由四部分组成，将从高级体系结构到渲染流程的细节来窥探 Chrome 浏览器的内部。如果你曾对浏览器是如何将代码转化为具有功能的网站，或者你并不确定为何建议使用某一技术来提升性能，那么本系列就是为你准备的。

## 1. 浏览器架构

\


本文作为此系列的第一部分，将介绍核心计算术语与 Chrome 的多进程体系架构。

\


**提示：** 如果你已熟悉 CPU/GPU，进程/线程的相关概念，可以直接跳到浏览器架构部分开始阅读。

\


### 1.1 计算机的核心是 CPU 与 GPU

\


为了了解浏览器运行的环境，我们需要了解几个计算机部件以及它们的作用。

\


#### 1.1.1 CPU

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59ba7a04aebe?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 1：4 个 CPU 核心作为办公人员，坐在办公桌前处理各自的工作

\


第一个需要了解的计算机部件是 **中央处理器（Central Processing Unit）**，或简称为 **CPU**。CPU 可以看作是计算机的大脑。一个 CPU 核心如图中的办公人员，可以逐一解决很多不同任务。它可以在解决从数学到艺术一切任务的同时还知道如何响应客户要求。过去 CPU 大多是单芯片的，一个核心就像存在于同芯片的另一个 CPU。随着现代硬件发展，你经常会有不止一个内核，为你的手机和笔记本电脑提供更多的计算能力。

\


#### 1.1.2 GPU

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59ba718eff29?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 2：许多带特定扳手的 GPU 内核意味着它们只能处理有限任务

\


**图形处理器**（**Graphics Processing Unit**，简称为 **GPU**）是计算机的另一部件。与 CPU 不同，GPU 擅长同时处理跨内核的简单任务。顾名思义，它最初是为解决图形而开发的。这就是为什么在图形环境中“使用 GPU” 或 “GPU 支持”都与快速渲染和顺滑交互有关。近年来随着 GPU 加速计算的普及，仅靠 GPU 一己之力也使得越来越多的计算成为可能。

\


当你在电脑或手机上启动应用时，是 CPU 和 GPU 为应用供能。通常情况下应用是通过操作系统提供的机制在 CPU 和 GPU 上运行。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59ba722810d1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 3：三层计算机体系结构。底部是机器硬件，中间是操作系统，顶部是应用程序。

\


### 1.2 在进程和线程上执行程序

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59ba71b8acd2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图四：进程作为边界框，线程作为抽象鱼在进程中游动

\


在深入学习浏览器架构之前需要了解的另一个理论是进程与线程。进程可以被描述为是一个应用的执行程序。线程存在于进程并执行程序任意部分。

\


启动应用时会创建一个进程。程序也许会创建一个或多个线程来帮助它工作，这是可选的。操作系统为进程提供了一个可以使用的“一块”内存，所有应用程序状态都保存在该私有内存空间中。关闭应用程序时，相应的进程也会消失，操作系统会释放内存。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59ba7084d3b7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 5 ：进程使用内存空间和存储应用数据的示意图

\


进程可以请求操作系统启动另一个进程来执行不同的任务。此时，内存中的不同部分会分给新进程。如果两个进程需要对话，他们可以通过**进程间通信**（**IPC**）来进行。许多应用都是这样设计的，所以如果一个工作进程失去响应，该进程就可以在不停止应用程序不同部分的其他进程运行的情况下重新启动。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59ba6fd6aee0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 6：独立进程通过 IPC 通信示意图

\


### 1.3 浏览器架构

\


那么如何通过进程和线程构建 web 浏览器呢？它可能由一个拥有很多线程的进程，或是一些通过 IPC 通信的不同线程的进程。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59bcefbf5a45?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 7：不同浏览器架构的进程/线程示意图

\


这里需要注意的重要一点是，这些不同的架构是实现细节。关于如何构建 web 浏览器并不存在标准规范。一个浏览器的构建方法可能与另一个迥然不同。

\


在本博客系列中，我们使用下图所示的 Chrome 近期架构进行阐述。

\


顶部是浏览器线程，它与处理应用其它模块任务的进程进行协调。对于渲染进程来说，创建了多个渲染进程并分配给了每个标签页。直到最近，Chrome 在可能的情况下给每个标签页分配一个进程。而现在它试图给每个站点分配一个进程，包括 iframe（参见站点隔离）。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59bcf0dee2ef?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 8：Chrome 的多进程架构示意图。渲染进程下显示了多个层，表明 Chrome 为每个标签页运行多个渲染进程。

\


### 1.4 进程各自控制什么？

\


下表展示每个 Chrome 进程与各自控制的内容：

| 进程  | 控制                                                                   |
| --- | -------------------------------------------------------------------- |
| 浏览器 | 控制应用中的 “Chrome” 部分，包括地址栏，书签，回退与前进按钮。以及处理 web 浏览器不可见的特权部分，如网络请求与文件访问。 |
| 渲染  | 控制标签页内网站展示。                                                          |
| 插件  | 控制站点使用的任意插件，如 Flash。                                                 |
| GPU | 处理独立于其它进程的 GPU 任务。GPU 被分成不同进程，因为 GPU 处理来自多个不同应用的请求并绘制在相同表面。          |

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59bcf9940968?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 9：不同进程指向浏览器 UI 的不同部分

\


还有更多进程如扩展进程与应用进程。如果你想要了解有多少进程运行在你的 Chrome 浏览器中，可以点击右上角的选项菜单图标，选择更多工具，然后选择任务管理器。然后会打开一个窗口，其中列出了当前正在运行的进程以及它们当前的 CPU/内存使用量。

\


### 1.5 Chrome 多进程架构的优点

\


前文中提到了 Chrome 使用多个渲染进程。最简单的情况下，你可以想象每个标签页都有自己的渲染进程。假设你打开了三个标签页，每个标签页都拥有自己独立的渲染进程。如果某个标签页失去响应，你可以关掉这个标签页，此时其它标签页依然运行着，可以正常使用。如果所有标签页都运行在同一进程上，那么当某个失去响应，所有标签页都会失去响应。这样的体验很糟糕。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59bd6fa601fc?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 10：如图所示每个标签页上运行的渲染进程

\


把浏览器工作分成多个进程的另一好处是安全性与沙箱化。由于操作系统提供了限制进程权限的方法，浏览器就可以用沙箱保护某些特定功能的进程。例如，Chrome 浏览器限制处理任意用户输入的进程(如渲染器进程)对任意文件的访问。

\


由于进程有自己的私有内存空间，所以它们通常包含公共基础设施的拷贝(如 V8，它是 Chrome 的 JavaScript 引擎)。这意味着使用了更多的内存，如果它们是同一进程中的线程，就无法共享这些拷贝。为了节省内存，Chrome 对可加速的内存数量进行了限制。具体限制数值依设备可提供的内存与 CPU 能力而定，但是当 Chrome 运行时达到限制时，会开始在同一站点的不同标签页上运行同一进程。

\


### 1.6 节省更多内存 —— Chrome 中的服务化

\


同样的方法也适用于浏览器进程。Chrome 正在经历架构变革，它转变为将浏览器程序的每一模块作为一个服务来运行，从而可以轻松实现进程的拆解或聚合。

\


通常观点是当 Chrome 运行在强力硬件上时，它会将每个服务分解到不同进程中，从而提升稳定性，但是如果 Chrome 运行在资源有限的设备上时，它会将服务聚合到一个进程中从而节省了内存占用。在这一架构变革实现前，类似的整合进程以减少内存使用的方法已经在 Android 类平台上使用。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59bd7545f32c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 11： Chrome 的服务化图，将不同的服务移动到多个进程和单个浏览器进程中

\


### 1.7 每个 iframe 的渲染进程 —— 站点隔离

\


[站点隔离](https://link.juejin.im/?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Fupdates%2F2018%2F07%2Fsite-isolation) 是近期引入到 Chrome 中的一个功能，它为每个 iframe 运行一个单独的渲染进程。我们已经讨论了许久每个标签页的渲染进程，它允许跨站点 iframe 运行在一个单独的渲染进程，在不同站点中共享内存。运行 [a.com](https://link.juejin.im/?target=http%3A%2F%2Fa.com) 与 [b.com](https://link.juejin.im/?target=http%3A%2F%2Fb.com) 在同一渲染进程中看起来还 ok。

\


[同源策略](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FSecurity%2FSame-origin\_policy) 是 web 的核心安全模型。同源策略确保站点在未得到其它站点许可的情况下不能获取其数据。安全攻击的一个主要目标就是绕过同源策略。进程隔离是分离站点的最高效的手段。随着 [Meltdown and Spectre](https://link.juejin.im/?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Fupdates%2F2018%2F02%2Fmeltdown-spectre) 的出现，使用进程来分离站点愈发势在必行。Chrome 67 版本后，桌面版 Chrome 都默认开启了站点隔离，每个标签页的 iframe 都有一个单独的渲染进程。

![](https://user-gold-cdn.xitu.io/2018/9/14/165d59bd6de81ed2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

\


图 12：站点隔离示意图，多个渲染进程指向站点内的 iframe

\


启用站点隔离是多年来工程人员努力的结果。站点隔离并不只是分配不同的渲染进程这么简单。它从根本上改变了 iframe 的通信方式。在一个页面上打开开发者工具，让 iframe 在不同的进程上运行，这意味着开发者工具必须在幕后工作，以使它看起来无缝。即使运行一个简单的 Ctrl + F 来查找页面中的一个单词，也意味着在不同的渲染器进程中进行搜索。你可以看到为什么浏览器工程师把发布站点隔离功能作为一个重要里程碑！

\


### 1.8 小结

\


本文从高级视角对浏览器架构与多进程架构的优点进行阐述。我们也对 Chrome 中与多进程架构密切相关的服务化与站点隔离进行了讲解。下一篇文章中，我们将开始深入了解进程与线程中到底发生了什么才能使网站得以呈现。

\


\


## 2. 基本工作流程

\


### 2.1 导航时发生了什么

\


这是关于 Chrome 内部工作的 4 篇博客系列的第 2 篇。在上一篇文章中，我们研究了不同的进程和线程如何处理浏览器的不同部分。在这篇文章中，我们会更深入研究每个进程和线程如何进行通信以展示网站。

\


让我们看一个网络浏览的简单用例：你在浏览器中键入 URL，然后浏览器从互联网获取数据并显示一个页面。在这篇文章中，我们将重点放在用户请求站点和浏览器准备渲染页面部分 —— 亦即导航。

\


### 2.2 它以浏览器进程开始

![](https://camo.githubusercontent.com/486077aefa205e006854c1fcbee342e24df62c0e/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f62726f7773657270726f6365737365732e706e67)

图 1：顶部是浏览器 UI，底部是拥有 UI、网络和存储线程的浏览器进程图

\


正如我们在[第 1 部分：CPU、GPU、内存和多进程架构](https://developers.google.com/web/updates/2018/09/inside-browser-part1)中所述，tab 外的一切都被浏览器进程处理。浏览器进程有很多线程，例如绘制浏览器按钮和输入栏的 UI 线程、处理网络栈以从因特网获取数据的网络线程、控制文件访问的存储线程等。当你在地址栏中键入 URL 时，你的输入将由浏览器进程的 UI 线程处理。

\


### 2.3 一个简单导航

\


#### 2.3.1 第 1 步：处理输入

\


当用户开始在地址栏键入时，UI 线程要问的第一件事是 “这是一次搜索查询还是一个 URL 地址？”。在 Chrome 中，地址栏同时也是一个搜索输入栏，所以 UI 线程需要解析和决定把你的请求发送到搜索引擎，或是你要请求的网站。

![](https://camo.githubusercontent.com/a4a350ffb52faedfd0d35ead3286fc62473a2b9f/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f696e7075742e706e67)

图 1：UI 线程询问输入内容是搜索查询还是 URL 地址

\


#### 2.3.2 第 2 步：开始导航

\


当用户按下 Enter 键时，UI 线程启用网络调取去获取站点内容。加载动画会显示在标签页的一角，网络线程会通过适当的协议，像 DNS 查找和为请求建立 TLS 连接。

![](https://camo.githubusercontent.com/bb07c05f0ae2cf685c24522d653ba780d67737db/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f6e617673746172742e706e67)

图 2：UI 线程告诉网络线程要导航到 mysite.com

\


在这时，网络线程可能会收到像 HTTP 301 那样的服务器重定向头。这种情况下，网络线程会告诉 UI 线程，服务器正在请求重定向。然后，另一个 URL 请求会被启动。

\


#### 2.3.3 第 3 步：读取响应

![](https://camo.githubusercontent.com/f5d7e2bbe6c03f280256cd68190cfb222b084805/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f726573706f6e73652e706e67)

图 3：包含 Content-Type 的响应头以及作为实际数据的 payload

\


一旦开始收到响应主体（payload），网络线程会在必要时查看数据流的前几个字节。响应报文的 Content-Type 字段会声明数据的类型，但是它有可能会丢失或者错误，所以就有了 [MIME 类型嗅探](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics\_of\_HTTP/MIME\_types)来解决这个问题。这是[源码](https://cs.chromium.org/chromium/src/net/base/mime\_sniffer.cc?sq=package:chromium\&dr=CS\&l=5)中评论的“棘手的问题”。你可以阅读注释看一下不同浏览器是怎么匹配 content-type 和 payload 的。

\


如果响应是一个 HTML 文件，那么下一步就会把数据传给渲染进程，但是如果是一个压缩文件或是其他文件，那么意味着它是一个下载请求，因此需要将数据传递给下载管理器。

![](https://camo.githubusercontent.com/4ac4e7ecb72e8a0948caf3b89f40732da56aaf9a/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f736e6966662e706e67)

图 4：网络线程询问一个响应数据是否是从安全网站来的 HTML

\


此时也会进行 [SafeBrowsing](https://safebrowsing.google.com) 检查。如果域名和响应数据似乎匹配到一个已知的恶意网站，那么网络线程会显示一个警告页面。除此之外，还会发生 [Cross Origin Read Blocking（CORB）](https://www.chromium.org/Home/chromium-security/corb-for-developers)检查，以确保敏感的跨域数据不被传给渲染进程。

\


#### 2.3.4 第 4 步：查找渲染进程

\


一旦所有的检查执行完毕并且网络线程确信浏览器会导航到请求的站点，网络线程会告诉 UI 线程所有的数据准备完毕。UI 线程会寻找渲染进程去开始渲染 web 页面。

![](https://camo.githubusercontent.com/e26f24b74d9b82ef52eb3c3e902678bf386ebeed/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f66696e6472656e64657265722e706e67)

图 5：网络线程告诉 UI 线程去查找渲染进程

\


由于网络请求会花费几百毫秒才获取回响应，因此可以应用一个优化措施。当第 2 步 UI 线程正发送一个 URL 请求给网络线程时，它已经知道它们会导航到哪个站点。在网络请求的同时，UI 并行地线程尝试主动寻找或开启一个渲染进程。这样，如果一切按预期进行，渲染进程在网络线程接受到数据时就已经处于待命状态。如果导航跨域重定向，这个待命进程也许不会被用到，这种情况下也许会用到另一个进程。

\


#### 2.3.5 第 5 步：提交导航

\


现在数据和渲染进程已经就绪，浏览器进程会发送一个 IPC（进程间通信）到渲染进程去提交导航。它也会传递数据流，所以渲染进程可以保持接收 HTML 数据。一旦浏览器进程收到渲染进程已经提交的确认消息，导航完毕并且文档加载解析开始。

\


这时，地址栏已经更新，安全指示器和站点设置 UI 会反映新页面的站点信息。此标签页的 session 历史记录会被更新，所以前进/后退按钮会走向刚导航过的站点。当你关闭标签页或者窗口，为了优化 tab/session 的还原，session 历史被保存在硬盘上。

![](https://camo.githubusercontent.com/05b1e10482ddf3306bae01f3e8e95dc290a765e7/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f636f6d6d69742e706e67)

图 6：浏览器和渲染进程间的 IPC，请求渲染页面。

\


#### 2.3.6 额外的步骤：初始加载完毕

\


一旦导航被提交，渲染进程开始加载资源和渲染页面。我们将在下一篇文章中讲解这个阶段发生什么的细节。一旦渲染进程渲染“完毕”。它会发送一个 IPC 返回给浏览器进程（这会在页面所有的 frame 的 `onload` 事件已经触发和执行完毕后发生）。这时，UI 线程停止标签页上的加载动画。

\


我之所以说“结束”，是因为客户端 JavaScript 可以在这时之后仍然加载额外的资源并且渲染新视图。

![](https://camo.githubusercontent.com/dcc653e051f4e71e86dd2d22576a4216db3024d2/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f6c6f616465642e706e67)

图 7：渲染进程发送 IPC 到浏览器进程通知页面“已被加载”

\


### 2.4 导航到另一个站点

\


简单导航已经完毕！但是用户在地址栏输入另一个 URL 会怎样呢？好吧，浏览器进程会执行相同的步骤来导航到一个不同的站点。但是在它做这个之前，它会检查当前已经渲染的站点是否关心 [beforeunload](https://developer.mozilla.org/en-US/docs/Web/Events/beforeunload) 事件。

\


`beforeunload` 可以在你试图导航离开或关闭标签页时创建“离开此站点？”警告。包括你的 JavaScript 代码，所有标签页内的东西都是由渲染进程处理，所以当新的导航请求到来时，浏览器进程必须要跟当前的渲染进程核对。

\


**注意：** 不要添加无条件的 `beforeunload` 处理程序。它会产生更多延迟，因为处理程序需要在导航开始之前执行。应仅在需要时添加此事件处理程序，例如如果需要警告用户他们可能会丢失他们在页面上输入的数据。

![](https://camo.githubusercontent.com/9d4bf243f5a5dd271055e6bcc17d7acfeb73445e/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f6265666f7265756e6c6f61642e706e67)

图 8：浏览器进程向渲染进程发送 IPC 告诉它将要导航到另一个站点

\


如果渲染进程已经启动了导航（像用户点击一个链接或者客户端 JavaScript 运行 `window.location = "https://newsite.com"`），渲染进程会先检查 `beforeunload` 事件处理程序。然后，它会像浏览器处理启动导航一样执行相同的步骤。唯一不同的是导航请求是由渲染进程发送到浏览器进程的。

\


当新导航到的站点不同于当前已渲染的站点时，会调用一个独立的渲染进程来处理新导航，同时保持当前的渲染进程来处理类似 `unload` 的事件。有关更多信息，请查看[页面生命周期概览](https://developers.google.com/web/updates/2018/07/page-lifecycle-api#overview\_of\_page\_lifecycle\_states\_and\_events)以及如何使用[页面生命周期 API](https://developers.google.com/web/updates/2018/07/page-lifecycle-api) 挂钩事件。

![](https://camo.githubusercontent.com/3b085e420fe871bdd46bf384ff212e9b1dcae946/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f756e6c6f61642e706e67)

图 9：2 个 IPC（从浏览器进程到新渲染进程）告知渲染页面并告知旧渲染进程卸载

\


### 2.5 如果有 Service Worker

\


最近对导航过程的改变是引入了 [service worker](https://developers.google.com/web/fundamentals/primers/service-workers/)。service worker 是一种在你的应用代码中编写网络代理的方法；允许 Web 开发者更好地控制本地缓存内容以及何时从网络获取新数据。如果将 service worker 设置为从缓存加载页面，则无需从网络请求数据。

\


要记住的重要部分是 Service Worker 是在渲染进程中运行的 JavaScript 代码。但是当导航请求进入时，浏览器进程如何知道该站点有 service worker？

![](https://camo.githubusercontent.com/82561d8c3715544d68b972eeebe34022c3447d5d/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f73636f70655f6c6f6f6b75702e706e67)

图 10：浏览器进程中的网络线程查找 service worker 作用域

\


当注册一个 service worker 时，保持 service worker 的作用域作为一个引用（你可以在这篇文章 [The Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)中阅读更多关于作用域的知识）。当一个导航发生时，网络线程用已注册的 service worker 作用域来检查域名，如果已经为该 URL 注册了一个 service worker，UI 线程会找一个渲染线程来执行 service worker 的代码。service worker 可能从缓存中加载数据，无需从网络请求数据，或者可以从网络请求新资源。

![](https://camo.githubusercontent.com/82e78c25df7c51f90483cc546268fb7dec37e8e8/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f73657276696365776f726b65722e706e67)

图 11：浏览器中的 UI 线程启动渲染进程来处理 service workers；然后，渲染进程中的工作线程从网络请求数据

\


### 2.6 导航预加载

\


你可以看到，如果 service worker 最终决定从网络请求数据，则浏览器进程和渲染器进程之间的往返可能会导致延迟。[导航预加载](https://developers.google.com/web/updates/2017/02/navigation-preload)是一种通过与 service worker 启动并行加载资源来加速此过程的机制。它用一个头部来标记这些请求，允许服务器决定为这些请求发送不同的内容；例如，只更新数据而不是完整文档。

![](https://camo.githubusercontent.com/c8ef0a1976a6fd09b4120cd6e2f95f6909140902/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274322f6e61767072656c6f61642e706e67)

图 12：浏览器进程中的 UI 线程启动渲染进程以在并行启动网络请求的同时处理 service worker

\


### 2.7 小结

\


在这篇文章中，我们研究了导航过程中发生了什么，以及你的 Web 应用代码（例如响应头和客户端 JavaScript）如何与浏览器交互。了解浏览器通过网络获取数据的步骤，可以更容易地理解为什么开发导航预加载等 API。在下一篇文章中，我们将深入探讨浏览器如何分析 HTML/CSS/JavaScript 以渲染页面。

\


\


## 3. 渲染机制

\


### 3.1 渲染进程的内部机制

\


这是关于浏览器工作原理博客系列四部分中的第三部分。之前，我们介绍了多进程架构和导航流。在这篇文章中，我们将一探渲染进程的内部机制。

\


渲染进程涉及 Web 性能的许多方面。由于渲染进程的流程太复杂，因此本文只进行概述。如果你想深入了解，可以在 [the Performance section of Web Fundamentals](https://developers.google.com/web/fundamentals/performance/why-performance-matters/) 找到相关资源。

\


### 3.2 渲染进程处理网站内容

\


渲染进程负责标签页内发生的所有事情。在渲染进程中，主线程处理服务器发送到用户的大部分代码。如果你使用 web worker 或 service worker，部分 JavaScript 将由工作线程处理。合成和光栅线程也在渲染进程内运行，以高效，流畅地呈现页面。

\


渲染进程的核心工作是将 HTML、CSS 和 JavaScript 转换为用户可以与之交互的网页。

![](https://camo.githubusercontent.com/cfe000fbf56aa0592ba26223e68ecfaeea525342/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f72656e64657265722e706e67)

图 1：渲染进程内部包含主线程、工作线程、合成线程和光栅线程

\


### 3.3 解析（Parsing）

\


#### 3.3.1 DOM 的构建

\


当渲染进程收到导航的提交消息并开始接收 HTML 数据时，主线程开始解析文本字符串（HTML）并将其转换为文档对象模型（**DOM**）。

\


DOM 是一个页面在浏览器内部表现，也是 Web 开发人员可以通过 JavaScript 与之交互的数据结构和 API。

\


将 HTML 到 DOM 的解析由 [HTML Standard](https://html.spec.whatwg.org) 规定。你可能已经注意到，将 HTML 提供给浏览器这一过程从不会引发错误。像 `Hi! <b>I'm <i>Chrome</b>!</i>` 这样的错误标记，会被理解为 `Hi! <b>I'm <i>Chrome</i></b><i>!</i>`，这是因为 HTML 规范会优雅地处理这些错误。如果你好奇这是如何做到的，可以阅读 [An introduction to error handling and strange cases in the parser](https://html.spec.whatwg.org/multipage/parsing.html#an-introduction-to-error-handling-and-strange-cases-in-the-parser) 的 HTML 规范部分。

\


#### 3.3.2 子资源加载

\


网站通常使用图像、CSS 和 JavaScript 等外部资源，这些文件需要从网络或缓存加载。在解析构建 DOM 时，主线程**会**按处理顺序逐个请求它们，但为了加快速度，“预加载扫描器（preload scanner）”会同时运行。如果 HTML 文档中有 `<img>` 或 `<link>` 之类的内容，则预加载扫描器会查看由 HTML 解析器生成的标记，并在浏览器进程中向网络线程发送请求。

![](https://camo.githubusercontent.com/fdf4ac799cc84ad4ea8d4813b698de8c8a598737/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f646f6d2e706e67)

图 2：主线程解析 HTML 并构建 DOM 树

\


#### 3.3.3 JavaScript 阻塞解析

\


当 HTML 解析器遇到 `<script>` 标记时，会暂停解析 HTML 文档，开始加载、解析并执行 JavaScript 代码。为什么？因为JavaScript 可以使用诸如 `document.write()` 的方法来改写文档，这会改变整个 DOM 结构（HTML 规范里的 [overview of the parsing model](https://html.spec.whatwg.org/multipage/parsing.html#overview-of-the-parsing-model) 中有一张不错的图片）。这就是 HTML 解析器必须等待 JavaScript 运行后再继续解析 HTML 文档原因。如果你对 JavaScript 执行中发生的事情感到好奇，可以看看 [V8 团队就此发表的演讲和博客文章](https://mathiasbynens.be/notes/shapes-ics)。

\


### 3.4 提示浏览器如何加载资源

\


Web 开发者可以通过多种方式向浏览器发送提示，以便很好地加载资源。如果你的 JavaScript 不使用 `document.write()`，你可以在 `<script>` 标签添加 [async](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#attr-async) 或 [defer](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#attr-defer) 属性，这样浏览器会异步加载运行 JavaScript 代码，而不阻塞解析。如果合适，你也可以使用 [JavaScript 模块](https://developers.google.com/web/fundamentals/primers/modules)。可以使用 `<link rel="preload">` 告知浏览器当前导航肯定需要该资源，并且你希望尽快下载。有关详细信息请参阅 [Resource Prioritization – Getting the Browser to Help You](https://developers.google.com/web/fundamentals/performance/resource-prioritization)。

\


### 3.5 样式计算

\


只拥有 DOM 不足以确定页面的外观，因为我们会在 CSS 中设置页面元素的样式。主线程解析 CSS 并确定每个 DOM 节点计算后的样式。这是有关基于 CSS 选择器对每个元素应用何种样式的信息，这可以在 DevTools 的 `computed` 部分中看到。

![](https://camo.githubusercontent.com/27aff0f201b6fae24448485bd4c9c3343ffba51d/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f636f6d70757465647374796c652e706e67)

图 3：主线程解析 CSS 以添加计算后样式

\


即使你不提供任何 CSS，每个 DOM 节点都具有计算样式。像 `<h1>` 标签看起来比 `<h2>` 标签大，每个元素都有 margin，这是因为浏览器具有默认样式表。如果你想知道更多 Chrome 的默认 CSS，[可以在这里看到源代码](https://cs.chromium.org/chromium/src/third\_party/blink/renderer/core/css/html.css)。

\


### 3.6 布局

\


现在，渲染进程知道每个节点的样式和文档的结构，但这不足以渲染页面。想象一下，你正试图通过手机向朋友描述一幅画：“这里有一个大红圈和一个小蓝方块”，这并不能让你的朋友知道这幅画究竟长什么样。

![](https://camo.githubusercontent.com/e6cdb1d48cca25c3d058dc63eaad9cb4c5eb40bc/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f74656c6c67616d652e706e67)

图 4：一个人站在一幅画前，电话线与另一个人相连

\


布局是计算元素几何形状的过程。主线程遍历 DOM，计算样式并创建布局树，其中包含 x y 坐标和边界框大小等信息。布局树可能与 DOM 树结构类似，但它仅包含页面上可见内容相关的信息。如果一个元素应用了 `display：none`，那么该元素不是布局树的一部分（但 `visibility：hidden` 的元素在布局树中）。类似地，如果应用了如 `p::before{content:"Hi!"}` 的伪类，则即使它不在 DOM 中，也包含于布局树中。

![](https://camo.githubusercontent.com/a51f434acb7cbae7fc23e751dec34c9f4fefe8e8/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f6c61796f75742e706e67)

图 5：主线程遍历计算样式后的 DOM 树，以此生成布局树

![](https://camo.githubusercontent.com/8f6a2500877622a9c8e4aae0c7cebffa83c1b680/68747470733a2f2f692e6c6f6c692e6e65742f323031382f31302f30372f356262393766643739306331382e676966)

图 6：由于换行而移动的盒子布局

\


确定页面布局是一项很有挑战性的任务。即使是从上到下的块流这样最简单的页面布局，也必须考虑字体的大小以及换行位置，这些因素会影响段落的大小和形状，进而影响下一个段落的位置。

\


CSS 可以使元素浮动到一侧、隐藏溢出的元素、更改书写方向。你可以想象这一阶段的任务之艰巨。Chrome 浏览器有整个工程师团队负责布局。[BlinkOn 会议的一些访谈](https://www.youtube.com/watch?v=Y5Xa4H2wtVA)记录了他们工作的细节，有兴趣可以了解一下，挺有趣的。

\


### 3.7 绘制

![](https://camo.githubusercontent.com/6be08726d8d506a80dfadcf6708f927be28aadc8/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f6472617767616d652e706e67)

图 7：一个人拿着笔站在画布前，思考着她应该先画圆形还是先画方形

\


拥有 DOM、样式和布局仍然不足以渲染页面。假设你正在尝试重现一幅画。你知道元素的大小、形状和位置，但你仍需要判断绘制它们的顺序。

\


例如，可以为某些元素设置 `z-index`，此时按 HTML 中编写的元素的顺序绘制会导致错误的渲染。

![](https://camo.githubusercontent.com/5dd8efc43cfd28dd3f6185e373c8e97a39d54776/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f7a696e6465782e706e67)

图 8：因为没有考虑 z-index，页面元素按 HTML 标记的顺序出现，导致错误的渲染图像

\


在绘制步骤中，主线程遍历布局树创建绘制记录。绘制记录是绘图过程的记录，就像是“背景优先，然后是文本，然后是矩形”。如果你使用过 JavaScript 绘制了 `<canvas>` 元素，那么这个过程对你来说可能很熟悉。

![](https://camo.githubusercontent.com/2ea60ba2f1274092a9b5a5509dcdb9b440a8e84a/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f7061696e742e706e67)

图 9：主线程遍历布局树并生成绘制记录

\


#### 3.7.1 更新渲染管道的成本很高

![](https://camo.githubusercontent.com/5561b36c021d68479286654f4ef1be3c6389c6e9/68747470733a2f2f692e6c6f6c692e6e65742f323031382f31302f30372f356262393766613438363831652e676966)

图 10：DOM + Style、布局和绘制树的生成顺序

\


渲染管道中最重要的事情是：每个步骤中，前一个操作的结果用于后一个操作创建新数据。例如，如果布局树中的某些内容发生改变，需要为文档的受影响部分重新生成“绘制”指令。

\


如果要为元素设置动画，则浏览器必须在每个帧之间运行这些操作。大多数显示器每秒刷新屏幕 60 次（60 fps），当屏幕每帧都在变化，人眼会觉得动画很流畅。但是，如果动画丢失了中间一些帧，页面看起来就会卡顿（janky）。

![](https://camo.githubusercontent.com/513e57d553b753b7d0b28d5db5d7c7e819636641/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f706167656a616e6b312e706e67)

图 11：时间轴上的动画帧

\


即使渲染操作能跟上屏幕刷新，这些计算也会在主线程上运行，这意味着当你的应用程序运行 JavaScript 时动画可能会被阻塞。

![](https://camo.githubusercontent.com/040f49e481f6734b8583debe27975c28f88d1f7e/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f706167656a616e6b322e706e67)

图 12：时间轴上的动画帧，但 JavaScript 阻塞了一帧

\


你可以将 JavaScript 操作划分为小块，并使用 `requestAnimationFrame()` 在每个帧上运行。有关此主题的更多信息，请参阅 [Optimize JavaScript Execution](https://developers.google.com/web/fundamentals/performance/rendering/optimize-javascript-execution)。你也可以[在 Web Worker 中运行 JavaScript](https://www.youtube.com/watch?v=X57mh8tKkgE) 以避免阻塞主线程。

![](https://camo.githubusercontent.com/eea6390ffecb848015841c9a76fbe5c11d238069/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f7261662e706e67)

图 13：时间轴上较小的 JavaScript 块与动画帧一起运行

\


### 3.8 合成

\


#### 3.8.1 如何绘制一个页面？

![](https://camo.githubusercontent.com/a16c683b7f029dbcd5c524b34c74dcf09c00a792/68747470733a2f2f692e6c6f6c692e6e65742f323031382f31302f30372f356262393830326536336539642e676966)

图 14：简单光栅处理示意动画

\


现在浏览器知道文档的结构、每个元素的样式、页面的几何形状和绘制顺序，它是如何绘制页面的？把这些信息转换为屏幕上的像素，我们称为光栅化。

\


处理这种情况的一种简单的方法是，先在光栅化视窗内的画面，如果用户滚动页面，则移动光栅框，并光栅化填充缺少的部分。这就是 Chrome 首次发布时处理光栅化的方式。但是，现代浏览器会运行一个更复杂的过程，我们称为合成。

\


#### 3.8.2 什么是合成

![](https://camo.githubusercontent.com/46f2961d0eba21a43e142d46446886bc2bc028ee/68747470733a2f2f692e6c6f6c692e6e65742f323031382f31302f30372f356262393830653932626237632e676966)

图 15：合成处理示意动画

\


合成是一种将页面的各个部分分层，分别光栅化，并在称为合成线程的单独线程中合成为页面的技术。如果发生滚动，由于图层已经光栅化，因此它所要做的只是合成一个新帧。动画也可以以相同的方式（移动图层和合成新帧）实现。

\


你可以在 DevTools 使用 [Layers 面板](https://blog.logrocket.com/eliminate-content-repaints-with-the-new-layers-panel-in-chrome-e2c306d4d752?gi=cd6271834cea) 看看你的网站如何被分层。

\


#### 3.8.3 分层

\


为了分清哪些元素位于哪些图层，主线程遍历布局树创建图层树（此部分在 DevTools 性能面板中称为“Update Layer Tree”）。如果页面的某些部分应该是单独图层（如滑入式侧面菜单）但没拆分出来，你可以使用 CSS 中的 `will-change` 属性来提示浏览器。

![](https://camo.githubusercontent.com/86ef34c09a66640f7a5376db25a48831ce359e8d/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f6c617965722e706e67)

图 16：主线程遍历布局树生成图层树

\


你可能想要为每个元素都分层，但是合成大量的图层可能会比每帧都光栅化页面的刷新方式更慢，因此测量应用程序的渲染性能至关重要。有关这个主题的更多信息，请参阅 [Stick to Compositor-Only Properties and Manage Layer Count](https://developers.google.com/web/fundamentals/performance/rendering/stick-to-compositor-only-properties-and-manage-layer-count)。

\


#### 3.8.4 主线程的光栅化和合成

\


一旦创建了图层树并确定了绘制顺序，主线程就会将该信息提交给合成线程。接着，合成线程会光栅化每个图层。一个图层可能会跟整个页面一样大，因此合成线程将它们分块后发送到光栅线程。光栅线程光栅化每个小块后会将它们存储在显存中。

![](https://camo.githubusercontent.com/73ad0483dfe9538bc00efc08f59905a0b5ba5085/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f7261737465722e706e67)

图17：光栅线程创建分块的位图并发送到 GPU

\


合成线程会给不同的光栅线程设置优先级，以便视窗（或附近）内的画面可以先被光栅化。图层还具有多个不同分辨率的块，可以处理放大操作等动作。

\


一旦块被光栅化，合成线程会收集这些块的信息（称为**绘制四边形**）创建**合成帧**。

\


绘制四边形

\


包含诸如图块在内存中的位置，以及合成时绘制图块在页面中的位置等信息。

\


合成帧

\


一个绘制四边形的集合，代表一个页面的一帧。

\


接着，合成帧通过 IPC（进程间通讯）提交给浏览器进程。此时，可以从 UI 线程或其他插件的渲染进程添加另一个合成帧。这些合成器帧被发送到 GPU 然后在屏幕上显示。如果接收到滚动事件，合成线程会创建另一个合成帧发送到 GPU。

![](https://camo.githubusercontent.com/fad0ba66e0eb67d0d25e46fe59d3d560f52dc96d/68747470733a2f2f646576656c6f706572732e676f6f676c652e636f6d2f7765622f757064617465732f696d616765732f696e736964652d62726f777365722f70617274332f636f6d706f7369742e706e67)

图 18：合成线程创建合成帧，将其发送到浏览器进程，再接着发送到 GPU

\


合成的好处是它可以在不涉及主线程的情况下完成。合成线程不需要等待样式计算或 JavaScript 执行。这就是为什么[仅合成动画](https://www.html5rocks.com/en/tutorials/speed/high-performance-animations/)被认为是流畅性能的最佳选择。如果需要再次计算布局或绘制，则必须涉及主线程。

\


### 3.9 小结

\


在这篇文章中，我们研究了渲染管道从解析到合成的整个过程，希望现在你能自主地去了解更多关于网站性能优化的信息。

\


在本系列的下一篇也是最后一篇文章中，我们将更详细地介绍合成线程，看看当用户移动或点击鼠标时会发生什么。

\


\


## 4. 事件机制

\


### 4.1 用户输入行为与合成器

\


内部揭秘系列博客对现代浏览器如何处理代码、显示页面展开探讨。该系列博客共四篇，这是最后一篇。在上篇博客里，我们了解了渲染进程与合成器。这里我们将一窥当用户输入行为发生时，合成器如何继续保障交互流畅。

\


### 4.2 浏览器视角下的输入事件

\


听到“输入事件”这个字眼，你脑海里闪现的恐怕只是输入文本或点击鼠标。但在浏览器眼中，输入意味着一切用户行为。不单滚动鼠标滑轮是输入事件，触摸屏幕、滑动鼠标同样也是用户输入事件。

\


诸如触摸屏幕之类用户手势产生时，浏览器进程会率先将其捕获。然而浏览器进程所掌握的信息仅限于行为发生的区域，因为标签页里的内容都由渲染进程负责处理，所以浏览器进程会将事件类型（如 `touchstart`）及其坐标发送给渲染进程。渲染进程会寻至事件目标，运行其事件监听器，妥善地处理事件。

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/input.png)

\


图 1：输入事件由浏览器进程发往渲染进程

\


### 4.3 合成器接收输入事件

\


![](https://i.loli.net/2018/10/08/5bbaaa3d26b97.gif)

\


图 2：悬于页面图层的视图窗口

\


在上篇文章里，我们探讨了合成器如何通过合成栅格化图层，实现流畅的页面滚动。如果页面上没有添加任何事件监听，合成器线程会创建独立于主线程的新合成帧。但要是页面上添加了事件监听呢？合成器线程又是如何得知事件是否需要处理的？

\


### 4.4 理解非立即可滚动区

\


因为运行 JavaScript 脚本是主线程的工作，所以页面合成后，合成进程会将页面里添加了事件监听的区域标记为“非立即可滚动区”。有了这个信息，如果输入事件发生在这一区域，合成进程可以确定应将其发往主线程处理。如输入事件发生在这一区域之外，合成进程则确定无需等待主线程，而继续合成新帧。

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/nfsr1.png)

\


图 3：非立即可滚动区输入描述示意图

\


#### 4.4.1 设置事件处理器时须注意

\


web 开发中常用的事件处理模式是事件代理。因为事件会冒泡，所以你可以在最顶层的元素中添加一个事件处理器，用来代理事件目标产生的任务。下面这样的代码，你可能见过，或许也写过。

\


```
document.body.addEventListener('touchstart', 
event => {
    if (event.target === area) {
        event.preventDefault();
    }
});
```

\


这样只需添加一个事件处理器，即可监听所有元素，的确十分省事。然而，如果站在浏览器的角度去考量，这等于把整个页面都标记成了“非立即可滚动区”，意味着即便你设计的应用本不必理会页面上一些区域的输入行为，合成线程也必须在每次输入事件产生后与主线程通信并等待返回。如此则得不偿失，使原本能保障页面滚动流畅的合成器没了用武之地。

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/nfsr2.png)

\


图 4：非立即可滚动区覆盖整个页面下的输入描述示意图

\


你可以给事件监听添加一个 [passive:true](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Improving\_scrolling\_performance\_with\_passive\_listeners) 选项 ，将这种负面效果最小化。这会提示浏览器你想继续在主线程中监听事件，但合成器不必停滞等候，可接着创建新的合成帧。

\


```
document.body.addEventListener('touchstart', event => {
    if (event.target === area) {
        event.preventDefault()
    }
 }, {passive: true});
```

\


### 4.5 检查事件是否可撤销

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/scroll.png)

\


图 5：部分区域仅可水平方向滚动的网页

\


设想一下这种情形：页面上有一个盒子，你要将其滚动方向限制为水平滚动。

\


为目标事件设置 `passive:true` 选项可让页面滚动平滑，但在你使用 `preventDefault` 以限制滚动方向时，垂直方向滚动可能已经触发。使用 `event.cancelable` 方法可以检查并阻止这种情况发生。

\


```
document.body.addEventListener('pointermove', event => {
    if (event.cancelable) {
        event.preventDefault(); // 阻止默认的滚动行为
        /*
        *  这里设置程序执行任务
        */
    } 
}, {passive:: true});
```

\


或者，你也可以应用 `touch-action` 这类 CSS 规则，完全地将事件处理器屏蔽掉。

\


```
#area { 
  touch-action: pan-x; 
}
```

\


### 4.6 定位事件目标

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/hittest.png)

\


图 6：主线程检查绘制记录查询坐标 x、y 处绘制内容

\


合成器将输入事件发送至主线程后，首先运行的是命中检测。命中检测会使用渲染进程中产生的绘制记录数据，找出事件发生坐标下的内容。

\


### 4.7 降低往主线程发送事件的频率

\


之前的文章里，我们探讨了常见显示屏如何以每秒 60 帧的频率刷新，以及我们要怎样与其刷新频率保持步调一致，以营造出流畅的动画效果。而对于用户的输入行为，常见触摸屏设备的事件传输频率为每秒 60\~120 次，常见鼠标设备的事件传输频率为每秒 100 次。可见，输入事件有着比显示屏幕更高的保真度。

\


如果一连串 `touchmove` 这样的事件以每秒 120 次的频率发送往主线程，那么可能会触发过量的命中检测及 JavaScript 脚本执行。相形而言，我们的屏幕刷新率则更为低下。

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/rawevents.png)

\


图 7：大量事件涌入合成帧时间轴会造成页面闪烁

\


为了降低往主线程中传递过量调用，Chrome 会合并这些连续事件（如：`wheel`, `mousewheel`, `mousemove`, `pointermove`, `touchmove` 等），并将其延迟至下一次 `requestAnimationFrame` 前发送。

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/coalescedevents.png)

\


图 8：相同的时间轴下事件被合并且延迟发送

\


所有独立的事件，如: `keydown`, `keyup`, `mouseup`, `mousedown`, `touchstart`, 及  `touchend` 则会立即发往主线程。

\


### 4.8 使用 `getCoalescedEvents` 获取帧内事件

\


事件合并可帮助大多数 web 应用构建良好的用户体验。然而，如果你开发的是一个绘图类应用，需要基于 `touchmove` 事件的坐标绘制线路，那么在你试图画下一根光滑的线条时，区间内的一些坐标点也可能会因事件合并而丢失。这时，你可以使用目标事件的  `getCoalescedEvents` 方法获取事件合并后的信息。

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/getCoalescedEvents.png)

\


图 9：左为流畅的触摸手势路径、右为事件合并后的有限路径

\


```
window.addEventListener('pointermove', event => {
    const events = event.getCoalescedEvents();
    for (let event of events) {
        const x = event.pageX;
        const y = event.pageY;
        // 使用 x、y 坐标画线
    }
});
```

\


### 4.9 后续步骤

\


本系列文章里，我们探讨了很多关于 web 浏览器内部的工作原理。如果之前你从来没想过：为什么 Devtools 推荐在事件处理器上添加 `{passive:true}` 选项、为什么有时须在 script 标签里添加 `async` 属性？那么我希望这一系列文章能帮助你了解，为什么传递这些信息给浏览器能让其提供更为迅捷流畅的 web 体验。

\


#### 4.9.1 使用 Lighthouse

\


如果你想构建出对浏览器更为友好的代码，却一直毫无头绪，那么不妨先从使用 [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 开始。Lighthouse 是个可以帮助你审查网站工具，并且能提供页面性能报告。性能报告会告诉你什么地方处理得当，什么地方有待提升。浏览审查列表也能让你了解浏览器着力关注的重点所在。

\


#### 4.9.2 学习如何评测性能

\


对于不同的站点，桎梏其性能之处可能不尽相同，所以专门为你自己的站点定制化一套性能评测方案，并择优选取技术应用，是重中之重。Chrome 的 Devtools 团队就 [如何测试你的站点性能](https://developers.google.com/web/tools/chrome-devtools/speed/get-started) 撰有相关教程可供参阅。

\


#### 4.9.3 为你的站点添加 Feature Policy

\


如果你想进一步采用更多方案，[Feature Policy](https://developers.google.com/web/updates/2018/06/feature-policy) 是一个新的 web 平台，可在开发时为你保驾护航。开启 feature policy 可以限制应用行为，并使你远离诸多技术弊端。举个例子，如果你想确保应用不会阻塞解析，那么可以采用同步脚本方案运行应用。开启 `sync-script:'none'` 后，导致解析阻塞的 JavaScript 脚本会被阻止运行。这就确保了你的代码不会阻塞解析，浏览器也无须考虑暂停运行解析器。

\


### 4.10 小结

\


![](https://developers.google.com/web/updates/images/inside-browser/part4/thanks.png)

\


刚踏上开发之路时，我几乎只关注怎样去写代码、怎样提升自己的生产效率。诚然，这些事情很重要，但与此同时我们也应当思考浏览器会怎么去处理我们书写的代码。现代浏览器一直致力探索如何提供更好的用户体验。书写对浏览器友好的代码，反过来也能提供友好的用户体验。路漫漫其修远兮，希望我们能携手共进，构建出对浏览器更为友好的代码。

\


\


## 5. 参考

\


* [https://github.com/xitu/gold-miner/blob/master/TODO1/inside-look-at-modern-web-browser-part1.md](https://github.com/xitu/gold-miner/blob/master/TODO1/inside-look-at-modern-web-browser-part1.md)
* [https://github.com/xitu/gold-miner/blob/master/TODO1/inside-browser-part2.md](https://github.com/xitu/gold-miner/blob/master/TODO1/inside-browser-part2.md)
* [https://github.com/xitu/gold-miner/blob/master/TODO1/inside-browser-part3.md](https://github.com/xitu/gold-miner/blob/master/TODO1/inside-browser-part3.md)
* [https://github.com/xitu/gold-miner/blob/master/TODO1/inside-browser-part4.md](https://github.com/xitu/gold-miner/blob/master/TODO1/inside-browser-part4.md)
* [https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/)
* [https://blog.csdn.net/dojiangv/article/details/51794535/](https://blog.csdn.net/dojiangv/article/details/51794535/)
* [https://zhuanlan.zhihu.com/p/47407398](https://zhuanlan.zhihu.com/p/47407398)

\
