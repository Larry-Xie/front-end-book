# JS 数据类型

## 一、数据类型

### 1. 类型

JavaScript 中共有八种数据类型，可以分为：

* **基本数据类型**： Undefined、Null、Boolean、Number、String、Symbol、BigInt；
* **引用数据类型**：对象、数组和函数；

其中 Symbol 和 BigInt 是 ES6 中新增的数据类型：

* **Symbol** 代表创建后独一无二且不可变的数据类型，主要是为了解决可能出现的全局变量冲突的问题。
* **BigInt** 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这个数已经超出了 Number 能够表示的安全整数范围。

> JavaScript 中 Number.MAX\_SAFE\_INTEGER 表示最⼤安全数字，计算结果是 9007199254740991，即在这个数范围内不会出现精度丢失（⼩数除外）。但是⼀旦超过这个范围，js 就会出现计算不准确的情况，这在⼤数计算的时候不得不依靠⼀些第三⽅库进⾏解决，因此官⽅提出了 BigInt 来解决此问题。

### 2. 区别

两种类型的最大区别在于**存储位置的不同：**

* 基本数据类型：存储在**栈**（stack）中，占据空间小、大小固定，且通常被频繁使用；
* 引用数据类型：存储在**堆**（heap）中，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。

### 3. 堆和栈

堆和栈的概念存在于数据结构和操作系统内存中，在数据结构中：

* 在数据结构中，栈中数据的存取方式为先进后出。
* 堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。

在操作系统中，内存被分为栈区和堆区：

* 栈区内存由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
* 堆区内存一般由开发着分配释放，若开发者不释放，程序结束时可能由垃圾回收机制回收。

## 二、数据类型检测

### **1. typeof**

```javascript
console.log(typeof 2);               // number
console.log(typeof true);            // boolean
console.log(typeof 'str');           // string
console.log(typeof []);              // object    
console.log(typeof function(){});    // function
console.log(typeof {});              // object
console.log(typeof undefined);       // undefined
console.log(typeof null);            // object
```

其中数组、对象、null 都会被判断为 object。所以通常用于基本数据类型的检测。

### **2. instanceof**

`instanceof`可以正确判断对象的类型，**其内部运行机制是判断在其原型链中能否找到该类型的原型**。

```javascript
console.log(2 instanceof Number);                    // false
console.log(true instanceof Boolean);                // false 
console.log('str' instanceof String);                // false 
console.log((new Number(2)) instanceof Number);      // false
console.log(new Boolean(true) instanceof Boolean);   // false 
console.log(new String('str') instanceof String);    // false 
 
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);                   // true
```

可以看到，`instanceof`**只能正确判断引用数据类型**，而不能判断基本数据类型。`instanceof` 运算符可以用来测试一个对象在其原型链中是否存在一个构造函数的 `prototype` 属性。

### **3. constructor**

```javascript
console.log((2).constructor === Number);               // true
console.log((true).constructor === Boolean);           // true
console.log(('str').constructor === String);           // true
console.log(([]).constructor === Array);               // true
console.log((function() {}).constructor === Function); // true
console.log(({}).constructor === Object);              // true
```

`constructor`有两个作用，一是判断数据的类型，二是对象实例通过 `constructor` 对象访问它的构造函数。需要注意，如果创建一个对象来改变它的原型，`constructor`就不能用来判断数据类型了：

```javascript
function Fn(){};
 
Fn.prototype = new Array();
 
var f = new Fn();
 
console.log(f.constructor===Fn);    // false
console.log(f.constructor===Array); // true
```

### **4. Object.prototype.toString.call()**

`Object.prototype.toString.call()` 使用 Object 对象的原型方法 toString 来判断数据类型：

```javascript
var a = Object.prototype.toString;
 
console.log(a.call(2));
console.log(a.call(true));
console.log(a.call('str'));
console.log(a.call([]));
console.log(a.call(function(){}));
console.log(a.call({}));
console.log(a.call(undefined));
console.log(a.call(null));
```

同样是检测对象 obj 调用 toString 方法，obj.toString() 的结果和 Object.prototype.toString.call(obj) 的结果不一样，这是为什么？

这是因为 toString 是 Object 的原型方法，而 Array、function 等**类型作为 Object 的实例，都重写了 toString 方法**。不同的对象类型调用 toString 方法时，根据原型链的知识，调用的是对应的重写之后的 toString 方法（function 类型返回内容为函数体的字符串，Array 类型返回元素组成的字符串…），而不会去调用 Object 上原型 toString 方法（返回对象的具体类型），所以采用 obj.toString() 不能得到其对象类型，只能将 obj 转换为字符串类型；因此，在想要得到对象的具体类型时，应该调用 Object 原型上的 toString 方法。

## 三、数据类型转换

### 1. 隐式类型转换

首先要介绍`ToPrimitive`方法，这是 JavaScript 中每个值隐含的自带的方法，用来将值 （无论是基本类型值还是对象）**转换为基本类型值**。如果值为基本类型，则直接返回值本身；如果值为对象，其看起来大概是这样：

```javascript
/**
* @obj 需要转换的对象
* @type 期望的结果类型
*/
ToPrimitive(obj, type)
```

`type`的值为`number`或者`string`。

**当**`type`**为**`number`**时规则如下：**

* 调用`obj`的`valueOf`方法，如果为原始值，则返回，否则下一步；
* 调用`obj`的`toString`方法，后续同上；
* 抛出`TypeError` 异常。

**当**`type`**为**`string`**时规则如下：**

* 调用`obj`的`toString`方法，如果为原始值，则返回，否则下一步；
* 调用`obj`的`valueOf`方法，后续同上；
* 抛出`TypeError` 异常。

可以看出两者的主要区别在于调用`toString`和`valueOf`的先后顺序。默认情况下：

* 如果对象为 Date 对象，则`type`默认为`string`；
* 其他情况下，`type`默认为`number`。

总结上面的规则，对于 Date 以外的对象，转换为基本类型的大概规则可以概括为一个函数：

```javascript
var objToNumber = value => Number(value.valueOf().toString())
objToNumber([]) === 0
objToNumber({}) === NaN
```

而 JavaScript 中的**隐式类型转换主要发生在`+、-、*、/`以及`==、>、<`这些运算符之间。而这些运算符只能操作基本类型值，所以在进行这些运算前的第一步就是将两边的值用`ToPrimitive`转换成基本类型，再进行操作**。

以下是基本类型的值在不同操作符的情况下隐式转换的规则 （对于对象，其会被`ToPrimitive`转换成基本类型，所以最终还是要应用基本类型转换规则）：

#### 1.1 `+`**操作符**

* `+`操作符的两边有**至少一个`string`类型**变量时，两边的变量都会被隐式**转换为字符串**；
* **其他**情况下两边的变量都会被**转换为数字；**

```javascript
1 + '23' // '123'
1 + false // 1 
1 + Symbol() // Uncaught TypeError: Cannot convert a Symbol value to a number
'1' + false // '1false'
false + true // 1
```

#### 1.2 `-`、`*`、`\`**操作符**

对于这些操作符，转换为数字进行操作，注意，`NaN`也是一个数字。

```javascript
1 * '23' // 23
1 * false // 0
1 / 'aa' // NaN
```

#### **1.3** `==`**操作符**

对于 `==` 来说，如果对比双方的类型**不一样**，就会进行**类型转换，**假如对比 `x` 和 `y` 是否相同，就会进行如下判断流程：

* 首先会判断两者类型是否\*\*相同，\*\*相同的话就比较两者的大小；
* 类型不相同的话，就会进行类型转换；
* 会先判断是否在对比 `null` 和 `undefined`，是的话就会返回 `true`
* 判断两者类型是否为 `string` 和 `number`，是的话就会将字符串转换为 `number`

```javascript
1 == '1'
      ↓
1 ==  1
```

* 判断其中一方是否为 `boolean`，是的话就会把 `boolean` 转为 `number` 再进行判断

```javascript
'1' == true
        ↓
'1' ==  1
        ↓
 1  ==  1
```

* 判断其中一方是否为 `object` 且另一方为 `string`、`number` 或者 `symbol`，是的话就会把 `object` 转为原始类型再进行判断

```javascript
'1' == { name: 'js' }        ↓'1' == '[object Object]'
```

其流程图如下：&#x20;

![== 操作符的类型转换](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c451c19e23dd4726b3f36223b6c18a1e\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### **1.4** `<`**和**`>`**比较符**

* 如果两边都是**字符串**，则**比较字母表顺序**：

```javascript
'ca' < 'bd' // false
'a' < 'b' // true
```

* **其他情况**下，**转换为数字**再比较：

```javascript
'12' < 13 // true
false > -1 // true
```

#### 1.5 对象

以上说的是基本类型的隐式转换，而对象会被`ToPrimitive`转换为基本类型再进行转换：

```javascript
var a = {}
a > 2 // false
```

其对比过程如下：

```javascript
a.valueOf() // {}, 上面提到过，ToPrimitive默认type为number，所以先valueOf，结果还是个对象，下一步
a.toString() // "[object Object]"，现在是一个字符串了
Number(a.toString()) // NaN，根据上面 < 和 > 操作符的规则，要转换成数字
NaN > 2 //false，得出比较结果
```

又比如：

```javascript
var a = {name:'Jack'}
var b = {age: 18}
a + b // "[object Object][object Object]"
```

运算过程如下：

```javascript
a.valueOf() // {}，上面提到过，ToPrimitive默认type为number，所以先valueOf，结果还是个对象，下一步
a.toString() // "[object Object]"
b.valueOf() // 同理
b.toString() // "[object Object]"
a + b // "[object Object][object Object]"
```

### 2. 强制类型转换

#### 2.1 String

* Null 和 Undefined 类型 ，null 转换为 "null"，undefined 转换为 "undefined"，
* Boolean 类型，true 转换为 "true"，false 转换为 "false"。
* Number 类型的值直接转换，不过那些极小和极大的数字会使用指数形式。
* Symbol 类型的值直接转换，但是只允许显式强制类型转换，使用隐式强制类型转换会产生错误。
* 对普通对象来说，除非自行定义 toString() 方法，否则会调用 toString()（Object.prototype.toString()）来返回内部属性 \[\[Class]] 的值，如"\[object Object]"。如果对象有自己的 toString() 方法，字符串化时就会调用该方法并使用其返回值。

#### 2.2 Number

* Undefined 类型的值转换为 NaN。
* Null 类型的值转换为 0。
* Boolean 类型的值，true 转换为 1，false 转换为 0。
* String 类型的值转换如同使用 Number() 函数进行转换，如果包含非数字值则转换为 NaN，空字符串为 0。
* Symbol 类型的值不能转换为数字，会报错。
* 对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。

为了将值转换为相应的基本类型值，抽象操作 ToPrimitive 会首先（通过内部操作 DefaultValue）检查该值是否有valueOf()方法。如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用 toString() 的返回值（如果存在）来进行强制类型转换。

如果 valueOf() 和 toString() 均不返回基本类型值，会产生 TypeError 错误。

#### 2.3 Boolean

以下这些是**假**值，假值的布尔强制类型转换结果为 **false**：

* undefined
* null
* false
* \+0、-0
* NaN
* ""

从逻辑上说，**假值列表以外的都应该是真值**。

### 3. 包装类型

在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象，如：

```javascript
const a = "abc";
a.length; // 3
a.toUpperCase(); // "ABC"
```

在访问`'abc'.length`时，JavaScript 将`'abc'`在后台转换成`String('abc')`，然后再访问其`length`属性。

JavaScript 也可以使用`Object`函数显式地将基本类型转换为包装类型：

```javascript
var a = 'abc'
Object(a) // String {"abc"}
```

也可以使用`valueOf`方法将包装类型倒转成基本类型：

```javascript
var a = 'abc'
var b = Object(a)
var c = b.valueOf() // 'abc'
```

看看如下代码会打印出什么：

```javascript
var a = new Boolean( false );
if (!a) {
    console.log( "Oops" ); // never runs
}
```

答案是什么都不会打印，因为虽然包裹的基本类型是`false`，但是`false`被包裹成包装类型后就成了对象，所以其非值为`false`，所以循环体中的内容不会运行。

## 四、考点

### 1. null 和 undefined 区别

* 首先 Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。
* undefined 代表的含义是**未定义**，null 代表的含义是**空对象**。一般变量声明了但还没有定义的时候会返回 undefined，null 主要用于赋值给一些可能会返回对象的变量，作为初始化。
* undefined 在 JavaScript 中不是一个保留字，这意味着可以使用 undefined 来作为一个变量名，但是这样的做法是非常危险的，它会影响对 undefined 值的判断。我们可以通过一些方法获得安全的 undefined 值，比如说 void 0。
* 当对这两种类型使用 typeof 进行判断时，Null 类型化会返回 “object”，这是一个历史遗留的问题。当使用双等号对两种类型的值进行比较时会返回 true，使用三个等号时会返回 false。

### 2. 判断数组的方式

* 通过 Object.prototype.toString.call() 做判断

```javascript
Object.prototype.toString.call(obj).slice(8,-1) === 'Array';
```

* 通过原型链做判断

```javascript
obj.__proto__ === Array.prototype;
```

* 通过 ES6 的 Array.isArray() 做判断

```javascript
Array.isArrray(obj);
```

* 通过 instanceof 做判断

```javascript
obj instanceof Array
```

* 通过Array.prototype.isPrototypeOf

```javascript
Array.prototype.isPrototypeOf(obj)
```

### 3. typeof null 的结果

typeof null 的结果是 Object。

在 JavaScript 第一个版本中，所有值都存储在 32 位的单元中，每个单元包含一个小的 **类型标签(1-3 bits)** 以及当前要存储值的真实数据。类型标签存储在每个单元的低位中，共有五种数据类型：

```javascript
000: object   - 当前存储的数据指向一个对象。
  1: int      - 当前存储的数据是一个 31 位的有符号整数。
010: double   - 当前存储的数据指向一个双精度的浮点数。
100: string   - 当前存储的数据指向一个字符串。
110: boolean  - 当前存储的数据是布尔值。
```

如果最低位是 1，则类型标签标志位的长度只有一位；如果最低位是 0，则类型标签标志位的长度占三位，为存储其他四种数据类型提供了额外两个 bit 的长度。

有两种特殊数据类型：

* undefined 的值是 (-2)30(一个超出整数范围的数字)；
* null 的值是机器码 NULL 指针(null 指针的值全是 0)

那也就是说null的类型标签也是000，和 Object 的类型标签一样，所以会被判定为Object。

### 4. typeof NaN 的结果

NaN 指“不是一个数字”（not a number），NaN 是一个“警戒值”（sentinel value，有特殊用途的常规值），用于指出数字类型中的错误情况，即“执行数学运算没有成功，这是失败后返回的结果”。

```javascript
typeof NaN; // "number"
```

NaN 是一个特殊值，它和自身不相等，是**唯一一个非自反**（自反，reflexive，即 x === x 不成立）的值。而 NaN !== NaN 为 true。

### 5. intanceof 的原理及实现

instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。

instanceof 原理就是一层一层查找 `__proto__`，如果和 `constructor.prototype` 相等则返回 true，如果一直没有查找成功则返回 false。

```javascript
function myInstanceof(left, right) {
  // 获取对象的原型
  let proto = Object.getPrototypeOf(left)
  // 获取构造函数的 prototype 对象
  let prototype = right.prototype; 
 
  // 判断构造函数的 prototype 对象是否在对象的原型链上
  while (true) {
    if (!proto) return false;
    if (proto === prototype) return true;
    // 如果没有找到，就继续从其原型上找，Object.getPrototypeOf方法用来获取指定对象的原型
    proto = Object.getPrototypeOf(proto);
  }
}
```

### 6. 如何获取安全的 undefined 值

因为 undefined 是一个标识符，所以可以被当作变量来使用和赋值，但是这样会影响 undefined 的正常判断。表达式 void \_\_\_ 没有返回值，因此返回结果是 undefined。void 并不改变表达式的结果，只是让表达式不返回值。因此可以用 void 0 来获得 undefined。

## 五、参考

* [高频前端面试题汇总之 JavaScript 篇（上）](https://juejin.cn/post/6940945178899251230)
* [网道 JavaScript 教程 - 数据类型转换](https://wangdoc.com/javascript/features/conversion.html)
