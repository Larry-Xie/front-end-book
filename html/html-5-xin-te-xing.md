# HTML 5 新特性

## 一、引言

HTML 5 增加了很多新的特性和标签。

## 二、结构标签

* \<header> ：标记定义一个页面或一个区域的头部
* \<nav> 标记定义导航链接
* \<section> 标记定义一个区域
* \<aside> 标记定义页面内容部分的侧边栏
* \<article> 标记定义一篇文章
* \<hgroup> 标记定义文件中一个区块的相关信息
* \<figure> 标记定义一组媒体内容以及它们的标题
* \<figcaption>标记定义 figure 元素的标题。
* \<dialog> 标记定义一个对话框(会话框)类似微信
* \<footer> 标记定义一个页面或一个区域的底部

![HTML 5 结构标签](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/2/16bb28ce64c66e7f\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

## 三、多媒体标签

### **1. video**

_兼容性：_

* safari支持mp4、不支持webm和ogv
* ie8（包含）以下都不支持 video 标签
* ie9 支持 video 标签（但只支持 mp4 ）

```html
<video src="..."
       autoplay="autoplay"
       controls="controls"
       loop="loop"
       width="500"
       height="500"
       poster="..."
       muted>

//其中loop设置循环 poster设置封面 muted静音
```

### **2. audio**

_兼容性：_

* safari支持mp3和wav、不支持ogg
* JS中new Audio() 等同于 html 上加一个 \<audio>\</audio>标签
* chrome和opera不能自动播放，需要一个页面元素上的交互才可以
* width / height 属性没有作用，必须使用 style 标签里面的样式去控制它
* 为了提升客户的体验度，可以在audio的开始和结束标签之间添加文字
* \<source> 标是为媒介元素定义媒介资源，不可以在\<source>开始标签里添加，而且\<source> 标签是单标签，没有结束标签。

```html
<audio src="..."
       autoplay="autoplay"
       controls="controls"
       loop="loop"
       width="500"
       height="500">
```

### **3. source：媒介元素（音视频）**

```html
<video autoplay="autoplay"
       controls="controls"
       loop="loop"
       width="500"
       height="500">
    <source src="..." type="video/mp4"
</video>复制代码
```

```html
<embed src="..."
       type="audio/mp3"
       width="300" 
       height="300" />
```

## 四、Web 应用标签

* Menu\
  \<menu> 命令列表（**目前所有主流浏览器都不支持**）\
  \<menuitem> menu命令列表标签（只有FireFox8.0+支持）\
  \<command> menu标记定义一个命令按钮（只有IE9支持）
* 状态标签\
  \<meter> 状态标签(实时状态显示：气压、气温)C、O\
  \<progress> 状态标签 (任务过程：安装、加载) C、F、O
* 列表标签\
  \<datalist> 为input标记定义一个下拉列表,配合option F、O\
  \<details> 标记定义一个元素的详细内容 ，配合summary C

## 五、其他标签

* \<ruby> 标记定义注释或音标
* \<rp> 告诉那些不支持 ruby元素的浏览器如何去显示
* \<rt> 标记定义对ruby的注释内容文本
* \<mark> 标记定义有标记的文本 (黄色选中状态)
* \<output> 标记定义一些输出类型，计算表单结果配合oninput事件
* \<keygen> 标记定义表单里一个生成的键值(加密信息传送)
* \<time> 标记定义一个日期/时间，目前所有主流浏览器都不支持

## 六、重定义标签

（显示不变，只是表达的含义进行了重新定义的标签）

* \<b> 代表内联文本，通常是粗体，没有传递表示重要的意思
* \<i> 代表内联文本，通常是斜体，没有传递表示重要的意思
* \<dd> 可以同details与figure一同使用，定义包含文本，dialog也可用
* \<dt> 可以同details与figure一同使用，汇总细节，dialog也可用
* 表示主题结束，而不是水平线，虽然显示相同
* \<menu> 重新定义用户界面的菜单，配合command或者menuitem使用
* \<small> 表示小字体，例如打印注释或者法律条款
* \<strong>表示重要性而不是强调符号\


## 七、Input新增的type

* email
* url
* number
* range
* Date picker：\
  Date —— 选取日、月、年Month —— 选取月、年Week —— 选取周和年Time —— 选取时间（小时和分钟）Datetime —— 选取时间、日、月、年（UTC 时间）Datetime-local —— 选取时间、日、月、年（本地时间）
* search
* color
* tel

## 八、表单属性

* autocomplete：自动完成，适用于 \<form> 标签，以及以下类型的 \<input> 标签：\
  text, search, url, telephone, email, password, datepickers, range, color。\
  用法：\<form autocomplete="on“>\</form>或者单独在input中用off
* autofocus：自动地获得焦点，适用于所有 \<input> 标签的类型\
  用法：\<input autofocus="autofocus" />
* multiple：可选择多个值，适用于\<input>中type为email和file\
  用法：\<input type="file" multiple="multiple" />
* placeholder：适用于\<input>中type为：text, search, url, telephone, email, password
* required：规定不能为空，适用于以下类型的 \<input> 标签：\
  text, search, url, telephone, email, password, date pickers, number, checkbox, radio, file\
  用法：\<input type="text" required="required" />

## 九、链接属性

* size\
  \<link rel=“icon” href=“icon.gif” type=“image/gif” size=“16x16”>
* target\
  \<base href=“http://localhost/” target=“\_blank”>
* 超链接\
  a：media=""（表示对设备进行优化，handhelp对“手持“设备进行支持，tv对”电视）；\
  a：hreflang="zh"（设置语言，这里设置语言是中文）；\
  a：ref=“external”（设置超链接的引用，这里超链接为外部链接）

## 十、其他属性

* defer:加载完脚本后并不执行，而是等整个页面加载完之后再执行\
  \<script defer src=“URL”>\</script>
* async:加载完脚本后立刻执行，不用等整个页面都加载完，属于异步执行。\
  \<script async src=“URL”>\</script>
* Start —— 起始值
* Reversed —— 倒叙排列\
  \<ol start=“10” reversed>\
  \<li>Html\</li>\
  \<li>Css\</li>\
  \<li>JavaScript\</li>\
  \</ol>
* manifest=“cache.manifest”（定义页面离线应用文件）\
  \<html manifest=“cache.manifest”>

## 十一、参考

* [https://juejin.cn/post/6844903878857588750](https://juejin.cn/post/6844903878857588750)
