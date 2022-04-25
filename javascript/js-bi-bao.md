# JS 闭包

## 一、引言

闭包是 JavaScript 中常常被提及的一个概念，并且也是随处可见的应用，也经常是被误认为造成内存泄漏的罪魁祸首，所以每个做前端开发的人都有必要了解闭包的概念和原理。

## 二、闭包

### 1. 概念

一句话总结，**闭包是一个特殊的对象**。

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures) 中的解释：一个函数和对其周围状态（**lexical environment，词法环境**）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是**闭包**（**closure**）。也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。

### **2. 词法作用域**

词法作用域并非 JavaScript 特有的说法，通常我们也直接称之为作用域。词法作用域表达的是一个静态关系，通常情况下，我们在代码编写时，语法规范就已经确定了词法作用域的作用范围。它具体体现在代码解析阶段，通过词法分析确认。

JavaScript 的词法作用域通过函数的 \[\[Scopes]] 属性来具体体现。而函数的 \[\[Scopes]] 属性，是在预解析阶段确认。

词法作用域是为了明确的告诉我们，当前的上下文环境中，能够访问哪些变量参与程序运行。在函数的执行上下文中，除了自身上下文中能够直接访问的声明之外，还能从函数体的 \[\[Scopes]] 属性访问其他作用域中的声明。

一个简单的例子：

```javascript
const g = 10

function foo() {
  let a = 10;
  let b = 20;

  function bar() {
    a = a + 1;
    const c = 30;

    return a + b + c;
  }  

  console.dir(bar)
  return bar
}

foo()
```

仅从语法上，我们就可以知道，函数 bar 能访问的声明，除了自身声明的变量 c 之外，还能访问 foo 声明的变量 a 与 b，以及全局声明的变量 g。最后还能访问整个全局对象。

能够访问自身的变量 c，具体体现为当前函数上下文中创建的 Local 对象。而其他的，则全部都体现在函数的 \[\[Scopes]] 属性中。如图。

![Scopes 属性](https://images.xiaozhuanlan.com/photo/2020/74b4cf47abcb3c09599d533b9e19f8b3.png)

### **3. 闭包理解**

在上面例子里，函数 bar 的 \[\[Scopes]] 中，有一个特殊的对象，**Closure**，就是我们将要学习的闭包对象。

**从概念上来说，闭包是一个特殊的对象，当函数 A 内部创建函数 B，并且函数 B 访问函数 A 中声明的变量等声明时，闭包就会产生。**例如上面的例子中，函数 foo 内部创建了函数 bar，并且在 bar 中，访问了 foo 中声明的变量 a 与 b，此时就会创建一个闭包。闭包是基于词法作用域的规则产生，让函数内部可以访问函数外部的声明。**闭包在代码解析时就能确定。**

从具体实现上来说，对于函数 bar 而言，闭包对象「Closure (foo)」的引用存在于自身的 \[\[Scopes]] 属性中。也就是说，只要函数体 bar 在内存中持久存在，闭包就会持久存在。而如果函数体被回收，闭包对象同样会被回收。

> 此处消除一个大多数人的误解：认为闭包在内存中永远都不会被回收，实际情况并不是这样的。

我们知道，在预解析阶段，函数声明会创建一个函数体，并在代码中持久存在。但是并非所有的函数体都能够持久存在。上面的例子就是一个非常典型的案例。函数 foo 的函数体能够在内存中持久存在，原因在于 foo 在全局上下文中声明，foo 的引用始终存在。因此我们总能访问到 foo。而函数 bar 则不同，函数 bar 是在函数 foo 的执行上下文中声明，当执行上下文执行完毕，执行上下文会被回收，在 foo 执行上下文中声明的函数 bar，也会被回收。如果不做特殊处理，foo 与 bar 产生的闭包对象，同样会被回收。

#### 示例1

微调上面的案例，多次调用 foo 的返回函数 bar 并打印 a 的值。

```javascript
const g = 10

function foo() {
  let a = 10;
  let b = 20;

  function bar() {
    a = a + 1;
    console.log(a)
    const c = 30;

    return a + b + c;
  }  
    console.dir(bar)
  return bar
}

// 函数作为返回值的应用：此时实际调用的是 bar 函数
foo()()
foo()()
foo()()
foo()()
```

分析一下执行过程。

当函数 foo 执行，会创建函数体 bar，并作为 foo 的返回值。foo 调用完毕，则对应创建的执行上下文会被回收，此时 bar 作为 foo 执行上下文的一部分，自然也会被回收。那么保存在 foo.\[\[Scopes]] 上的闭包对象，自然也会被回收。

因此，多次执行 foo()()，实际上是在创建多个不同的 foo 执行上下文，中间与 bar 创建的闭包对象，始终都没有被保存下来，会随着 foo 的上下文一同被回收。因此，多次执行 foo()()，实际上创建了不同的闭包对象，他们也不会被保留下来，相互之间也不会受到影响。如图

> 这个过程，也体现了 JavaScript 边执行边解析的特性

![](https://images.xiaozhuanlan.com/photo/2020/e988fa0c2663f295d7bfe43d17b174d6.png)

#### 示例2

而当我们使用一些方式，保留了函数体 bar 的引用，情况就会发生变化，微调上面的代码如下：

```javascript
const g = 10

function foo() {
  let a = 10;
  let b = 20;

  function bar() {
    a = a + 1;
    console.log(a)
    const c = 30;

    return a + b + c;
  }  
    console.dir(bar)
  return bar
}

// 在全局上下文中，保留 foo 的执行结果，也就是 内部函数 bar 的引用
var bar = foo()

// 多次执行
bar()
bar()
bar()
```

分析一下，微调之后，代码中，在全局上下文使用新的变量 bar 保存了 foo 的内部函数 bar 的引用。也就意味着，即使 foo 执行完毕，foo 的上下文会被回收，但是由于函数 bar 有新的方式保存引用，那么即使函数体 bar 是属于 foo 上下文的一部分，它也不会被回收，而会在内存中持续存在。

因此，当 bar 多次执行，其实执行的是同一个函数体。所以函数体 bar 中的闭包对象「Closure (foo)」也是同一个。那么在 bar 函数内部修改的变量 a，就会出现累加的视觉效果。因为他们在不停的修改同一个闭包对象。\


![](https://images.xiaozhuanlan.com/photo/2020/f702b2e8af133e3ea56f840c49e8ff5a.png)

#### 示例3

再次微调

```javascript
const g = 10

function foo() {
  let a = 10;
  let b = 20;

  function bar() {
    a = a + 1;
    console.log(a)
    const c = 30;

    return a + b + c;
  }  
    console.dir(bar)
  return bar
}

// 在全局上下文中，保留 foo 的执行结果，也就是 内部函数 bar 的引用
var bar1 = foo()

// 多次执行
bar1()
bar1()
bar1()

// 在全局上下文中，保留 foo 的执行结果，也就是 内部函数 bar 的引用
var bar2 = foo()

// 多次执行
bar2()
bar2()
bar2()
```

调整之后我们观察一下。

虽然 bar1 与 bar2 都是在保存 foo 执行结果返回的 bar 函数的引用。但是他们对应的函数体却不是同一个。foo 每次执行都会创建新的上下文，因此 bar1 和 bar2 是不同的 bar 函数引用。因此他们对应的闭包对象也就不同。所以执行结果就表现为：\


![](https://images.xiaozhuanlan.com/photo/2020/e0fadfdc66ae6980f01b4a182178c354.png)

#### 示例4

接下来我们要继续修改上面的例子，来进一步理解闭包。

```javascript
function foo() {
  let a = 10;
  let b = 20;

  function bar() {
    a = a + 1;
    console.log('in bar', a)
    let c = 30;

    function fn() {
      a = a + 1;
      c = c + 1
      console.log('in fn', a)
    }

    console.dir(fn)
    return fn
  }

  console.dir(bar)
  return bar()
}

var fn = foo()
fn()
fn()
fn()
```

函数 foo 中声明函数 bar，\
函数 bar 中声明函数 fn。

函数 bar 中访问 函数 foo 中声明的变量 a。显然，此时能生成闭包对象 「Closure (foo)」\
函数 fn 中访问 函数 foo 中声明的变量 a，与函数 bar 中声明的变量 c。此时也能生成闭包对象「Closure (foo)」与 「Closure (bar)」

我们会发现，bar.\[\[Scopes]] 中的闭包对象「Closure (foo)」与 fn.\[\[Scopes]] 中的闭包对象 「Closure (foo)」是同一个闭包对象。

输入结果如下图所示：\
\
闭包对象 foo 中的变量 a 的值，受到 bar 与 fn 操作共同影响。

![](https://images.xiaozhuanlan.com/photo/2020/0b2b4d091ad78fcd61121d60d1b459fe.png)

### 4. 闭包小结

* 闭包的产生非常简单，只需要在函数内部声明函数，并且内部函数访问上层函数作用域中的声明就会产生闭包；
* 闭包对象真实存在于函数体的 \[\[Scopes]] 属性之中；
* 闭包对象是在代码解析阶段，根据词法作用域的规则产生；
* 闭包对象并非不能被垃圾回收机制回收，仍然需要视情况而定；
* 透彻理解闭包的真实体现，要结合引用数据类型，作用域链，执行上下文和内存管理一起理解；

## 三、闭包的应用

对于闭包的理解，有许多错误的言论。其中一个非常错误的言论就是：我们应该尽量避免使用闭包。

闭包给我们的功能实现，带来了许多的便利，在代码中，闭包几乎是无处不在，**无论你愿意或者不愿意，你都在有意或者无意的使用闭包**来解决问题。

闭包的作用可以总结为两个：

* 延长变量的生命周期
* 创建私有环境

利用这两个特性可以有以下的一些实际应用。

### **1. 单例模式**

在 JavaScript 中有许多解决特定问题的编程模式，例如工厂模式，发布订阅模式，装饰模式，单例模式等。其中，单例模式是我们在实践中最常用的模式之一，而它在实践中的应用，与闭包息息相关。

**所谓的单例模式，就是只有一个实例的对象。**

对象字面量的方法，就是最简单的单例模式，我们可以将属性与方法依次放在字面量里。

```javascript
var per = {
  name: 'Jake',
  age: 20,
  getName: function () {
    return this.name;
  },
  getAge: function () {
    return this.age;
  }
}
```

但是这样的单例有一个小问题，他的属性可以被外部修改。因此在许多场景这样的写法并不符合我们的需求。我们期望对象能够有自己的私有方法与属性。

通过前面的知识我们知道，实例想要拥有一个自己的私有方法/属性，那么需要一个单独的作用域将实例内部与外部隔离开。我们借助匿名函数自执行的方式就能够达到这个目的。

将上面的代码修改如下：

```javascript
var per = (function () {
  var name = 'Jake';
  var age = 20;

  return {
    getName: function () {
      return name;
    },

    getAge: function () {
      return age;
    }
  }
})();

// 访问私有变量
per.getName();
```

我们将属性 name 与 age 封装在实例内部，并且对外抛出两个方法 getName 和 getAge。当我们想要访问内部属性 name 时，就不能直接通过 `per.name` 的方式访问，而只能通过公开的方法 getName 进行访问。

私有变量的好处在于，外界要对私有变量进行什么操作，是可控的。我们可以提供一个 getName 方法用于外界访问到属性 name，也可以提供一个 setName 方法来修改 name，对外提供什么样的能力，完全由我们自己决定。

不知不觉中，我们已经在利用闭包来解决问题。我们在匿名函数中，声明变量 name，并且在 getName 中访问它，闭包就已经产生。

而这个时候，我们使用的案例，已经非常接近模块化思维了。在模块化的开发中，每一个模块都是一个与此类似的单例。

有的时候「使用频次较少」，我们希望自己的实例，仅仅只是在调用的时候才会初始化，而不是如上面的例子那样，在声明时就已经是一个实例了。此时需要在上面例子的基础上，做一个简单的判断。

```javascript
var person = (function () {
  // 定义一个变量，用来保存实例
  var instance = null;
  var name = 'Jake';
  var age = 20;

  // 初始化方法
  function initial() {
    return {
      getName: function () { return name; },
      getAge: function () { return name; }
    }
  }

  return {
    getInstance: function () {
      if (!instance) {
        instance = initial();
      }
      return instance;
    }
  }
})();

// 只在使用时获得实例
var p1 = person.getInstance();
var p2 = person.getInstance();

console.log(p1 === p2); // true
```

在匿名函数中，定义了一个 instance 变量来保存实例。在 getInstance 方法中判断是否对它进行重新赋值。由于这个判断的存在，变量 instance 就永远只会被赋值一次。所以这样写就完美符合了单例模式的思路。

### **2. 模块化**

如果我们想要项目中的所有地方，都能够访问到同一个变量，应该怎么办？在实践中有非常多这样的需求，例如全局状态管理。

但是我们之前也有提到过，实践中不能轻易使用全局变量，这个时候想要一个变量能够被所有地方访问，就需要一点转变。模块化思维能够轻松帮我们解决这个问题。这是目前最流行，也是必须要掌握的一种开发思路。

模块化其实是建立在单例模式的基础之上，一个模块，其实就是一个单例。我们可以直接使用自执行函数模拟一个模块，变量名就是模块名。

```javascript
var module_test = (function() {

})();
```

每一个模块想要与其他模块进行交互，那么就必须具备获取其他模块的能力，例如 requirejs 中的 require，ES6 Modules 中 import

```javascript
// require
var $ = require('jquery');

// es6 modules
import $ from 'jquery';
```

当然，我们这里再定义模块时，模块名就已经是一个全局变量了，于是就省略了这一步。

每一个模块都应该有对外提供的接口，以保证具备与其他模块交互的能力，我们这里直接使用 return 返回一个字面量对象的方式来对外提供接口即可。

```javascript
var module_test = (function () {
  ...

  return {
    testfn1: function () { },
    testfn2: function () { }
  }
})();
```

有了这些关于模块的基本概念，那么我们现在结合一个简单的例子，走一遍模块化开发的过程。

我们这个案例，想要实现的功能是每隔一秒，body 背景就随着一个数字的递增而在固定的三种颜色之间切换。

首先创建一个专门用来管理全局状态的模块。这个模块中，有一个私有变量，保存了所有的状态值，并对外提供了访问与设置这个私有变量的方法：

```javascript
var module_status = (function () {
  var status = {
    number: 0,
    color: null
  }

  var get = function (prop) {
    return status[prop];
  }

  var set = function (prop, value) {
    status[prop] = value;
  }

  return {
    get: get,
    set: set
  }
})();
```

然后专门创建一个负责 body 背景颜色变化的模块

在这个模块中，引入了管理状态的模块，并且将颜色的管理与改变方式都定义在该模块中，在使用只需要调用 render 方法就可以了。

```javascript
var module_color = (function () {
  // 假装用这种方式执行第二步引入模块 类似于 import state from 'module_status';
  var state = module_status;
  var colors = ['orange', '#ccc', 'pink'];

  function render() {
    var color = colors[state.get('number') % 3];
    document.body.style.backgroundColor = color;
  }

  return {
    render: render
  }
})();
```

接下来，我们需要创建另外一个模块来负责显示当前的 number 值，用于参考对比。

```javascript
var module_context = (function () {
  var state = module_status;

  function render() {
    document.body.innerHTML = 'this Number is ' + state.get('number');
  }

  return {
    render: render
  }
})();
```

OK，这些功能模块都创建完毕之后，最后只需要创建一个主模块即可。这个模块的目的，是借助功能模块来实现最终的效果。

```javascript
var module_main = (function () {
  var state = module_status;
  var color = module_color;
  var context = module_context;

  setInterval(function () {
    var newNumber = state.get('number') + 1;
    state.set('number', newNumber);

    color.render();
    context.render();
  }, 1000);
})();
```

如果你有过模块化开发的经验，那么结合前面对于闭包的理解，这段代码就再简单不过了，而如果你是初次正式学习模块化的概念，这个例子也是非常值得细细品味。具体的展示效果，大家把这段代码摘抄到一个 html文件的script标签中即可查看。

完整代码如下：

```javascript
var module_status = (function () {
  var status = {
    number: 0,
    color: null
  }

  var get = function (prop) {
    return status[prop];
  }

  var set = function (prop, value) {
    status[prop] = value;
  }

  return {
    get: get,
    set: set
  }
})();

var module_color = (function () {
  // 假装用这种方式执行第二步引入模块 类似于 import state from 'module_status';
  var state = module_status;
  var colors = ['orange', '#ccc', 'pink'];

  function render() {
    var color = colors[state.get('number') % 3];
    document.body.style.backgroundColor = color;
  }

  return {
    render: render
  }

})();

var module_context = (function () {
  var state = module_status;

  function render() {
    document.body.innerHTML = 'this Number is ' + state.get('number');
  }

  return {
    render: render
  }
})();

var module_main = (function () {
  var state = module_status;
  var color = module_color;
  var context = module_context;

  setInterval(function () {
    var newNumber = state.get('number') + 1;
    state.set('number', newNumber);

    color.render();
    context.render();
  }, 1000);
})();
```

## 四、考点

### **1. 闭包常见面试题**

修改如下代码使得输出为 1，2，3。

```javascript
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0]();	// 3
data[1]();	// 3
data[2]();	// 3
```

#### 方法1：立即执行函数 <a href="#fang-fa-1-li-ji-zhi-hang-han-shu" id="fang-fa-1-li-ji-zhi-hang-han-shu"></a>

```javascript
for (var i = 0; i < 3; i++) {
    (function(num) {
        setTimeout(function() {
            console.log(num);
        }, 1000);
    })(i);
}
// 0
// 1
// 2
```

#### 方法2：返回一个匿名函数赋值 <a href="#fang-fa-2-fan-hui-yi-ge-ni-ming-han-shu-fu-zhi" id="fang-fa-2-fan-hui-yi-ge-ni-ming-han-shu-fu-zhi"></a>

```javascript
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = (function (num) {
      return function(){
          console.log(num);
      }
  })(i);
}

data[0]();	// 0
data[1]();	// 1
data[2]();	// 2
```

无论是**立即执行函数**还是**返回一个匿名函数赋值**，原理上都是因为变量的按值传递，所以会将变量`i`的值复制给实参`num`，在匿名函数的内部又创建了一个用于访问`num`的匿名函数，这样每个函数都有了一个`num`的副本，互不影响了。

#### 方法3：使用 let <a href="#fang-fa-3-shi-yong-es6-zhong-de-let" id="fang-fa-3-shi-yong-es6-zhong-de-let"></a>

```javascript
var data = [];

for (let i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0]();
data[1]();
data[2]();
```

解释下**原理**：

```javascript
var data = [];// 创建一个数组data;

// 进入第一次循环
{ 
    let i = 0; // 注意：因为使用let使得for循环为块级作用域
	       // 此次 let i = 0 在这个块级作用域中，而不是在全局环境中
    data[0] = function() {
    	console.log(i);
    };
}
```

循环时，`let`声明`i`,所以整个块是块级作用域，那么data\[0]这个函数就成了一个闭包。这里用｛｝表达并不符合语法，只是希望通过它来说明let存在时，这个for循环块是块级作用域，而不是全局作用域。

上面的块级作用域，就像函数作用域一样，函数执行完毕，其中的变量会被销毁，但是因为这个代码块中存在一个闭包，闭包的作用域链中引用着块级作用域，所以在闭包被调用之前，这个块级作用域内部的变量不会被销毁。

```javascript
// 进入第二次循环
{ 
	let i = 1; // 因为 let i = 1 和上面的 let i = 0     
	           // 在不同的作用域中，所以不会相互影响
	data[1] = function(){
         console.log(i);
	}; 
}
```

当执行`data[1]()`时，进入下面的执行环境。

```javascript
{ 
     let i = 1; 
     data[1] = function(){
          console.log(i);
     }; 
}
```

在上面这个执行环境中，它会首先寻找该执行环境中是否存在`i`，没有找到，就沿着作用域链继续向上到了其所在的块作用域执行环境，找到了`i = 1`,于是输出了`1`。

## 五、参考

* [JavaScript 核心进阶 - 闭包](https://xiaozhuanlan.com/advance/3294571608)
* [这样理解闭包](https://juejin.cn/post/6844903858636849159)
* [JavaScript 闭包](https://segmentfault.com/a/1190000006875662)
* [JavaScript 深入之闭包面试题解](https://muyiy.cn/blog/2/2.3.html#%E4%BD%9C%E7%94%A8%E5%9F%9F)
