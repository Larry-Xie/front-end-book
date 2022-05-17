# ECMAScript

## &#x20;一、ECMAScript背景

### 1.1 ECMAScript 和 JavaScript 的关系

要讲清楚ECMAScript 和 JavaScript 到底是什么关系这个问题，需要回顾历史。1996 年 11 月，JavaScript 的创造者 Netscape 公司，决定将 JavaScript 提交给标准化组织 ECMA，希望这种语言能够成为国际标准。次年，ECMA 发布 262 号标准文件（ECMA-262）的第一版，规定了浏览器脚本语言的标准，并将这种语言称为 ECMAScript，这个版本就是 1.0 版。

该标准从一开始就是针对 JavaScript 语言制定的，但是之所以不叫 JavaScript，有两个原因。一是商标，Java 是 Sun 公司的商标，根据授权协议，只有 Netscape 公司可以合法地使用 JavaScript 这个名字，且 JavaScript 本身也已经被 Netscape 公司注册为商标。二是想体现这门语言的制定者是 ECMA，不是 Netscape，这样有利于保证这门语言的开放性和中立性。

因此，ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现（另外的 ECMAScript 方言还有 Jscript 和 ActionScript）。日常场合，这两个词是可以互换的。

### 1.2 ES6 与 ECMAScript 2015 的关系

ECMAScript 2015（简称 ES2015）这个词，也是经常可以看到的。它与 ES6 是什么关系呢？

2011 年，ECMAScript 5.1 版发布后，就开始制定 6.0 版了。因此，ES6 这个词的原意，就是指 JavaScript 语言的下一个版本。

但是，因为这个版本引入的语法功能太多，而且制定过程当中，还有很多组织和个人不断提交新功能。事情很快就变得清楚了，不可能在一个版本里面包括所有将要引入的功能。常规的做法是先发布 6.0 版，过一段时间再发 6.1 版，然后是 6.2 版、6.3 版等等。

但是，标准的制定者不想这样做。他们想让标准的升级成为常规流程：任何人在任何时候，都可以向标准委员会提交新语法的提案，然后标准委员会每个月开一次会，评估这些提案是否可以接受，需要哪些改进。如果经过多次会议以后，一个提案足够成熟了，就可以正式进入标准了。这就是说，标准的版本升级成为了一个不断滚动的流程，每个月都会有变动。

标准委员会最终决定，标准在每年的 6 月份正式发布一次，作为当年的正式版本。接下来的时间，就在这个版本的基础上做改动，直到下一年的 6 月份，草案就自然变成了新一年的版本。这样一来，就不需要以前的版本号了，只要用年份标记就可以了。

ES6 的第一个版本，就这样在 2015 年 6 月发布了，正式名称就是《ECMAScript 2015 标准》（简称 ES2015）。2016 年 6 月，小幅修订的《ECMAScript 2016 标准》（简称 ES2016）如期发布，这个版本可以看作是 ES6.1 版，因为两者的差异非常小（只新增了数组实例的`includes`方法和指数运算符），基本上是同一个标准。根据计划，2017 年 6 月发布 ES2017 标准。

因此，ES6 既是一个历史名词，也是一个泛指，含义是 5.1 版以后的 JavaScript 的下一代标准，涵盖了 ES2015、ES2016、ES2017 等等，而 ES2015 则是正式名称，特指该年发布的正式版本的语言标准。本书中提到 ES6 的地方，一般是指 ES2015 标准，但有时也是泛指“下一代 JavaScript 语言”。

### 1.3 语法提案的批准流程

任何人都可以向标准委员会（又称 TC39 委员会）提案，要求修改语言标准。

一种新的语法从提案到变成正式标准，需要经历五个阶段。每个阶段的变动都需要由 TC39 委员会批准。

* Stage 0 - Strawman（展示阶段）
* Stage 1 - Proposal（征求意见阶段）
* Stage 2 - Draft（草案阶段）
* Stage 3 - Candidate（候选人阶段）
* Stage 4 - Finished（定案阶段）

一个提案只要能进入 Stage 2，就差不多肯定会包括在以后的正式标准里面。ECMAScript 当前的所有提案，可以在 TC39 的官方网站[Github.com/tc39/ecma262](https://github.com/tc39/ecma262)查看。

### 1.4 ECMAScript 的历史

ES6 从开始制定到最后发布，整整用了 15 年。

前面提到，ECMAScript 1.0 是 1997 年发布的，接下来的两年，连续发布了 ECMAScript 2.0（1998 年 6 月）和 ECMAScript 3.0（1999 年 12 月）。3.0 版是一个巨大的成功，在业界得到广泛支持，成为通行标准，奠定了 JavaScript 语言的基本语法，以后的版本完全继承。直到今天，初学者一开始学习 JavaScript，其实就是在学 3.0 版的语法。

2000 年，ECMAScript 4.0 开始酝酿。这个版本最后没有通过，但是它的大部分内容被 ES6 继承了。因此，ES6 制定的起点其实是 2000 年。

为什么 ES4 没有通过呢？因为这个版本太激进了，对 ES3 做了彻底升级，导致标准委员会的一些成员不愿意接受。ECMA 的第 39 号技术专家委员会（Technical Committee 39，简称 TC39）负责制订 ECMAScript 标准，成员包括 Microsoft、Mozilla、Google 等大公司。

2007 年 10 月，ECMAScript 4.0 版草案发布，本来预计次年 8 月发布正式版本。但是，各方对于是否通过这个标准，发生了严重分歧。以 Yahoo、Microsoft、Google 为首的大公司，反对 JavaScript 的大幅升级，主张小幅改动；以 JavaScript 创造者 Brendan Eich 为首的 Mozilla 公司，则坚持当前的草案。

2008 年 7 月，由于对于下一个版本应该包括哪些功能，各方分歧太大，争论过于激烈，ECMA 开会决定，中止 ECMAScript 4.0 的开发，将其中涉及现有功能改善的一小部分，发布为 ECMAScript 3.1，而将其他激进的设想扩大范围，放入以后的版本，由于会议的气氛，该版本的项目代号起名为 Harmony（和谐）。会后不久，ECMAScript 3.1 就改名为 ECMAScript 5。

2009 年 12 月，ECMAScript 5.0 版正式发布。Harmony 项目则一分为二，一些较为可行的设想定名为 JavaScript.next 继续开发，后来演变成 ECMAScript 6；一些不是很成熟的设想，则被视为 JavaScript.next.next，在更远的将来再考虑推出。TC39 委员会的总体考虑是，ES5 与 ES3 基本保持兼容，较大的语法修正和新功能加入，将由 JavaScript.next 完成。当时，JavaScript.next 指的是 ES6，第六版发布以后，就指 ES7。TC39 的判断是，ES5 会在 2013 年的年中成为 JavaScript 开发的主流标准，并在此后五年中一直保持这个位置。

2011 年 6 月，ECMAscript 5.1 版发布，并且成为 ISO 国际标准（ISO/IEC 16262:2011）。

2013 年 3 月，ECMAScript 6 草案冻结，不再添加新功能。新的功能设想将被放到 ECMAScript 7。

2013 年 12 月，ECMAScript 6 草案发布。然后是 12 个月的讨论期，听取各方反馈。

2015 年 6 月，ECMAScript 6 正式通过，成为国际标准。从 2000 年算起，这时已经过去了 15 年。

## 二、ECMAScript 2015

直接看阮一峰的\<ECMAScript 6入门>：[http://es6.ruanyifeng.com](http://es6.ruanyifeng.com/)

## 三、ECMAScript 2016

ECMAScript 2016 新增两个新特性：

* `Array.protrtype.includes`
* Exponentiation operator（指数运算符）

| Proposal                                                                       | Author           | Champions                               | TC39 meeting notes                                                                                                             | Publication Year |
| ------------------------------------------------------------------------------ | ---------------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | ---------------- |
| [Array.protrtype.includes](https://github.com/tc39/Array.prototype.includes)   | Domenic Denicola | <p>Domenic Denicola<br>Rick Waldron</p> | [November 2015](https://github.com/rwaldron/tc39-notes/blob/master/es7/2015-11/nov-17.md#arrayprototypeincludes)               | 2016             |
| [Exponentiation operator](https://github.com/rwaldron/exponentiation-operator) | Rick Waldron     | Rick Waldron                            | [January 2016](https://github.com/rwaldron/tc39-notes/blob/master/es7/2016-01/2016-01-28.md#5xviii-exponentiation-operator-rw) | 2016             |

### 3.1 Array.prototype.includes

#### 3.1.1 用法

数组的原型上增加了 `includes` 方法，用于判断传入的值在当前数组中是否存在，存在就返回 true，否则返回 false。类似于 `indexOf` 方法，唯一的区别是 `includes` 方法能找到 `NaN`，但是 `indexOf` 方法不行。具体用法如下:

```javascript
Array.prototype.includes(value): boolean
```

#### 3.1.2 用例

常规用法：

```javascript
['a', 'b', 'c'].includes('a');  // true

['a', 'b', 'c'].includes('d');  // false

```

`includes` 方法与 `indexOf` 方法很相似，下面两个表达式是等价的：

```javascript
arr.includes(x)
arr.indexOf(x) >= 0
```

唯一的区别是 `includes` 方法能找到 `NaN`，而 `indexOf` 不行：

```javascript
let numbers = [1, 2, 3, 4, NaN];
console.log(numbers.indexOf(NaN));   // -1
console.log(numbers.includes(NaN));  // true
```

与其他 JavaScript 特性表现一致，`includes` 不会区分 +0 和 -0 ：

```javascript
[-0].includes(+0);  //true
```

### 3.2 Exponentiation operator

指数运算符

#### 3.2.1 用法

增加了新的运算符 `**`，类似于 `Math.pow` 方法，用于进行指数运算。用法如下：

```javascript
x ** y
```

#### 3.2.2 用例

常规用法：

```javascript
let squared = 3 ** 2; // 9

let num = 3;
num **= 2;
console.log(num);     // 9
```

类似于 `Math.pow` 方法：

```javascript
7 ** 2         //49

Math.pow(7, 2) //49
```

## 四、ECMAScript 2017

ECMAScript 2017 新增六个新特性：

* `Object.values` / `Object.entries`
* String padding（字符串填充）
* `Object.getOwnPropertyDescriptors`
* Trailing commas in function parameter lists and calls（尾部逗号）
* Async functions（异步函数）
* Shared memory and atomics（共享内存和原子）

| Proposal                                                                                                           | Author                                     | Champions                                  | TC39 meeting notes                                                                                                       | Publication Year |
| ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ | ---------------- |
| [Object.values / Object.entries](https://github.com/tc39/proposal-object-values-entries)                           | Jordan Harband                             | Jordan Harband                             | [March 2016](https://github.com/rwaldron/tc39-notes/blob/master/es7/2016-03/march-29.md#objectvalues--objectentries)     | 2017             |
| [String padding](https://github.com/tc39/proposal-string-pad-start-end)                                            | Jordan Harband                             | <p>Jordan Harband<br>Rick Waldron</p>      | [May 2016](https://github.com/rwaldron/tc39-notes/blob/master/es7/2016-05/may-25.md#stringprototypepadstartend-jhd)      | 2017             |
| [Object.getOwnPropertyDescriptors](https://github.com/ljharb/proposal-object-getownpropertydescriptors)            | <p>Jordan Harband<br>Andrea Giammarchi</p> | <p>Jordan Harband<br>Andrea Giammarchi</p> | [May 2016](https://github.com/rwaldron/tc39-notes/blob/master/es7/2016-05/may-25.md#objectgetownpropertydescriptors-jhd) | 2017             |
| [Trailing commas in function parameter lists and calls](https://github.com/tc39/proposal-trailing-function-commas) | Jeff Morrison                              | Jeff Morrison                              | [July 2016](https://github.com/rwaldron/tc39-notes/blob/master/es7/2016-07/jul-26.md#9ie-trailing-commas-in-functions)   | 2017             |
| [Async functions](https://github.com/tc39/ecmascript-asyncawait)                                                   | Brian Terlson                              | Brian Terlson                              | [July 2016](https://github.com/rwaldron/tc39-notes/blob/master/es7/2016-07/jul-28.md#10iv-async-functions)               | 2017             |
| [Shared memory and atomics](https://github.com/tc39/ecmascript\_sharedmem)                                         | Lars T Hansen                              | Lars T Hansen                              | [January 2017](https://github.com/rwaldron/tc39-notes/blob/master/es8/2017-03/mar-21.md#10ia-template-literal-updates)   | 2017             |

### 4.1 Object.values

#### 4.1.1 用法

`Object.values`，返回一个指定对象可枚举属性值的数组。用法如下：

```javascript
Object.values(obj)
```

* 若参数是对象并且属性名为数值，则按照数值从小到大遍历，否则默认排序遍历；
* 若参数是数组，则返回该数组；
* 若参数为字符串，则返回由拆分后的每个字符串组成的数组；&#x20;
* 若参数为数值，则返回空数组；&#x20;
* 若参数为布尔值，则返回空数组；

#### 4.1.2 用例

```javascript
let obj1 = { 33: 3, 11: 1, 22: 2 },
    obj2 = { "三": 3, "一": 1, "二": 2 },
    obj3 = "Hello",
    obj4 = 123,
    obj5 = true;

console.log( Object.values(obj1) );     //  [1, 2, 3]
console.log( Object.values(obj2) );     //  [3, 1, 2]
console.log( Object.values(obj3) );     //  ["H", "e", "l", "l", "o"]
console.log( Object.values(obj4) );     //  []
console.log( Object.values(obj5) );     //  []
```

### 4.2 Object.entries

#### 4.2.1 用法

`Object.entries`，返回一个给定对象可枚举 \[key, value] 的数组。用法如下：

```javascript
Object.entries(obj)
```

#### 4.2.2 用例

```javascript
let obj1 = { 33: 3, 11: 1, 22: 2 },
    obj2 = { "三": 3, "一": 1, "二": 2 },
    obj3 = "let",
    obj4 = 123,
    obj5 = true;

console.log( Object.entries(obj1) );     //  [ ["11",1] , ["22",2] , ["33",3] ]
console.log( Object.entries(obj2) );     //  [ ["三",3] , ["一",1] , ["二",2] ]
console.log( Object.entries(obj3) );     //  [ ["0","l"] , ["1","e"] , ["2","t"] ] 
console.log( Object.entries(obj4) );     //  []
console.log( Object.entries(obj5) );     //  []
```

### 4.3 String padding

#### 4.3.1 用法

在 String 的原型上新增了两个方法： `padStart` 和 `padEnd`。作用是对字符串的开头或结尾进行填充，从而使字符串获得给定的长度，并返回填充后的字符串。用法如下：

```javascript
str.padStart(targetLength [, padString])
str.padEnd(targetLength [, padString])
```

有两个参数，其中 `targetLength` 表示生成字符串的总长度，如果给定长度小于当前字符串长度，则保持不变。`padString` 是可选参数，表示用来填充的字符或字符串，默认值是空格。

#### 4.3.2 用例

```javascript
"abc".padStart(6)            // "   abc"  未填写填充字符时候以空格填充
"abc".padStart(6, "3")       // "333abc"
"abc".padStart(6, "12")      // "121abc"
"abc".padStart(6, "123456")  // "123abc" 自动忽略句尾多余的填充字符
"abc".padStart(2, "123456")  // "abc" 原字符串大于要求的数据时候则不发生变化返回原字符串
```

```javascript
"123".padEnd(8)              // "123     "   
"123".padEnd(8, "c")         // "123ccccc"
"123".padEnd(6, "ab")        // "123aba"
"123".padEnd(8, "123456")    // "12312345"  
"123".padEnd(2, "123456")    // "123"
```

### 4.4 Object.getOwnPropertyDescriptors

#### 4.4.1 用法

`Object.getOwnPropertyDescriptors`，返回一指定对象自己所有的属性描述符，并且属性内容只是自身直接定义的，而不是从原型继承而来的。用法如下：

```
Object.getOwnPropertyDescriptors(obj)
```

#### 4.4.2 用例

```javascript
Object.getOwnPropertyDescriptors(true)  // {}  布尔值返回空对象
Object.getOwnPropertyDescriptors(1)     // {}  数值返回空对象

Object.getOwnPropertyDescriptors([1])   // 数组
{ 
  0: {value: 1, writable: true, enumerable: true, configurable: true},
  length: {value: 1, writable: true, enumerable: false, configurable: false}
}

Object.getOwnPropertyDescriptors('a')   // 字符串
{
  0: {value: "a", writable: false, enumerable: true, configurable: false},
  length: {value: 1, writable: false, enumerable: false, configurable: false}
}

const obj = { name: 'hello', test: function() {return;}, get test1() {return 1;} };
Object.getOwnPropertyDescriptors(obj)   // 对象
{
  name: {value: "hello", writable: true, enumerable: true, configurable: true}
  test: {value: ƒ, writable: true, enumerable: true, configurable: true}
  test1: {get: ƒ, set: undefined, enumerable: true, configurable: true}
}
```

### 4.5 Trailing commas in function parameter lists and calls

函数参数列表和调用中尾部的逗号

#### 4.5.1 用法

在函数参数尾部使用逗号时不会再触发错误警告，包括函数声明、定义和调用。

#### 4.5.2 用例

```javascript
let arr = [1, 2, 3, 4,];    // 不报错

func(1, 2, 3,)              // 不报错
```

### 4.6 Async functions

异步函数

#### 4.6.1 用法

使用 `async/await` 语法来定义与执行异步函数，异步函数本身会返回 `Promise`。异步函数的出现避免了回调地狱并且使代码看上去更简单。 需要注意的是，**只有在使用 async 关键字的函数中， await 关键字才能被使用**。

```javascript
async function foo() {};            // 异步函数声明
const foo = async function () {};   // 异步函数表达式
const foo = async () => {};         // 异步箭头函数
```

#### 4.6.2 用例

```javascript
function fetchText() {
  return new Promise((resolve) => { 
    setTimeout(() => { 
      resolve("World");
    }, 2000);
  });
}

async function say1() { 
  const text = await fetchText();
  console.log(`Hello ${text}`);                    // Hello World
}

async function say2() {
  const text = await fetchText();
  return `Hello ${text}`;
}
say2().then((response) => console.log(response));  // Hello World
```

### 4.7 Shared memory and atomics

共享内存和原子

#### 4.7.1 用法

新增了构造函数 `SharedArrayBuffer` 以及拥有许多静态方法的命名空间对象 `Atomic` 。当内存被共享时，多个线程可以并发读、写内存中相同的数据。原子操作可以确保那些被读、写的值都是可预期的，即新的事务是在旧的事务结束之后启动的，旧的事务在结束之前并不会被中断。

`Atomic` 对象类似于 `Math` 对象，拥有许多静态方法，所以我们不能把它当做构造函数。 `Atomic` 对象有如下常用的静态方法：

* `add` / `sub` ：为某个指定的 value 值在某个特定的位置增加或者减去某个值
* `and` / `or` / `xor` ：进行位操作
* `load` ：获取特定位置的值

## 五、ECMAScript 2018

ECMAScript 2018 新增八个新特性：

* Lifting template literal restriction（非转义序列的模板字符串）
* `s` (`dotAll`) flag for regular expressions（正则表达式 dotAll 模式）
* RegExp named capture groups（正则表达式命名捕获组）
* Rest/Spread Properties（Rest/Spread 属性）
* RegExp Lookbehind Assertions（正则表达式反向断言）
* RegExp Unicode Property Escapes（正则表达式 Unicode 转义）
* `Promise.prototype.finally`
* Asynchronous Iteration（异步迭代）

| Proposal                                                                                            | Author                                                 | Champions                                                  | TC39 meeting notes                                                                                                                | Publication Year |
| --------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| [Lifting template literal restriction](https://github.com/tc39/proposal-template-literal-revision)  | Tim Disney                                             | Tim Disney                                                 | [March 2017](https://github.com/rwaldron/tc39-notes/blob/master/es8/2017-03/mar-21.md#10ia-template-literal-updates)              | 2018             |
| [s (dotAll) flag for regular expressions](https://github.com/tc39/proposal-regexp-dotall-flag)      | Mathias Bynens                                         | <p>Brian Terlson<br>Mathias Bynens</p>                     | [November 2017](https://github.com/rwaldron/tc39-notes/blob/master/es8/2017-11/nov-28.md#9ie-regexp-dotall-status-update)         | 2018             |
| [RegExp named capture groups](https://github.com/tc39/proposal-regexp-named-groups)                 | <p>Gorkem Yakin<br>Daniel Ehrenberg</p>                | <p>Daniel Ehrenberg<br>Brian Terlson<br>Mathias Bynens</p> | [November 2017](https://github.com/rwaldron/tc39-notes/blob/master/es8/2017-11/nov-28.md#9if-regexp-named-captures-status-update) | 2018             |
| [Rest/Spread Properties](https://github.com/tc39/proposal-object-rest-spread)                       | Sebastian Markbåge                                     | Sebastian Markbåge                                         | [January 2018](https://github.com/rwaldron/tc39-notes/blob/master/es8/2018-01/jan-23.md#restspread-properties-for-stage-4)        | 2018             |
| [RegExp Lookbehind Assertions](https://github.com/tc39/proposal-regexp-lookbehind)                  | <p>Gorkem Yakin<br>Nozomu Katō<br>Daniel Ehrenberg</p> | <p>Daniel Ehrenberg<br>Mathias Bynens</p>                  | [January 2018](https://github.com/rwaldron/tc39-notes/blob/master/es8/2018-01/jan-23.md#conclusionresolution-16)                  | 2018             |
| [RegExp Unicode Property Escapes](https://github.com/tc39/proposal-regexp-unicode-property-escapes) | Mathias Bynens                                         | <p>Brian Terlson<br>Daniel Ehrenberg<br>Mathias Bynens</p> | [January 2018](https://github.com/rwaldron/tc39-notes/blob/master/es8/2018-01/jan-24.md#conclusionresolution-1)                   | 2018             |
| [Promise.prototype.finally](https://github.com/tc39/proposal-promise-finally)                       | Jordan Harband                                         | Jordan Harband                                             | [January 2018](https://github.com/rwaldron/tc39-notes/blob/master/es8/2018-01/jan-24.md#conclusionresolution-2)                   | 2018             |
| [Asynchronous Iteration](https://github.com/tc39/proposal-async-iteration)                          | Domenic Denicola                                       | Domenic Denicola                                           | [January 2018](https://github.com/rwaldron/tc39-notes/blob/master/es8/2018-01/jan-25.md#conclusionresolution)                     | 2018             |

### 5.1 Lifting template literal restriction

非转义序列的模板字符串

#### 5.1.1 用法

从 ES2016 开始，tagged 模板字符串会对以 `\u`、`\u{}`、`\x` 开头或者 `\` 加数字开头的字符串进行转义。但是如果在模板字符串中出现以上述字符开头的非转义字符的话，会报语法错误。

ES2018 中取消了 tagged 模板中对于转义字符的限制，正常解析的话可以正常获取转义字符的值，否则的话返回 undefined。

#### 5.1.2 用例

```javascript
function latex(str) { 
  return { "cooked": str[0], "raw": str.raw[0] }
} 

latex`\unicode`
// { cooked: undefined, raw: "\unicode"}
```

### 5.2 `s` (`dotAll`) flag for regular expressions

正则表达式 dotAll 模式

#### 5.2.1 用法

我们知道元字符`.`无法匹配   `\u{2048}` `\u{2049}`等换行符。

在 ES2018 中为正则表达式增加了一个新的标志 `s` 用来表示属性 `dotAll`。以使 `.`可以匹配任意字符, 包括换行符。

#### 5.2.2 用例

```javascript
const re = /foo.bar/s;
re.test('foo\nbar');  // true
re.dotAll             // true
re.flags              // 's'
```

### 5.3 RegExp named capture groups

正则表达式命名捕获组

#### 5.3.1 用法

正则表达式中可以通过数字索引来引用()语法捕获的部分匹配结果。在 ES2018 中提供了命名捕获组的功能。我们可以通过`(?<name>...)`语法给捕获组命名。

#### 5.3.2 用例

```javascript
let re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
let result = re.exec('2015-01-02');
// result.groups.year === '2015';
// result.groups.month === '01';
// result.groups.day === '02';
```

### 5.4 Rest/Spread Properties

Rest/Spread 属性

#### 5.4.1 用法

ES2015 引入了 Rest 参数和扩展运算符用于数组。ES2018 为对象解构提供了和数组一样的 Rest 参数和展开操作符。可以使用扩展运算符拷贝一个对象，像是这样 `obj2 = {...obj1}`，但是 **这只是一个对象的浅拷贝**。

#### 5.4.2 用例

```javascript
const obj = { a: 1, b: 2, c: 3};

const { a, ...x } = obj;
// a = 1
// x = { b: 2, c: 3 }

function restParam({ a, ...x }) { }

restParam(obj);
// a = 1
// x = { b: 2, c: 3 }

const obj1 = { a: 1, b: 2, c: 3 };
const obj2 = { ...obj1, z: 26 };
// obj2 is { a: 1, b: 2, c: 3, z: 26 }
```

### 5.5 RegExp Lookbehind Assertions

正则表达式反向断言

#### 5.5.1 用法

正则表达式中支持先行断言（lookahead）。这意味着匹配会发生，但不会有任何捕获，并且断言没有包含在整个匹配字段中。ES2018 引入以相同方式工作但是匹配前面的反向断言（lookbehind）。并且有肯定反向断言和否定反向断言。

#### 5.5.2 用例

```javascript
const reLookbehind = /(?<=\D)\d+/,
      match = reLookbehind.exec('$123.89');

console.log( match[0] );  // 123.89，肯定反向断言

const reLookbehindNeg = /(?<!\D)\d+/,
      match = reLookbehind.exec('$123.89');

console.log( match[0] );  // null，否定反向断言
```

### 5.6 RegExp Unicode Property Escapes

正则表达式 Unicode 转义

#### 5.6.1 用法

到目前为止，在正则表达式中本地访问 Unicode 字符属性是不被允许的。ES2018 添加了 Unicode 属性转义——形式为 `\p{...}` 和 `\P{...}`，在正则表达式中使用标记 `u` (unicode) 设置，在 `\p` 块儿内，可以以键值对的方式设置需要匹配的属性而非具体内容。此特性可以避免使用特定 Unicode 区间来进行内容类型判断，提升可读性和可维护性。

#### 5.6.2 用例

```javascript
const reGreekSymbol = /\p{Script=Greek}/u;
reGreekSymbol.test('π'); // true
```

### 5.7 `Promise.prototype.finally`

#### 5.7.1 用法

一个 Promise 调用链要么成功到达最后一个 `.then()`，要么失败触发 `.catch()`。在某些情况下，你想要在无论 Promise 运行成功还是失败，运行相同的代码。ES2018新增的 `.finally()` 允许你指定最终的逻辑。

#### 5.7.2 用例

```javascript
function doSomething() {
  doSomething1()
  .then(doSomething2)
  .then(doSomething3)
  .catch(err => {
    console.log(err);
  })
  .finally(() => {
    // finish here!
  });
}
```

### 5.8 Asynchronous Iteration

异步迭代

#### 5.8.1 用法

在 `async/await` 的某些时刻，你可能尝试在同步循环中调用异步函数。例如：

```javascript
async function process(array) {
  for (let i of array) {
    await doSomething(i);
  }
}
```

这段代码不会正常运行，下面这段同样也不会：

```javascript
async function process(array) {
  array.forEach(async (i) => {
    await doSomething(i);
  });
}
```

这段代码中，循环本身依旧保持同步，并在在内部异步函数之前全部调用完成。

ES2018 引入异步迭代器（asynchronous iterators），这就像常规迭代器，除了`next()`方法返回一个Promise。因此`await`可以和`for...of`循环一起使用，**以串行的方式运行异步操作**。

#### 5.8.2 用例

```javascript
async function process(array) {
  for await (let i of array) {
    doSomething(i);
  }
}
```

## 六、ECMAScript 2019

[https://medium.freecodecamp.org/whats-new-in-javascript-es2019-8af4390d8494\
\
https://mp.weixin.qq.com/s/FObn78n5SQyLYZpyuOD8qw\
\
https://scq000.github.io/2019/08/12/ES2019%E6%96%B0%E7%89%B9%E6%80%A7%E9%A2%84%E8%A7%88/](https://medium.freecodecamp.org/whats-new-in-javascript-es2019-8af4390d8494https://mp.weixin.qq.com/s/FObn78n5SQyLYZpyuOD8qwhttps:/scq000.github.io/2019/08/12/ES2019%E6%96%B0%E7%89%B9%E6%80%A7%E9%A2%84%E8%A7%88/)

## 七、ECMAScript 2020

## 八、ECMAScript 2021

## 九、ECMAScript 2022

