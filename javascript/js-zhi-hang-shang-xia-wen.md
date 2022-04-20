# JS 执行上下文

## 一、引言

如果你是或者想成为一名 JavaScript 开发者，你必须知道 JavaScript 程序内部是如何执行的。理解执行上下文和执行栈对于理解其他 JavaScript 概念（如变量声明提升，作用域和闭包）至关重要。

网上对于执行上下文的解释通常有**两种：**

* 一种是执行上下文包含：`scope`(作用域)、`variable object`(变量对象)、`this`(this 值)；
* 另外一个种是包含：`lexical environment`(词法环境)、`variable environment`(变量环境)、`this`(this 值)。

其实第一种是 ES3 的规范，经典书籍《JavaScript 高级程序设计》第三版就是这样解释的，也是网上广为流传的一种。第二种是 ES5 的规范，但是后续的版本例如 ES2018 中，执行上下文又有变化了，已经增加了更多的内容了，但是按照第二种解释来理解暂时已经足够。

> 以后看到变量对象、活动对象知道是 ES3 里面的内容，而如果是词法环境、变量环境这种词就是 ES5 以后的内容。

## **二、执行上下文** <a href="#e4-bb-80-e4-b9-88-e6-98-af-e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-ef-bc-9f" id="e4-bb-80-e4-b9-88-e6-98-af-e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-ef-bc-9f"></a>

### 1. 概念

简而言之，**执行上下文是计算和执行 JavaScript 代码的环境的抽象概念**。每当 JavaScript 代码在运行的时候，它都是在执行上下文中运行。

### **2. 类型** <a href="#e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-e7-9a-84-e7-b1-bb-e5-9e-8b" id="e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-e7-9a-84-e7-b1-bb-e5-9e-8b"></a>

JavaScript 中有三种执行上下文类型。

* **全局执行上下文** — 这是默认或者说基础的上下文，任何不在函数内部的代码都在全局上下文中。它会执行两件事：创建一个全局的 window 对象（浏览器的情况下），并且设置 `this` 的值等于这个全局对象。**一个程序中只会有一个全局执行上下文**。
* **函数执行上下文** — 每当一个函数被调用时, 都会为该函数创建一个新的上下文。每个函数都有它自己的执行上下文，不过是在函数被调用时创建的。函数上下文可以有任意多个。每当一个新的执行上下文被创建，它会按定义的顺序执行一系列步骤。
* **Eval 函数执行上下文** — 执行在 `eval` 函数内部的代码也会有它属于自己的执行上下文，但由于 JavaScript 开发者并不经常使用 `eval`，所以在这里不会讨论它。

### **3. 生命周期** <a href="#e6-80-8e-e4-b9-88-e5-88-9b-e5-bb-ba-e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-ef-bc-9f" id="e6-80-8e-e4-b9-88-e5-88-9b-e5-bb-ba-e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-ef-bc-9f"></a>

对于每个执行上下文，我们都可以把它看作有个生命周期，生命周期可以分成两个阶段：

* **创建阶段**
* **执行阶段**

### **4. 创建阶段** <a href="#e5-88-9b-e5-bb-ba-e9-98-b6-e6-ae-b5" id="e5-88-9b-e5-bb-ba-e9-98-b6-e6-ae-b5"></a>

在 JavaScript 代码执行前，执行上下文将经历创建阶段。在创建阶段会发生三件事：

1. **this** 值的决定，即我们所熟知的 **this 绑定**。
2. 创建**词法环境(Lexical Environment)**组件。
3. 创建**变量环境(Variable Environment)**组件。

所以执行上下文在概念上表示如下：

```javascript
ExecutionContext = {
  ThisBinding = <this value>,
  LexicalEnvironment = { ... },
  VariableEnvironment = { ... },
}
```

#### **4.1 this 绑定**

在全局执行上下文中，`this` 的值指向全局对象。(在浏览器中，`this`引用 Window 对象)。

在函数执行上下文中，`this` 的值取决于该函数是如何被调用的。如果它被一个引用对象调用，那么 `this` 会被设置成那个对象，否则 `this` 的值被设置为全局对象或者 `undefined`（在严格模式下）。

例如：

```javascript
let foo = {
  baz: function() {
    console.log(this);
  }
}

foo.baz();   // 'this' 引用 'foo', 因为 'baz' 被对象 'foo' 调用

let bar = foo.baz;
bar();       // 'this' 指向全局 window 对象，因为没有指定引用对象
```

#### **4.2 创建词法环境**

官方的 ES6 文档把词法环境定义为

> **词法环境**是一种规范类型，基于 ECMAScript 代码的词法嵌套结构来定义**标识符**和具体变量和函数的关联。一个词法环境由环境记录器和一个可能的引用外部词法环境的空值组成。

简单来说词法环境是一种持有**标识符—变量**映射的结构（这里的**标识符**指的是变量/函数的名字，而**变量**是对实际对象\[包含函数类型对象]或原始数据的引用）。

现在，在词法环境的内部有两个组件：

1. **环境记录器**是存储变量和函数声明的实际位置。
2. **外部环境的引用**意味着它可以访问其父级词法环境（**作用域**）。

> 外部环境已经跟 ES3 规定的作用域的作用类似。

词法环境有两种类型：

* 在**全局执行上下文**中，是没有外部环境引用的词法环境。全局环境的外部环境引用是 **null**。它拥有内建的 Object/Array/等、在环境记录器内的原型函数（关联全局对象，比如 window 对象）还有任何用户定义的全局变量，并且 `this`的值指向全局对象。
* 在**函数执行上下文**中，函数内部用户定义的变量存储在**环境记录器**中。并且引用的外部环境可能是全局环境，或者任何包含此内部函数的外部函数。

环境记录器也有两种类型：

1. **声明式环境记录器**(Declarative environment record)存储变量、函数和参数。
2. **对象环境记录器**(Object environment record)用来定义出现在**全局上下文**中的变量和函数的关系。

简而言之，

* 在**全局执行上下文**中，环境记录器是对象环境记录器。
* 在**函数执行上下文**中，环境记录器是声明式环境记录器。

> 对于**函数执行上下文**，**声明式环境记录器**还包含了一个传递给函数的 `arguments` 对象（此[对象存储](https://cloud.tencent.com/product/cos?from=10680)索引和参数的映射）和传递给函数的参数的 **length**。

抽象地讲，词法环境在伪代码中看起来像这样：

```javascript
/* 全局执行上下文 */
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
    }
    outer: <null>
  }
}

/* 函数执行上下文 */
FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
    }
    outer: <Global or outer function environment reference>
  }
}
```

#### **4.3 创建变量环境**

它**同样是一个词法环境**，其环境记录器持有**变量声明语句**在执行上下文中创建的绑定关系。

> 变量环境也是一个词法环境，所以它有着上面词法环境定义的所有属性。

**在 ES6 中，词法环境组件和变量环境的一个不同就是前者被用来存储函数声明和变量（`let` 和 `const`）绑定，而后者只用来存储 `var` 变量绑定。**

### **5. 执行阶段** <a href="#e6-89-a7-e8-a1-8c-e9-98-b6-e6-ae-b5" id="e6-89-a7-e8-a1-8c-e9-98-b6-e6-ae-b5"></a>

在此阶段，完成对所有这些变量的分配，最后执行代码。至于执行的过程，就交给 JavaScript 引擎，在这里不做过多介绍。

> 在执行阶段，如果 JavaScript 引擎不能在 let 声明的实际位置找到变量的值，它会被赋值为 `undefined`。

### 6. 示例

我们看点样例代码来理解上面的概念：

```javascript
let a = 20;
const b = 30;
var c;

function multiply(e, f) {
 var g = 20;
 return e * f * g;
}

c = multiply(20, 30);
```

执行上下文看起来像这样：

```javascript
GlobalExectionContext = {

  ThisBinding: <Global Object>,

  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
      a: < uninitialized >,
      b: < uninitialized >,
      multiply: < func >
    }
    outer: <null>
  },

  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
      c: undefined,
    }
    outer: <null>
  }
}

FunctionExectionContext = {
  ThisBinding: <Global Object>,

  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
      Arguments: {0: 20, 1: 30, length: 2},
    },
    outer: <GlobalLexicalEnvironment>
  },

  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
      g: undefined
    },
    outer: <GlobalLexicalEnvironment>
  }
}
```

> 只有遇到调用函数 `multiply` 时，函数执行上下文才会被创建。

可能你已经注意到 `let` 和 `const` 定义的变量并没有关联任何值，但 `var` 定义的变量被设成了 `undefined`。

这是因为在创建阶段时，引擎检查代码找出变量和函数声明，虽然函数声明完全存储在环境中，但是变量最初设置为 `undefined`（`var` 情况下），或者未初始化（`let` 和 `const` 情况下）。

这就是为什么你可以在声明之前访问 `var` 定义的变量（虽然是 `undefined`），但是在声明之前访问 `let` 和 `const` 的变量会得到一个引用错误。这就是我们说的**变量声明提升**。

## **三、执行栈** <a href="#e6-89-a7-e8-a1-8c-e6-a0-88" id="e6-89-a7-e8-a1-8c-e6-a0-88"></a>

### 1. 概念

执行栈，也就是在其它编程语言中所说的“调用栈”，是一种拥有 LIFO（后进先出）的数据结构，被用来存储代码运行时创建的所有执行上下文。

当 JavaScript 引擎第一次遇到你的脚本时，它会创建一个全局的执行上下文并且压入当前执行栈。**每当引擎遇到一个函数调用，它会为该函数创建一个新的执行上下文并压入栈的顶部**。

**引擎会执行处于栈顶的执行上下文的函数。当该函数执行结束时，执行上下文从栈中弹出**，控制流程到达当前栈中的下一个上下文。

### 2. 示例

让我们通过下面的代码示例来理解：

```javascript
let a = 'Hello World!';

function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}

function second() {
  console.log('Inside second function');
}

first();
console.log('Inside Global Execution Context');
```

![执行栈示例](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/20/165f539572076fe3\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

执行栈的调用过程如下：

* 当上述代码在浏览器加载时，JavaScript 引擎创建了一个全局执行上下文并把它压入当前执行栈；
* 当遇到 `first()` 函数调用时，JavaScript 引擎为该函数创建一个新的执行上下文并把它压入当前执行栈的顶部；
* 当从 `first()` 函数内部调用 `second()` 函数时，JavaScript 引擎为 `second()` 函数创建了一个新的执行上下文并把它压入当前执行栈的顶部；
* 当 `second()` 函数执行完毕，它的执行上下文会从当前栈弹出，并且控制流程到达下一个执行上下文，即 `first()` 函数的执行上下文；
* 当 `first()` 执行完毕，它的执行上下文从栈弹出，控制流程到达全局执行上下文；
* 一旦所有代码执行完毕，JavaScript 引擎从当前栈中移除全局执行上下文。

## 四、参考 <a href="#e6-80-8e-e4-b9-88-e5-88-9b-e5-bb-ba-e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-ef-bc-9f" id="e6-80-8e-e4-b9-88-e5-88-9b-e5-bb-ba-e6-89-a7-e8-a1-8c-e4-b8-8a-e4-b8-8b-e6-96-87-ef-bc-9f"></a>

* [深入理解 JavaScript 执行上下文和执行栈](https://cloud.tencent.com/developer/article/1604839)
* [Understanding Execution Context and Execution Stack in Javascript](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)
