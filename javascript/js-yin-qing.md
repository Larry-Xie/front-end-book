# JS 引擎

## 一、引言

我们都听说过 JS 引擎这个东西，但是相信对于大多数人来说，这是一个黑盒子，对其了解不深，反正我就把 JS 代码交给你了，你自己机灵点，执行好了再告诉我结果。那么对于 JS 引擎这个黑盒子，这篇文章也不会太过深入，只是可以让你对 JS 引擎这个东西有一个基本的了解。

## 二、JS 引擎

> 百度百科的话：JavaScript 引擎是一个专门处理 JavaScript 脚本的虚拟机，一般会附带在网页浏览器之中。

JS 引擎，即 **JavaScript 虚拟机**， 我们可以简单地把它理解成是一个 **翻译程序**，**它将人类能够理解的编程语言 JavaScript 翻译成机器能够理解的机器语言**。

现在市面上有很多种 JS 引擎，诸如 SpiderMonkey、Rhino、TraceMonkey、JaegerMonkey、JavaScriptCore、V8 等。我们最熟悉的听到最多的当然就是 **V8** 了，V8 是由谷歌开发的开源项目，是当下使用最广泛的 JavaScript 虚拟机，全球有超过 25 亿台安卓设备，而这些设备中都使用了 Chrome 浏览器，所以我们写的 JavaScript 应用，大都跑在 V8 上。

**V8 的主要功能，就是结合 JavaScript 语言的特性和本质来编译执行它**。如果我们了解 JavaScript 这门语言的一些基本特性和设计思想，那么我们去了解 V8 到底是怎么执行 JavaScript 代码的也将会变得简单一些。

## 三、语言类型

每种编程语言都具有内建的数据类型，它们的数据类型有很多不同之处，使用方式也很不一样。可以根据 **数据类型是否需要在使用前确认** 划分为静态语言和动态语言，根据 **是否支持隐式类型转换** 划分为**弱类型语言和强类型语言**。

* **在使用之前就需要确认其变量数据类型的称为静态语言**
* **在运行过程中需要检查变量数据类型的语言称为动态语言**
* **支持变量进行隐式类型转换的语言称为弱类型语言**
* **不支持变量进行隐式类型转换的语言称为强类型语言**

比如 C#、Java 就是强类型的静态语言；C、C++ 就是弱类型的静态语言；Python、Ruby 就是强类型的动态语言；JavaScript、PHP 就是弱类型的动态语言。

**JavaScript 是一种弱类型的动态语言**：

* **弱类型**，意味着不需要告诉 JS 引擎变量是什么数据类型，它会在运行代码的时候自己检查计算出来，这一层计算也意味着要比强类型的语言多消耗一些性能。
* **动态**，意味着可以用一个变量来保存不同类型的数据。

## 四、编译器和解释器

机器是不能够直接理解我们所写的代码的，所以 **要在执行程序之前把代码转译成机器能读懂的机器语言**。那么按照语言的执行流程，又可以把语言划分为 **编译型语言** 和 **解释型语言**。

### **1. 编译型语言**

编译型语言在程序执行之前，需要经过 **编译器** 的编译过程，并且编译之后会直接保留机器能读懂的二进制文件，这样在每次运行程序的时候，都可以直接运行该二进制文件，就不需要再次重新编译了。比如 C/C++、GO 等都是编译型语言。

* 在编译型语言的编译过程中，编译器首先会依次对源代码进行 **词法分析**、**语法分析**，**生成抽象语法树（AST）**，然后是 **优化代码**，最后再 **生成处理器能够理解的机器码**。
* 如果编译成功，将会生成一个可执行的文件。
* 但如果编译过程发生了语法或者其他的错误，那么编译器就会抛出异常，最后的二进制文件也不会生成成功。

### **2. 解释型语言**

解释型语言编写的程序，在每次运行时都需要通过 **解释器** 对程序进行动态解释和执行。比如 Python、JavaScript 等都属于解释型语言。

* 在解释型语言的解释过程中，同样解释器也会对源代码进行 **词法分析**、**语法分析**，**生成抽象语法树（AST）**，不过它会 **再基于抽象语法树生成字节码**，最后 **再根据字节码来执行程序、输出结果**。

### 3. 编译型 vs 解释型

编译型语言和解释器语言代码执行的具体流程如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a7680519fd0e43c4bb2ee3ffff19d358\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

两者的执行流程如下：

* 在编译型语言的编译过程中，编译器首先会依次对源代码进行词法分析、语法分析，生成抽象语法树（AST），然后优化代码，最后再生成处理器能够理解的机器码。如果编译成功，将会生成一个可执行的文件。但如果编译过程发生了语法或者其他的错误，那么编译器就会抛出异常，最后的二进制文件也不会生成成功。
* 在解释型语言的解释过程中，同样解释器也会对源代码进行词法分析、语法分析，并生成抽象语法树（AST），不过它会再基于抽象语法树生成字节码，最后再根据字节码来执行程序、输出结果。

### **4. 即时编译（JIT）**

在 V8 出现之前，所有的 JavaScript 虚拟机所采用的都是 **解释执行** 的方式，这是 JavaScript 执行速度过慢的一个主要原因。而 V8 率先引入了 **即时编译（JIT）的双轮驱动的设计**，这是一种权衡策略，**混合编译执行和解释执行这两种手段**，给 JavaScript 的执行速度带来了极大的提升。

**即时编译（JIT）是一种使用字节码配合解释器和编译器执行代码的技术**，在 V8 里面，解释器在解释执行字节码的同时，会进行代码信息的收集，当它发现某一部分代码变热（一段代码被执行多次）了之后，编译器便会把热点字节码转换为机器码，并且把转换后的机器码保存起来，以备下次使用。

## 五、V8 执行 JS 的过程

V8 执行一段代码的过程如下流程图所示：

![js 执行过程](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ebb92461d914be68a53962d02306cfb\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

V8 在执行过程用到了**解释器和编译器。** 其执行过程如下：

1. Parse 阶段：V8 引擎将 JS 代码转换成 AST（抽象语法树）；
2. Ignition 阶段：解释器将 AST 转换为字节码，解析执行字节码也会为下一个阶段优化编译提供需要的信息；
3. TurboFan 阶段：编译器利用上个阶段收集的信息，将字节码优化为可以执行的机器码；
4. Orinoco 阶段：垃圾回收阶段，将程序中不再使用的内存空间进行回收。

这里前三个步骤是 JavaScript 的执行过程，最后一步是垃圾回收的过程。下面就先来看看 V8 执行  JavaScript 的过程。

### **1. 生成抽象语法树**

这个过程就是将源代码转换为**抽象语法树（AST）**，并生成**执行上下文**，执行上下文就是代码在执行过程中的环境信息。

将 JS 代码解析成 AST 主要分为两个阶段：

1. **词法分析**：这个阶段会将源代码拆成最小的、不可再分的词法单元，称为 token。比如代码 var a = 1；通常会被分解成 var 、a、=、1、; 这五个词法单元。代码中的空格在 JavaScript 中是直接忽略的，简单来说就是**将 JavaScript 代码解析成一个个令牌（Token）**。
2. **语法分析**：这个过程是将上一步生成的 token 数据，根据语法规则转为 AST。如果源码符合语法规则，这一步就会顺利完成。如果源码存在语法错误，这一步就会终止，并抛出一个语法错误，简单来说就是**将令牌组装成一棵抽象的语法树（AST）**。

通过词法分析会对代码逐个字符进行解析，生成类似下面结构的令牌（Token），这些令牌类型各不相同，有关键字、标识符、符号、数字等。代码 var a = 1；会转化为下面这样的令牌：

```typescript
Keyword(var)
Identifier(name)
Punctuator(=)
Number(1)
```

语法分析阶段会用令牌生成一棵抽象语法树，生成树的过程中会去除不必要的符号令牌，然后按照语法规则来生成。下面来看两段代码：

```javascript
// 第一段代码
var a = 1;
// 第二段代码
function sum (a,b) {
  return a + b;
}
```

将这两段代码分别转换成 AST 抽象语法树之后返回的 JSON 如下：

* 第一段代码，编译后的结果：

```javascript
{
  "type": "Program",
  "start": 0,
  "end": 10,
  "body": [
    {
      "type": "VariableDeclaration",
      "start": 0,
      "end": 10,
      "declarations": [
        {
          "type": "VariableDeclarator",
          "start": 4,
          "end": 9,
          "id": {
            "type": "Identifier",
            "start": 4,
            "end": 5,
            "name": "a"
          },
          "init": {
            "type": "Literal",
            "start": 8,
            "end": 9,
            "value": 1,
            "raw": "1"
          }
        }
      ],
      "kind": "var"
    }
  ],
  "sourceType": "module"
}
```

它的样子大致如下：

![生成 AST](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4d54015e4f3e4227893b23f1c0c750cf\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

* 第二段代码，编译出来的结果：

```javascript
{
  "type": "Program",
  "start": 0,
  "end": 38,
  "body": [
    {
      "type": "FunctionDeclaration",
      "start": 0,
      "end": 38,
      "id": {
        "type": "Identifier",
        "start": 9,
        "end": 12,
        "name": "sum"
      },
      "expression": false,
      "generator": false,
      "async": false,
      "params": [
        {
          "type": "Identifier",
          "start": 14,
          "end": 15,
          "name": "a"
        },
        {
          "type": "Identifier",
          "start": 16,
          "end": 17,
          "name": "b"
        }
      ],
      "body": {
        "type": "BlockStatement",
        "start": 19,
        "end": 38,
        "body": [
          {
            "type": "ReturnStatement",
            "start": 23,
            "end": 36,
            "argument": {
              "type": "BinaryExpression",
              "start": 30,
              "end": 35,
              "left": {
                "type": "Identifier",
                "start": 30,
                "end": 31,
                "name": "a"
              },
              "operator": "+",
              "right": {
                "type": "Identifier",
                "start": 34,
                "end": 35,
                "name": "b"
              }
            }
          }
        ]
      }
    }
  ],
  "sourceType": "module"
}
```

它的样子大致如下：

![生成 AST](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f00d5ce491434c8f8528ce6c0a3bde46\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

可以看到，AST 只是源代码语法结构的一种抽象的表示形式，计算机也不会去直接去识别 JS 代码，转换成抽象语法树也只是识别这一过程中的第一步。AST 的结构和代码的结构非常相似，其实也可以把 AST 看成代码的结构化的表示，编译器或者解释器后续的工作都需要依赖于 AST。

**AST 的应用场景：**

AST 是一种很重要的数据结构，很多地方用到了AST。比如在 Babel 中，Babel 是一个代码转码器，可以将 ES6 代码转为 ES5 代码。Babel 的工作原理就是先将 ES6 源码转换为 AST，然后再将 ES6 语法的 AST 转换为 ES5 语法的 AST，最后利用 ES5 的 AST 生成 JavaScript 源代码。

除了 Babel 之外，ESLint 也使用到了 AST。ESLint 是一个用来检查 JavaScript 编写规范的插件，其检测流程也是需要将源码转换为 AST，然后再利用 AST 来检查代码规范化的问题。

除了上述应用场景，AST 的应用场景还有很多：

* JS 反编译，语法解析；
* 代码高亮；
* 关键字匹配；
* 代码压缩。

### **2. 生成字节码**

有了 抽象语法树 AST 和执行上下文后，就轮到解释器就登场了，它会根据 AST 生成字节码，并解释执行字节码。

在 V8 的早期版本中，是通过 AST 直接转换成机器码的。将 AST 直接转换为机器码会存在一些问题：

* 直接转换会带来内存占用过大的问题，因为将抽象语法树全部生成了机器码，而机器码相比字节码占用的内存多了很多；
* 某些 JavaScript 使用场景使用解释器更为合适，解析成字节码，有些代码没必要生成机器码，进而尽可能减少了占用内存过大的问题。

为了解决内存占用问题，就在 V8 引擎中引入了字节码。那什么是字节码呢？为什么引入字节码就能解决内存占用问题呢？

**字节码就是介于 AST 和机器码之间的一种代码。** 需要将其转换成机器码后才能执行，字节码是对机器码的一个抽象描述，相对于机器码而言，它的代码量更小，从而可以减少内存消耗。解释器除了可以快速生成没有优化的字节码外，还可以执行部分字节码。

### **3. 生成机器码**

生成字节码之后，就进入执行阶段了，实际上，这一步就是**将字节码生成机器码**。 ​

一般情况下，如果字节码是第一次执行，那么解释器就会逐条解释执行。在执行字节码过程中，如果发现有**热代码**（重复执行的代码，运行次数超过某个阈值就被标记为热代码），那么后台的编译器就会把该段热点的字节码编译为高效的机器码，然后当再次执行这段被优化的代码时，只需要执行编译后的机器码即可，这样提升了代码的执行效率。

字节码配合解释器和编译器的技术就是 **即时编译（JIT）**。在 V8 中就是指解释器在解释执行字节码的同时，收集代码信息，当它发现某一部分代码变热了之后，编译器便闪亮登场，把热点的字节码转换为机器码，并把转换后的机器码保存起来，以备下次使用。

因为 V8 引擎是多线程的，编译器的编译线程和生成字节码不会在同一个线程上，这样可以和解释器相互配合着使用，不受另一方的影响。下面是JIT技术的工作机制：

![机器码](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/df45f4316a2f440c90b532719d1ca5a6\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

解释器在得到 AST 之后，会按需进行解释和执行。也就是说如果某个函数没有被调用，则不会去解释执行它。在这个过程中解释器会将一些重复可优化的操作收集起来生成分析数据，然后将生成的字节码和分析数据传给编译器，编译器会依据分析数据来生成高度优化的机器码。 ​

优化后的机器码的作用和缓存很类似，当解释器再次遇到相同的内容时，就可以直接执行优化后的机器码。当然优化后的代码有时可能会无法运行（比如函数参数类型改变），那么会再次反优化为字节码交给解释器。 ​

整个过程如下图所示：

![V8 全流程](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6df0e400bc574cd9a072af7386d4c39c\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 4. 执行过程优化

如果 JavaScript 代码在执行前都要完全经过解析才能执行，那可能会面临以下问题：

* 代码执行时间变长：一次性解析所有代码会增加代码的运行时间。
* 消耗更多内存：解析完的 AST 以及根据 AST 编译后的字节码都会存放在内存中，会占用更多内存空间。
* 占用磁盘空间：编译后的代码会缓存在磁盘上，占用磁盘空间。

所以，V8 引擎使用了**延迟解析**：在解析过程中，对于不是立即执行的函数，只进行预解析；只有当函数调用时，才对函数进行全量解析。 ​

进行预解析时，只验证函数语法是否有效、解析函数声明、确定函数作用域，不生成 AST，而实现预解析的，就是 Pre-Parser 解析器。 ​

以下面代码为例：

```typescript
function sum(a, b) {
    return a + b;
}
const a = 666;
const c = 996;
sum(1, 1);
```

V8 解析器是从上往下解析代码的，当解析器遇到函数声明 sum 时，发现它不是立即执行，所以会用 Pre-Parser 解析器对其预解析，过程中只会解析函数声明，不会解析函数内部代码，不会为函数内部代码生成 AST。 ​

之后解释器会把 AST 编译为字节码并执行，解释器会按照自上而下的顺序执行代码，先执行 const a = 666;  和 const c = 996; ，然后执行函数调用 sum(1, 1) ，这时 Parser 解析器才会继续解析函数内的代码、生成 AST，再交给解释器编译执行。

## 六、参考

* [我们常说的 JS 引擎是什么](https://juejin.cn/post/6977006929918836750)
* [12张图带你看看 V8 是如何执行和回收 JavaScript 代码](https://juejin.cn/post/7002763440800399391)