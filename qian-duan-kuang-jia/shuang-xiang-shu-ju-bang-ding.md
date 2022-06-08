# 双向数据绑定

## 一、引言

在前一篇文章中我们知道 MVVM 由以下三个内容组成：

* View：界面
* Model：数据模型
* ViewModel：作为桥梁负责沟通 View 和 Model

![MVVM](https://cdn.ttgtmedia.com/rms/onlineimages/whatis-model\_view\_viewmodel.png)

在 MVVM 中，最核心的是 View 和 ViewModel 之间的双向数据绑定。就是指数据的传递有两个方向：

* 从数据到视图，数据变化时，视图也变化。
* 从视图到数据，例如 input 框的内容变化时，JS 维护的数据也会变化。

若要实现以上的数据传递，我们需要去监听数据变化和视图的变化，才能做出相应改变。

MVVM 双向数据绑定在Angular1.x版本的时候通过的是**脏值检测**来处理。而现在无论是 React 还是 Vue 还是最新的 Angular，其实实现方式都更相近了，那就是通过**数据劫持+发布订阅模式。**

真正实现靠的是 ES5 中的 **Object.defineProperty**，兼容性问题使得这些框架只能支持较新版本的浏览器。在 Vue 3 中则是基于 **Proxy** 来实现的。

## 二、脏值检测 <a href="#zang-shu-ju-jian-ce" id="zang-shu-ju-jian-ce"></a>

当触发了指定事件后会进入脏数据检测，这时会调用 `$digest` 循环遍历所有的数据观察者，判断当前值是否和先前的值有区别。若有区别，调用 `$watch` 函数， 然后再调用 `$digest` 循环直到发现没有变化。循环至少两次，至多十次（PS：随框架升级会有偏差）。

脏数据检测虽存在低效的问题，但不关心数据是通过什么方式改变的，都可以完成任务；但是，这在 Vue 中的双向绑定是存在问题的。并且脏数据检测可以实现批量检测出更新的值，再去统一更新 UI， 大大减少对 DOM 操作的次数。因此，低效也是相对的，这就仁者见仁，智者见智啦。

## 三、数据劫持 <a href="#shu-ju-jie-chi" id="shu-ju-jie-chi"></a>

在 JavaScript 中通过静态方法 `Object.defineProperty(obj, prop, descriptor)` 直接在对象上定义一个新的属性，或者修改已有的属性，并返回该对象。随后当需要调用该属性时使用 get 方法；给这个属性赋值时，使用 set 方法来实现效果。

```javascript
var o = {}; // 创建一个新对象

// 在对象中添加一个属性与存取描述符的示例
var messageValue;
Object.defineProperty(o, "message", {
  get : function(){
    return messageValue;
  },
  set : function(newValue){
    messageValue = newValue;
  },
});
```

以上代码，我们就为对象 o 定义了属性 message。同时在 o.message 时会调用 message 对应的 get 方法，在 o.message = "some message" 时会调用 message 的 set 方法来实现。可以预见，我们可以在某个属性被设置修改时，执行我们的特定代码，我们正在监视某些属性的变化。有了这个监听，就可以实现当模型改变时，去更新对象的视图部分。

以上的代码，在设计上称之为：数据劫持。这个设计也是 MVVM 的核心原理之一。

> 关于 `Object.defineProperty` 大家可以参考：[Object.defineProperty](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global\_Objects/Object/defineProperty)。除此之外 ES6 的 Proxy 也可以完成此项功能。vue3 就会使用 Proxy 代替 defineProperty。

## 四、发布-订阅模式 <a href="#undefined" id="undefined"></a>

发布-订阅模式 和 观察者模式 很像，但是不同的是，观察者模式只有两个角色，且 subject 与 observers 间存在一对多关系。当一个对象被修改时，则会自动通知依赖它的对象，即 obsevers 是知道 subject 的事件，属于行为型模式。但是在 发布-订阅 模式中，publisher、subscriber 不直接取得联系，通过类似于中间件（event bus）的帮助下代理事件通信，传入消息并完成相应的分发。[更多点此](https://hackernoon.com/observer-vs-pub-sub-pattern-50d3b27f838c)\


![发布订阅模式](https://www.zairesinatra.com/content/images/2021/09/------pubscrib.png)

## 五、实现

下面以 Vue 为例子，来看看其原理以及如何实现一个双向数据绑定。[​](https://febook.hzfe.org/awesome-interview/book1/frame-vue-data-binding#1-%E5%89%8D%E7%BD%AE%E6%A6%82%E5%BF%B5)在详细说明原理之前，需要对以下概念有一定的认知：

1. Dep：实现发布订阅模式的模块。
2. Watcher：订阅更新和触发视图更新的模块。

![Vue 双向数据绑定](https://user-images.githubusercontent.com/3984824/132991936-828bba61-2c87-4ee2-acf0-ffef294760fb.png)

上图是 Vue 官网描述 Vue 内数据变化与发布更新的流程，我们以响应式对象、依赖收集、数据更新的顺序详细说明整个过程。

### **1. 响应式对象**[**​**](https://febook.hzfe.org/awesome-interview/book1/frame-vue-data-binding#21-%E5%93%8D%E5%BA%94%E5%BC%8F%E5%AF%B9%E8%B1%A1)

Vue2 通过 `Object.defineProperty`，Vue3 通过 Proxy 来劫持 state 中各个属性的 getter、setter。其中 getter 中主要是通过 Dep 收集依赖这个属性的订阅者，setter 中则是在属性变化后通知 Dep 收集到的订阅者，派发更新。

以下是 Dep 的伪代码：

```javascript
export default class Dep {
  static target?: Watcher;
  subs: Array<Watcher>;

  constructor () {
    this.subs = []
  }

  /**
   * 添加订阅者（watcher）
   **/
  addSub (sub: Watcher) {
    this.subs.push(sub)
  }

  /**
   * 移除订阅者（watcher）
   **/
  removeSub (sub: Watcher) {
    remove(this.subs, sub)
  }

  /**
   * 添加订阅（调用订阅者上的 addDep 方法）
   **/
  depend () {
    if (Dep.target) {
      Dep.target.addDep(this)
    }
  }

  /**
   * 遍历通知订阅者更新
   **/
  notify () {
    const subs = this.subs.slice()
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update()
    }
  }
}

Dep.target = null
```

以下是生成响应式对象的伪代码：

```javascript
/**
 * 生成响应式对象
 * 为了方便理解，以下代码略有修改，省略了部分不相关内容
 */
export function defineReactive (
  obj: Object,
  key: string,
  val: any,
) {
  const dep = new Dep()
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      // 每次 get 时如果有订阅者则添加订阅
      if (Dep.target) {
        dep.depend()
      }
      return val
    },
    set: function reactiveSetter (newVal) {
      val = newVal
      // 每次更新数据之后广播更新
      dep.notify()
    }
  })
}
```

### **2. 依赖收集**[**​**](https://febook.hzfe.org/awesome-interview/book1/frame-vue-data-binding#22-%E4%BE%9D%E8%B5%96%E6%94%B6%E9%9B%86)

Vue 会在需要使用到属性的地方新建一个 Watcher 的实例 watcher，watcher 实例化时会读取对应属性的内容，从而触发 1.1 中的 getter，将 watcher 注册进 Dep 中。

以下是 Watcher 的伪代码：

```javascript
/**
 * 为了方便理解，以下代码略有修改，省略了部分不相关内容
 */
export default class Watcher {
  vm: Component;
  getter: Function;
  value: any;
  cb: Function;

  constructor (
    vm: Component,
    exp: string
  ) {
    this.vm = vm
    this.cb = cb
    // 获取表达式对应的属性的 getter
    this.getter = parsePath(exp)
    this.value = this.get()
  }

  /**
   * 获取最新的值
   **/
  get () {
    // 将 Dep 的当前订阅者指向当前 watcher
    Dep.target = this
    let value
    const vm = this.vm
    // 获取对应属性值
    value = this.getter.call(vm, vm)
    // 清空 Dep 当前订阅者
    Dep.target = null
    return value
  }

  /**
   * 订阅
   **/
  addDep (dep: Dep) {
    // 将当前 watcher 添加到 Dep 的订阅者列表中
    dep.addSub(this)
  }

  /**
   * 更新视图
   **/
  update () {
    const value = this.get()
    const oldValue = this.value
    // 调用 callback 更新视图
    this.cb.call(this.vm, value, oldValue)
  }
}
```

### **3. 数据更新**[**​**](https://febook.hzfe.org/awesome-interview/book1/frame-vue-data-binding#23-%E6%95%B0%E6%8D%AE%E6%9B%B4%E6%96%B0)

state 属性更新时会触发属性的 setter，setter 中会触发 Dep 的更新，Dep 通知收集到的 watcher 更新，watcher 获取到更新的数据之后触发更新视图。

以上实现了一个简单的双向绑定，核心思路为手动触发一次属性的 getter 来实现发布订阅的添加。

## 六、Proxy vs Object.defineProperty <a href="#proxy-yu-objectdefineproperty-dui-bi" id="proxy-yu-objectdefineproperty-dui-bi"></a>

### 1. Object.defineProperty

`Object.defineProperty` 虽然已实现双向绑定，但仍存在缺陷。

* 只能对属性进行数据劫持，需要深度遍历整个对象
* 无法监听到数组的数据变化

虽然 Vue 中确实能检测到数组数据的变化，但其实是使用了 hack 办法，并且仍存在缺陷。

```javascript
const arrayProto = Array.prototype
export const arrayMethods = Object.create(arrayProto)

// hack 以下几个函数
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]

methodsToPatch.forEach(function(method) {
  // 获得原生函数
  const original = arrayProto[method]

  def(arrayMethods, method, function mutator(...args) {
    // 调用原生函数
    const result = original.apply(this, args)
    const ob = this.__ob__
    let inserted

    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }

    if (inserted) {
      ob.observeArray(inserted)
    }

    // 触发更新
    ob.dep.notify()

    return result
  })
})
```

### 2. Proxy

反观 Proxy 就没以上的问题，原生支持监听数组变化，并且可直接对整个对象进行拦截。因此，在 Vue3 中使用 Proxy 代替 Object.defineProperty。

```javascript
const onWatch = (object, setBind, getLogger) => {
  const handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver)
    },
    set(target, property, receiver) {
      setBind(value)
      return Reflect.set(target, property, receiver)
    }
  }

  return new Proxy(object, handler)
}

let value
const object = { a: 1 }
const p = onWatch(
  object,
  v => {
    value = v
  },
  (target, property) => {
    console.log(`Get '${property}' = ${target[property]}`)
  }
)

p.a = 2 // bind `value` to `2`
p.a // -> Get 'a' = 2
```

### 3. 比较[​](https://febook.hzfe.org/awesome-interview/book1/frame-vue-data-binding#4-vue2-%E4%B8%8E-vue3-%E7%9A%84%E5%B7%AE%E5%BC%82)

Vue2 与 Vue3 数据绑定机制的主要差异是劫持方式。Vue2 使用的是 `Object.defineProperty` 而 Vue3 使用的是 `Proxy`。`Proxy` 可以创建一个对象的代理，从而实现对这个对象基本操作的拦截和自定义。

| 特性         | defineProperty | Proxy |
| ---------- | -------------- | ----- |
| 劫持新创建属性    | 否              | 是     |
| 劫持数组变化     | 否              | 是     |
| 劫持索引修改数组元素 | 否              | 是     |
| 兼容性        | IE8及以上         | 不支持IE |

由于 Vue 3 中改用 Proxy 实现数据劫持，Vue 2 中的 `Vue.set/vm.$set` 在 Vue 3 中被移除。

## 七、参考 <a href="#lu-you-yuan-li" id="lu-you-yuan-li"></a>

* [Vue 的数据绑定机制](https://febook.hzfe.org/awesome-interview/book1/frame-vue-data-binding)
* [框架通识 - MVVM](https://www.qiuzi.fun/front-end/framework.html#mvvm)
* [双向数据绑定原理](https://www.zairesinatra.com/vuebind/)
* [MVVM 双向数据绑定之核心原理](https://zhuanlan.zhihu.com/p/62342112)
* [耽误你的十分钟，让MVVM原理还给你](https://juejin.cn/post/6844903586103558158)
