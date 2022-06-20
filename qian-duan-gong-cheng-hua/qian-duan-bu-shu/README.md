# 前端部署

## 一、引言

部署是指将代码发布到服务器的一种行为。自动化部署就是使用工具来帮助你实现部署的过程，无需亲自动手。

在没有学会自动化部署前，我是这样部署项目的：

1. 执行测试 `npm run test`。
2. 构建项目 `npm run build`。
3. 将打包好的文件放到静态服务器。

偶尔一次两次还行，如果每次部署项目都这样，就会把很多时间浪费在重复的操作上。所以我们要学会自动部署，彻底解放双手。

## 二、自动部署

自动部署（又叫持续部署 Continuous Deployment，英文缩写 CD）一般有两种触发方式：

1. 定时触发。
2. 监听 `webhook` 事件，事件触发时执行自动打包、部署等操作。

### 1. 定时触发 <a href="#ding-shi-chu-fa" id="ding-shi-chu-fa"></a>

定时触发，就是构建软件每隔一段时间自动执行打包、部署操作。

这种方式不太好，很有可能软件刚部署完开发就改代码了。为了看到新的页面效果，不得不等到下一次构建开始。另外还有一个副作用，假如开发一天都没更改代码，构建软件还是会不停的执行打包、部署操作，白白的浪费资源。

所以现在基本都是采用监听 `webhook` 事件的方式来进行部署。

### 2. 监听 `webhook` 事件 <a href="#jian-ting-webhook-shi-jian" id="jian-ting-webhook-shi-jian"></a>

webhook 钩子函数，就是在你的构建软件上进行设置，监听某一个事件（一般是监听 `push` 事件），当事件触发时，自动执行定义好的脚本。

例如 `Github Actions`，就有这个功能。

![](https://img-blog.csdnimg.cn/img\_convert/220d8459a48a91fdd5c60968712f1a81.png)

而对于部署本身而言，可以使用 [Jenkins (opens new window)](https://www.jenkins.io/zh/)

## 三、部署方式 <a href="#bu-shu-fang-shi" id="bu-shu-fang-shi"></a>

部署有很多种方式，据我所知的有：蓝绿部署、滚动发布、灰度发布等等。当然，还有更简单的方式，直接停掉服务器，上传代码后再重新开启服务器。不过这种方式有一个很大的缺点：在服务器重启过程中，用户无法访问网站的服务，所以你可能会收到大量的投诉。

下面让我们来简单地了解一下这三种部署方式的区别吧（参考自[脉冲云文档 (opens new window)](https://maichong.io/help/deployment/canary.html)）。

### 1. 蓝绿部署 <a href="#lan-lv-bu-shu" id="lan-lv-bu-shu"></a>

![](https://img-blog.csdnimg.cn/img\_convert/3aaf991ad66c6070beba781024dda558.png)

蓝绿部署是指在部署过程中同时运行两个版本的程序。部署新版本时，不停掉旧版本的服务器，然后等新版本运行起来后，再将流量切换到新版本。缺点是在部署过程中，需要配置双倍的服务器。

### 2. 滚动发布 <a href="#gun-dong-fa-bu" id="gun-dong-fa-bu"></a>

滚动发布是指在升级过程中，逐台逐台的替换旧版本服务器。先启动一台新版本的服务器，再停掉一台旧版本的服务器。这样在部署过程中只需要 N + 1 台的服务器。

![](https://img-blog.csdnimg.cn/img\_convert/bc3696dbf56407661330d3e8dfabb99b.png)

### 3. 灰度发布 <a href="#hui-du-fa-bu" id="hui-du-fa-bu"></a>

灰度发布也叫金丝雀发布，起源是，矿井工人发现，金丝雀对瓦斯气体很敏感，矿工会在下井之前，先放一只金丝雀到井中，如果金丝雀不叫了，就代表瓦斯浓度高。

![](https://img-blog.csdnimg.cn/img\_convert/dfd738a930f2b24a1cfbc30897de059a.png)

灰度发布在新开启一台服务器后，先不将流量切换过来。而是先由测试人员对其进行测试，如果运行没问题，再将流量切换过来。同时在运行期间收集各种数据，如果此时将新旧版本的数据进行对比，就是所谓的 A/B 测试。

当发现新版本运行良好后，再将剩下的服务器用同样的过程逐步替换。最后完全关掉旧版本的服务器，完成灰度发布。

如果在发布过程中发现新版本有问题，就可以将流量切回到旧版本服务器，这样将负面影响控制到最小。

## 四、参考

* [带你入门前端工程 - 前端部署](https://woai3c.gitee.io/introduction-to-front-end-engineering/06.html#gitea-jenkins-%E8%87%AA%E5%8A%A8%E6%9E%84%E5%BB%BA%E5%89%8D%E7%AB%AF%E9%A1%B9%E7%9B%AE%E5%B9%B6%E9%83%A8%E7%BD%B2%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8)
