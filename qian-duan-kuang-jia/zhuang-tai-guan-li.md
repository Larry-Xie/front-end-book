# 状态管理

## 一、引言 <a href="#shen-me-shi-zhuang-tai" id="shen-me-shi-zhuang-tai"></a>

为什么前端需要状态管理？现代主流的前端框架，不管是 react 还是 vue，都是组件化开发。组件化虽然可以提高代码复用率，但是同时也带来一个问题，那就是组件之间的通信。

父子组件之间的通信我们可以从容应对，但是当组件之间的关系变得复杂，甚至组件之间没有关系时，要在他们之间通信就变得不太容易。

这个时候我们就需要把这些在多个组件中都用到的数据提取出来，独立于组件树，放在一个新的地方。这些数据往往会因为组件上事件的触发而发生变更，同时这些数据的变更又会作用到组件上，引起视图的变更。

因此，针对这些数据的管理就变更尤为重要，这里的数据也被称为状态（state），因此也叫状态管理。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/347fa5bcfa8945ba8d71f8906881c182\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 二、状态管理含义

近年来，随着单页面应用的兴起，`JavaScript` 需要管理比任何时候都要多的**状态**，或者可以说是数据，这些**状态**可能包括服务器响应、缓存数据、本地生成尚未持久化到服务器的数据，也包括 UI 状态，如激活的路由，被选中的标签，是否显示加载动效或者分页器等等，这些都是十年前二十年前的 web 开发没有遇到的挑战。

但是其实，无论系统如何复杂，前端页面的所要完成的事其实很简单，**就是把业务的信息渲染出来，反馈给用户，并进行人机交互，返回给服务端，这是前端技术解决的核心问题**。而几乎所有的 web 系统都不会把用户的一些数据和系统的状态维护在客户端，例如不会把用户的会员等级，新增的待办事项仅仅保存在浏览器，因为这些都是“转瞬即逝”的东西，用户换一个浏览器这些信息就会全部消失不见，所以这些数据（状态）必然会在服务器上存储起来，当用户重新登录的时候，页面会从服务器中重新拿到最新的数据，把页面渲染出来。

自从 `Ajax` 的诞生，使得 Web 应用不用大量和频繁跟服务器通信，原本需要服务器返回整个页面的数据，现在也只需要通过 Ajax 传递少量的信息，剩下的东西由 `javascript` 自己操作页面，对页面的元素修修改改。因为不用刷新整个页面，这种 Ajax 应用用户体验非常好，给人非常快的感觉，而 `Javascript` 对页面上的操作的总总过程，其实就是页面 UI 跟 `Javascript` 变量的同步，也涉及跟服务端数据的同步，**也就是我们所说的状态管理**。

### 1. 广义状态管理

**状态管理是一个十分广泛的概念，因为状态无处不在。**服务端也有状态，`Spring` 等框架会管理状态，`RDS` 和 `redis` 等组件会保存数据，手机 `App` 也会把数据保存到手机内存里，状态管理不是什么新鲜事物，在 web 前端还没有变复杂之前，很多系统的网页用着纯 Javascript 或者 jQuery 来做一些状态管理，或者说是一些数据管理吧，当然，这里边要做的事情其实很复杂，主要表现在状态的同步上，可以看下面的一个例子。

#### 1.1 javascript 无框架时代状态管理例子

```
addBook(book) {
  // js 状态逻辑
  this.books.push(book);

  // 同步服务器逻辑
  this.callAddBookApi(book).then(this.ajustUIBook);

  // UI 逻辑（同步）
  this.mayBeDoSomethingElse();

  const li = document.createElement('li');
  const span = document.createElement('span');
  span.innerText = book.name;

  this.$bookUl.appendChild(li);
  li.appendChild(span);
}
```

上面的代码中，我们通过某个表单增加了一本书，`addBook` 这个方法先是在本地的 `books` 变量中加进去，然后请求服务器，把新增加的 `Book` 信息传过去，当然结果可能成功也可能失败，所以要调用一个回调方法进行调整，然后再操作 Dom 来更新页面的展示。当然，还有更复杂的情况，例如在页面别的地方，可能有所有 `Book` 的计数，或者有“用户最近的 `Books`”的展示，程序需要找到那些元素的 Dom，并用特定的领域逻辑对 Dom 内容进行更新。

> 事实上，在 2019 年的今天，我在很多手机 App 上还能看到相识的 Bug，例如我在闲鱼上看到一条应用内消息，但是到消息页面查看，对应联系人消息框中却没有看到，要刷新一下才会显示，这应该是典型没做好状态管理的例子。

这样的问题在大型的 web 应用中会变得非常复杂，Facebook 的 web 应用就深受这样的问题的困扰。

#### _1.2 Facebook 遇到的问题_

Facebook 的站点是最早开始 Web App 这个概念的一批，在 Facebook 没有创造出 Flux 这种架构之前，Facebook 就有很多状态未同步的 bug，我在曾经很少使用的几次经历中，也目睹过这些 bug，就是当在 Facebook 逛着逛着，突然来了几条通知，但是点进去之后竟然没有了，过了一会儿，通知又来了，点进去看到了之前的消息，但是新的还是没有来。

虽然 Facebook 在的客户端结构跟流行的 MVC 架构或者 MVP 架构不一样，但是他们问题的原因大概就是在 [_http://fluxxor.com/what-is-flux.html_](https://link.zhihu.com/?target=http%3A//fluxxor.com/what-is-flux.html) 描述的这样

> [_http://fluxxor.com/what-is-flux.html_](https://link.zhihu.com/?target=http%3A//fluxxor.com/what-is-flux.html)\
> 为了最好地描述流量，我们将其与领先的客户端架构之一：MVC进行比较。在客户端MVC应用程序中，用户交互会触发控制器中的代码。控制器知道如何通过调用模型上的方法来协调对一个或多个模型的更改。当模型更改时，它们会通知一个或多个视图，这些视图又从模型中读取新数据并进行相应的更新，以便用户可以看到该新数据。

![](https://pic3.zhimg.com/80/v2-9534dcd79a7d2b6c782dec9fc151bf5a\_720w.jpg)

> 一个简单的MVC流程\
> 随着MVC应用程序的增长以及控制器，模型和视图的添加，依赖性变得越来越复杂。

![](https://pic2.zhimg.com/80/v2-95afea773dc983b1d2e6bf434cfe1f31\_720w.jpg)

> 仅添加三个视图，一个控制器和一个模型，就已经很难跟踪依赖图。当用户与UI交互时，将执行多个分支代码路径，并且在弄清楚这些潜在代码路径中的一个（或多个）中的哪个模块（或多个模块）包含错误的情况下，在应用程序状态下进行调试成为一种练习。在最坏的情况下，用户交互将触发更新，进而触发其他更新，从而导致沿其中一些路径（有时是重叠的路径）的易于出错且难以调试的级联效应。\
> \
> Flux避开此设计，而采用单向数据流。视图中的所有用户交互都将调用操作创建者，这将导致从单例调度程序中发出操作事件。调度程序是通量应用程序中所有动作的单发射点。该操作从调度程序发送到存储，存储根据响应更新自身。

事实上，在同一时期，Facebook 的 `React` 跟 `Flux` 一起设计出来，光芒四射的 `React` 席卷了整个前端，同时在 `Flux` 的启发下，状态管理也正式走进了众多开发者的眼前，状态管理开始规范化地走进了人们的眼前。

### 2. 狭义状态管理

狭义的状态管理，也就是通常我们所说的前端框架状态管理。现在的状态管理已经成为了前端开发技术栈的一个主题，工具库和框架都十分丰富。现在谈及状态管理，虽然概念还是很很宽泛，但是还是能收敛到几个点上。

1. 数据流的方向性管理，如 Flux
2. 系统状态的框架性工具管理，例如 Redux，Mobx
3. 组件生命周期内的状态管理，例如在 React 中使用 `setState` 或者 `hooks`
4. ...

事实上，现在前端开发谈及状态管理，其实就是指的是像 Redux 这样的东西，**用单一数据流的思想指导整个系统，并把状态存储到特定的地方，在 UI 组件层通过一些选择器把需要组件取出，渲染到 UI 上。**

状态管理的工具库并不仅仅有 `Redux`，像 `Mobx`，`Vuex` 等，都是十分优秀的库，思想也大抵相似。

当然，我也见过有不少应用并没有使用状态管理库，而是自己手写一套状态管理方案（事实上， 这个事情完全不困难，因为像 `Redux` 这样的库，实际上十分的简单，早期的 `Redux` 代码库只有一两百行代码）。例如像 `Angular` 技术栈，有人会通过 `Angular Service` 这个载体，利用 `Rxjs` 做简单的状态管理，也有亮点；也例如 React，也有人直接通过单例对象或者 `hook` 来连接一个自定义状态管理服务，这些个做法不论在大在小的项目，都有自己的玩法。**状态管理设计这个事情并不需要囿于框架和工具库，甚至在工具库中也可以有自己的玩法。**

虽然说状态管理的定义事实上很广泛，也有很多优秀实践，但是做好状态管理还是要遵循一些原则。

#### **1. UI 分层**

在后端开发中，领域驱动设计（DDD）要求把业务模型隔离开来，做到跟基础设施无关，无侵入，并用 Adaptor 隔离网络，数据库等操作，一个美好的假设是，假如以后做大重构，或者更换 web 框架，那么只需要更换架构外层的 Adaptor 即可，领域层无需改动。

这个道理放到前端也是适用的，不过现在的前端是组件驱动的时代，前端的核心领域是 UI 层，**好的状态管理应该做到 UI 层独立，并让状态管理的逻辑尽量少侵入到 UI 层。**另一个说法是要**保持 UI 层的纯度**，只要相同的数据传到 UI 层，就应该是相同的表现，那么以后换一个状态管理方案，或者说一些 Api 层面的改动，再或者是引入了 `BFF`，减轻了前端逻辑的负担，那么这些改动只需要在状态管理的领域中完成即可，UI 层完全可以不用改动，这种做法大大减轻了前端开发的上下文负担，而且对单元测试十分友好。

> 有一些前端应用的逻辑也很重且复杂，又是另一回事了，除了 UI 层，也应该对那些核心逻辑进行建模和分层，维护双核心。不过，除了一些富应用（例如思维导图应用，drawio 一样的工具），我十分不建议在前端放过多的领域/业务逻辑。

#### **2. 单一数据源**和**单向数据流**

**单一数据源**和**单向数据流**是做好状态管理的关键，这能使应用从乱七八糟的状态中解救出来，单一数据源要求客户端应用的**关键**数据都要从同一个地方获取，而**单向数据流**要求应用内状态管理的参与者都要按照一条流向来获取数据和发出动作，不允许双向交换数据，如下图

![](https://pic3.zhimg.com/80/v2-9bbfc2db75b7c5690361f82e4c276066\_720w.jpg)

**单一数据源**和**单向数据流**这两个概念长得很像，也互有联系，**单一数据源**保证了 UI 渲染的单纯性，只需要对单一来源的数据做出响应，来什么样的数据就通过可以预测的数据来转换 UI，保证不会出错，这就好像“[_手表定律_](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%25E6%2589%258B%25E8%25A1%25A8%25E5%25AE%259A%25E5%25BE%258B/4307956%3Ffromtitle%3D%25E6%2589%258B%25E8%25A1%25A8%25E6%2595%2588%25E5%25BA%2594%26fromid%3D1789873)”一样，如果有两个上司指挥你，你就会不知所措。**单向数据流**通过限制数据和动作的流向令数据可以进行追溯，可以实现一些日志打印，热加载，时间旅行，同构应用等功能，另一个作用是在 UI 层对状态变更进行控制反转([_Inversion of Control_](https://link.zhihu.com/?target=http%3A//fluxxor.com/what-is-flux.html%23inversion-of-control))，从而实现解耦的目的。

举个Redux的例子，一个页面中渲染了数据列表 `books`，这个 books 变量是通过 store 获取的，符合单向数据流 但是这时候要添加一本书，我们调用了一个 api 请求 `createBook`，通常有人为了方便，会在组件里面调用异步的 Action 或者通过 http 调用请求后直接把新增的 `book` 加到列表里面。这是一个典型错误的做法，同时违法了单一数据源和单向数据流，这样的情况一多，意味着应用内的状态重新变成七零八落的情况，重归混沌，同步失效，bug从生。

> 这里要强调一下并不是把所有的变量都往单一数据流中的 `store` 里塞，一些**组件内的状态**应该在**组件内完成自己的生命周期**，例如一个 Selector 的 `isSelecting` 状态，又例如一个 Modal 的 `isOpen` 状态，这些属于组件状态，无需进入到全局状态树中。

## 三、状态管理思路

状态不会是一个，多个状态的集合会用对象的 key、value 来表示，比如 React 的 state 对象，Vue 的 data 对象(虽然叫 data 也是指的状态)。

怎么监听一个对象的变化呢?

我们是不是可以提供一个 api 来修改，在这个 api 内做 state 变化之前的处理，并且在 state 变化之后做联动处理。

这样的方案只能通过 api 触发状态修改，直接修改 state 是触发不了状态管理逻辑的。

React 的 setState 就是这种思路，通过 setState 修改状态会做状态变化之前的批量异步的状态合并，会触发状态变化之后视图渲染和 hooks、生命周期的重新执行。但是直接修改 state 是没用的。

那怎么让直接修改状态也能监听到变化呢?

可以对状态对象做一层代理，代理它的 get、set，当执行状态的 get 的时候把依赖该状态的逻辑收集起来，当 set 修改状态的时候通知所有依赖它的逻辑(视图渲染、逻辑执行)做更新。

Vue 的 data 监听变化就是用的这种思路，在状态 get 的时候把依赖封装成 Watcher，当 set 的时候通知所有 Watcher 做更新。

这种思路叫做响应式(reactive)，也就是状态变化之后自动响应变化做联动处理的意思。

代理 get、set 可以用 Object.defineProperty 的 api，但是它不能监听动态增删的对象属性，所以 Vue3 改为了用 Proxy 的 api 实现。

监听对象的变化就这两种方式：

* 提供 api 来修改，内部做联动处理。
* 对对象做一层代理，set 的时候做联动处理，通知 get 时收集的所有依赖。

## 四、状态管理方案

### 1. Redux

说到状态管理，那就不得不说 Redux。Redux是将整个应用的状态存储到到一个地方，称为 store。所有的数据都存在 state 中。各个组件可以派发 dispatch 行为 action 给 store，而不是直接通知其它组件。其它组件可以通过订阅 store 中的状态(state)来刷新自己的视图。没错，这就是典型的发布订阅模式。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/858bdc970b374caa969f90629f009c44\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5f8e6b4970864d42b66491464cb47dfc\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

Redux有以下几个特征：

* 整个应用的 state 被储存在一个 object 中
* state 是只读的，惟一改变 state 的方法就是触发 action
* action 是一个用于描述已发生事件的普通对象，使用纯函数来执行修改
* 为了描述 action 如何改变state ，需要编写 reducers
* 单一数据源的设计让 React 的组件之间的通信更加方便，同时也便于状态的统一管理

主要有以下几个方法：

* createStore /\* 创建store\*/
* combineReducers /\* 组合多个reducers\*/
* bindActionCreators /\* 绑定action和store.dispatch\*/
* applyMiddleware /\* 中间件机制\*/
* compose

#### 1.1 Redux 实现原理

```js
// 创建state仓库
function createStore(reducer,preloadedState){

  // reducer ：action 改变 state 的动作
  let currentReducer = reducer;
  // 初始化状态
  let currentState = preloadedState;
  // 订阅的事件，当 state 变更时触发
  let currentListeners = [];
  
  // 获取当前的 state
  function getState() {
    return currentState;
  }

  // 事件订阅
  function subscribe(listener) {
    currentListeners.push(listener)
    // 返回取消事件订阅
    return function unsubscribe() {
      const index = currentListeners.indexOf(listener)
      currentListeners.splice(index, 1)
    }
  }

  // 派发 action
  function dispatch(action) {
    currentState = currentReducer(currentState, action)
    for (let i = 0; i < currentListeners.length; i++) {
      const listener = currentListeners[i];
      listener();
    }
    return action;
  }

  // 初始化状态
  dispatch({ type: ActionTypes.INIT });

  const store = ({
    dispatch,
    subscribe,
    getState
  })
  
  // 返回store
  return store
}
```

#### 1.2 Redux 使用案例

```js
import { createStore} from 'redux';

const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';
let initState = { number: 0 };

const reducer = (state = initState, action) => {
    switch (action.type) {
        case INCREMENT:
            return { number: state.number + 1 };
        case DECREMENT:
            return { number: state.number - 1 };
        default:
            return state;
    }
}
let store = createStore(reducer);
function render() {
    console.log(store.getState().number);
}
store.subscribe(render);
render();
store.dispatch({ type: INCREMENT });
store.dispatch({ type: DECREMENT });
```

总结一下 Redux 的用法：

* 1.定义 reducer，建立 action 改变 state 的规则
* 2.createStore 创建 store
* 3.subscribe 订阅函数，当 state 变化时触发
* 4.dispatch 方法派发 action

### 2. React-Redux

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f4d6d82068b04c908f24ec89dab3e4ba\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

Redux 是一个状态管理的库，但是它不针对于任何一个框架。如果我们要在 react 中使用 store，就需要在每一个用到的组件中引入，这样用起来比较麻烦。因此针对 React 在 Redux 的基础上诞生了 React-Redux。React-Redux 主要由以下几个部分组成：

* ReactReduxContext 函数

> 创建全局上下文

```js
import React from 'react';
export const ReactReduxContext = React.createContext(null)
export default ReactReduxContext;
复制代码
```

* Provider 组件

> 把 store 挂载到全局上下文

```js
import React from 'react'
import ReactReduxContext from './ReactReduxContext';

export default function(props){
  return (
    <ReactReduxContext.Provider value={{ store: props.store }}>
      {props.children}
    </ReactReduxContext.Provider>
  )
}
复制代码
```

* connect 函数

> connect 函数接受两个参数：mapStateToProps 和 mapDispatchToProps。mapStateToProps 是从state 对象中筛选出当前需要的属性；mapDispatchToProps 当前需要用到的 actions。返回的是一个高阶函数，它接受一个组件为参数，返回一个函数组件。相当于把原来的组件经过包装之后，变成了拥有 store 中特定 state 和 action 的组件。

```js
import React, { useContext, useMemo, useLayoutEffect, useReducer } from "react";
import { bindActionCreators } from "../redux";
import ReactReduxContext from "./ReactReduxContext";

export default function (mapStateToProps, mapDispatchToProps) {
  return function (WrappedComponent) {
    return function (props) {
      const { store } = useContext(ReactReduxContext);
      const { getState, dispatch, subscribe } = store;
      const prevState = getState();
      const stateProps = useMemo(() => mapStateToProps(prevState), [prevState]);
      const dispatchProps = useMemo(() => {
        let dispatchProps;
        if (typeof mapDispatchToProps === "object") {
          dispatchProps = bindActionCreators(mapDispatchToProps, dispatch);
        } else if (typeof mapDispatchToProps === "function") {
          dispatchProps = mapDispatchToProps(dispatch, props);
        } else {
          dispatchProps = { dispatch };
        }
        return dispatchProps;
      }, [dispatch,props]);
      const [, forceUpdate] = useReducer(x => x + 1, 0)
      useLayoutEffect(() => subscribe(forceUpdate), [subscribe]);
      return <WrappedComponent {...props} {...stateProps} {...dispatchProps} />;
    }
  };
}

复制代码
```

总结一下 React-Redux：

* 1.借助 Provider 组件，使得子组件可以获得 store 实例
* 2.通过 connect 函数，以高阶组件的方式传递特定的 state 和 actions。

截止目前，Redux 和 React-Redux 中所接触的概念还是比较容易理解的，并不算太难。可是 Redux 中还有一个不成文的规定，那就是 reducers 函数必须是纯函数。

> 纯函数是函数式编程的概念，只要是同样的输入，必定得到同样的输出。同时函数执行过程中不能产生副作用，比如修改参数，或者向后端发起请求等。Reducer 是纯函数，就可以保证同样的 state，必定得到同样的 view。但也正因为这一点，reducer 函数里面不能改变 State，必须返回一个全新的对象。

那该如何解决副作用的问题呢？Redux 中间件就此诞生。

### 3. Redux 中间件

如果没有中间件的运用,redux 的工作流程是这样 action -> reducer，这是相当于同步操作，由dispatch 触发action后，直接去reducer执行相应的动作。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d916e426f1504b52b56d9bdf344db9dd\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

但是在某些比较复杂的业务逻辑中，这种同步的实现方式并不能很好的解决我们的问题。比如我们有一个这样的需求，点击按钮 -> 获取服务器数据 -> 渲染视图，因为获取服务器数据是需要异步实现，所以这时候我就需要引入中间件，改变 redux 同步执行的流程，形成异步流程来实现我们所要的逻辑。

有了中间件，redux 的工作流程就变成这样 action -> middlewares -> reducer，点击按钮就相当于dispatch 触发action，接下去获取服务器数据 middlewares 的执行，当 middlewares 成功获取到服务器就去触发reducer对应的动作，更新需要渲染视图的数据。

中间件的机制可以让我们改变数据流，实现如异步 action ，action 过滤，日志输出，异常报告等功能。

```js
function applyMiddleware(...middlewares){
    return function(createStore){
        return function(reducer){
            let store = createStore(reducer);
            let dispatch;
            let middlewareAPI= {
                getState:store.getState,
                dispatch:(action)=>dispatch(action);
            let chain = middlewares.map(middleware=>middleware(middlewareAPI));
            dispatch  = compose(...chain)(store.dispatch);
            return {
                ...store,
                dispatch
            };
        }
    }
}
export default applyMiddleware;
```

#### 中间件书写样例 redux-thunk

```js
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => (next) => (action) => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }

    return next(action);
  };
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```

* 使用:接受参数用户名，并返回一个函数(参数为dispatch)

```js
const login = (userName) => (dispatch) => {
  dispatch({ type: 'loginStart' })
  request.post('/api/login', { data: userName }, () => {
    dispatch({ type: 'loginSuccess', payload: userName })
  })
}
store.dispatch(login('ant'))
```

大家是否好奇为什么会出现一个 redux-thunk ？其实，最开始的时候，redux-thunk 还没有独立，而是写在 redux 的 action 分发函数中的一个代码分支而已，和现在的逻辑一样。现在将redux-thunk独立出去，用 middleware 的方式实现，会让 redux 更纯。

### 4. Redux-saga

> It is a Redux middleware for handling side effects. —— Yassine Elouafi

Redux-saga 是 redux 的中间件，主要就是用来处理副作用（异步任务）。

#### 特点：

* sages 采用 Generator 函数来 yield Effects（包含指令的文本对象）
* Generator 函数的作用是可以暂停执行，再次执行的时候从上次暂停的地方继续执行
* Effect 是一个简单的对象，该对象包含了一些给 middleware 解释执行的信息。
* 可以通过使用 effects API 如 fork，call，take，put，cancel 等来创建 Effect。

#### 与 redux-thunk 不同之处：

* 将所有的异步流程控制都移入到了 sagas，UI 组件不用执行业务逻辑，只需 dispatch action 就行，增强组件复用性。
* UI 组件不用执行业务逻辑，只需 dispatch 一个 plain Object 的 action。
* 借助 Generator 函数和自身的 effect 实现复杂控制流

#### 缺点：

* 概念太多，不容易理解，上手成本高
* 在业务中需要写很多 saga

Ps: saga 本意是表示使用分布式事务管理长期运行的业务流程。

> The term saga, in relation to distributed systems, was originally defined in the paper ["Sagas"](https://link.juejin.cn/?target=http%3A%2F%2Fwww.cs.cornell.edu%2Fandru%2Fcs711%2F2002fa%2Freading%2Fsagas.pdf) by Hector Garcia-Molina and Kenneth Salem. This paper proposes a mechanism that it calls a saga as an alternative to using a distributed transaction for managing a long-running business process.The paper recognizes that business processes are often comprised of multiple steps, each of which involves a transaction, and that overall consistency can be achieved by grouping these individual transactions into a distributed transaction.

### 5. MobX

#### 核心思想

MobX背后的哲学很简单: 任何源自应用状态的东西都应该自动地获得。

MobX 支持单向数据流，也就是动作改变状态，而状态的改变会更新所有受影响的视图。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3c8715e78e6a4018beaa11e8ddcc2ba6\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

特点： 不同于其他的框架，MobX 对于如何处理用户事件是完全自由的。可以以最直观、最简单的方式来处理事件，直接修改 state。最后全部归纳为: 状态应该以某种方式来更新。

当状态更新后，MobX 会以一种高效且无障碍的方式处理好剩下的事情。像下面如此简单的语句，已经足够用来自动更新用户界面了。

从技术上层面来讲，并不需要触发事件、调用分派程序或者类似的工作。归根究底 React 组件只是状态的华丽展示，而状态的衍生由 MobX 来管理。

### 6. Vuex

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a644d25dcfb34520a136d42ff511a672\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```js
const store = createStore({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

Ps: Vuex 中为什么把异步操作封装在 action，把同步操作放在 mutations？

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c021625813834bac9934c275d7165728\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 7. dva

#### 数据流向

数据的改变发生通常是通过用户交互行为或者浏览器行为（如路由跳转等）触发的，当此类行为会改变数据的时候可以通过 dispatch 发起一个 action，如果是同步行为会直接通过 Reducers 改变 State ，如果是异步行为（副作用）会先触发 Effects 然后流向 Reducers 最终改变 State。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3405094eba93459f8b320fa8cca7d6d8\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```js
app.model({
  namespace: 'count',
  state: {
    record: 0,
    current: 0,
  },
  reducers: {
    add(state) {
      const newCurrent = state.current + 1;
      return { ...state,
        record: newCurrent > state.record ? newCurrent : state.record,
        current: newCurrent,
      };
    },
    minus(state) {
      return { ...state, current: state.current - 1};
    },
  },
  effects: {
    *add(action, { call, put }) {
      yield call(delay, 1000);
      yield put({ type: 'minus' });
    },
  },
  subscriptions: {
    keyboardWatcher({ dispatch }) {
      key('⌘+up, ctrl+up', () => { dispatch({type:'add'}) });
    },
  },
});
```

对比 vuex 和 dva ，我们可以发现，这两者的书写样式还是比较相似的。也是相对来说比较友好的一种方式。

### 8. Hooks 时代的 React 状态管理方案

随着 Hooks 的发布，React 社区又掀起了一波基于 Hooks 的造轮子大潮。

在 React Europe 2020 Conference上，Facebook 软件工程师 Dave McCabe 介绍了一个新的状态管理库Recoil。Recoil 现在还处于实验阶段，当前最新版本为 0.2.0，现在已经在 Facebook 一些内部产品中用于生产环境。

像 Redux、Mobx 本身虽然提供了强大的状态管理能力，但是使用的成本非常高，需要编写大量冗长的代码，另外像异步处理或缓存计算也不是这些库本身的能力，甚至需要借助其他的外部库。并且，它们并不能访问 React内部的调度程序，而 Recoil 在后台使用 React 本身的状态，在未来还能提供并发模式这样的能力。

毕竟是 Facebook 官方推出的状态管理框架，其主打的是高性能以及可以利用 React 内部的调度机制，包括其承诺即将会支持的并发模式，所以还是值得期待的。 设计理念

场景：有 List 和 Canvas 两个组件，List 中一个节点更新后，Canvas 中的节点也对应更新。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f81dd59eb5c0487c818485985501a519\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

常规则做法是将一个state通过父组件分发给List和Canvas两个组件，显然这样的话每次state改变后 所有节点都会全量更新。

Recoil 本身就是为了解决 React 全局数据流管理的问题，采用分散管理原子状态的设计模式。改变一个原子状态只会渲染特定的子组件，并不会让整个父组件重新渲染。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a1808cb42524e6eb7a648fdc0d06ace\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### 核心概念

* Atoms

Atom 是最小状态单元。它们可以被订阅和更新：当它更新时，所有订阅它的组件都会使用新数据重绘；它可以在运行时创建；它也可以在局部状态使用；同一个 Atom 可以被多个组件使用与共享。

* Selectors

Selector 是一个入参为 Atom 或者其他 Selector 的纯函数。当它的上游 Atom 或者 Selector 更新时，它会进行重新计算。Selector 可以像 Atom 一样被组件订阅，当它更新时，订阅它的组件将会重新渲染。 Selector 通常用于计算一些基于原始状态的派生数据。因为不需要使用 reducer 来保证数据的一致性和有效性，所以可以避免冗余数据。我们使用 Atom 保存一点原始状态，其他数据都是在其基础上计算得来的。因为 Selector 会追踪使用它们的组件以及它们依赖的数据状态，所以函数式编程会比较高效。

* RecoilRoot

组件使用 Recoil 状态之前需要在它的外面包裹一层RecoilRoot组件，可以直接短平快地放在根组件外面。

* useRecoilState

基于 Atom 和 Selector 的 Hooks，返回对应的 state 和修改 state 的方法。

```js
import {
  atom,
  selector,
  RecoilRoot,
  useRecoilState,
  useRecoilValue
} from "recoil";
import React from "react";

// atom
const fontSizeState = atom({
  key: "fontSizeState",
  default: 14
});

// selector
const fontSizeLabelState = selector({
  key: "fontSizeLabelState",
  get: ({ get }) => {
    const fontSize = get(fontSizeState);
    const unit = "px";

    return `${fontSize}${unit}`;
  }
});

function FontButton() {
  const [fontSize, setFontSize] = useRecoilState(fontSizeState);
  const fontSizeLabel = useRecoilValue(fontSizeLabelState);

  return (
    <>
      <div>当前字号(atom): {fontSize}</div>
      <div>当前字号(selector): {fontSizeLabel}</div>

      <button onClick={() => setFontSize(fontSize + 1)} style={{ fontSize }}>
        增大字号
      </button>
    </>
  );
}

function Text() {
  const [fontSize, setFontSize] = useRecoilState(fontSizeState);
  return <p style={{ fontSize }}>这里的字号会同步增大:{fontSize}</p>;
}

// RecoilRoot
export default function App() {
  return (
    <RecoilRoot>
      <FontButton />
      <Text />
    </RecoilRoot>
  );
}
```

## 五、参考

* [为什么要做状态管理](https://zhuanlan.zhihu.com/p/140073055)
* [前端状态管理](https://juejin.cn/post/6949926185547595812)
* [理解了状态管理，就理解了前端开发的核心](https://developer.51cto.com/article/707931.html)
* [浅析前端状态管理](http://kmanong.top/kmn/qxw/form/article?id=9642\&cate=85)
