# WebAssembly

## 一、引言

WebAssembly 是近几年出现的一门新技术，已经被 W3C 标准化了，且有实际的应用。

WebAssembly 或者 wasm 是一个可移植、体积小、加载快并且兼容 Web 的全新格式。

我们通过一个简单的例子来看看 WebAssembly 到底是什么。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/7/166ed3c3acfb3aad\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

上图的左侧是用 C++ 实现的求递归的函数。中间是十六进制的 Binary Code。右侧是指令文本。中间的十六进制的 Binary Code 就是 WebAssembly。

我们可以看到，其可写性和可读性差到无法想象。那是因为 WebAssembly 不是用手**一行一行撸**的代码，WebAssembly 是一个**编译目标**。什么是编译目标？当我们写 TypeScript 的时候，Webpack 最后打包生成的 JavaScript 文件就是编译目标。上图的 Binary 就是左侧的 C++ 代码经过编译器编译之后的结果。

## 二、WebAssembly 由来

### 1. 性能瓶颈

在业务需求越来越复杂的现在，前端的开发逻辑越来越复杂，相应的代码量随之变的越来越多。相应的，整个项目的起步的时间越来越长。在性能不好的电脑上，启动一个前端的项目甚至要花上十多秒。这些其实还好，说明前端越来越受到重视，越来越多的人开始进行前端的开发。

但是除了逻辑复杂、代码量大，还有另一个原因是 JavaScript 这门语言本身的缺陷，JavaScript 没有静态变量类型。这门解释型编程语言的作者 Brendan Eich，仓促的创造了这门如今被广泛使用的语言，以至于JavaScript 的发展史甚至在某种层面上变成了填坑史。为什么说没有静态类型会降低效率。这会涉及到一些 JavaScript 引擎的一些知识。

### 2. 动态语言之踵

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/7/166ed498c346cec4\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

这是 Microsoft Edge 浏览器的 JavaScript 引擎 ChakraCore 的结构。我们来看一看我们的 JavaScript 代码在引擎中会经历什么。

* JavaScript 文件会被下载下来。
* 然后进入 Parser，Parser 会把代码转化成 AST（抽象语法树）.
* 然后根据抽象语法树，Bytecode Compiler 字节码编译器会生成引擎能够直接阅读、执行的字节码。
* 字节码进入翻译器，将字节码一行一行的翻译成效率十分高的 Machine Code.

但其实我们平时写的代码有很多可以优化的地方，如多次执行同一个函数，那么可以将这个函数生成的 Machine Code 标记可优化，然后打包送到 JIT Compiler（Just-In-Time），下次再执行这个函数的时候，就不需要经过 Parser-Compiler-Interpreter 这个过程，可以直接执行这份准备好的 Machine Code，大大提高的代码的执行效率。

但是上述的 JIT 优化只能针对静态类型的变量，如我们要优化的函数，它只有两个参数，每个参数的类型是确定的，而 JavaScript 却是一门动态类型的语言，这也意味着，函数在执行过程中，可能类型会动态变化，参数可能变成三个，第一个参数的类型可能从对象变为数组，这就会导致 JIT 失效，需要重新进行 Parser-Compiler-Interpreter-Execuation，而 Parser-Compiler 这两步是整个代码执行过程中最耗费时间的两步，这也是为什么 JavaScript 语言背景下，Web 无法执行一些高性能应用，如大型游戏、视频剪辑等。

### 3. 静态语言优化

通过上面的说明了解到，其实 JS 执行慢的一个主要原因是因为其动态语言的特性，导致 JIT 失效，所以如果我们能够为 JS 引入静态特性，那么可以保持有效的 JIT，势必会加快 JS 的执行速度，这个时候 WebAssembly 的前身，asm.js 诞生了。

asm.js 只提供两种数据类型：

* 32 位带符号整数
* 64 位带符号浮点数

其他类似如字符串、布尔值或对象都是以数值的形式保存在内存中，通过 TypedArray 调用。

asm.js 是一个 Javascript 的严格子集，合理合法的 asm.js 代码一定是合理合法的 JavaScript 代码，但是反之就不成立。同 WebAssembly 一样，asm.js 不是用来给各位用手**一行一行撸**的代码，asm.js 是一个**编译目标**。它的可读性、可读性虽然比 WebAssembly 好，但是对于开发者来说，仍然是无法接受的。

asm.js 强制静态类型，举个例子。

```
function asmJs() {
    'use asm';
    
    let myInt = 0 | 0;
    let myDouble = +1.1;
}
```

> asm.js 要求变量的类型在运行时确定且不可改变，且去除了 JavaScript 拥有的垃圾回收机制，需要开发者手动管理内存。这样 JS 引擎就可以基于 asm.js 的代码进行大量的 JIT 优化，据统计 asm.js 在浏览器里面的运行速度，大约是原生代码（机器码）的 50% 左右。

### 4. WebAssembly 出现

但是不管 asm.js 再怎么静态化，干掉一些需要耗时的上层抽象（垃圾收集等），也还是属于 JavaScript 的范畴，代码执行也需要 Parser-Compiler 这两个过程，而这两个过程也是代码执行中最耗时的。

所以在 2015 年，我们迎来了 WebAssembly。WebAssembly 是经过编译器编译之后的代码，体积小、起步快。WebAssembly 是可以直接和 Machine Code 打交道的汇编语言，干掉了 Parser-Compiler 的过程。在语法上完全脱离 JavaScript，同时具有沙盒化的执行环境。WebAssembly 同样的强制静态类型，是 C/C++/Rust 的编译目标。能够进行最大限度的 JIT 优化，使得 WebAssembly 的速度能够无限逼近 C/C++ 等原生代码。

相当于下面的过程：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ce0b1c01d7e4129a2e974ac94baf2f6\~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

## 三、WebAssembly 详解

### 1. WebAssembly 位置

我们可以通过一张图来直观了解 WebAssembly 在 Web 中的位置：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/629f7bdc3cc3466886bb7d223b19e75d\~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

WebAssembly（也称为 WASM），是一种可在 Web 中运行的全新语言格式，同时兼具体积小、性能高、可移植性强等特点，在底层上类似 Web 中的 JavaScript，同时也是 W3C 承认的 Web 中的第 4 门语言。

为什么说在底层上类似 JavaScript，主要有以下几个理由：

* 和 JavaScript 在同一个层次执行：JS Engine，如 Chrome 的 V8
* 和 JavaScript 一样可以操作各种 Web API

同时 WASM 也可以运行在 Node.js 或其他 WASM Runtime 中。

### 2. WABT

实际上 WASM 是一堆可以直接执行二进制格式，但是为了易于在文本编辑器或开发者工具里面展示，WASM 也设计了一种 “中间态” 的[文本格式](https://link.juejin.cn/?target=https%3A%2F%2Fwebassembly.github.io%2Fspec%2Fcore%2Ftext%2Findex.html)，以 `.wat` 或 `.wast` 为扩展命名，然后通过 [wabt](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FWebAssembly%2Fwabt) 等工具，将文本格式下的 WASM 转为二进制格式的可执行代码，以 `.wasm` 为扩展的格式。

来看一段 WASM 文本格式下的模块代码：

```
(module

  (func $i (import "imports" "imported_func") (param i32))

  (func (export "exported_func")

    i32.const 42

    call $i

  )

)
```

上述代码逻辑如下：

* 首先定义了一个 WASM 模块，然后从一个 `imports` JS 模块导入了一个函数 `imported_func` ，将其命名为 `$i` ，接收参数 `i32`
* 然后导出一个名为 `exported_func` 的函数，可以从 Web App，如 JS 中导入这个函数使用
* 接着为参数 `i32` 传入 42，然后调用函数 `$i`

我们通过 wabt 将上述文本格式转为二进制代码：

* 将上述代码复制到一个新建的，名为 `simple.wat` 的文件中保存
* 使用 [wabt](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FWebAssembly%2Fwabt) 进行编译转换

当你安装好 wabt 之后，运行如下命令进行编译：

```
wat2wasm simple.wat -o simple.wasm
```

虽然转换成了二进制，但是无法在文本编辑器中查看其内容，为了查看二进制的内容，我们可以在编译时加上 `-v` 选项，让内容在命令行输出：

```
wat2wasm simple.wat -v
```

输出结果如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1541619506104e90b1c8e7d451b0b966\~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

可以看到，WebAssembly 其实是二进制格式的代码，即使其提供了稍为易读的文本格式，也很难真正用于实际的编码，更别提开发效率了。

### 3. AssemblyScript

因为上述的二进制和文本格式都不适合编码，所以不适合将 WASM 作为一门可正常开发的语言。

为了突破这个限制，[AssemblyScript](https://link.juejin.cn/?target=https%3A%2F%2Fwww.assemblyscript.org%2F) 走到台前，AssemblyScript 是 TypeScript 的一种变体，为 JavaScript 添加了 [**WebAssembly 类型**](https://link.juejin.cn/?target=https%3A%2F%2Fwww.assemblyscript.org%2Ftypes.html%23type-rules) **，** 可以使用 [Binaryen](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FWebAssembly%2Fbinaryen) 将其编译成 WebAssembly。

Binaryen 会前置将 AssemblyScript 静态编译成强类型的 WebAssembly 二进制，然后才会交给 JS 引擎去执行，所以说虽然 AssemblyScript 带来了一层抽象，但是实际用于生产的代码依然是 WebAssembly，保有 WebAssembly 的性能优势。AssemblyScript 被设计的和 TypeScript 非常相似，提供了一组内建的函数可以直接操作 WebAssembly 以及编译器的特性.

通过基础的类型、内建库、标准库和扩展库，AssemblyScript 基本上构造了 JavaScript 所拥有的的全部特性，同时 AssemblyScript 提供了类似 TypeScript 的语法，在写法上严格遵循强类型静态语言的规范。

AssemblyScript 为我们打开了一扇新的大门，可以以 TS 形式的语法，遵循静态强类型的规范进行高效编码，同时又能够便捷的操作 WebAssembly/编译器相关的 API，代码写完之后，通过 Binaryen 编译器将其编译为 WASM 二进制，然后获取到 WASM 的执行性能。

得益于 AssemblyScript 兼具灵活性与性能，目前使用 AssemblyScript 构建的应用生态已经初具繁荣，目前在区块链、构建工具、编辑器、模拟器、游戏、图形编辑工具、库、IoT、测试工具等方面都有大量使用。

### 4. Emscripten

虽然 AssemblyScript 的出现极大的改善了 WebAssembly 在高效率编码方面的缺陷，但是作为一门新的编程语言，其最大的劣势就是生态、开发者与积累。

WebAssembly 的设计者显然在设计上同时考虑到了各种完善的情况，既然 WebAssembly 是一种二进制格式，那么其就可以作为其他语言的编译目标，如果能够构建一种编译器，能够将已有的、成熟的、且兼具海量的开发者和强大的生态的语言编译到 WebAssembly 使用，那么相当于可以直接复用这个语言多年的积累，并用它们来完善 WebAssembly 生态，将它们运行在 Web、Node.js 中。

幸运的是，针对 C/C++ 已经有 [Emscripten](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Femscripten-core%2Femscripten) 这样优秀的编译器存在了。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/671768b6f6e1489bbfb1bf84466e6179\~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

可以通过下面这张图直观的阐述 Emscripten 在开发链路中的地位：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8d136ed0580042dfa06b87e4012f6efa\~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

即将 C/C++ 的代码（或者 Rust/Go 等）编译成 WASM，然后通过 JS 胶水代码将 WASM 跑在浏览器中（或 Node.js）的 runtime，如 ffmpeg 这个使用 C 编写音视频转码工具，通过 Emscripten 编译器编译到 Web 中使用，可直接在浏览器前端转码音视频。

> 上述的 JS “Gule” 代码是必须的，因为如果需要将 C/C++ 编译到 WASM，还能在浏览器中执行，就得实现映射到 C/C++ 相关操作的 Web API，这样才能保证执行有效，这些胶水代码目前包含一些比较流行的 C/C++ 库，如 [SDL](https://link.juejin.cn/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FSimple\_DirectMedia\_Layer)、[OpenGL](https://link.juejin.cn/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FOpenGL)、[OpenAL](https://link.juejin.cn/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FOpenAL)、以及 [POSIX](https://link.juejin.cn/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FPOSIX) 的一部分 API。

目前使用 WebAssembly 最大的场景也是这种将 C/C++ 模块编译到 WASM 的方式，比较有名的例子有 [Unreal Engine 4](https://link.juejin.cn/?target=https%3A%2F%2Fblog.mozilla.org%2Fblog%2F2014%2F03%2F12%2Fmozilla-and-epic-preview-unreal-engine-4-running-in-firefox%2F)、[Unity](https://link.juejin.cn/?target=https%3A%2F%2Fblogs.unity3d.com%2F2018%2F08%2F15%2Fwebassembly-is-here%2F) 之类的大型库或应用。

### 5. WebAssembly vs JavaScript

> WebAssembly 会取代 JavaScript 吗？

答案是否定的，请看下图。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/8/166f12bfd8a03f40\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

根据上面的层层阐述，实际上 WASM 的设计初衷就可以梳理为以下几点：

* 最大程度的复用现有的底层语言生态，如 C/C++ 在游戏开发、编译器设计等方面的积淀
* 在 Web、Node.js 或其他 WASM runtime 获得近乎于原生的性能，也就是可以让浏览器也能跑大型游戏、图像剪辑等应用
* 还有最大程度的兼容 Web、保证安全
* 同时在开发上（如果需要开发）易于读写和可调试，这一点 AssemblyScript 走得更远

所以从初衷出发，WebAssembly 的作用更适合下面这张图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/767f978dded4443b803423751029ee08\~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

WASM 桥接各种系统编程语言的生态，进一步补齐了 Web 开发生态之外，还为 JS 提供性能的补充，正是 Web 发展至今所缺失的重要的一块版图。

## 四、WebAssembly 使用

WebAssembly 给 Web 平台添加了两块内容：一种二进制格式代码，以及一系列可用于加载和执行二进制代码的 API。

WebAssembly 目前处于一个萌芽的节点，之后肯定会涌现出很多工具，而目前有四个主要的入口：

* 使用 EMScripten 来移植 C/C++ 应用
* 在汇编层面直接编写和生成 WebAssembly 代码
* 编写 Rust 应用，然后将 WebAssembly 作为它的输出
* 使用 AssemblyScript，它是一门类似 TypeScript 的语言，能够编译成 WebAssembly 二进制

### 1. 移植 C/C++ 应用

虽然也有一些其他工具如：

* [WasmFiddle](https://link.juejin.cn/?target=https%3A%2F%2Fwasdk.github.io%2FWasmFiddle%2F)
* [WasmFiddle++](https://link.juejin.cn/?target=https%3A%2F%2Fanonyco.github.io%2FWasmFiddlePlusPlus%2F)
* [WasmExplorer](https://link.juejin.cn/?target=https%3A%2F%2Fmbebenita.github.io%2FWasmExplorer%2F)

但是这些工具都缺乏 EMScripten 的工具链和优化操作，EMScripten 的具体运行过程如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32108a067fc84b05bc88ef081acbefa1\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

1. EMScripten 将 C/C++ 代码喂给 Clang 编译器（一个基于 LLVM 编译架构的 C/C++ 编译器），编译成 LLVM IR
2. EMScripten 将 LLVM IR 转换成 .wasm 的二进制字节码
3. WebAssembly 无法直接获取到 DOM，只能调用 JS，传入整形或浮点型的等原始数据类型，因此 WebAssembly 需要调用 JS 来获取 Web API 和调用，EMScripten 则通过创建了 HTML 文件和 JS 胶水代码来达到上述效果

> 未来 WebAssembly 也可以[直接调用 Web API](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FWebAssembly%2Fgc%2Fblob%2Fmaster%2FREADME.md)。

上述的 JS 胶水代码并不像想象中那么简单，一开始，EMScripten 实现了一些流行的 C/C++ 库，如 SDL、OpenGL、OpenAL、以及一部分 POSIX 库，这些库都是根据 Web API 来实现的，所以需要 JS 胶水代码来帮助 WebAssembly 和底层的 Web API 进行交互。

所以，有部分胶水代码实现了 C/C++ 代码需要用到的对应的库的功能，胶水代码还同时包含调用上述 WebAssembly JavaScript API 的以获取、加载和运行 .wasm 文件的逻辑。

生成的 HTML 文档加载 JS 胶水代码，然后将输出写入到 `<textarea>` 中去，如果应用使用到了 OpenGL，HTML 也包含 `<canvas>` 元素来作为渲染目标，你可以很方便的改写 EMScripten 的输出，将其转换成 Web 应用需要的形式。

### 2. 直接编写 WebAssembly 代码

如果你想构建自己的编译器、工具链，或者能够在运行时生成 WebAssembly 代码的 JS 库，你可以选择手写 WebAssembly 代码。和物理汇编语言类似，WebAssembly 的二进制格式也有一种文本表示，你可以手动编写或生成这种文本格式，并通过 WebAssembly 的文本到二进制（text-to-binary）的工具将文本转为二进制格式。

### 3. 编写 Rust 代码，并编译为 WebAssembly

多谢 Rust WebAssembly 工作组的不懈努力，我们现在可以将 Rust 代码编译为 WebAssembly 代码。

可以参考这个链接：[developer.mozilla.org/en-US/docs/…](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWebAssembly%2FRust\_to\_wasm)

### 4. 使用 AssemblyScript

对于 Web 开发者来说，可是使用类 TypeScript 的形式来尝试 WebAssembly 的编写，而不需要学习 C 或 Rust 的细节，那么 AssemblyScript 将会是最好的选择。AssemblyScript 将 TypeScript 的变体编译为 WebAssembly，使得 Web 开发者可以使用 TypeScript 兼容的工具链，例如 Prettier、VSCode Intellisense，你可以查看[它的文档](https://link.juejin.cn/?target=https%3A%2F%2Fwww.assemblyscript.org%2F)来了解如何使用。

## 五、WebAssembly 优势

### 1. 和 asm.js 性能对比

下面的图是 Unity WebGL 使用和不使用 WebAssembly 的起步时间对比的一个 BenchMark，给大家当作一个参考。 可以看到，在 FireFox 中，WebAssembly 和 asm.js 的性能差异达到了2倍，在 Chrome 中达到了3倍，在 Edge 中甚至达到了6倍。通过这些对比也可以从侧面看出，目前所有的主流浏览器都已经支持 WebAssembly V1（Node >= 8.0.0）.

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/8/166f14622827f306\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. 和 JavaScript 性能对比

在一个用`create-react-app`新建的项目中，分别对比了 WebAssembly 版本和原生 JavaScript 版本的递归无优化的 Fibonacci 函数，下图是这两个函数在值是 45、48、50 的时候的性能对比。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/11/8/166f0fe7bc778c8c\~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

看图说话，这就是 WebAssembly 与 JavaScript 很实际的一个性能对比。几乎稳定的是 JavaScript 的两倍。

## 六、WebAssembly 应用

### 1. 应用场景

说了这么多，我到底什么时候该使用它呢？总结下来，大部分情况分两个点。

* 对性能有很高要求的App/Module/游戏
* 在 Web 中使用 C/C++/Rust/Go 的库。举个简单的例子。如果你要实现的 Web 版本的 Ins 或者 Facebook， 你想要提高效率。那么就可以把其中对图片进行压缩、解压缩、处理的工具，用 C++ 实现，然后再编译回 WebAssembly。

### 2. 实际应用

在这里能够举的例子还是很多，比如 AutoCAD、GoogleEarth、Unity、Unreal、PSPDKit、WebPack 等等。拿其中几个来简单说一下。

#### 2.1 AutoCAD

这是一个用于画图的软件，在很长的一段时间是没有Web的版本的，原因有两个，其一，是 Web 的性能的确不能满足他们的需求。其二，在 WebAssembly 没有面世之前，AutoCAD 是用 C++ 实现的，要将其搬到 Web 上，就意味着要重写他们所有的代码，这代价十分的巨大。

而在 WebAssembly 面世之后，AutoCAD 得以利用编译器，将其沉淀了 30 多年的代码直接编译成 WebAssembly，同时性能基于之前的普通 Web 应用得到了很大的提升。正是这些原因，得以让 AutoCAD 将其应用从 Desktop 搬到 Web 中。

#### 2.2 Google Earth

Google Earth 也就是谷歌地球，因为需要展示很多 3D 的图像，对性能要求十分高，所以采取了一些Native 的技术。最初的时候就连 Google Chrome 浏览器都不支持 Web 的版本，需要单独下载 Google Earth 的 Destop 应用。而在 WebAssembly 之后呢，谷歌地球推出了 Web 的版本。而据说下一个可以运行谷歌地球的浏览器是 FireFox。

#### 2.3 Unity 和 Unreal 游戏引擎

这里给两个油管的链接自己体验一下。

* Unity WebGL 的戳[这里](https://link.juejin.cn/?target=https%3A%2F%2Fyoutu.be%2FrIyIlATjNcE)
* Unreal 引擎的戳[这里](https://link.juejin.cn/?target=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3DTwuIRcpeUWE)

## 七、WebAssembly 发展

WebAssembly 的出现并没有要替代JavaScript，而是作为 JavaScript 的补充，给了 Web 更好的性能以及更多的可能。

这篇文章没有涉及到的内容有 [WASI](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FWebAssembly%2FWASI)，一种将 WebAssembly 跑在任何系统上的标准化系统接口，当 WebAssembly 的性能逐渐增强时，WASI 可以提供一种确实可行的方式，可以在任意平台上运行任意的代码，就像 Docker 所做的一样，但是不需要受限于操作系统。正如 Docker 的创始人所说：

> “ 如果 WASM+WASI 在 2008 年就出现的话，那么就不需要创造 Docker 了，服务器上的 WASM 是计算的未来，是我们期待已久的标准化的系统接口。

另一个有意思的内容是 WASM 的客户端开发框架如 [yew](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fyewstack%2Fyew)，未来可能将像 React/Vue/Angular 一样流行。

而 WASM 的包管理工具 [WAPM](https://link.juejin.cn/?target=https%3A%2F%2Fwapm.io%2F)，得益于 WASM 的跨平台特性，可能会变成一种在不同语言的不同框架之间共享包的首选方式。

同时 WebAssembly 也是由 W3C 主要负责开发，各大厂商，包括 Microsoft、Google、Mozilla 等赞助和共同维护的一个项目，相信 WebAssembly 会有一个非常值得期待的未来。

## 八、参考

* [WebAssembly 完全入门](https://juejin.cn/post/6844903709806182413)
* [为什么说 WebAssembly 是 Web 的未来](https://juejin.cn/post/7056612950412361741)
* [WebAssembly，你应该这样学](https://juejin.cn/post/7013286944553566215)
