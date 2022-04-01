# 基本概念

## 一、引言

计算机网络：是一个将分散的、具有独立功能的计算机系统，通过通信设备与线路连接起来，由功能完善的软件实现资源共享和信息传递的系统。

这里需要注意的是，按分布范围，计算机网络里有局域网 LAN 和广域网 WAN，其中局域网的代表以太网，以及这两种网络最重要的区分点，**局域网基于广播技术**，**广域网基于分组交换技术**。

## 二、计算机网络性能指标

### **1. 速率**

速率就是数据传输（数据是指0和1）的速率，比如你用迅雷下载，1兆每秒，来衡量目前数据传输的快慢。它是计算机网络中最重要的一个性能指标。

### **2. 带宽**

在计算机网络中，网络带宽是指在单位时间（一般指的是1秒钟）内能传输的数据量，比如说你家的电信网络是100兆比特，意思是，一秒内最大的传输速率是100兆比特。

### **3. 吞吐量**

吞吐量表示在单位时间内通过某个网络（或信道、接口）的数据量。

以上三点，我们举个案例

* 一条路每秒最多能过100辆车（宽带就相当于100辆/秒）。
* 而并不是每秒都会有100辆车过，假如第一秒有0辆，第二秒有10辆...，（但是最多不能超过100辆）。
* 所以有第1秒0辆/秒，第2秒10辆/秒，第3秒30辆/秒，这不能说带宽多少吧，于是就用吞吐量表示具体时间通过的量有多少（也有可能等于带宽的量）。
* 由此可知带宽是说的是最大值速率，吞吐量说的是某时刻速率。但吞吐量不能超过最大速率。

### **4. 时延**

时延是指数据（报文/分组/比特流）从网络（或链路）的一端传送到另一端所需的时间。单位是s。 时延分一下几种：

#### 4.1 发送时延

就是说我跟你说话，从我开始说，到说话结束这段时间，就是`发送时延`。

![发送时延](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b012e6acb9d34c26bdb1835810f2c537\~tplv-k3u1fbpfcp-watermark.awebp)

#### 4.2 传播时延

如gif图所示，信道上第一个比特开始，到最后一个比特达到主机接口需要的时间就是`传播时延`。

![传播时延](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9141f6056ff0469eb4a4fb6f5a9abda3\~tplv-k3u1fbpfcp-watermark.awebp)

#### 4.3 排队时延

* 分组在经过网络传输时，要经过很多的路由器。
* 但分组在进入路由器后要先在输入队列中排队等待处理。
* 在路由器确定了转发接口后，还要在输出队列中排队等待转发，这就产生了排队时延。
* 排队时延的长短往往却决于网络当时的通信量，当网络的通信量很大时会发生排队溢出，是分组丢失。

![排队时延](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d9659ec80a14539b7a5f2375f32708e\~tplv-k3u1fbpfcp-watermark.awebp)

#### 4.4 处理时延

路由器或主机在收到数据包时，要花费一定时间进行处理，例如分析数据包的首部、进行首部差错检验，查找路由表为数据包选定准发接口，这就产生了处理时延。

![处理时延](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/327c709e5a8f42ba9352744c51671080\~tplv-k3u1fbpfcp-watermark.awebp)

#### 4.5 往返时间（RTT）

在计算机网络中，往返时间也是一个重要的性能指标，它表示从发送方发送数据开始，到发送方收到来自接收方的确认（接受方收到数据后便立即发送确认）总共经历的时间。

#### 4.6 时延带宽积

是指传播时延乘以带宽。

## 三、参考

* [https://www.jianshu.com/p/254a9ee23a08](https://www.jianshu.com/p/254a9ee23a08)
* [https://juejin.cn/post/6844904079974465544](https://juejin.cn/post/6844904079974465544)