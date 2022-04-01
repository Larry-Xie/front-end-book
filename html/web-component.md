# Web Component

## 一、引言 <a href="#adiem" id="adiem"></a>

组件是前端的发展方向，现在流行的 React 和 Vue 都是组件框架。

谷歌公司由于掌握了 Chrome 浏览器，一直在推动浏览器的原生组件，即 Web Components API。相比第三方框架，原生组件简单直接，符合直觉，不用加载任何外部模块，代码量小。目前，它还在不断发展，但已经可用于生产环境。

![Web Components](https://www.wangbase.com/blogimg/asset/201908/bg2019080604.jpg)

## 二、概念 <a href="#umssv" id="umssv"></a>

狭义的来说，Web Component 是浏览器环境提供的一些新的原生支持的api和模版，它们可以允许你创建全新可定制、可重用并且封装的 HTML 标签，从而在普通网页及 web 应用中使用。

广义的说，它是一套可以支持原生实现组件化的技术，定制的组件基于 Web Component 标准构建，可以在现在浏览器上使用，也可以和任意与 HTML 交互的 JavaScript 库和框架配合使用。

用于支持 Web Component 的特性正逐渐加入 HTML 和 DOM 的规范，Web 开发者使用纯粹的 JS/HTML/CSS 就可以创建可重用的组件，使得使用封装好样式和定制行为的新元素来拓展 HTML 会变得轻而易举。

Web Component 的诞生，是为了解决代码复用、组件自定义、复用管理等问题。

HTML 和 DOM 标准定义了四种新的标准来帮助定义 Web Component。这些标准如下：

1. [定制元素(Custom Elements)](https://link.juejin.cn/?target=https%3A%2F%2Fwww.w3.org%2FTR%2Fcustom-elements%2F): Web 开发者可以通过定制元素创建新的 HTML 标签、增强已有的 HTML 标签或是二次开发其它开发者已经完成的组件。这个 API 是 Web Component 的基石。
2. [HTML 模板(HTML Templates)](https://link.juejin.cn/?target=https%3A%2F%2Fwww.html5rocks.com%2Fen%2Ftutorials%2Fwebcomponents%2Ftemplate%2F%23toc-pillars): HTML 模板定义了新的元素，描述一个基于 DOM 标准用于客户端模板的途径。模板允许你声明标记片段，它们可以被解析为 HTML。这些片段在页面开始加载时不会被用到，之后运行时会被实例化。
3. [Shadow DOM](https://link.juejin.cn/?target=https%3A%2F%2Fdom.spec.whatwg.org%2F%23shadow-trees): Shadow DOM 被设计为构建基于组件的应用的一个工具。它可以解决 web 开发的一些常见问题，比如允许你把组件的 DOM 和作用域隔离开，并且简化 CSS 等等。
4. [HTML 引用(HTML Imports)](https://link.juejin.cn/?target=https%3A%2F%2Fwww.html5rocks.com%2Fen%2Ftutorials%2Fwebcomponents%2Fimports%2F): HTML 模板(HTML Templates)允许你创建新的模板，同样的，HTML 引用(HTML imports)允许你从不同的文件中引入这些模板。通过独立的HTML文件管理组件，可以帮助你更好的组织代码。

## 三、Web Component Api <a href="#eqvgb" id="eqvgb"></a>

### 1. 自定义组件的声明和使用 <a href="#fxite" id="fxite"></a>

所依赖的主要接口是CustomElementRegistry，该接口提供了，用作支持自定义组件的使用和声明：

#### window.customElements.define <a href="#y8t3m" id="y8t3m"></a>

该方法用来声明自定义组件，接受3个参数，无返回值：

1. name：将要全局注册的自定义组件名字(必须是中划线的形式)。
2. constructor：一个类，如果声明的是自主定制元素，则必须继承自HTMLElement；如果声明的是自定义内置元素，则必须继承它将要扩展的原生元素所属的类(如要扩展div，那就必须继承HTMLDivElement)。并且类的构造函数中，必须执行super。
3. options：一个可选的配置对象，只有在声明自定义内置元素时使用，且当前只有一个配置项extends,值为将要扩展的原生元素的标签名。

#### window.customElements.get <a href="#jjhz8" id="jjhz8"></a>

该方法用来获取自定义组件的构造函数，接受一个参数，即声明过的自定义组件的name，返回构造函数。

#### window.customElements.upgrade <a href="#je3ny" id="je3ny"></a>

该方法是用来更新挂载主文档之前的包含shadow dom的自定义组件的，接受一个参数，即包含了shadow dom的自定义组件节点，无返回值。

#### window.customElements.whenDefined <a href="#fsoov" id="fsoov"></a>

该方法是用来检测并提供自定义组件被定义声明完毕的时机得，接受一个参数，即自定义元素的name，返回值是一个promise(只检测自定义组件是否被defined，不检测是否被挂载于主文档)。若提供的name无效，则触发promise的catch。

### 2. 自定义组件的生命周期 <a href="#lorn7" id="lorn7"></a>

#### constructor <a href="#z5wvg" id="z5wvg"></a>

自定义组件的第一个生命周期，用来初始化自定义组件本身。触发的时机在自定义组件被document.createElement的时候(前提是组件已经被customElements.define过，如果组件是先create，后defined，那么constructor的执行时机在append到主文档里时)。

#### connectedCallback <a href="#et0hv" id="et0hv"></a>

在组件被成功添加到主文档时触发的生命周期，在constructor之后。

#### attributeChangedCallback <a href="#usk7e" id="usk7e"></a>

自定义组件最关键的一个生命周期。触发时机在组件属性被增加、删除或修改的时候。如果你是在组件被append之前设置了属性，那么就会在connectedCallback之前触发；反之，则在connectedCallback之后触发。

需要配合静态方法observedAttributes来使用，只有注册在observedAttributes中的属性才会被监听。

#### adoptedCallback <a href="#e4wrx" id="e4wrx"></a>

当元素被移动到新的文档时，被调用。即元素是另一个文档的元素，而adoptedCallback是新文档下的自定义组件的回调。

#### disconnectedCallback <a href="#k6cls" id="k6cls"></a>

自定义组件的最后一个生命周期，触发的时机在组件被成功从主文档移除时。

注意：浏览器关闭或tabs关闭，不会触发disconnectedCallback。

### 3. Shadow DOM 的使用 <a href="#y37mr" id="y37mr"></a>

其作用是将标记结构、样式和行为隐藏起来，并与页面上的其他代码相隔离。Shadow DOM 都不是一个新事物，在过去的很长一段时间里，浏览器用它来封装一些元素的内部结构。

#### ele.attachShadow <a href="#elnae" id="elnae"></a>

为元素附加Shadow DOM，attachShadow接受一个对象参数，只需关注一个配置属性mode，如果设置为open，表示可以从外部获取Shadow DOM内部的元素；如果设置为closed，则表示隐藏Shadow DOM内部。

#### 操作元素的Shadow DOM并添加样式 <a href="#ruwsa" id="ruwsa"></a>

当为一个元素附加了Shadow DOM后，就可以使用同操作正常dom一样的方法去操作了。

### 4. 模板 <a href="#dcsdg" id="dcsdg"></a>

#### template <a href="#udr4y" id="udr4y"></a>

使用包裹的内容不会在页面上显示，但是却可以被js引用到。这就意味着有些内容我们不用重复构建多遍，使用构建一遍，然后多次引用处理就好了。

#### slot <a href="#hq4oi" id="hq4oi"></a>

在template的基础上，更加灵活的内容分发，可以配合template使用(在template中定义占位符，然后将template的内容clone到shadow dom中)。也可以直接在shadow dom中添加占位符。然后在自定义组件的innerhtml中使用即可。

#### slotchange <a href="#jqab9" id="jqab9"></a>

用于监听shadow dom中的slot插入或移除的事件。

### 5. 其它 Api <a href="#v7c4z" id="v7c4z"></a>

#### element.attachShadow(opt) <a href="#gcaoq" id="gcaoq"></a>

用来给指定元素挂载shadow dom。opt的配置项：

* mode：如果为open，表示可以在外部通过element.shadowRoot获取shadow dom节点。并且方法会返回shadow dom对象。如果为closed，表示不允许外部访问shadow dom节点，并且方法返回null。
* delegatesFocus：表示是否减轻自定义元素的聚焦性能问题。当shadow DOM中不可聚焦的部分被点击时, 让第一个可聚焦的部分成为焦点, 并且shadow host将提供所有可用的 :focus 样式.

#### css伪类 <a href="#agqs3" id="agqs3"></a>

* :defined：表示所有内置元素及已经通过customElements.define注册的元素。
* :host：只能在shadow dom的样式表内书写。表示当前所在的自定义组件的所有实例及shadow dom下所有的元素。
* :host(\[选择器])：只能在shadow dom的样式表内书写。是:host的增强，表示:host()所在的自定义组件的所有实例中选择器符合括号中名称的实例及其包含的shadow dom下属所有元素。
* :host-context(\[选择器])：只能在shadow dom的样式表内书写。是:host的增强，表示:host()-context所在的自定义组件的所有实例的父元素中选择器符合括号中名称的实例及其包含的shadow dom下属所有元素。
* :slotted(\[选择器]):只能在shadow dom的样式表内书写。表示: slotted()所在的自定义组件的所有实例中选择器符合括号中名称的slot元素，若选择器为\*，则表示命中所有slot。

#### 节点相关拓展 <a href="#fhzyq" id="fhzyq"></a>

* getRootNode:使用方式为ele. getRootNode(opt)，opt中包含一个属性composed，为true时，检索到的根元素为document；为false时，如果ele是属于shadow dom，那么检索到shadow dom，否则检索到document。
* isConnected:是元素的一个只读属性接口。返回元素是否与dom树连接的boolean值。即是否被append到主文档中。

#### event扩展 <a href="#feqth" id="feqth"></a>

* composed属性：用来指示该事件是否可以从 Shadow DOM 传递到一般的 DOM(测试后发现不论是普通dom还是shadow dom均为true)。
* path属性：返回事件的路径。如果shadow root是使用mode为closed创建的，则不包括shadow树中的节点(测试后发现尽管shadowdom设置了mode为closed，依然能获取完整的path)。

#### 关于slot <a href="#ooaju" id="ooaju"></a>

* ele.assignedSlot：用来获取ele元素上代表插入slot的元素。但如果ele.attachShadow中的mode是closed为closed时，返回null。
* ele.slot：用来获取元素上slot的name值。

## 四、实例 <a href="#qotre" id="qotre"></a>

下图是一个用户卡片。

![User Card](https://www.wangbase.com/blogimg/asset/201908/bg2019080405.jpg)

本文演示如何把这个卡片，写成 Web Components 组件，这里是最后的[完整代码](https://jsbin.com/yobopor/1/edit?html,js,output)。

网页只要插入下面的代码，就会显示用户卡片。

```html
<user-card></user-card>
```

这种自定义的 HTML 标签，称为自定义元素（custom element）。根据规范，自定义元素的名称必须包含连词线，用与区别原生的 HTML 元素。所以，\<user-card>不能写成\<usercard>。

### 1. 自定义元素 <a href="#nr5rz" id="nr5rz"></a>

自定义元素需要使用 JavaScript 定义一个类，所有\<user-card>都会是这个类的实例。

```javascript
class UserCard extends HTMLElement {
  constructor() {
    super();
  }
}
```

上面代码中，UserCard就是自定义元素的类。注意，这个类的父类是HTMLElement，因此继承了 HTML 元素的特性。

接着，使用浏览器原生的customElements.define()方法，告诉浏览器\<user-card>元素与这个类关联。

```javascript
window.customElements.define('user-card', UserCard);
```

### 2. 自定义元素内容 <a href="#qdvxf" id="qdvxf"></a>

自定义元素\<user-card>目前还是空的，下面在类里面给出这个元素的内容。

```javascript
class UserCard extends HTMLElement {
  constructor() {
    super();

    var image = document.createElement('img');
    image.src = 'https://semantic-ui.com/images/avatar2/large/kristy.png';
    image.classList.add('image');

    var container = document.createElement('div');
    container.classList.add('container');

    var name = document.createElement('p');
    name.classList.add('name');
    name.innerText = 'User Name';

    var email = document.createElement('p');
    email.classList.add('email');
    email.innerText = 'yourmail@some-email.com';

    var button = document.createElement('button');
    button.classList.add('button');
    button.innerText = 'Follow';

    container.append(name, email, button);
    this.append(image, container);
  }
}
```

上面代码最后一行，this.append() 的 this 表示自定义元素实例。完成这一步以后，自定义元素内部的 DOM 结构就已经生成了。

使用 JavaScript 写上一节的 DOM 结构很麻烦，Web Components API 提供了\<template>标签，可以在它里面使用 HTML 定义 DOM。

```
<template id="userCardTemplate">
  <img src="https://semantic-ui.com/images/avatar2/large/kristy.png" class="image">
  <div class="container">
    <p class="name">User Name</p>
    <p class="email">yourmail@some-email.com</p>
    <button class="button">Follow</button>
  </div>
</template>
```

然后，改写一下自定义元素的类，为自定义元素加载\<template>。

```
class UserCard extends HTMLElement {
  constructor() {
    super();

    var templateElem = document.getElementById('userCardTemplate');
    var content = templateElem.content.cloneNode(true);
    this.appendChild(content);
  }
}  
```

上面代码中，获取\<template>节点以后，克隆了它的所有子元素，这是因为可能有多个自定义元素的实例，这个模板还要留给其他实例使用，所以不能直接移动它的子元素。

到这一步为止，完整的代码如下。

```
<body>
  <user-card></user-card>
  <template>...</template>

  <script>
    class UserCard extends HTMLElement {
      constructor() {
        super();

        var templateElem = document.getElementById('userCardTemplate');
        var content = templateElem.content.cloneNode(true);
        this.appendChild(content);
      }
    }
    window.customElements.define('user-card', UserCard);    
  </script>
</body>
```

### 3. 添加样式 <a href="#j92h9" id="j92h9"></a>

自定义元素还没有样式，可以给它指定全局样式，比如下面这样。

```
user-card {
  /* ... */
}
```

但是，组件的样式应该与代码封装在一起，只对自定义元素生效，不影响外部的全局样式。所以，可以把样式写在\<template>里面。

```
<template id="userCardTemplate">
  <style>
   :host {
     display: flex;
     align-items: center;
     width: 450px;
     height: 180px;
     background-color: #d4d4d4;
     border: 1px solid #d5d5d5;
     box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.1);
     border-radius: 3px;
     overflow: hidden;
     padding: 10px;
     box-sizing: border-box;
     font-family: 'Poppins', sans-serif;
   }
   .image {
     flex: 0 0 auto;
     width: 160px;
     height: 160px;
     vertical-align: middle;
     border-radius: 5px;
   }
   .container {
     box-sizing: border-box;
     padding: 20px;
     height: 160px;
   }
   .container > .name {
     font-size: 20px;
     font-weight: 600;
     line-height: 1;
     margin: 0;
     margin-bottom: 5px;
   }
   .container > .email {
     font-size: 12px;
     opacity: 0.75;
     line-height: 1;
     margin: 0;
     margin-bottom: 15px;
   }
   .container > .button {
     padding: 10px 25px;
     font-size: 12px;
     border-radius: 5px;
     text-transform: uppercase;
   }
  </style>

  <img src="https://semantic-ui.com/images/avatar2/large/kristy.png" class="image">
  <div class="container">
    <p class="name">User Name</p>
    <p class="email">yourmail@some-email.com</p>
    <button class="button">Follow</button>
  </div>
</template>
```

上面代码中，\<template>样式里面的:host伪类，指代自定义元素本身。

### 4. 自定义元素的参数 <a href="#iquwr" id="iquwr"></a>

\<user-card>内容现在是在\<template>里面设定的，为了方便使用，把它改成参数。

```
<user-card
  image="https://semantic-ui.com/images/avatar2/large/kristy.png"
  name="User Name"
  email="yourmail@some-email.com">
</user-card>
```

\<template>代码也相应改造。

```
<template id="userCardTemplate">
  <style>...</style>

  <img class="image">
  <div class="container">
    <p class="name"></p>
    <p class="email"></p>
    <button class="button">Follow John</button>
  </div>
</template>
```

最后，改一下类的代码，把参数加到自定义元素里面。

```
class UserCard extends HTMLElement {
  constructor() {
    super();

    var templateElem = document.getElementById('userCardTemplate');
    var content = templateElem.content.cloneNode(true);
    content.querySelector('img').setAttribute('src', this.getAttribute('image'));
    content.querySelector('.container>.name').innerText = this.getAttribute('name');
    content.querySelector('.container>.email').innerText = this.getAttribute('email');
    this.appendChild(content);
  }
}
window.customElements.define('user-card', UserCard);    
```

### 5. Shadow DOM <a href="#flajy" id="flajy"></a>

我们不希望用户能够看到\<user-card>的内部代码，Web Component 允许内部代码隐藏起来，这叫做 Shadow DOM，即这部分 DOM 默认与外部 DOM 隔离，内部任何代码都无法影响外部。

自定义元素的this.attachShadow()方法开启 Shadow DOM，详见下面的代码。

```
class UserCard extends HTMLElement {
  constructor() {
    super();
    var shadow = this.attachShadow( { mode: 'closed' } );

    var templateElem = document.getElementById('userCardTemplate');
    var content = templateElem.content.cloneNode(true);
    content.querySelector('img').setAttribute('src', this.getAttribute('image'));
    content.querySelector('.container>.name').innerText = this.getAttribute('name');
    content.querySelector('.container>.email').innerText = this.getAttribute('email');

    shadow.appendChild(content);
  }
}
window.customElements.define('user-card', UserCard);
```

上面代码中，this.attachShadow()方法的参数{ mode: 'closed' }，表示 Shadow DOM 是封闭的，不允许外部访问。

至此，这个 Web Component 组件就完成了，完整代码可以访问[这里](https://jsbin.com/yobopor/1/edit?html,js,output)。可以看到，整个过程还是很简单的，不像第三方框架那样有复杂的 API。

### 6. 组件扩展 <a href="#kgozt" id="kgozt"></a>

在前面的基础上，可以对组件进行扩展。

#### 与用户互动 <a href="#k00u1" id="k00u1"></a>

用户卡片是一个静态组件，如果要与用户互动，也很简单，就是在类里面监听各种事件。

```
this.$button = shadow.querySelector('button');
this.$button.addEventListener('click', () => {
  // do something
});
```

#### 组件的封装 <a href="#isryr" id="isryr"></a>

上面的例子中，\<template>与网页代码放在一起，其实可以用脚本把\<template>注入网页。这样的话，JavaScript 脚本跟\<template>就能封装成一个 JS 文件，成为独立的组件文件。网页只要加载这个脚本，就能使用\<user-card>组件。

## 五、使用贴士 <a href="#rfbpw" id="rfbpw"></a>

还有一些关于 Web Component 的小贴士和技巧。

### 1. 组件的命名 <a href="#nf9vs" id="nf9vs"></a>

定制元素的名称必须包含一个短横线。所以 \<my-tabs> 和 \<my-amazing-website> 是合法的名称, 而\<foo> 和 \<foo\_bar> 不行。

在 HTML 添加新标签时需要确保向前兼容，不能重复注册同一个标签。

定制元素标签不能是自闭合的，因为 HTML 只允许一部分元素可以自闭合。需要写成像 \<app-drawer>\</app-drawer> 这样的闭合标签形式。

### 2. 拓展组件 <a href="#qk2ob" id="qk2ob"></a>

创建组件时可以使用继承的方式。举个例子，如果想要为两种不同的用户创建一个 UserCard，你可以先创建一个基本的 UserCard 然后将它拓展为两种特定的用户卡片。想要了解更多组件继承的知识，可以查看[Google web developers’ article](https://link.juejin.cn/?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fweb-components%2Fcustomelements%23extend)。

### 3. 生命周期回调函数 <a href="#n5pnr" id="n5pnr"></a>

我们创建了当元素加入 DOM 后自动触发的 connectedCallback 方法。我们同样有元素从 DOM 中移除时触发的 disconnectedCallback 方法。 attributesChangedCallback(attribute, oldval, newval)方法会在我们改变定制组件的属性时被触发。

### 4. 组件元素是类的实例 <a href="#juz2e" id="juz2e"></a>

既然组件元素是类的实例，就可以在这些类中定义公用方法。这些公用方法可以用来允许其它定制组件/脚本来和这些组件产生交互，而不是只能改变这些组件的属性。

### 5. 定义私有方法 <a href="#shgb8" id="shgb8"></a>

可以通过多种方式定义私有方法。我倾向于使用（立即执行函数），因为它们易写和易理解。举个例子，如果你创建的组件有非常复杂的内部功能，你可以像下面这样做：

```
(function() {

  // 使用第一个self参数来定义私有函数
  // 当调用这些函数时，从类中传递参数
  function _privateFunc(self, otherArgs) { ... }

  // 现在函数只可以在你的类的作用域中可用
  class MyComponent extends HTMLElement {
    ...

    // 定义下面这样的函数可以让你有途径和这个元素交互
    doSomething() {
      ...
      _privateFunc(this, args)
    }
    ...
  }

  customElements.define('my-component', MyComponent);
})()
```

### 6. 冻结类 <a href="#nab9j" id="nab9j"></a>

为了防止新的属性被添加，需要冻结你的类。这样可以防止类的已有属性被移除，或者已有属性的可枚举、可配置或可写属性被改变，同样也可以防止原型被修改。你可以使用下面的方法:

```
class MyComponent extends HTMLElement { ... }
const FrozenMyComponent = Object.freeze(MyComponent);
customElements.define('my-component', FrozenMyComponent);
```

注意: 冻结类会阻止你在运行时添加补丁并且会让你的代码难以调试。

## 六、参考 <a href="#y0mth" id="y0mth"></a>

* [http://www.ruanyifeng.com/blog/2019/08/web\_components.html](http://www.ruanyifeng.com/blog/2019/08/web\_components.html)
* [https://juejin.cn/post/6844903534169686023](https://juejin.cn/post/6844903534169686023)
* [https://juejin.cn/post/7010580819895844878](https://juejin.cn/post/7010580819895844878)
