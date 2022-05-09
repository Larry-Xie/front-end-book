# JS 常用函数

## 一、引言

可以

* [JavaScript 工具函数大全](https://juejin.cn/post/6844903966526930951)
* [常用的前端 JavaScript 方法封装](https://www.jianshu.com/p/cc01443d1359)
* [前端常用的 js 函数方法](https://juejin.cn/post/7030346779539275806)

二、

### 1.为什么需要函数防抖和函数节流？

> * 在浏览器中某些计算和处理要比其他的昂贵很多。例如`DOM`操作比起非`DOM`交互需要更多的内存和CPU占用时间。连续尝试进行过多的`DOM`操作可能会导致浏览器挂起，甚至崩溃；
> * 例如当调整浏览器大小的时候，`resize`事件会连续触发；如果在`resize`事件处理程序内部尝试进行`DOM`操作，其高频率的更改可能会让浏览器崩溃；
> * 为了绕开上面的问题，需要对该类函数进行节流；

### 2.什么是函数防抖和函数节流

> 防抖（**`debounce`**）和节流（**`throttle`**）都是用来控制某个函数在一定时间内执行多少次的技巧，两者相似而又不同。 背后的基本思想是**某些代码不可以在没有间断的情况下连续重复执行。**

#### 2.1 函数防抖 (`debounce`)

> 如果一个事件被频繁触发多次，并且触发的时间间隔过短，则防抖函数可以使得对应的事件处理函数只执行最后触发的一次。 函数防抖可以把多个顺序的调用合并成一次。

#### 2.2 函数节流 (`throttle`)

> 如果一个事件被频繁触发多次，节流函数可以按照固定频率去执行对应的事件处理方法。 函数节流保证一个事件一定时间内只执行一次。

### 3.应用场景

| 类型   | 场景                                                                                                                                                                                                                                                             |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 函数防抖 | <p>1. 手机号、邮箱输入检测<br>2. 搜索框搜索输入（只需最后一次输入完后，再放松Ajax请求）<br>3. 窗口大小<code>resize</code>（只需窗口调整完成后，计算窗口大小，防止重复渲染）<br>4.滚动事件<code>scroll</code>（只需执行触发的最后一次滚动事件的处理程序）<br>5. 文本输入的验证（连续输入文字后发送 AJAX 请求进行验证，（停止输入后）验证一次就好</p>                                           |
| 函数节流 | <p>1. <code>DOM</code>元素的拖拽功能实现（<code>mousemove</code>）<br>2. 射击游戏的 <code>mousedown</code>/<code>keydown</code> 事件（单位时间只能发射一颗子弹）<br>3. 计算鼠标移动的距离（<code>mousemove</code>）<br>4. 搜索联想（<code>keyup</code>）<br>5. 滚动事件<code>scroll</code>，（只要页面滚动就会间隔一段时间判断一次）</p> |

### 4.如何实现

#### 4.1 函数防抖实现

```
function debounce(fn, delay, scope) {
    let timer = null;
    // 返回函数对debounce作用域形成闭包
    return function () {
        // setTimeout()中用到函数环境总是window,故需要当前环境的副本；
        let context = scope || this, args = arguments;
        // 如果事件被触发，清除timer并重新开始计时
        clearTimeout(timer);
        timer = setTimeout(function () {
            fn.apply(context, args);
        }, delay);
    }
}
复制代码
```

> * 代码解读
> * 第一次调用函数，创建一个定时器，在指定的时间间隔之后运行代码；
> * 当第二次调用该函数时，它会清除前一次的定时器并设置另一个；
> * 如果前一个定时器已经执行过了，这个操作就没有任何意义；
> * 然而，如果前一个定时器尚未执行，其实就是将其替换为一个新的定时器；
> * 目的是**只有在执行函数的请求停止了`delay`时间之后才执行**。

#### 4.2 函数节流实现

**4.2.1 利用时间戳简单实现**

```
function throttle(fn, threshold, scope) {
    let timer;
    let prev = Date.now();
    return function () {
        let context = scope || this, args = arguments;
        let now = Date.now();
        if (now - prev > threshold) {
            prev = now;
            fn.apply(context, args);
        }
    }
}
复制代码
```

**4.2.2 利用定时器简单实现**

```
function throttle2(fn, threshold, scope) {
    let timer;
    return function () {
        let context = scope || this, args = arguments;
        if (!timer) {
            timer = setTimeout(function () {
                fn.apply(context, args);
                timer = null;
            }, threshold)
        }
    }
}
复制代码
```

### 5 举例（`scroll`事件）

#### CSS代码

```
    .wrap {
       width: 200px;
        height: 330px;
        margin: 50px;
        margin-top: 200px;
        position: relative;
        float: left;
        background-color: yellow;
    }
    .header{
        width: 100%;
        height: 30px;
        background-color: #a8d4f4;
        text-align: center;
        line-height: 30px;
    }
    .container {
        background-color: pink;
        box-sizing: content-box;
        width: 200px;
        height: 300px;
        overflow: scroll;
        position: relative;
    }
    .content {
        width: 140px;
        height: 800px;
        margin: auto;
        background-color: #14ffb2;
    }
复制代码
```

#### HTML代码

```
    <div class="wrap">
        <div class="header">滚动事件：普通</div>
        <div class="container">
            <div class="content"></div>
        </div>
    </div>
    <div class="wrap">
        <div class="header">滚动事件:<strong>加了函数防抖</strong></div>
        <div class="container">
            <div class="content"></div>
        </div>
    </div>
    <div class="wrap">
        <div class="header">滚动事件:<strong>加了函数节流</strong></div>
        <div class="container">
            <div class="content"></div>
        </div>
    </div>
复制代码
```

#### JS代码

```
    let els = document.getElementsByClassName('container');
    let count1 = 0,count2 = 0,count3 = 0;
    const THRESHOLD = 200;

    els[0].addEventListener('scroll', function handle() {
        console.log('普通滚动事件！count1=', ++count1);
    });
    els[1].addEventListener('scroll', debounce(function handle() {
        console.log('执行滚动事件!（函数防抖） count2=', ++count2);
    }, THRESHOLD));
    els[2].addEventListener('scroll', throttle(function handle() {
        console.log(Date.now(),', 执行滚动事件!（函数节流） count3=', ++count3);
    }, THRESHOLD));
复制代码
```

```
// 函数防抖
function debounce(fn, delay, scope) {
    let timer = null;
    let count = 1;
    return function () {
        let context = scope || this,
            args = arguments;
        clearTimeout(timer);
        console.log(Date.now(), ", 触发第", count++, "次滚动事件！");
        timer = setTimeout(function () {
            fn.apply(context, args);
            console.log(Date.now(), ", 可见只有当高频事件停止，最后一次事件触发的超时调用才能在delay时间后执行!");
        }, delay);
    }
}
复制代码
```

```
// 函数节流
function throttle(fn, threshold, scope) {
    let timer;
    let prev = Date.now();
    return function () {
        let context = scope || this, args = arguments;
        let now = Date.now();
        if (now - prev > threshold) {
            prev = now;
            fn.apply(context, args);
        }
    }
}
复制代码
```

#### 执行结果展示及比较

![测试函数防抖和函数节流](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/30/16763cbf16380be5\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)![普通滚动](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/30/16763cc36a505659\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)![防抖滚动](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/30/16763cc7839bcb8e\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)![节](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/30/16763ccc923dfd5a\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 6.总结

> * `debounce`和`throttle`均是通过减少高频触发事件的实际事件处理程序的执行来提高事件处理函数运行性能的手段，并没有实质上减少事件的触发次数。
> * **`debounce`可以把多个顺序的调用合并成一次**。
> * **`throttle`保证一个事件一定时间内只执行一次。**
