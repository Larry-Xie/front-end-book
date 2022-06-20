# 统一规范

## 一、引言 <a href="#dai-ma-gui-fan" id="dai-ma-gui-fan"></a>

统一规范是前端工程化的重要一环，不管是开发规范还是流程规范，都需要有个统一的标准来遵守，以保障各方面的顺利进展。

## 二、代码规范 <a href="#dai-ma-gui-fan" id="dai-ma-gui-fan"></a>

代码规范是指程序员在编码时要遵守的规则，规范的目的就是为了让程序员编写易于阅读、可维护的代码。

试想一下，一个几十万行代码的项目，存在几种不同的代码规范，阅读起来是什么感受？连代码缩进使用空格还是 Tab 都能引发不少程序员的争论，可以说统一代码规范是非常重要的事情。

统一代码规范除了刚才所说的两点外，还有其他好处：

* 规范的代码可以促进团队合作
* 规范的代码可以降低维护成本
* 规范的代码有助于 code review（代码审查）
* 养成代码规范的习惯，有助于程序员自身的成长

当团队的成员都严格按照代码规范来写代码时，可以保证每个人的代码看起来都像是一个人写的，看别人的代码就像是在看自己的代码（代码一致性），阅读起来更加顺畅。更重要的是我们能够认识到规范的重要性，并坚持规范的开发习惯。

### 1. 制订代码规范 <a href="#ru-he-zhi-ding-dai-ma-gui-fan" id="ru-he-zhi-ding-dai-ma-gui-fan"></a>

代码规范一般包含了代码格式规范、变量和函数命名规范、文档注释规范等等。

#### **1.1 代码格式**

一般是指代码缩进使用空格还是 Tab、每行结尾要不要加分号、左花括号需不需要换行等等。

#### **1.2 命名规范**

命名规范一般指命名是使用驼峰式、匈牙利式还是帕斯卡式；用名词、名词组或动宾结构来命名。

```
const smallObject = {} // 驼峰式，首字母小写
const SmallObject = {} // 帕斯卡式，首字母大写
const strName = 'strName' // 匈牙利式，前缀表示了变量是什么。这个前缀 str 表示了是一个字符串
```

变量命名和函数命名的侧重点不同。

变量命名的重点是表明这个变量“是什么”，倾向于用名词命名。而函数命名的重点是表明这个函数“做什么”，倾向于用动宾结构来命名（动宾结构就是 `doSomething`）。

```
// 变量命名示例
const appleNum = 1
const sum = 10

// 函数命名示例
function formatDate() { ... }
function toArray() { ... }
```

由于拼音同音字太多，千万不要使用拼音来命名。

#### **1.3 文档注释**

文档注释比较简单，例如单行注释使用 `//`，多行注释使用 `/**/`。

```
/**
 * 
 * @param {number} a 
 * @param {number} b 
 * @return {number}
 */
function add(a, b) {
    return a + b
}

// 单行注释
const active = true
```

如果要让团队从头开始制订一份代码规范，工作量会非常大，也不现实。所以强烈建议找一份比较好的开源代码规范，在此基础上结合团队的需求作个性化修改。

下面列举一些比较出名的 JavaScript 代码规范：

* [airbnb (101k star 英文版) (opens new window)](https://github.com/airbnb/javascript)，[airbnb-中文版(opens new window)](https://github.com/lin-123/javascript)
* [standard (24.5k star) 中文版(opens new window)](https://github.com/standard/standard/blob/master/docs/README-zhcn.md)
* [百度前端编码规范 3.9k star(opens new window)](https://github.com/ecomfe/spec)

CSS 代码规范也有不少，例如：

* [styleguide 2.3k star(opens new window)](https://github.com/fex-team/styleguide/blob/master/css.md)
* [spec 3.9k star(opens new window)](https://github.com/ecomfe/spec/blob/master/css-style-guide.md)

#### **1.4 注释规范**

有同学可能会听过这样一种说法：好的代码是不需要写注释的。其实这种说法有点片面。

如果你写的函数类似于以下这种：

```
function timestampToDate(timestamp = 0) {
    if (/\s/.test(timestamp)) {
        return timestamp
    }

    let date = new Date(timestamp)
    return date.toLocaleDateString().replace(/\//g, '-') + ' ' + date.toTimeString().split(' ')[0]
}

function objToUrlParam(obj = {}) {
    let param = ''
    for (let key in obj) {
        param += '&' + key + '=' + obj[key]
    }
    
    return param? '?' + param.substr(1) : ''
}
```

那不写注释很正常，代码逻辑简单，变量、函数命名完全契合代码逻辑。

但在工作中还有很多业务逻辑很复杂的需求，很有可能一个函数要写很多代码，再好的函数命名、变量命名也不一定能看懂代码逻辑。并且有些业务逻辑会跨多个模块，需要跟不同模块的函数打交道。

像这种复杂的代码，还有绕来绕去的业务逻辑，如果不写注释，分分钟变成传说中的“屎山”。

我们平时强调的代码规范、项目规范、重构等等，不就是为了减少沟通，提高开发效率吗。写注释的目的也是为了让代码更加容易理解，以后出问题了，也能快速定位问题，从而解决问题。

所以我觉得这个说法应该这样理解：不是不写注释，而是不写垃圾注释。

什么是垃圾注释？罗里吧嗦一大堆讲不到重要的就是垃圾注释，注释应该着重描述“做了什么”而不是“怎么做”。

```
function objToUrlParam(obj = {}) {
    let param = ''
    for (let key in obj) {
        param += '&' + key + '=' + obj[key]
    }
    
    return param? '?' + param.substr(1) : ''
}
```

例如上面这个函数，你可以这样写注释：“将对象转化为 URL 参数”。也可以这样写：“首先遍历对象，获取每一个键值对，将它们拼在一起，最后在前面补个问号，变成 URL 参数”。

第一个注释虽然描述做了什么，但对于这么简单的函数来说是不用注释的。第二个注释是垃圾注释的典型示例，描述了怎么做。

下面再看一个辣眼睛的：

```
public class Program  
{  
    static void Main(string[] args)  
    {  
        /* 这个程序是用来在屏幕上  
         * 循环打印1百万次”I Rule!”  
         * 每次输出一行。循环计数  
         * 从0开始，每次加1。  
         * 当计数器等于1百万时，  
         * 循环就会停止运行*/  
 
        for (int i = 0; i < 1000000; i++)  
        {  
            Console.WriteLine(“I Rule!”);  
        }  
    }  
}
```

总的来说，注释是必要的，并且要写好注释，着重描述代码做了什么。如果还有人说不写注释，让他看看 linux 项目去，每一个文件都有注释。

### 2. 检查代码规范 <a href="#ru-he-jian-cha-dai-ma-gui-fan" id="ru-he-jian-cha-dai-ma-gui-fan"></a>

规范制订下来了，那怎么确保它被严格执行呢？目前有两个方法：

1. 使用工具校验代码格式。
2. 利用 code review 审查变量命名、注释。

建议使用这两个方法双管齐下，确保代码规范被严格执行。

#### **2.1 ESLint**

> ESLint最初是由Nicholas C. Zakas 于2013年6月创建的开源项目。它的目标是提供一个插件化的javascript代码检测工具。

* 下载依赖

```
// eslint-config-airbnb-base 使用 airbnb 代码规范
npm i -D babel-eslint eslint eslint-config-airbnb-base eslint-plugin-import
```

* 配置 `.eslintrc` 文件

```
{
    "parserOptions": {
        "ecmaVersion": 2019
    },
    "env": {
        "es6": true,
    },
    "parser": "babel-eslint",
    "extends": "airbnb-base",
}
```

* 在 `package.json` 的 `scripts` 加上这行代码 `"lint": "eslint --ext .js test/ src/"`。然后执行 `npm run lint` 即可开始验证代码。代码中的 `test/ src/` 是要进行校验的代码目录，这里指明了要检查 `test`、`src` 目录下的代码。

不过这样检查代码效率太低，每次都得手动检查。并且报错了还得手动修改代码。

为了改善以上缺点，我们可以使用 VSCode。使用它并加上适当的配置可以在每次保存代码的时候，自动验证代码并进行格式化，省去了动手的麻烦（下一节讲如何使用 VSCode 自动格式化代码）。

#### 2.2 Code Review 代码审查

代码审查是指让其他人来审查自己代码的一种行为。审查有多种方式：例如结对编程（一个人写，一个人看）或者统一某个时间点大家互相做审查（单人或多人）。

代码审查的目的是为了检查代码是否符合代码规范以及是否有错误，另外也能让评审人了解被审人所写的功能。经常互相审查，能让大家都能更清晰地了解整个项目的功能，这样就不会因为某个核心开发人员离职了而引起项目延期。

当然，代码审查也是有缺点的：一是代码审查非常耗时，二是有可能引发团队成员争吵。据我了解，目前国内很多开发团队都没有代码审查，包括很多大厂。

个人建议在找工作时，可以询问一下对方团队是否有测试规范、测试流程、代码审查等。如果同时拥有以上几点，说明是一个靠谱的团队，可以优先选择。

## 三、git 规范 <a href="#git-gui-fan" id="git-gui-fan"></a>

git 规范一般包括两点：分支管理规范和 git commit 规范。

### 1. 分支管理规范 <a href="#fen-zhi-guan-li-gui-fan" id="fen-zhi-guan-li-gui-fan"></a>

一个项目可以创建两个分支：master 和 dev。master 对应线上分支，不能直接在 master 分支上写代码，开发时需要从 master 上拉一个 dev 分支进行开发。

#### **1.1 开发新功能**

当团队成员开发新功能时，需要从 dev 上拉一个 `feature-功能名称-开发姓名` 分支进行开发，例如：`feature-login-tgz`。开发完成后需要合并回 dev 分支。

#### **1.2 修改 bug**

当团队成员修改 bug 时，需要从有 bug 的分支（环境）上拉一个 `bug-功能名称-开发姓名` 分支进行修复，例如：`bug-login-tgz`。修复完成后需要合并回原来出现 bug 的分支。

以 `feature` 或 `bug` 开始的分支都属于临时分支，在通过测试并上线后需要将临时分支进行删除。避免 git 上出现太多无用的分支。

#### **1.3 合并分支**

在将一个分支合并到另一个分支时（例如将 `feature-*` 合并到 dev），需要查看自己的新分支中有没有多个重复提交或意义不明的 commit。如果有，则需要对它们进行合并（git rebase）。示例：

```
# 这两个 commit 可以合并成一个
chore: 修改按钮文字
chore: 修改按钮样式

# 合并后
chore: 修改按钮样式及文字
```

**注意**：在将 `feature-*` 合并到 dev 时，需要先将 dev 分支合并到 `feature-*` 分支，然后再将 `feature-*` 合并到 dev 分支，避免出现代码冲突的情况。同理，合并 `bug-*` 分支也一样。

#### **1.4 部署**

当 dev 分支通过测试后，就可以合并到 master 进行发布了。

#### **1.5 发布时可能出现的意外情况**

举个例子，假设程序要新增 a、b 两个功能，我们的操作流程是这样的：

1. 从 dev 分支拉两个新分支 `feature-a-tgz`、`feature-b-tgz`。
2. 开发完成合并回 dev。
3. dev 测试完毕后，合并到 master 进行发布。

如果这时突然被告知 b 功能不上，只上 a 功能。我们可以将 `feature-a-tgz` 分支重新部署到测试环境，这样就不用做任何的代码回滚。只要 `feature-a-tgz` 分支测试通过就可以直接合到 master 进行线上发布。

### 2. git commit 规范 <a href="#gitcommit-gui-fan" id="gitcommit-gui-fan"></a>

git 在每次提交时，都需要填写 commit message。

```
git commit -m 'this is a test'
```

commit message 就是对你这次的代码提交进行一个简单的说明，好的提交说明可以让人一眼就明白这次代码提交做了什么。

![](https://img-blog.csdnimg.cn/img\_convert/6ddb0c3a6a923d70d4d8a72229a2e9b6.png)

既然明白了 commit message 的重要性，那我们就更要好好的学习一下 commit message 规范。下面让我们看一下 commit message 的格式：

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

我们可以发现，commit message 分为三个部分(使用空行分割):

1. 标题行（subject）: 必填, 描述主要修改类型和内容。
2. 主题内容（body）: 描述为什么修改, 做了什么样的修改, 以及开发的思路等等。
3. 页脚注释（footer）: 可以写注释，放 BUG 号的链接。

#### **2.1 type**

commit 的类型：

* feat: 新功能、新特性
* fix: 修改 bug
* perf: 更改代码，以提高性能（在不影响代码内部行为的前提下，对程序性能进行优化）
* refactor: 代码重构（重构，在不影响代码内部行为、功能下的代码修改）
* docs: 文档修改
* style: 代码格式修改, 注意不是 css 修改（例如分号修改）
* test: 测试用例新增、修改
* build: 影响项目构建或依赖项修改
* revert: 恢复上一次提交
* ci: 持续集成相关文件修改
* chore: 其他修改（不在上述类型中的修改）
* release: 发布新版本

#### **2.2 scope**

commit message 影响的功能或文件范围, 比如: route, component, utils, build...

#### **2.3 subject**

commit message 的概述

#### **2.4 body**

具体修改内容, 可以分为多行.

#### **2.5 footer**

一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接.

## 四、项目规范 <a href="#xiang-mu-gui-fan" id="xiang-mu-gui-fan"></a>

项目规范主要是指项目文件的组织方式和命名方式。统一项目规范是为了方便管理与修改，不会出现同样性质的文件出现在不同的地方。例如同样是图片，一个出现在 `assets` 目录，一个出现在 `img` 目录。

创建目录，需要按照用途来划分。例如较常见的目录有：文档 `doc`、资源 `src`、测试 `test`...

```
├─doc
├─src
├─test
```

`src` 资源目录又可以细分：

```
├─api
├─asset
├─component
├─style
├─router
├─store
├─util
└─view
```

现在文件命名有很多种方式（是否简写 `img` `image`、是否复数 `img` `imgs`、文件名过长是用驼峰还是用-连接 `oneTwo` `one-two`）。其实用哪种方式不重要，最重要的是命名方式一定要统一。

例如团队成员有人命名目录喜欢用复数形式（`apis`），有人喜欢用单数（`api`），这样是不允许的，一定要统一。

## 五、UI 规范 <a href="#ui-gui-fan" id="ui-gui-fan"></a>

注意，这里的 UI 规范是指项目里常用 UI 组件的表现方式以及组件的命名方式，而不是指 UI 组件如何设计。

### 1. 表现方式 <a href="#biao-xian-fang-shi" id="biao-xian-fang-shi"></a>

现在开源的 UI 组件库有很多，不同的组件库的组件表现方式也不一样。例如有些按钮组件点击时颜色变深，有些组件则是变浅。所以建议在 PC 端和移动端都使用统一的 UI 组件库（PC 端、移动端各一个），或者同一个项目里只使用一个 UI 组件库。

另外，项目里常用的组件表现方式也需要通过文档确定下来。例如收缩展开的动画效果，具体到动画持续时间、动画是缓进快出还是快进缓出等等。

如果不把这些表现方式的规范确定下来，就有可能出现以下这种情况：

1. 同样的组件，在不同的页面有不同的表现方式（例如动画效果）。因为没有规范，开发根据个人喜好添加表现效果。
2. 同样的二次确认弹窗，提示语不一样，按钮类型也不一样。

### 2. 统一命名 <a href="#tong-yi-ming-ming" id="tong-yi-ming-ming"></a>

统一命名，也是为了减少沟通成本。

举个例子，现在的日期组件可以选单个日期、也可以选择范围日期，有的还可以选择时间。这样一来，一个日期组件就有四种情况：

1. 单个日期带时间
2. 单个日期不带时间
3. 日期范围带时间
4. 日期范围不带时间

如果这种情况不区分好，开发在看产品文档的时候就会疑惑，从而增加了开发与产品的沟通成本。

综上所述，我们可以发现制定 UI 规范的好处有两点：

1. 统一页面 UI 标准，节省 UI 设计时间。
2. 减少沟通成本，提高前端开发效率。

## 六、参考 <a href="#xiao-jie" id="xiao-jie"></a>

* [带你入门前端工程 - 代码规范](https://woai3c.gitee.io/introduction-to-front-end-engineering/02.html#%E4%BB%A3%E7%A0%81%E8%A7%84%E8%8C%83)
