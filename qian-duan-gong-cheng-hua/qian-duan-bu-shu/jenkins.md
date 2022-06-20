# Jenkins

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2130be79949f4db3bdc644fe914296a1\~tplv-k3u1fbpfcp-zoom-crop-mark:1304:1304:1304:734.awebp?)

## 一、DevOps

提到 Jenkins，想到的第一个概念就是 CI/CD 在这之前应该再了解一个概念。

DevOps `Development` 和 `Operations` 的组合，是一种方法论，并不特指某种技术或者工具。DevOps 是一种重视 `Dev` 开发人员和 `Ops` 运维人员之间沟通、协作的流程。通过自动化的软件交付，使软件的构建，测试，发布更加的快捷、稳定、可靠。

## 二、CI

CI 的英文名称是`Continuous Integration`，中文翻译为：持续集成。

试想软件在开发过程中，需要不断的提交，合并进行单元测试和发布测试版本等等，这一过程是痛苦的。**持续集成`CI`是在源代码变更后自动检测、拉取、构建的过程。**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1adedfe48a414d0fb0fa710643b31491\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

## 三、CD

CD 对应两个概念 持续交付`Continuous Delivery` 持续部署`Continuous Deployment`

### 1. 持续交付

提交交付顾名思义是要拿出点东西的。在 CI 的自动化流程阶段后，运维团队可以快速、轻松地将应用部署到生产环境中或发布给最终使用的用户。

从前端的角度考虑，在某些情况下肯定是不能直接通过自动化的方式将最终的 build 结果直接扔到生产机的。持续交互就是可持续性交付供生产使用的的最终 build。最后通过运维或者后端小伙伴进行部署。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/efe048697960427c996a9f0c1bda602b\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

### 2. 持续部署

作为持续交付的延伸，持续部署可以自动将应用发布到生产环境。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dcfcdacd3a03408ba90e1ebc17af14e4\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

## 四、Jenkins 安装

> 示例服务器为 阿里云 CentOS 服务器。**安全组中增加 8080 端口 Jenkins 默认占用**

> Jenkins 安装大体分两种方式，一种使用 Docker 另一种则是直接安装，示例选择后者。**不管使用哪种方式安装，最终使用层面都是一样的。** [Linux 安装](https://link.juejin.cn/?target=https%3A%2F%2Fwww.jenkins.io%2Fdoc%2Fbook%2Finstalling%2Flinux%2F)， [Docker 安装](https://link.juejin.cn/?target=https%3A%2F%2Fwww.jenkins.io%2Fdoc%2Fbook%2Finstalling%2Fdocker%2F)

## 五、Jenkins 使用

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87d0fc3da64e4322a3ce23c1ed3ffdf4\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

首次进入使用 `cat /var/lib/jenkins/secrets/initialAdminPassword` 查看密码。

随后进入插件安装页面，暂时安装系统推荐插件即可。

然后创建用户

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5fb96f24c5dd4c6aa40a44842249c42a\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

### 1. 构建目标：拉取 github 代码

点击 **新建 Item** 创建一个 `Freestyle Project`

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b47056dd01e545719ca5b461c79b5f95\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

在 **源码管理** 处选择 git ，输入仓库地址，点击添加。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb0c956a428c4e5ca334b8e86ea8b711\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

输入 github 账号和密码，这里的密码有时候可能会出现问题，可以使用 `token` [github 如何生成 token ？](https://link.juejin.cn/?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Fauthentication%2Fkeeping-your-account-and-data-secure%2Fcreating-a-personal-access-token%23creating-a-token)

配置只是一方面，同时服务器也要具备 git 环境。 `yum install git`

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b06e27cf5424641a88f6eb64f35f8ed\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

### 2. 构建目标：部署到本机

部署前端项目肯定是离不开 `nginx` 的。 `yum install nginx`。

安装完成后同样可以使用 `systemctl` 命令管理 `nginx` 服务。

`nginx` 具体配置这里就不说了。本示例项目中，静态文件托管目录为 `/usr/share/nginx/html/dist`。

接着来到 `Jenkins` 这里。想要部署前端项目还需要依赖一个 `Node` 环境，需要在 **Manage Jenkins -> Manage Plugins** 在可选插件中搜索 `nodejs` 选择对应插件进行安装，安装完成后需要重启才会生效。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e3d1d823f7844ee6b2327f20eb6f8f0d\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

然后到 **系统管理 -> 全局工具配置** 中配置 `Node` (吐槽：没有安装任何插件时系统管理以及其子页面全是英文，安装完插件后又变成了中文。这国际化不知道是系统原因还是它的原因 😂)。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0c6f2871a7b4fb08ec0e9b41d18cb4f\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

随后去修改刚才创建的任务。在 **构建环境** 中会多出一个选项 `Provide Node & npm bin/ folder to PATH` 勾选即可。然后在 **构建** 中选择 **增加构建步骤 -> 执行 shell** 输入打包发布相关的命令。`Jenkins` 会逐行执行。

```bash
npm install yarn -g
yarn install
yarn build
# 打包 build 后的文件
tar -zcvf dist.tar.gz dist/
# 删除 build 后的文件
rm -rf dist/
# 移动 build 后的压缩包到 nginx 托管目录下。
sudo mv dist.tar.gz /usr/share/nginx/html
# 进入托管目录下
cd /usr/share/nginx/html
# 解压
sudo tar -zxcf dist.tar.gz
# 删除压缩包
sudo rm -rf dist.tar.gz
```

* 由于项目构建时是在 `Jenkins` 的工作目录下执行脚本，会出现权限问题。导致即使使用了 `sudo` 还会出现类似以下错误。

```
We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.
```

解决方案：在 `/etc/sudoers` 文件中增加 `jenkins ALL=(ALL) NOPASSWD:ALL` 表示在执行 sudo 时不需要输入密码。

* 如果不使用 `sudo` 则会出现以下错误。

```
xxxxxxx: Permission denied
```

解决方案：修改 `/lib/systemed/system/jenkins.service` 文件。将 `User=jenkins` 修改为 `User=root`，表示给 `Jenkins` 赋权限。修改配置文件后记得重启服务。

* 构建的过程中还可能出现以下错误

```
ERROR: Error fetching remote repo 'origin'
```

解决方案：由于需要构建的代码在 `github` 上面，这种错误表示拉取代码失败了，重试几次就可以了。

#### **工作目录**

上面提到一个很重要的概念就是 **工作目录** 在上面的 `shell` 默认就是在这里执行的。工作目录是由两部分组成。

* `/var/lib/jenkins/workspace/` 类似于前缀吧。
* `web-deploy` 这个其实是上面构建任务的名字。

总结：`Jenkins` 的执行目录是 `/var/lib/jenkins/workspace/web-deploy`。也就是说输入的每一条命令都是在这里面执行的。（搞清楚定位能避免好多问题，特别是前端的部署，就是打包，移动，解压很容易搞错路径。）

### 3. 构建目标：侦听 git 提交到指定分支进行构建

* 来到 `Jenkins` 中选择 **系统管理 -> 系统配置** 找到 `Jenkins URL` 将其复制。
* 随后在尾部添加 `github-webhook/` 尾部斜杠一定不要丢。 整体结构大致为 `http://192.168.0.1:8080/github-webhook/`
* 登录 `github` 需要集成的项目中添加 `webhook`。在 `Payload URL` 中将上述内容填入。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1229f4ca5c304430b0e56c21f31b5b37\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

* 然后修改 `Jenkins` 任务配置 **构建触发器中选择 GitHub hook trigger for GITScm polling**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4d6b334f428347ea8e33cb5a75e169da\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

由于在上面的**源码管理**中已经指定了`main`分支，此时如果这个分支的代码有改动就会触发自动构建。

### 4. 构建目标：部署到目标主机

> 在真实的开发场景中，`Jenkins` 几乎不会和前端资源放到一个服务器。大多数情况下 `Jenkins` 所处的服务器环境就是一个工具用的服务器，放置了一些公司中常用的工具。因此构建到指定的服务器也至关重要。

1，**系统管理 -> 插件管理** 搜索 `Publish Over SSH` 进行安装。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c7a452662a354788bc57395d0b6cb628\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

2，然后在**系统管理 -> 系统配置**中找到 `Publish over SSH` 点击新增，再点击高级，然后选中 `Use password authentication, or use a different key`

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/395b67bc78284d1eb395ff86a1ca61bc\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

完成后可点击右下角 `Test Confirguration` 进行测试。

3，继续修改构建任务。先修改原有的构建脚本。因为要发布到远程，所以原有的发布命令要进行去除。

```bash
npm install yarn -g
yarn install
yarn build
# 只打包，然后删除文件夹。
tar -zcvf dist.tar.gz dist/
rm -rf dist/
```

4，选择**构建后操作 -> Send build artifacts over SSH**

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99eb889ac4ee46d7855faeea5a0d2fe8\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

* `Rransfer Set Source files`：要上传到目标服务器的文件。**它是一个相对路径，相对于 Jenkins 的工作目录** 由于上面的 shell 执行之后在工作目录中只有一个压缩包，so 直接写一个文件名即可。
* `Remove prefix`：去前缀。假设此时打包文件在 `/var/lib/jenkins/workspace/web-deploy/assets/dist.tar.gz`，那么 `Rransfer Set Source files` 则应该为 `assets/dist.tar.gz`，此时 `Remove prefix` 配置为 `assets/` 则可以去除这个前缀，否则会在目标服务中创建 `assets` 。
* `Remote directory`：远程的静态资源托管目录。由于配置服务器默认为 `/`，所以 `usr/share/nginx/html/` 不用以 `/` 开头。
* `Exec command`：远程机执行 `shell`，由于配置服务器默认为 `/`， 所以 **工作目录也是以 `/` 开始**。

执行成功后查看执行日志会有类似以下结果：

```bash
SSH: Connecting from host [iZuf6dwyzch3wm3imzxgqfZ]
SSH: Connecting with configuration [aliyun-dev] ...
SSH: EXEC: completed after 202 ms
SSH: Disconnecting configuration [aliyun-dev] ...
# 如果 Transferred 0 file 则需要查看配置的路径是否正确。表示文件并没有被移动到远程主机中。
SSH: Transferred 1 file(s)
Finished: SUCCESS
```

### 5. 构建目标：钉钉机器人通知

1，**系统管理 -> 插件管理** 搜索 `DingTalk` 进行安装。[文档](https://link.juejin.cn/?target=https%3A%2F%2Fjenkinsci.github.io%2Fdingtalk-plugin%2F)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5ed382ed60aa4f1dbe5470d6bf36eb75\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

2，钉钉群创建机器人。**钉钉群 -> 只能群助手 -> 添加机器人 -> 自定义**

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8ece23c5bd34ba5b5a45298b2a5f6d3\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

3，定义机器人名字和关键字，创建完成后先将 `webhook` 中的内容复制。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e0bb0df1979344d5b562218e73761b9c\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

4，`Jenkins` 中 **系统管理 -> 系统配置 -> 钉钉 -> 新增** 配置完成后可点击右下角进行测试。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a40cb8630ccc431183218b851f39b407\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

5，修改构建任务配置。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0650e64aceee4a289a80d1f476e79062\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

* 通知人：atAll 勾选后 `@` 不到准确的人。😂。输入框内可填写需要被 `@` 人的手机号，多个换行。
* 自定义内容：支持 `markdown` 写法，可以使用一些环境变量。`192.168.0.1:8080/env-vars.html/`
* [实现默认 `@` 执行人](https://link.juejin.cn/?target=https%3A%2F%2Fjenkinsci.github.io%2Fdingtalk-plugin%2Fadvance%2Fuser-property.html)

6，构建成功

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/378c17c7bfe546aa81f74aee1413cd79\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

## 六、Pipline 构建

上一章节中着重介绍了如何构建 `freestyle` 的任务，但是 `Jenkins` 远不止于此。在本章开始之前强烈建议[阅读文档](https://link.juejin.cn/?target=https%3A%2F%2Fwww.jenkins.io%2Fzh%2Fdoc%2F)，重点关注流水线相关内容。

**新建任务 -> 选择流水线** 其他内容可以都不用管，只关注**流水线** 有两种选择，演示就选择第一种。

直接在 `Jenkins` 中书写配置。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1b8c9737c1f14c29a7aed0d57aa189d7\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

在项目的 `Jenkinsfile` 配置文件中写配置。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/08f1bd34e62c43169d266f04b65ca1e3\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

在正式开始之前应该了解 `Jenkins Pipline` 的基础概念。

```javascript
pipeline {
    agent any // 在任何可用的代理上，执行流水线或它的任何阶段。
    stages {
        stage('Build') { // 定义 "Build" 阶段。
            steps {
                // 执行与 "Build" 阶段相关的步骤。
            }
        }
        stage('Deploy') { // 定义 "Deploy" 阶段。
            steps {
                // 执行与 "Deploy" 阶段相关的步骤。
            }
        }
    }
}
```

* `pipline`： 定义流水线整个结构，可以看做是根节点
* `agent`：指示 `Jenkins` 为整个流水线分配一个执行器，比如可以配置 `Docker`
* `stages`：对整个 `CI` 流的包裹，个人认为没多大用，还必须得有。
* `stage`： 可以理解为是对某一个环节的描述。注意：参数就是描述内容，可以是任何内容。不要想歪了只能传递 `Build` `Deploy` 这些。
* `steps`： 描述了 `stage` 中的步骤，可以存在多个。

了解到这里还是不够的。[流水线入门](https://link.juejin.cn/?target=https%3A%2F%2Fwww.jenkins.io%2Fzh%2Fdoc%2Fbook%2Fpipeline%2F) [流水线语法参考](https://link.juejin.cn/?target=https%3A%2F%2Fwww.jenkins.io%2Fzh%2Fdoc%2Fbook%2Fpipeline%2Fsyntax%2F)

#### Pipline 复刻 Freestyle

这里先直接把配置贴出来。后续结合内容在进行分析。

```
// 自定义 钉钉插件 的 错误信息和成功信息
def successText = [
    """ ### 新的构建信息，请注意查收""",
    """ ${env.JOB_BASE_NAME}任务构建<font color=green>成功</font> ，点击查看[构建任务 #${env.BUILD_NUMBER}](http://106.14.185.47:8080/job/${env.JOB_BASE_NAME}/${env.BUILD_NUMBER}/)"""
]
def failureText = [
    """ ### 新的构建信息，请注意查收""",
    """ ${env.JOB_BASE_NAME}任务构建<font color=red>失败</font> ，点击查看[构建任务 #${env.BUILD_NUMBER}](http://106.14.185.47:8080/job/${env.JOB_BASE_NAME}/${env.BUILD_NUMBER}/)"""
]
// 1，侦听 github push 事件
properties([pipelineTriggers([githubPush()])])

pipeline {
    agent any
    // 环境变量定义。
    environment {
        GIT_REPO = 'http://github.com/vue-ts-vite-temp.git'
    }
    stages {
        // 2，拉取 github 代码，通过 GitSCM 侦听 push 事件。
        stage('Pull code') {
            steps {
                checkout(
                    [
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        extensions: [],
                        userRemoteConfigs: [
                            [
                                credentialsId: '381325e4-0f9c-41ea-b5f6-02f8ea2a475a',
                                url: env.GIT_REPO
                            ]
                        ],
                        changelog: true,
                        poll: true,
                    ]
                )
            }
        }
        stage('Install and build') {
            steps {
                // 3，前面安装过的 nodejs 插件使用
                nodejs('v14.19.0') {
                    sh 'npm install yarn -g'
                    sh 'yarn install'
                    sh 'yarn build'
                }
            }
        }
        stage('Pack') {
            steps {
                sh 'tar -zcvf dist.tar.gz dist/'
                sh 'rm -rf dist/'
            }
        }
        stage('Deploy') {
            steps {
                // 4，前面下载的 Publish Over SSH 插件的使用
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'aliyun-dev',
                            transfers: [
                                sshTransfer(
                                    cleanRemote: false,
                                    excludes: '',
                                    execCommand: '''
                                        cd /usr/share/nginx/html/
                                        tar -zxvf dist.tar.gz
                                        rm -rf dist.tar.gz
                                    ''',
                                    execTimeout: 120000,
                                    flatten: false,
                                    makeEmptyDirs: false,
                                    noDefaultExcludes: false,
                                    patternSeparator: '[, ]+',
                                    remoteDirectory: '/usr/share/nginx/html/',
                                    remoteDirectorySDF: false,
                                    removePrefix: '',
                                    sourceFiles: 'dist.tar.gz'
                                )
                            ],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: false
                        )
                    ]
                )
            }
        }
    }
    post {
        success {
            // 5，DingTalk 插件的使用。
            dingtalk (
                robot: '1314',
                type: 'ACTION_CARD',
                title: 'Jenkins构建提醒',
                text: successText,
                btns: [
                    [
                        title: '控制台',
                        actionUrl: 'http://106.14.185.11:8080/'
                    ],
                    [
                        title: '项目预览',
                        actionUrl: 'http://github.com/'
                    ],
                ],
                at: []
            )
        }
        failure {
            dingtalk(
                robot: '1314',
                type: 'ACTION_CARD',
                title: 'Jenkins构建提醒',
                text: failureText,
                btns: [
                    [
                        title: '控制台',
                        actionUrl: 'http://106.14.185.11:8080/'
                    ],
                    [
                        title: '项目预览',
                        actionUrl: 'http://github.com/'
                    ],
                ],
                at: []// 这里是手机号多个之间,隔开
            )
        }
    }
}

```

这么多内容手写无疑是很难受的，好在 `Jenkins` 提供了一些帮助工具。访问地址为：`Jenkins`地址 + `/job` + 当前任务 + `/pipeline-syntax/`，例如：`http://localhost:8080/job/dev-deploy/pipeline-syntax/`，或者进入任务构建页面，点击**流水线语法**进入

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a651b7d67ea4ed8af49e1d7b3db4f97\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

进入该页面后请熟读并背诵以下三项。重点放到第一项。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3f7205934a774dad822917b16e33d3f5\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

回头看上面的脚本注释都带有序号。根据注释序号开始解释。

1，在片段生成器中选择 `properties: Set job properties` 生成代码片段。由于只是使用了 `git hook trigger` 所以要对生成的片段稍作修改。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a267d467ebf9415f913e84030a04ca24\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

2，如果不是为了侦听 `github push` 选择 `git: Git` 即可，但现在应该选择 `checkout: Check out from version control`，随后填写信息生成代码即可。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fe78cf11eebf4007962b55df58d08769\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

3，选择 `nodejs: Provide Node & npm bin/folder to Path`

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6424f3b947d147bc81b96407be58bba6\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

4，选择 `sshPublisher: Send build artifacts over SSH`，像上面流水线一样配置之后直接生成代码即可。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4bac03e041dd4e639fb3f6eb26ec2902\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

5，[`DingTalk` 文档](https://link.juejin.cn/?target=undefined)

**总结：** 通过插件生成的代码，稍作组合就成为了完整的配置。但整体难度还是要略高于 `Freestyle` 任务。毕竟生成的代码有部分也不是拿来即用的，并且 **Pipline 基本语法一定要有所掌握**。不然生成的代码都不晓得放到哪里合适。

## 七、参考

* [保姆级教学 Jenkins 部署前端项目](https://juejin.cn/post/7102360505313918983)
