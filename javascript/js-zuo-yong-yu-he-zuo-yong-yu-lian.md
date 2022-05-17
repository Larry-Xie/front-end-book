# JS 作用域和作用域链

## 一、引言

在 JavaScript 里面，函数、块、模块都可以形成作用域，他们之间可以相互嵌套，作用域之间会形成引用关系，这条链叫做作用域链。理解作用域和作用域链是理解 JS 执行流程的基础，也是理解闭包的基础。

## 二、作用域

**作用域**是规定变量与函数可访问范围的一套规则。

**简单而言，作用域就是一个独立的地盘，让变量不会外泄、暴露出去**。也就是说**作用域最大的用处就是隔离变量，不同作用域下同名变量不会有冲突。**

ES6 之前 JS 中只有全局作用域和函数作用域。ES6 中引入了块级作用域的概念。

### **1. 全局作用域**

全局作用域中声明的变量与函数，可以在代码的任何地方被访问。

#### 1.1 属于全局作用域

一般来说，以下三种情形属于全局作用域。

* **全局对象下的属性与方法**

```javascript
window.name
window.location
window.top
```

* **在最外层声明的变量与方法**

```javascript
const foo = function() {}
let str = 'out variable';
let arr = [1, 2, 3];
function bar() {}
```

* **在非严格模式下，函数作用域中未定义但直接赋值的变量与方法。**

在非严格模式下，这样的变量自动变成全局对象 window 的属性。因此他们也是属于全局作用域。

```javascript
function foo() {
  bar = 20;
}

function fn() {
  foo();
  return bar + 30;
}

fn(); // 50
```

#### 1.2 避免滥用全局作用域

从一个完整的大型应用的角度来考虑，我们应该尽量少的将变量或者方法定义为全局。

* 我们可能会无意间修改全局变量的值，但是其他场景并不知道
* 命名冲突：不同的开发者，在同一个项目里如果都使用全局变量的话，很容易造成命名冲突
* 应用程序执行过程中，全局变量的内存无法被释放

#### 1.3 全局声明的差异

这里有一个细节需要重点关注，全局声明的变量，还存在一些差异。

我们使用 var 声明的变量，会被挂载到全局对象 window 之下。

```javascript
var a = 100
console.log(window.a) // 100
```

而使用 let/const 声明的变量，并不会修改 window 对象，而是被挂载到一个新的对象 Script 之下。

```javascript
const b = 20
console.log(window.b) // undefined
```

![Script](https://images.xiaozhuanlan.com/photo/2020/be8e92a8f924cc48213050d1ae6c6fde.png)

### **2. 函数作用域**

每一个花括号 `{}` 都是一个代码块。但需要注意的是，并不是所有花括号，都能够具备自己的作用域。函数声明或者函数表达式，能够让花括号具备作用域，我们称之为函数作用域。函数作用域中声明的变量与方法，只能被下层子作用域访问，不能被其他不相关的作用域访问。

```javascript
function foo() {
  var a = 20;
  var b = 30;
}
foo();

function bar() {
  return a + b;
}

bar(); // 因为作用域的限制，bar中无法访问到变量a,b，因此执行报错
```

```javascript
function foo() {
  var a = 20;
  var b = 30;

  function bar() {
    return a + b;
  }
  return bar();
}
foo(); // 50 bar中的作用域为foo的子作用域，因此能访问到变量a, b
```

在 ES6 以前，ECMAScript 没有块级作用域，因此使用时需要特别注意，一定是在函数环境中才能生成新的作用域。如下情况则不会有作用域的限制。

```javascript
var arr = [1, 2, 3, 4, 5];

for (var i = 0; i < arr.length; i++) {
  console.log('do something by ', i);
}

console.log(i); // i == 5
```

因为没有块级作用域，单独的 `{}` 并不会产生新的作用域。这个时候 i 的值会保留下来，在 for 循环结束之后仍然能够访问。

**作用域的范围信息，是在预解析阶段就已经确定的**，因此在代码执行之前，就已经明确了作用域的范围。

例如：

```javascript
function foo() {
  var a = 1;
  function bar() {
    console.log(a);
  }
  console.dir(bar)
}

foo()
```

例子中，我在 foo 函数内部定义了 bar 函数，执行 foo 函数时，并未执行 bar 函数，而是打印出来看看 bar 函数的详细信息，此时 bar 仅仅只是被预解析，而我们可以看到，在打印结果中，bar 函数的作用域信息已经生成了。\


![](https://images.xiaozhuanlan.com/photo/2020/b2691d6a68dcd4749a3e519fae830655.png)

### **3. 块级作用域**

变量的声明，有三种方式。

#### **3.1 var**

在 ES6 标准之前，我们只能使用 var 声明变量。这样变量声明的方式约束性很弱。对于已经声明的变量，我们可以重复声明，我们也可以任意修改变量声明的值。并且除函数之外的其他花括号也不会对 var 声明的变量有作用域的约束。它完美符合了 JavaScript 弱类型语言的定位。

```javascript
// 同名变量可以重复声明
var a = 10;
var a = 20;

// 声明的变量可以任意修改
var b = 30;
b = 40;
b = function() {}


// 花括号不会约束 var 变量的作用范围
{
  var c = 50
}
console.log(c) // 50
```

var 声明的变量，具备变量提升的特性。

```javascript
console.log(a) // 输出 undefined
var a = 20
console.log(a) // 输出 20
```

**变量提升**的意思是，将变量声明提升到当前作用域最前面执行，并且赋值为 undefined，而赋值操作留在原处。因此上述代码的真实执行顺序为

```javascript
var a = undeinfd
console.log(a)
a = 20
console.log(a)
```

正是因为 var 变量的灵活性，以及变量提升带来的理解偏差，导致许多使用者在使用 var 声明变量时，造成了很多困扰，对一些意外情况无法理解。

因此，为了减少开发者在使用过程中的错误，ES6 提供了新的，具有更强约束性的变量声明方式。

#### **3.2 let 声明**

let 声明最核心的特点，就是它声明的变量，能够被任何花括号约束。这也就是我们常说的**块级作用域**。

```javascript
{
  let a = 10
}
console.log(a) // Uncaught ReferenceError: a is not defined
```

```javascript
if (true) {
  let a = 20
}
console.log(a) // Uncaught ReferenceError: a is not defined
```

```javascript
for(let i = 0; i <= 10; i++) {
  console.log(i) // 依次输出 0 ~ 10
}
console.log(i) // Uncaught ReferenceError: i is not defined
```

```javascript
with(location) {
  let s = search.substring(1)
  let hostName = hostname
}
console.log(s) // Uncaught ReferenceError: s is not defined
```

**let 声明的变量，不能重复声明**，否则将会被解析为一个语法错误。

```javascript
let a = 20
let a = 30 // Uncaught SyntaxError: Identifier 'a' has already been declared
```

**let 声明的变量，不会提前赋值为 undefined**。

```javascript
// 记得在 js 文件或者 script 标签中执行，而不是在 console 面板中
console.log(a) // Cannot access 'a' before initialization
let a = 20
console.log(a)
```

**let 声明的变量，存在暂时性死区**。在当前块级作用域中，let 声明的变量，在赋值之前，都不能对该变量进行额外的访问与操作，否则就会报错。

```javascript
var a = 10;

if (true) {
  a = 20; // Uncaught ReferenceError: Cannot access 'a' before initialization
  let a = 30; // 当然这里也会报错，因为重复声明了，只不过提前报错了，所以暴露不到这里来
}
```

从这里的报错信息也可以看出，**let 声明的变量，其实也是存在变量提升的，只不过并没有进行初始化赋值「initialization」**，具体的我们后面再深入分析。

#### **3.3 const**

const 声明具备与 let 声明差不多的特性，他们只有一个点不一样，那就是 **const 声明的变量，对应的值不能被修改**。

**如果变量对应的是基础数据类型，则值不能被修改**

**如果变量对应的是引用数据类型，则是该引用的的内存地址不能被修改。**

```javascript
const a = 20
a = 30 // Uncaught SyntaxError: Identifier 'a' has already been declared

const person = { name: 'TOM' }
person.name = 'Jake'  // 可以修改
```

在实践中，let 和 const 的使用通常遵循以下规则：

**能用 const 的，就先使用 const，不能使用的，再使用 let 来声明。**

基于这样的原则，我们能够很快初步明确变量所对应的值，在当前上下文中的处境。

## **三、作用域链**

作用域是一个很抽象的概念。但是每一个函数内，能够访问哪些变量，都必须要有一个具体范围体现出来。因此有了作用域链的概念。

### 1. Scopes 属性

每一个函数都有一个 **\[\[Scopes]]** 属性，**它是由一系列对象组成的数组。每个对象，都对应某一个父级作用域。它们是从对应的父级函数作用域中，收集到的当前函数作用域内会使用到的变量声明、函数声明、函数参数的集合。**

仅仅从字面上理解有一点困难，我们结合一个例子来分析一下。

先从最简单的例子开始，我们直接在全局作用域中声明一个函数，观察一下这个函数的作用域链是什么

```javascript
function foo() {
  const a = 10;
  function bar() {}
}

console.dir(foo)
```

![](https://images.xiaozhuanlan.com/photo/2020/52f8e3ab7ffa56207ef809d468bab60f.png)

我们发现当前函数 foo 的 \[\[Scopes]] 属性中，只有一个全局的 window 对象。该对象代表了全局作用域的所有属性与方法。

接下来，将例子修改得稍微复杂一点

```javascript
function foo() {
  var a = 1;
  let b = 2;

  function bar() {
    console.log(a);
  }

  console.dir(bar)
}

foo()
```

在函数内部，我声明了两个变量 a，b，一个函数 bar。然后观察一下 bar 的作用域链。\


![](https://images.xiaozhuanlan.com/photo/2020/6b99697758431e9424e7ecc452a5aa51.png)

打印结果发现，bar 的 \[\[Scopes]] 属性中有两个对象，他们分别来自全局作用域，与父级函数 foo 的作用域。这里需要注意的是，父级函数 foo 中，声明了两个变量一个函数，但是在 bar 的作用域链对象中，仅仅只出现了变量 a。这是因为在函数 bar 内部，只访问了变量 a。这是在代码解析时针对性的做了优化。

如果我们同时在 bar 中访问了所有变量与函数，那么结果就会不一样。\


![](https://images.xiaozhuanlan.com/photo/2020/dc6dfeb00e62aafb6d78d1b40f204aa9.png)

重新修改一下代码

```javascript
function foo() {
  var a = 1;
  let b = 2;
  function bar() {
    function inner() {
      return a + b;
    }
    console.dir(inner)
  }
  console.dir(bar)
  bar()
}

foo()
```

在 bar 函数内部，新增函数 inner，inner 直接访问 foo 中声明的变量 a，b。此时同时观察函数 bar 与函数 inner 的 \[\[Scopes]] 属性，我们会发现，他们居然都是一样的。

![](https://images.xiaozhuanlan.com/photo/2020/5fcd15612eba2c6f65cd5995a9047d8c.png)

无论是对于 bar 还是 inner 来说，内部只访问了 foo 函数作用域中的 a，b，因此这样的结果是符合优化原则的，那些没有被访问的函数声明不会解析到作用域链中。

当然，从理论上而言，inner 函数的作用域链中，应该包含所有父级作用域中声明的变量/函数/参数。

```javascript
inner.[[Scopes]] = [O(bar), O(foo), O(window)]
```

只是O(bar) 中，一个变量也没有，因此就被优化掉了。

**在当前函数中，要寻找到变量的值是从哪里来的，就首先会从当前执行上下文中查找，如果没有找到，则会去作用域链中查找。这里需要注意的是，作用域链本身就是存在于函数对象中的一个属性 \[\[Scopes]]，因此不是一层一层的往上查找「这里经常理解有误」，该属性是在代码预解析阶段就已经确认好的。**

### **2. 作用域链的组成**

通过上面的学习，我们知道，每一个函数在声明后，都具备一个属性 \[\[Scopes]]，该属性中存储了当前函数可访问的所有变量对象。这些变量对象又有所不同。

* **Global 全局对象**：不会做任何优化，会包含全局对象中的所有属性与方法
* **Script 对象**：在全局环境下，由 let 和 const 声明的变量对象
* **Closure 对象**：我们讨论比较多的闭包对象，嵌套函数生成，仅会保存当前作用域能够访问的变量属性
* **Local 对象**：以上的几种变量对象，都会存在于函数的 \[\[Scopes]]属性之中，因为他们都能够在函数解析时确认，而 Local 对象则不行，需要在函数的执行过程中才能确定，并且在执行过程中，该对象中的属性是随时会发生变化的，该对象除了会存储当前函数上下文中所有的变量与函数声明，还会额外记录this 的指向。

通过该例子结合图示进行分析：

```javascript
// const 声明，在其他函数的作用域链中，会归属于 Script 对象中去
const a = 20;

function test() {
  const b = a + 10;
  var m = 20;
  const x = 10
  const y = 20

  function innerTest() {
    console.log(x, y)
    const c = 10 + m;
    console.dir(innerTest)

    function bar() {
      console.log(c)
      console.dir(bar)
    }
    return bar;
  }

  return innerTest();
}

var foo = test();
foo()
```

函数 innerTest 的完整作用域链如下：Local -> Closure(test) -> Script -> Global\


![](https://images.xiaozhuanlan.com/photo/2020/962707397439eea1b2feedf2e9008469.png)

函数 bar 的完整作用域链如下： Local -> Closure (innerTest) -> Closure (test) -> Script -> Global\


![](https://images.xiaozhuanlan.com/photo/2020/419ff83192d14c2020090fee955a4f0a.png)

大家可以利用该案例，结合断点调试观察一下在执行过程中，Local 对象的变化过程。

在作用域链的组成对象里，除了 Local 对象是在函数执行时确认的之外，其他的都是在函数声明时就能够明确的，因此在后续的文章里，我们称 Local 对象为**活动对象**，其他对象为**变量对象**。

> 注意，此处的 Local 对象，并非后续会介绍到的词法环境记录对象，或者变量环境记录对象，不过他们有很大的相似之处。

## 四、考点

### 1. 作用域与执行上下文

许多开发人员经常混淆作用域和执行上下文的概念，误认为它们是相同的概念，但事实并非如此。

我们知道JavaScript属于解释型语言，JavaScript的执行分为：解释和执行两个阶段,这两个阶段所做的事并不一样：

#### 解释阶段：

* 词法分析
* 语法分析
* 作用域规则确定

#### 执行阶段：

* 创建执行上下文
* 执行函数代码
* 垃圾回收

JavaScript解释阶段便会确定作用域规则，因此作用域在函数定义时就已经确定了，而不是在函数调用时确定，但是执行上下文是函数执行之前创建的。执行上下文最明显的就是this的指向是执行时确定的。而作用域访问的变量是编写代码的结构确定的。

作用域和执行上下文之间最大的区别是： **执行上下文在运行时确定，随时可能改变；作用域在定义时就确定，并且不会改变**。

一个作用域下可能包含若干个上下文环境。有可能从来没有过上下文环境（函数从来就没有被调用过）；有可能有过，现在函数被调用完毕后，上下文环境被销毁了；有可能同时存在一个或多个（闭包）。**同一个作用域下，不同的调用会产生不同的执行上下文环境，继而产生不同的变量的值**。

### 2. 变量提升

* 任何声明在某个作用域内的变量，都将附属于这个作用域。
* 包括变量和函数在内的所有声明都会在任何代码被执行前首先被处理。
* `var a = 2;`会被看成两个声明，`var a;`和`a = 2;`，第一个声明在**编译阶段**进行，第二个赋值声明会被留在原地等待**执行阶段**。
* 所有的声明（变量和函数）都会被\*\*“移动”**到各自作用域的最顶端，这个过程叫做**提升\*\*
* 只有声明本身会被提升，而包括函数表达式在内的赋值或其他运行逻辑并不会提升。

```javascript
a = 2;

var a;

console.log( a ); // 2

---------------------------------------
// 实际按如下形式进行处理
var a; // 编译阶段

a = 2; // 执行阶段

console.log( a ); // 2
```

```javascript
console.log( a ); // undefinde

var a = 2;

---------------------------------------
// 实际按如下形式进行处理
var a; // 编译

console.log( a ); // undefinde

a = 2; // 执行
```

* 每个作用域都会进行变量提升

```javascript
function foo() {
    var a;
    
    console.log( a ); // undefinde
    
    a = 2;
}

foo();
```

* 函数声明会被提升，但是**函数表达式不会被提升**。

```javascript
foo(); // 不是ReferenceError，而是TypeError!

var foo = function bar() {
    // ...
};
```

上面这段程序中，变量标识符`foo()`被提升并分配给所在作用域，因此`foo()`不会导致ReferenceError。此时`foo`并没有赋值（**如果它是一个函数声明而不是函数表达式，那么就会赋值**），`foo()`由于对`undefined`值进行函数调用而导致非法操作，因此抛出`TypeError`异常。

* 即使是具名的函数表达式，名称标识符在赋值之前也无法在所在作用域中使用。

```javascript
foo(); // TypeError
bar(); // ReferenceError

var foo = function bar() {
    // ...
};

---------------------------------------
// 实际按如下形式进行处理
var foo;

foo(); // TypeError
bar(); // ReferenceError

foo = function() {
    var bar = ...self...
    // ...
};
```

### 3. Local 对象

我们知道，对于一个函数而言，完整的作用域包含一个函数自身的 Local 对象。\


![](https://images.xiaozhuanlan.com/photo/2020/f46f1d48a4dad4baff5ebe57f4bcda7e.png)

Local 对象，由**函数参数，var 声明的变量，let/const 声明的变量，function 声明的变量，class 声明的变量，this 指向等**共同组成。

**仅仅只有处于栈顶的执行上下文，才会生成 Local 对象**。并且 Local 对象的具体内容会在执行上下文的生命周期中不断变化。也就意味着，在执行上下文的创建阶段，只有函数参数、function 声明的变量、this 指向 能够明确具体的值，其他变量的初始值都为 undefined，然后在代码执行过程中逐步明确赋值。

![](https://images.xiaozhuanlan.com/photo/2020/8628f8ff00b43405c718fb15d98d2635.png)

需要注意的是，Local 对象与环境记录对象非常相似，但他们并非相同的对象。不过这样的差别仅仅只是体现在具体的实现上，从理解执行上下文运行机制的理论角度来说，我们可以认为他们是同一个对象。

### 4. let、const、var的区别

**（1）块级作用域：** 块作用域由 `{ }`包括，let和const具有块级作用域，var不存在块级作用域。块级作用域解决了ES5中的两个问题：

* 内层变量可能覆盖外层变量
* 用来计数的循环变量泄露为全局变量

**（2）变量提升：** var存在变量提升，let和const不存在变量提升，即在变量只能在声明之后使用，否在会报错。

**（3）给全局添加属性：** 浏览器的全局对象是window，Node的全局对象是global。var声明的变量为全局变量，并且会将该变量添加为全局对象的属性，但是let和const不会。

**（4）重复声明：** var声明变量时，可以重复声明变量，后声明的同名变量会覆盖之前声明的遍历。const和let不允许重复声明变量。

**（5）暂时性死区：** 在使用let、const命令声明变量之前，该变量都是不可用的。这在语法上，称为**暂时性死区**。使用var声明的变量不存在暂时性死区。

**（6）初始值设置：** 在变量声明时，var 和 let 可以不用设置初始值。而const声明变量必须设置初始值。

**（7）指针指向：** let和const都是ES6新增的用于创建变量的语法。 let创建的变量是可以更改指针指向（可以重新赋值）。但const声明的变量是不允许改变指针的指向。

| **区别**    | **var** | **let** | **const** |
| --------- | ------- | ------- | --------- |
| 是否有块级作用域  | ×       | ✔️      | ✔️        |
| 是否存在变量提升  | ✔️      | ×       | ×         |
| 是否添加全局属性  | ✔️      | ×       | ×         |
| 能否重复声明变量  | ✔️      | ×       | ×         |
| 是否存在暂时性死区 | ×       | ✔️      | ✔️        |
| 是否必须设置初始值 | ×       | ×       | ✔️        |
| 能否改变指针指向  | ✔️      | ✔️      | ×         |

## 五、参考

* [JavaScript 核心进阶 - 作用域与作用域链](https://xiaozhuanlan.com/advance/3850496721)
* [JavaScript 的静态作用域链与“动态”闭包链](https://juejin.cn/post/6957913856488243237#heading-2)
* [「你不知道的 JavaScript」之作用域和闭包](https://juejin.cn/post/6844903766542516231)
