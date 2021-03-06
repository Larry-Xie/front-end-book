# 浏览器架构

## 一、引言

浏览器有很多功能，比如网络请求、页面渲染、JavaScript 执行和 Web 安全防范等等，而这些功能是分散在浏览器的各个功能组件中的，比较多也比较散，那么通过学习浏览器的多进程架构来把这些知识点串起来是很有必要的。

在学习多进程架构之前先来理解一些概念，比如 CPU 和 GPU，并行和并发、进程和线程。

## 二、CPU 和 GPU

为了了解浏览器运行的环境，我们需要了解几个计算机部件以及它们的作用。

### 1. CPU

![CPU 示例](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/14/165d59ba7a04aebe\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

第一个需要了解的计算机部件是 **中央处理器（Central Processing Unit）**，或简称为 **CPU**。CPU 可以看作是计算机的大脑。一个 CPU 核心如图中的办公人员，可以逐一解决很多不同任务。它可以在解决从数学到艺术一切任务的同时还知道如何响应客户要求。

过去 CPU 大多是单芯片的，一个核心就像存在于同芯片的另一个 CPU。随着现代硬件发展，你经常会有不止一个内核，为你的手机和笔记本电脑提供更多的计算能力。

### 2. GPU

![GPU 示例](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/14/165d59ba718eff29\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

**图形处理器**（**Graphics Processing Unit**，简称为 **GPU**）是计算机的另一部件。与 CPU 不同，GPU 擅长同时处理跨内核的简单任务。顾名思义，它最初是为解决图形而开发的。这就是为什么在图形环境中“使用 GPU” 或 “GPU 支持”都与快速渲染和顺滑交互有关。近年来随着 GPU 加速计算的普及，仅靠 GPU 一己之力也使得越来越多的计算成为可能。

当你在电脑或手机上启动应用时，是 CPU 和 GPU 为应用供能。通常情况下应用是通过操作系统提供的机制在 CPU 和 GPU 上运行。

![计算机体系结构](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/14/165d59ba722810d1\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

上图是一种简易的三层计算机体系结构。底部是机器硬件，中间是操作系统，顶部是应用程序。

## 二、并发和并行

### 1. 概念

首先了解下并发和并行的概念：

* **并发**：有任务 A 和 任务 B，在一段时间内通过任务之间的**切换**来完成这两个任务，这个情况是并发。
* **并行**：还是任务 A 和任务 B，但是 CPU 存在两个核心，可以**同时**执行这两个任务，这种情况是并行。

就好比我现在在敲代码，面前有一杯可乐，但是我必须先敲完代码才能去喝可乐，这种情况下我既不是并发也不是并行。

还是敲代码和喝可乐，我先敲一段代码，然后再去喝一口可乐之后继续敲代码，通过敲代码和喝可乐之间的**切换**，我完成了这两个任务，这种情况叫**并发**。

还是敲代码和喝可乐，我可以一边敲代码一边喝可乐，这两个任务可以**同时**执行，这种情况叫**并行**。

### 2. 比较

* **并发的关键是要有处理多个任务的能力，不一定要同时**。
* **并行的关键是要有同时处理多个任务的能力**。

关键在于：**是否是同时**。

## 三、进程和线程

### 1. 概念

* **进程**：**一个进程就是一个程序的运行实例**。详细解释就是，启动一个程序的时候，操作系统会为该程序创建一块内存，用来存放代码、运行中的数据和一个执行任务的主线程，我们把这样的一个运行环境叫进程。
* **线程**：**线程是依附于进程存在**，而进程中使用多线程并行处理能提升运算效率。

![进程和线程的关系](<../.gitbook/assets/image (7).png>)

### 2. 关系

进程和线程之间的关系有以下特点：

* **一个进程可以包含有多个线程**；
* **线程之间可以共享进程中的数据**；
* **进程之间的内容相互隔离，不同进程数据不能共享**；
* **进程中的任意一线程执行出错，都会导致整个进程的崩溃**；
* **当一个进程关闭之后，操作系统会回收进程所占用的内存**。

![进程和线程比较](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/16cdfb5c58ac470aa1cc5571ce36d036\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 3. 运行时

启动应用时会创建一个进程。程序也许会创建一个或多个线程来帮助它工作，这是可选的。操作系统为进程提供了一个可以使用的“一块”内存，所有应用程序状态都保存在该私有内存空间中。关闭应用程序时，相应的进程也会消失，操作系统会释放内存。

一个进程还可以要求操作系统生成另一个进程来执行不同的任务，系统会为新的进程分配独立的内存，两个进程之间可以通过 **IPC 机制**（Inter Process Communication）来进行通信。很多应用都会采用这样的设计，如果一个工作进程反应迟钝，重启这个进程不会影响应用其它进程的工作。

![IPC](<../.gitbook/assets/image (8).png>)

## 四、浏览器的架构

### 1. 架构示意图

那么如何通过进程和线程构建 web 浏览器呢？它可能由一个拥有很多线程的进程，或是一些通过 IPC 通信的不同线程的进程。这些不同的架构是实现细节。关于如何构建 web 浏览器并不存在标准规范。一个浏览器的构建方法可能与另一个迥然不同。

![不同浏览器架构的进程/线程示意图](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/14/165d59bcefbf5a45\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

而我们主要讨论的是常用的 Chrome 的架构。

Chrome 浏览器采用多进程架构，顶部是浏览器主进程，它与处理应用其它模块任务的进程进行协调。对于渲染进程来说，创建了多个渲染进程并分配给了每个标签页。直到最近，Chrome 在可能的情况下给每个标签页分配一个进程。而现在它试图给每个站点分配一个进程，包括 iframe。

![Chrome 多进程架构示意图](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/14/165d59bcf0dee2ef\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

![Chrome 架构图](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5d7a7adba4004f19a3cf0f0f2663231e\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. 各个进程功能

最新的 Chrome 浏览器包括：1 个浏览器主进程（Browser Process）、1 个 GPU 进程（GPU Process）、1 个网络进程（NetWork Process）、多个渲染进程（Renderer Process）和多个插件进程（Plugin Process）。各个进程的功能如下：

* **浏览器主进程**：主要负责页面显示、用户交互、子进程管理、文件存储等功能。
* **渲染进程**：核心任务是将 HTML、CSS 和 JavaScript 转换为用户可以与之交互的网页。
  * 渲染引擎 Blink 和 JavaScript 引擎 V8 都是运行在该进程中，默认情况下，Chrome 会为每个 Tab 标签创建一个渲染进程。
  * 出于安全考虑，渲染进程都是运行在沙箱模式下。
* **GPU 进程**：负责处理 GPU 相关的任务。用来绘制 Chrome 界面和网页内容。
* **网络进程**：主要负责页面的网络资源加载，之前是作为一个模块运行在浏览器进程里面的，之后独立处理成为一个单独的进程。
* **插件进程**：主要负责控制一个网页用到的所有插件。因为插件容易崩溃，所以需要通过插件进程来隔离，以保证插件进程崩溃不会对浏览器和页面造成影响。

![浏览器各个进程对应的功能](<../.gitbook/assets/image (5).png>)

### 3. 查看浏览器进程

我们可以通过 Chrome 浏览器提供的 **任务管理器** 功能来查看当前浏览器中运行的所有进程和每个进程所占用的系统资源，右键单击还可以查看更多类别信息。

通过浏览器的工具->任务管理器或者快捷键 `Shift + Esc` 可以打开任务管理器面板：

![Chrome 进程实例](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/71e34762ed04450fa8caf0d5236db88f\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 4. 站点隔离

[站点隔离](https://link.juejin.cn/?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Fupdates%2F2018%2F07%2Fsite-isolation) 是近期引入到 Chrome 中的一个功能，它为每个 iframe 运行一个单独的渲染进程。

同源策略是 web 的核心安全模型。同源策略确保站点在未得到其它站点许可的情况下不能获取其数据。安全攻击的一个主要目标就是绕过同源策略。进程隔离是分离站点的最高效的手段。随着 [Meltdown and Spectre](https://link.juejin.cn/?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Fupdates%2F2018%2F02%2Fmeltdown-spectre) 的出现，使用进程来分离站点愈发势在必行。Chrome 67 版本后，桌面版 Chrome 都默认开启了站点隔离，每个标签页的 iframe 都有一个单独的渲染进程。

![站点隔离示意图](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/14/165d59bd6de81ed2\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

启用站点隔离是多年来工程人员努力的结果。**站点隔离并不只是分配不同的渲染进程这么简单。它从根本上改变了 iframe 的通信方式**。在一个页面上打开开发者工具，让 iframe 在不同的进程上运行，这意味着开发者工具必须在幕后工作，以使它看起来无缝。即使运行一个简单的 Ctrl + F 来查找页面中的一个单词，也意味着在不同的渲染器进程中进行搜索。你可以看到为什么浏览器工程师把发布站点隔离功能作为一个重要里程碑！

### 5. 多进程架构优缺点

#### 优点

浏览器采用多进程的架构模型，通过进程之间的协作来实现网络请求、页面渲染、JavaScript 执行和 Web 安全防范等功能，并且 **提升了浏览器的稳定性、流畅性和安全性**。

* **稳定性**：进程之间相互隔离，某一个进程出现问题不会影响到其他进程。
  * 例如插件是比较容易出现问题的模块，如果是运行在同一个进程里面，插件的意外崩溃会引起整个浏览器的崩溃。
* **流畅性**：网络请求、页面渲染、JavaScript 执行环境和插件等运行在不同的进程里面，减少了线程阻塞的可能性。
  * 例如 JavaScript 是运行在渲染进程中的，所以即使 JavaScript 阻塞了渲染进程，影响到的也只是当前的渲染页面，而并不会影响浏览器和其他页面，因为其他页面的脚本是运行在它们自己的渲染进程中的。
* **安全性**：浏览器在系统层面上限定了不同进程的权限。
  * 例如渲染进程是运行在安全沙箱里面的，因为渲染进程所有的内容都是通过网络获取的，会存在一些恶意代码利用浏览器漏洞对系统进行攻击，所以运行在渲染进程里面的代码是不被信任的。

#### 缺点

不过凡事都有两面性，虽然浏览器的多进程模型提升了浏览器的稳定性、流畅性和安全性，但是也带来了一些其它的问题：例如**更高的资源占用和更复杂的体系结构**。

对于上面这两个问题，Chrome 团队也一直在寻求一种弹性方案，既可以解决资源占用高的问题，也可以解决复杂的体系架构的问题。

## 五、参考

* [https://juejin.cn/post/6974796760489132069](https://juejin.cn/post/6974796760489132069)
* [https://juejin.cn/post/6844903679389073415](https://juejin.cn/post/6844903679389073415)
