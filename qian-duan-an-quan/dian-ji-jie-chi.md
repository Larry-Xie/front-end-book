# 点击劫持

## 一、引言

点击劫持（Clickjacking）是一种通过视觉欺骗的手段来达到攻击目的手段。往往是攻击者将目标网站通过 iframe 嵌入到自己的网页中，通过 opacity 等手段设置 iframe 为透明的，使得肉眼不可见，这样一来当用户在攻击者的网站中操作的时候，比如点击某个按钮（这个按钮的顶层其实是 iframe），从而实现目标网站被点击劫持。这种行为又称为界面伪装(UI Redressing) 。

## 二、攻击方式

大概有两种方式：

* 攻击者使用一个透明 iframe，覆盖在一个网页上，然后诱使用户在该页面上进行操作，此时用户将在不知情的情况下点击透明的 iframe 页面；
* 攻击者使用一张图片覆盖在网页，遮挡网页原有的位置含义。

一张图了解：

![点击劫持](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/10/11/c47f50c6712710f7686e249458dce62d\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

一般步骤：

* 黑客创建一个网页利用 iframe 包含目标网站；
* 隐藏目标网站，使用户无法无法察觉到目标网站存在；
* 构造网页，诱变用户点击特点按钮
* 用户在不知情的情况下点击按钮，触发执行恶意网页的命令。

## 三、防范措施 <a href="#user-content" id="user-content"></a>

### 1. X-FRAME-OPTIONS

X-FRAME-OPTIONS HTTP 响应头是用来给浏览器指示允许一个页面可否在`<frame>`, `<iframe>` 或者 `<object>` 中展现的标记。网站可以使用此功能，来确保自己网站内容没有被嵌到别人的网站中去，也从而避免点击劫持的攻击。

有三个值：

* DENY：表示页面不允许在 frame 中展示，即便是在相同域名的页面中嵌套也不允许。
* SAMEORIGIN：表示该页面可以在相同域名页面的 frame 中展示。
* ALLOW-FROM url：表示该页面可以在指定来源的 frame 中展示。

配置 X-FRAME-OPTIONS：

* Apache：把下面这行添加到 'site' 的配置中：

```
Header always append X-Frame-Options SAMEORIGIN
```

* nginx：把下面这行添加到 'http', 'server' 或者 'location'，配置中

```
add_header X-Frame-Options SAMEORIGIN;
```

* IIS：添加下面配置到 Web.config 文件中

```
  <system.webServer>
...

<httpProtocol>
  <customHeaders>
    <add name="X-Frame-Options" value="SAMEORIGIN" />
  </customHeaders>
</httpProtocol>

...
</system.webServer>
```

### 2. 判断当前网页是否被 iframe 嵌套

```javascript
function locationTop(){
  if (top.location != self.location) {
     top.location = self.location; return false;
  }
  return true; 
 }
locationTop();
```

不过这种方式可以很容易破解：

```javascript
// 破解：
// 顶层窗口中放入代码
var location = document.location;
//或者
var location = "";
```

## 四、参考

* [前端安全知识](https://juejin.cn/post/6844903502968258574)
* [前端安全汇总](https://blog.flqin.com/390.html)
