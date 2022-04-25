# JS 中的 this

## 一、引言

`this` 是很多人会混淆的概念，但是其实它一点都不难。而掌握 this 的关键，是要明白，函数的 this，是在什么时候确认的。

在执行上下文中的环境记录对象中，提供了一个内部属性 \[\[ThisValue]] 用于记录 this 的指向，同时还提供了一个内部方法 BindThisValue 用于设置 \[\[ThisValue]] 的值。也就意味着，对于函数而言，**this 的指向，是在执行上下文的创建过程中，被确定的。**

明确了这一点，我们就很容易知道，只有在函数调用时(箭头函数除外)，this 的指向才会被确定。于是一个函数的 this 的指向，就变得非常灵活。这也是导致 this 难以理解的真正原因。

``

## 二、调用位置 <a href="#1-tiao-yong-wei-zhi" id="1-tiao-yong-wei-zhi"></a>

既然 this 的指向是再执行上下文创建的过程中确定的，并且和函数调用有关，那我们就需要知道是谁调用了该函数。调用位置就是函数在代码中**被调用的位置**（而不是声明的位置）。

可以通过以下方法来查找调用：

* 分析调用栈：调用位置就是当前正在执行的函数的**前一个调用**中

```javascript
function baz() {
    // 当前调用栈是：baz
    // 因此，当前调用位置是全局作用域
    
    console.log( "baz" );
    bar(); // <-- bar的调用位置
}

function bar() {
    // 当前调用栈是：baz --> bar
    // 因此，当前调用位置在baz中
    
    console.log( "bar" );
    foo(); // <-- foo的调用位置
}

function foo() {
    // 当前调用栈是：baz --> bar --> foo
    // 因此，当前调用位置在bar中
    
    console.log( "foo" );
}

baz(); // <-- baz的调用位置
```

* 使用开发者工具得到调用栈：

设置断点或者插入`debugger;`语句，运行时调试器会在那个位置暂停，同时展示当前位置的函数调用列表，这就是**调用栈**。找到栈中的**第二个元素**，这就是真正的调用位置。

## 三、绑定规则 <a href="#2-bang-ding-gui-ze" id="2-bang-ding-gui-ze"></a>

`this`的绑定规则总共有下面5种。

1. 默认绑定（全局上下文，分严格/非严格模式）
2. 隐式绑定（obj.fn()）
3. 显式绑定（call, apply, bind）
4. new绑定
5. 箭头函数绑定

### 1. 默认绑定 <a href="#21-mo-ren-bang-ding" id="21-mo-ren-bang-ding"></a>

* **独立函数调用**，可以把默认绑定看作是无法应用其他规则时的默认规则，this 指向**全局对象**。
* **严格模式**下，不能将全局对象用于默认绑定，this会绑定到`undefined`。只有函数**运行**在非严格模式下，默认绑定才能绑定到全局对象。在严格模式下**调用**函数则不影响默认绑定。

```javascript
function foo() { // 运行在严格模式下，this会绑定到undefined
    "use strict";
    
    console.log( this.a );
}

var a = 2;

// 调用
foo(); // TypeError: Cannot read property 'a' of undefined

// --------------------------------------

function foo() { // 运行
    console.log( this.a );
}

var a = 2;

(function() { // 严格模式下调用函数则不影响默认绑定
    "use strict";
    
    foo(); // 2
})();
```

### 2. 隐式绑定 <a href="#22-yin-shi-bang-ding" id="22-yin-shi-bang-ding"></a>

当函数引用有**上下文对象**时，隐式绑定规则会把函数中的 this 绑定到这个上下文对象。对象属性引用链中只有上一层或者说最后一层在调用中起作用。

```javascript
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2,
    foo: foo
};

obj.foo(); // 2
```

#### 隐式丢失

被隐式绑定的函数特定情况下会丢失绑定对象，应用默认绑定，把 this 绑定到全局对象或者 undefined上。

```javascript
// 虽然bar是obj.foo的一个引用，但是实际上，它引用的是foo函数本身。
// bar()是一个不带任何修饰的函数调用，应用默认绑定。
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo; // 函数别名

var a = "oops, global"; // a是全局对象的属性

bar(); // "oops, global"
```

参数传递就是一种隐式赋值，传入函数时也会被隐式赋值。回调函数丢失 this 绑定是非常常见的。

```javascript
function foo() {
    console.log( this.a );
}

function doFoo(fn) {
    // fn其实引用的是foo
    
    fn(); // <-- 调用位置！
}

var obj = {
    a: 2,
    foo: foo
};

var a = "oops, global"; // a是全局对象的属性

doFoo( obj.foo ); // "oops, global"

// ----------------------------------------

// JS环境中内置的setTimeout()函数实现和下面的伪代码类似：
function setTimeout(fn, delay) {
    // 等待delay毫秒
    fn(); // <-- 调用位置！
}
```

### 3. 显式绑定 <a href="#23-xian-shi-bang-ding" id="23-xian-shi-bang-ding"></a>

通过`call(..)` 或者 `apply(..)`方法。第一个参数是一个对象，在调用函数时将这个对象绑定到 this。因为直接指定 this 的绑定对象，称之为显示绑定。

```javascript
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2
};

foo.call( obj ); // 2  调用foo时强制把foo的this绑定到obj上
```

显示绑定无法解决丢失绑定问题。

解决方案：

* 硬绑定

创建函数 bar()，并在它的内部手动调用 foo.call(obj)，强制把 foo 的 this 绑定到了 obj。

```javascript
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2
};

var bar = function() {
    foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2

// 硬绑定的bar不可能再修改它的this
bar.call( window ); // 2
```

典型应用场景是创建一个包裹函数，负责接收参数并返回值。

```javascript
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
}

var obj = {
    a: 2
};

var bar = function() {
    return foo.apply( obj, arguments );
};

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

创建一个可以重复使用的辅助函数。

```javascript
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
}

// 简单的辅助绑定函数
function bind(fn, obj) {
    return function() {
        return fn.apply( obj, arguments );
    }
}

var obj = {
    a: 2
};

var bar = bind( foo, obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

ES5 内置了`Function.prototype.bind`，bind 会返回一个硬绑定的新函数，用法如下。

```javascript
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
}

var obj = {
    a: 2
};

var bar = foo.bind( obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

* API 调用的 “上下文”

JS 许多内置函数提供了一个可选参数，被称之为“上下文”（context），其作用和`bind(..)`一样，确保回调函数使用指定的this。这些函数实际上通过`call(..)`和`apply(..)`实现了显式绑定。

```javascript
function foo(el) {
	console.log( el, this.id );
}

var obj = {
    id: "awesome"
}

var myArray = [1, 2, 3]
// 调用foo(..)时把this绑定到obj
myArray.forEach( foo, obj );
// 1 awesome 2 awesome 3 awesome
```

### 4. new 绑定 <a href="#24new-bang-ding" id="24new-bang-ding"></a>

* 在 JS 中，`构造函数`只是使用`new`操作符时被调用的`普通`函数，他们不属于某个类，也不会实例化一个类。
* 包括内置对象函数（比如`Number(..)`）在内的所有函数都可以用`new`来调用，这种函数调用被称为构造函数调用。
* 实际上并不存在所谓的“构造函数”，只有对于函数的“**构造调用**”。

使用`new`来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

1. 创建（或者说构造）一个新对象。
2. 这个新对象会被执行`[[Prototype]]`连接。
3. 这个新对象会绑定到函数调用的`this`。
4. 如果函数没有返回其他对象，那么`new`表达式中的函数调用会自动返回这个新对象。

使用`new`来调用`foo(..)`时，会构造一个新对象并把它（`bar`）绑定到`foo(..)`调用中的this。

```javascript
function foo(a) {
    this.a = a;
}

var bar = new foo(2); // bar和foo(..)调用中的this进行绑定
console.log( bar.a ); // 2
```

* **手写一个new实现**

```javascript
function create() {
	// 创建一个空的对象
    var obj = new Object(),
	// 获得构造函数，arguments中去除第一个参数
    Con = [].shift.call(arguments);
	// 链接到原型，obj 可以访问到构造函数原型中的属性
    obj.__proto__ = Con.prototype;
	// 绑定 this 实现继承，obj 可以访问到构造函数中的属性
    var ret = Con.apply(obj, arguments);
	// 优先返回构造函数返回的对象
	return ret instanceof Object ? ret : obj;
};
```

使用这个手写的 new

```javascript
function Person() {...}

// 使用内置函数new
var person = new Person(...)
                        
// 使用手写的new，即create
var person = create(Person, ...)
```

代码原理解析：

* 用`new Object()`的方式新建了一个对象`obj`
* 取出第一个参数，就是我们要传入的构造函数。此外因为 shift 会修改原数组，所以 `arguments`会被去除第一个参数
* 将 `obj`的原型指向构造函数，这样`obj`就可以访问到构造函数原型中的属性
* 使用`apply`，改变构造函数`this` 的指向到新建的对象，这样 `obj`就可以访问到构造函数中的属性
* 返回 `obj`

## 四、优先级 <a href="#3-you-xian-ji" id="3-you-xian-ji"></a>

当同时有多种绑定方式使用时，它们之间会根据一定的优先级规则来确定最终的 this。

![this](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a5aba94bd75d4429b01f6e0f57838373\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 五、绑定例外 <a href="#4-bang-ding-li-wai" id="4-bang-ding-li-wai"></a>

### 1. 被忽略的 this <a href="#41-bei-hu-lve-de-this" id="41-bei-hu-lve-de-this"></a>

把`null`或者`undefined`作为`this`的绑定对象传入`call`、`apply`或者`bind`，这些值在调用时会被忽略，实际应用的是默认规则。

下面两种情况下会传入`null`

* 使用`apply(..)`来“展开”一个数组，并当作参数传入一个函数
* `bind(..)`可以对参数进行柯里化（预先设置一些参数）

```javascript
function foo(a, b) {
    console.log( "a:" + a + "，b:" + b );
}

// 把数组”展开“成参数
foo.apply( null, [2, 3] ); // a:2，b:3

// 使用bind(..)进行柯里化
var bar = foo.bind( null, 2 );
bar( 3 ); // a:2，b:3 
```

总是传入`null`来忽略this绑定可能产生一些副作用。如果某个函数确实使用了this，那默认绑定规则会把this绑定到全局对象中。

#### 更安全的this

安全的做法就是传入一个特殊的对象（空对象），把this绑定到这个对象不会对你的程序产生任何副作用。

JS中创建一个空对象最简单的方法是\*\*`Object.create(null)`\*\*，这个和`{}`很像，但是并不会创建`Object.prototype`这个委托，所以比`{}`更空。

```javascript
function foo(a, b) {
    console.log( "a:" + a + "，b:" + b );
}

// 我们的空对象
var ø = Object.create( null );

// 把数组”展开“成参数
foo.apply( ø, [2, 3] ); // a:2，b:3

// 使用bind(..)进行柯里化
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2，b:3 
```

### 2. 间接引用 <a href="#42-jian-jie-yin-yong" id="42-jian-jie-yin-yong"></a>

间接引用下，调用这个函数会应用默认绑定规则。间接引用最容易在**赋值**时发生。

```javascript
// p.foo = o.foo的返回值是目标函数的引用，所以调用位置是foo()而不是p.foo()或者o.foo()
function foo() {
    console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4};

o.foo(); // 3
(p.foo = o.foo)(); // 2
```

### 3. 软绑定 <a href="#43-ruan-bang-ding" id="43-ruan-bang-ding"></a>

* 硬绑定可以把 this 强制绑定到指定的对象（`new`除外），防止函数调用应用默认绑定规则。但是会降低函数的灵活性，使用**硬绑定之后就无法使用隐式绑定或者显式绑定来修改 this**。
* **如果给默认绑定指定一个全局对象和 undefined 以外的值**，那就可以实现和硬绑定相同的效果，同时保留隐式绑定或者显示绑定修改 this 的能力。

```javascript
// 默认绑定规则，优先级排最后
// 如果this绑定到全局对象或者undefined，那就把指定的默认对象obj绑定到this,否则不会修改this
if(!Function.prototype.softBind) {
    Function.prototype.softBind = function(obj) {
        var fn = this;
        // 捕获所有curried参数
        var curried = [].slice.call( arguments, 1 ); 
        var bound = function() {
            return fn.apply(
            	(!this || this === (window || global)) ? 
                	obj : this,
                curried.concat.apply( curried, arguments )
            );
        };
        bound.prototype = Object.create( fn.prototype );
        return bound;
    };
}
```

使用：软绑定版本的 foo() 可以手动将 this 绑定到 obj2 或者 obj3 上，但如果应用默认绑定，则会将 this 绑定到 obj。

```javascript
function foo() {
    console.log("name:" + this.name);
}

var obj = { name: "obj" },
    obj2 = { name: "obj2" },
    obj3 = { name: "obj3" };

// 默认绑定，应用软绑定，软绑定把this绑定到默认对象obj
var fooOBJ = foo.softBind( obj );
fooOBJ(); // name: obj 

// 隐式绑定规则
obj2.foo = foo.softBind( obj );
obj2.foo(); // name: obj2 <---- 看！！！

// 显式绑定规则
fooOBJ.call( obj3 ); // name: obj3 <---- 看！！！

// 绑定丢失，应用软绑定
setTimeout( obj2.foo, 10 ); // name: obj
```

## 六、this 词法 <a href="#5this-ci-fa" id="5this-ci-fa"></a>

ES6新增一种特殊函数类型：箭头函数，箭头函数无法使用上述四条规则，而是根据外层（函数或者全局）作用域（**词法作用域**）来决定 this。

* `foo()`内部创建的箭头函数会捕获调用时`foo()`的this。由于`foo()`的 this 绑定到`obj1`，`bar`(引用箭头函数)的 this 也会绑定到`obj1`，**箭头函数的绑定无法被修改**(`new`也不行)。

```javascript
function foo() {
    // 返回一个箭头函数
    return (a) => {
        // this继承自foo()
        console.log( this.a );
    };
}

var obj1 = {
    a: 2
};

var obj2 = {
    a: 3
}

var bar = foo.call( obj1 );
bar.call( obj2 ); // 2，不是3！
```

ES6 之前和箭头函数类似的模式，采用的是词法作用域取代了传统的 this 机制。

```javascript
function foo() {
    var self = this; // lexical capture of this
    setTimeout( function() {
        console.log( self.a ); // self只是继承了foo()函数的this绑定
    }, 100 );
}

var obj = {
    a: 2
};

foo.call(obj); // 2
```

代码风格统一问题：如果既有 this 风格的代码，还会使用 `seft = this` 或者箭头函数来否定 this 机制。

* 只使用词法作用域并完全抛弃错误this风格的代码；
* 完全采用this风格，在必要时使用`bind(..)`，尽量避免使用 `self = this` 和箭头函数。

## 七、函数柯里化 <a href="#shang-qi-si-kao-ti-jie" id="shang-qi-si-kao-ti-jie"></a>

关于函数柯里化，在这不做过多介绍，只要知道函数柯里化是什么：

在计算机科学中，柯里化（英语：`Currying`），又译为`卡瑞化`或`加里化`，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且**返回接受余下的参数而且返回结果的新函数**的技术。

例子可以看[这篇文章](https://juejin.cn/post/6844903512942313479)里的。

## 八、考点

### 1. apply vs call vs bind

#### apply vs call

当函数 fn 调用 call/apply 时，其实是在执行 fn 函数自身，只是此时 call/apply 接受第一个参数，将函数内部的 this 明确的指向这个传入的参数。

call/apply 具备同样的能力，他们的**区别在于参数的传递方式**不同。

* **call** 的第一个参数是函数内部指定的 this 指向，后续的参数是函数本身执行应该传入的参数，**一个一个**的传递。
* **apply** 的第一个参数与 call 完全一样，函数本身应该传入的参数，则以**数组**的形式，作为 apply 的第二个参数传入。

```javascript
function fn(num1, num2) {
return this.a + num1 + num2;
}

var a = 20;
var object = { a: 40 }

// 正常执行
fn(10, 10); // 40

// 通过call改变this指向
fn.call(object, 10, 10); // 60

// 通过apply改变this指向
fn.apply(object, [10, 10]); // 60
```

#### bind

bind 方法也能够显示的指定函数内部的 this 指向，但是它与 call/apply 有所不同。

* 当函数调用 call/apply 时，函数会立即执行。
* 当函数**调用 bind 时，函数并不会立即执行，而是返回一个新的函数**，这个新的函数与原函数具有内容一样的函数体，但是它并非原函数，并且新函数的参数与 this 指向都已经被绑定，参数为 bind 的后续参数。

```javascript
function fn(num1, num2) {
  return this.a + num1 + num2;
}

var a = 20;
var object = { a: 40 }

var _fn = fn.bind(object, 1, 2);

console.log(_fn === fn); // false
_fn(); // 43
_fn(1, 4); // 43 因为参数被绑定，因此重新传入参数是无效的
```

call/apply/bind的特性，让JavaScript变得十分灵活，他们的应用场景十分广泛。

## 九、参考

* [JavaScript 深入之史上最全 this 绑定全面解析](https://muyiy.cn/blog/3/3.1.html)
* [图解 javascript 的 this 指向](https://juejin.cn/post/6844903941021384711)
* [JavaScript 核心进阶 - this](https://xiaozhuanlan.com/advance/0293581764)
