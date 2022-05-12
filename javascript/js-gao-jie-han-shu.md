# JS 高阶函数

## 一、引言 <a href="#yin-yan" id="yin-yan"></a>

在 JavaScript 中，函数是一种特殊类型的对象，它们是 Function objects。而在 JavaScript 中，也存在高阶函数的概念，它虽然听起来很复杂，但其实并不难。使 JavaScript 适合函数式编程的原因是它接受高阶函数。

高阶函数在 JavaScript 中广泛使用。 如果你已经用 JavaScript 编程了一段时间，你可能已经在不知不觉中用过它们了。要完全理解这个概念，首先必须了解函数式编程以及一等函数（First-Class Functions）的概念。

## 二、函数式编程

### **1. 概念**

**什么是函数式编程**在大多数简单的术语中，函数式是一种编程形式，你可以将函数作为参数传递给其他函数，并将它们作为值返回。 在函数式编程中，我们以函数的形式思考和编程。JavaScript，Haskell，Clojure，Scala 和 Erlang 是部分实现了函数式编程的语言。

### 2. 一等函数

如果你在学习 JavaScript，你可能听说过 JavaScript 将函数视为一等公民。 那是因为在 JavaScript 及其他函数式编程语言中，函数是对象。在 JavaScript 中，函数是一种特殊类型的对象。 它们是 Function objects。例如：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/13/1670cb42fdbcc0eb\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

为了证明 JavaScript 中函数是对象，我们可以这样做：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/13/1670cb42febf5510\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

注意 - 虽然这在 JavaScript 中完全有效，但这被认为是 harmful 的做法。 你不应该向函数对象添加随机属性，如果不得不这样做，请使用对象。在 JavaScript 中，你对其他类型（如对象，字符串或数字）执行的所有操作都可以对函数执行。 你可以将它们作为参数传递给其他函数（回调函数），将它们分配给变量并传递它们等等。这就是 JavaScript 中的函数被称为一等函数的原因。

### 3. 把函数赋值给变量

我们可以在 JavaScript 中将函数赋值给变量。 例如：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/13/1670cb432133ac00\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

我们也可以传递它们。 例如：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/13/1670cb43a1b8fcb2\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 4. 将函数作为参数传递

我们可以将函数作为参数传递给其他函数。 例如：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/13/1670cb43a4ff0381\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)既然我们已经知道一等函数是什么了，那就让我们开始学习 JavaScript 中的高阶函数叭\~\
作者：蚂蚁RichLab前端团队\
链接：https://juejin.cn/post/6844903713060945934\
来源：稀土掘金\
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 三、高阶函数概念 <a href="#gao-jie-han-shu" id="gao-jie-han-shu"></a>

高阶函数英文叫 Higher-order function，它的定义很简单，就是至少**满足下一个条件**的函数：

* **接受一个或多个函数作为输入**
* **输出一个函数**

也就是说高阶函数是对其他函数进行操作的函数，可以将它们作为参数传递，**或者**是返回它们。 简单来说，高阶函数是一个接收函数作为参数传递**或者**将函数作为返回值输出的函数。

### 1. 函数作为参数传递 <a href="#han-shu-zuo-wei-can-shu-chuan-di" id="han-shu-zuo-wei-can-shu-chuan-di"></a>

JavaScript 语言中内置了一些高阶函数，比如 Array.prototype.map，Array.prototype.filter 和 Array.prototype.reduce，它们接受一个函数作为参数，并应用这个函数到列表的每一个元素。我们来看看使用它们与不使用高阶函数的方案对比。

#### 1.1 Array.prototype.map <a href="#array-prototype-map" id="array-prototype-map"></a>

`map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个**提供的函数**后返回的结果，**原始数组不会改变**。传递给 map 的回调函数（`callback`）接受三个参数，分别是 currentValue、index（可选）、array（可选），除了 `callback` 之外还可以接受 this 值（可选），用于执行 `callback` 函数时使用的`this` 值。

来个简单的例子方便理解，现在有一个数组 `[1, 2, 3, 4]`，我们想要生成一个新数组，其每个元素皆是之前数组的两倍，那么我们有下面两种使用高阶和不使用高阶函数的方式来实现。

**不使用高阶函数**

```javascript
const arr1 = [1, 2, 3, 4];
const arr2 = [];
for (let i = 0; i < arr1.length; i++) {
  arr2.push( arr1[i] * 2);
}

console.log( arr2 );
// [2, 4, 6, 8]
console.log( arr1 );
// [1, 2, 3, 4]
```

**使用高阶函数**

```javascript
const arr1 = [1, 2, 3, 4];
const arr2 = arr1.map(item => item * 2);

console.log( arr2 );
// [2, 4, 6, 8]
console.log( arr1 );
// [1, 2, 3, 4]
```

#### 1.2 Array.prototype.filter <a href="#array-prototype-filter" id="array-prototype-filter"></a>

`filter()` 方法创建一个新数组, 其包含通过提供函数实现的测试的所有元素，**原始数组不会改变**。接收的参数和 map 是一样的，其返回值是一个新数组、由通过测试的所有元素组成，如果没有任何数组元素通过测试，则返回空数组。

来个例子介绍下，现在有一个数组 `[1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4]`，我们想要生成一个新数组，这个数组要求没有重复的内容，即为去重。

**不使用高阶函数**

```javascript
const arr1 = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
const arr2 = [];
for (let i = 0; i < arr1.length; i++) {
  if (arr1.indexOf( arr1[i] ) === i) {
    arr2.push( arr1[i] );
  }
}

console.log( arr2 );
// [1, 2, 3, 5, 4]
console.log( arr1 );
// [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4]
```

**使用高阶函数**

```javascript
const arr1 = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
const arr2 = arr1.filter( (element, index, self) => {
    return self.indexOf( element ) === index;
});

console.log( arr2 );
// [1, 2, 3, 5, 4]
console.log( arr1 );
// [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4]
```

#### 1.3 Array.prototype.reduce <a href="#array-prototype-reduce" id="array-prototype-reduce"></a>

`reduce()` 方法对数组中的每个元素执行一个提供的 **reducer** 函数(升序执行)，将其结果汇总为单个返回值。传递给 reduce 的回调函数（`callback`）接受四个参数，分别是累加器 accumulator、currentValue、currentIndex（可选）、array（可选），除了 `callback` 之外还可以接受初始值 initialValue 值（可选）。

* 如果没有提供 initialValue，那么第一次调用 `callback` 函数时，accumulator 使用原数组中的第一个元素，currentValue 即是数组中的第二个元素。 在没有初始值的空数组上调用 reduce 将报错。
* 如果提供了 initialValue，那么将作为第一次调用 `callback` 函数时的第一个参数的值，即 accumulator，currentValue 使用原数组中的第一个元素。

来个简单的例子介绍下，现在有一个数组 `[0, 1, 2, 3, 4]`，需要计算数组元素的和，需求比较简单，来看下代码实现。

**不使用高阶函数**

```javascript
const arr = [0, 1, 2, 3, 4];
let sum = 0;
for (let i = 0; i < arr.length; i++) {
  sum += arr[i];
}

console.log( sum );
// 10
console.log( arr );
// [0, 1, 2, 3, 4]
```

**使用高阶函数**

* **无 initialValue 值**

```javascript
const arr = [0, 1, 2, 3, 4];
let sum = arr.reduce((accumulator, currentValue, currentIndex, array) => {
  return accumulator + currentValue;
});

console.log( sum );
// 10
console.log( arr );
// [0, 1, 2, 3, 4]
```

上面是没有 initialValue 的情况，代码的执行过程如下，callback 总共调用四次。

| callback    | accumulator | currentValue | currentIndex | array            | return value |
| ----------- | ----------- | ------------ | ------------ | ---------------- | ------------ |
| first call  | 0           | 1            | 1            | \[0, 1, 2, 3, 4] | 1            |
| second call | 1           | 2            | 2            | \[0, 1, 2, 3, 4] | 3            |
| third call  | 3           | 3            | 3            | \[0, 1, 2, 3, 4] | 6            |
| fourth call | 6           | 4            | 4            | \[0, 1, 2, 3, 4] | 10           |

* **有 initialValue 值**

我们再来看下有 initialValue 的情况，假设 initialValue 值为 10，我们看下代码。

```javascript
const arr = [0, 1, 2, 3, 4];
let sum = arr.reduce((accumulator, currentValue, currentIndex, array) => {
  return accumulator + currentValue;
}, 10);

console.log( sum );
// 20
console.log( arr );
// [0, 1, 2, 3, 4]
```

代码的执行过程如下所示，callback 总共调用五次。

| callback    | accumulator | currentValue | currentIndex | array            | return value |
| ----------- | ----------- | ------------ | ------------ | ---------------- | ------------ |
| first call  | 10          | 0            | 0            | \[0, 1, 2, 3, 4] | 10           |
| second call | 10          | 1            | 1            | \[0, 1, 2, 3, 4] | 11           |
| third call  | 11          | 2            | 2            | \[0, 1, 2, 3, 4] | 13           |
| fourth call | 13          | 3            | 3            | \[0, 1, 2, 3, 4] | 16           |
| fifth call  | 16          | 4            | 4            | \[0, 1, 2, 3, 4] | 20           |

### 2. 函数作为返回值输出 <a href="#han-shu-zuo-wei-fan-hui-zhi-shu-chu" id="han-shu-zuo-wei-fan-hui-zhi-shu-chu"></a>

这个很好理解，就是返回一个函数，下面直接看两个例子来加深理解。

#### 2.1 isType 函数 <a href="#istype-han-shu" id="istype-han-shu"></a>

我们知道在判断类型的时候可以通过 `Object.prototype.toString.call` 来获取对应对象返回的字符串，比如：

```javascript
let isString = obj => Object.prototype.toString.call( obj ) === '[object String]';

let isArray = obj => Object.prototype.toString.call( obj ) === '[object Array]';

let isNumber = obj => Object.prototype.toString.call( obj ) === '[object Number]';
```

可以发现上面三行代码有很多重复代码，只需要把具体的类型抽离出来就可以封装成一个判断类型的方法了，代码如下。

```javascript
let isType = type => obj => {
  return Object.prototype.toString.call( obj ) === '[object ' + type + ']';
}

isType('String')('123');		// true
isType('Array')([1, 2, 3]);	// true
isType('Number')(123);			// true
```

这里就是一个高阶函数，因为 isType 函数将 `obj => { ... }` 这一函数作为返回值输出。

#### 2.2 add 函数 <a href="#add-han-shu" id="add-han-shu"></a>

我们看一个常见的面试题，用 JS 实现一个无限累加的函数 `add`，示例如下：

```javascript
add(1); // 1
add(1)(2);  // 3
add(1)(2)(3)； // 6
add(1)(2)(3)(4)； // 10 

// 以此类推
```

我们可以看到结构和上面代码有些类似，都是**将函数作为返回值输出**，然后接收新的参数并进行计算。

我们知道打印函数时会自动调用 `toString()`方法，函数 `add(a)` 返回一个闭包 `sum(b)`，函数 `sum()` 中累加计算 `a = a + b`，只需要重写`sum.toString()`方法返回变量 `a` 就可以了。

```javascript
function add(a) {
    function sum(b) { // 使用闭包
    	a = a + b; // 累加
    	return sum;
    }
    sum.toString = function() { // 重写toString()方法
        return a;
    }
    return sum; // 返回一个函数
}

add(1); // 1
add(1)(2);  // 3
add(1)(2)(3)； // 6
add(1)(2)(3)(4)； // 10 
```

## 四、高阶函数应用 <a href="#si-kao-ti" id="si-kao-ti"></a>

### 1. 实现 AOP

**AOP**(面向切面编程)的主要作用就是把一些和核心业务逻辑模块无关的功能抽取出来，然后再通过“动态织入”的方式掺到业务模块种。这些功能一般包括日志统计，安全控制，异常处理等。AOP 是 Java Spring 架构的核心。下面我们就来探索一下在 Javascript 中如何实现 **AOP。**

在 JavaScript 种实现AOP，都是指把一个函数“动态织入”到另外一个函数中，具体实现的技术有很多，我们使用`Function.prototype`来做到这一点。代码如下

```javascript
/**
* 织入执行前函数
* @param {*} fn 
*/
Function.prototype.aopBefore = function(fn){
  console.log(this)
  // 第一步：保存原函数的引用
  const _this = this
  // 第四步：返回包括原函数和新函数的“代理”函数
  return function() {
    // 第二步：执行新函数，修正this
    fn.apply(this, arguments)
    // 第三步 执行原函数
    return _this.apply(this, arguments)
  }
}
/**
* 织入执行后函数
* @param {*} fn 
*/
Function.prototype.aopAfter = function (fn) {
  const _this = this
  return function () {
    let current = _this.apply(this,arguments)// 先保存原函数
    fn.apply(this, arguments) // 先执行新函数
    return current
  }
}
/**
* 使用函数
*/
let aopFunc = function() {
  console.log('aop')
}
// 注册切面
aopFunc = aopFunc.aopBefore(() => {
  console.log('aop before')
}).aopAfter(() => {
  console.log('aop after')
})
// 真正调用
aopFunc()
```

### 2. 函数柯里化&#x20;

关于 **curring** 我们首先要聊的是什么是**函数柯里化**。

> **curring** 又称部分求值。一个 **curring** 的函数首先会接受一些参数，接受了这些参数之后，该函数并不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数形成的闭包中被保存起来。待到函数中被真正的需要求值的时候，之前传入的所有参数被一次性用于求值。

生硬的看概念不太好理解，我们来看接下来的例子 我们需要一个函数来计算一年12个月的消费，在每个月月末的时候我们都要计算消费了多少钱。正常代码如下

```javascript
// 未柯里化的函数计算开销
let totalCost = 0
const cost = function(amount, mounth = '') {
 console.log(`第${mounth}月的花销是${amount}`)
 totalCost += amount
 console.log(`当前总共消费：${totalCost}`)
}
cost(1000, 1) // 第1个月的花销
cost(2000, 2) // 第2个月的花销
// ...
cost(3000, 12) // 第12个月的花销
```

总结一下不难发现，如果我们要计算一年的总消费，没必要计算12次。只需要在年底执行一次计算就行，接下来我们对这个函数进行部分柯里化的函数帮助我们理解

```javascript
// 部分柯里化完的函数
const curringPartCost = (function() {
 // 参数列表
 let args = []
 return function (){
   /**
    * 区分计算求值的情况
    * 有参数的情况下进行暂存
    * 无参数的情况下进行计算
    */
   if (arguments.length === 0) {
     let totalCost = 0
     args.forEach(item => {
       totalCost += item[0]
     })
     console.log(`共消费：${totalCost}`)
     return totalCost
   } else {
     // argumens并不是数组，是一个类数组对象
     let currentArgs = Array.from(arguments)
     args.push(currentArgs)
     console.log(`暂存${arguments[1] ? arguments[1] : '' }月，金额${arguments[0]}`)
   }
 }
})()
curringPartCost(1000,1)
curringPartCost(100,2)
curringPartCost()
```

接下来我们编写一个通用的curring， 以及一个即将被curring的函数。代码如下

```javascript
// 通用curring函数
const curring = function(fn) {
 let args = []
 return function () {
   if (arguments.length === 0) {
     console.log('curring完毕进行计算总值')
     return fn.apply(this, args)
   } else {
     let currentArgs = Array.from(arguments)[0]
     console.log(`暂存${arguments[1] ? arguments[1] : '' }月，金额${arguments[0]}`)
     args.push(currentArgs)
     // 返回正被执行的 Function 对象，也就是所指定的 Function 对象的正文，这有利于匿名函数的递归或者保证函数的封装性
     return arguments.callee
   }
 }
}
// 求值函数
let costCurring = (function() {
 let totalCost = 0
 return function () {
   for (let i = 0; i < arguments.length; i++) {
     totalCost += arguments[i]
   }
   console.log(`共消费：${totalCost}`)
   return totalCost
 }
})()
// 执行curring化
costCurring = curring(costCurring)
costCurring(2000, 1)
costCurring(2000, 2)
costCurring(9000, 12)
costCurring()
```

### 3. 分时函数

> 节流函数为我们提供了一种限制函数被频繁调用的解决方案。下面我们将遇到另外一个问题，某些函数是用户主动调用的，但是由于一些客观的原因，这些操作会严重的影响页面性能，此时我们需要采用另外的方式去解决。

如果我们需要在短时间内才页面中插入大量的DOM节点，那显然会让浏览器吃不消。可能会引起浏览器的假死，所以我们需要进行分时函数，分批插入。

```javascript
/**
* 分时函数
* @param {*创建节点需要的数据} list 
* @param {*创建节点逻辑函数} fn 
* @param {*每一批节点的数量} count 
*/
const timeChunk = function(list, fn, count = 1){
 let insertList = [], // 需要临时插入的数据
     timer = null // 计时器
 const start = function(){
   // 对执行函数逐个进行调用
   for (let i = 0; i < Math.min(count, list.length); i++) {
     insertList = list.shift()
     fn(insertList)
   }
 }
 return function(){
   timer = setInterval(() => {
     if (list.length === 0) {
       return window.clearInterval(timer)
     }
     start()
   },200)
 }
}
// 分时函数测试
const arr = []
for (let i = 0; i < 94; i++) {
 arr.push(i)
}
const renderList = timeChunk(arr, function(data){
 let div =document.createElement('div')
 div.innerHTML = data + 1
 document.body.appendChild(div)
}, 20)
renderList()
```

### 4. 惰性加载函数

> 在 Web 开发中，因为一些浏览器中的差异，一些嗅探工作总是不可避免的。

因为浏览器的差异性，我们要常常做各种各样的兼容，举一个非常简单常用的例子：在各个浏览器中都能够通用的事件绑定函数。

常见的写法是这样的：

```javascript
// 常用的事件兼容
const addEvent = function(el, type, handler) {
 if (window.addEventListener) {
   return el.addEventListener(type, handler, false)
 }
 // for IE
 if (window.attachEvent) {
   return el.attachEvent(`on${type}`, handler)
 }
}
```

这个函数存在一个缺点，它每次执行的时候都会去执行 if 条件分支。虽然开销不大，但是这明显是多余的，下面我们优化一下， 提前一下嗅探的过程：

```javascript
const addEventOptimization = (function() {
 if (window.addEventListener) {
   return (el, type, handler) => {
     el.addEventListener(type, handler, false)
   }
 }
 // for IE
 if (window.attachEvent) {
   return (el, type, handler) => {
     el.attachEvent(`on${type}`, handler)
   }
 }
})()
```

这样我们就可以在代码加载之前进行一次嗅探，然后返回一个函数。但是如果我们把它放在公共库中不去使用，这就有点多余了。下面我们使用惰性函数去解决这个问题：

```javascript
// 惰性加载函数
let addEventLazy = function(el, type, handler) {
 if (window.addEventListener) {
   // 一旦进入分支，便在函数内部修改函数的实现
   addEventLazy = function(el, type, handler) {
     el.addEventListener(type, handler, false)
   }
 } else if (window.attachEvent) {
   addEventLazy = function(el, type, handler) {
     el.attachEvent(`on${type}`, handler)
   }
 }
 addEventLazy(el, type, handler)
}
addEventLazy(document.getElementById('eventLazy'), 'click', function() {
 console.log('lazy ')
})
```

一旦进入分支，便在函数内部修改函数的实现，重写之后函数就是我们期望的函数，在下一次进入函数的时候就不再存在条件分支语句。

## 五、参考

* [JavaScript 高阶函数浅析](https://muyiy.cn/blog/6/6.1.html)
* [JavaScript 中高阶函数的魅力](https://juejin.cn/post/6844903668651819016)
