# 变化检测

## 一、引言

变化检测是前端框架中很有趣的一部分内容，各个前端的框架也都有自己的一套方案，一般情况下我们不太需要过多的了解变化检测，因为框架已经帮我们完成了大部分的工作。不过随着我们深入的使用框架，我们会发现我们很难避免的要去了解变化检测，了解变化检测可以帮助我们更好的理解框架、排查错误、进行性能优化等等。

什么是变化检测，简单的来说，变化检测就是通过检测视图与状态之间的变化，在状态发生了变化后，帮助我们更新视图，这种将视图和我们的数据同步的机制就叫变化检测。

![变化检测](https://atlas-rc.pingcode.com/files/public/61c5293ce429e861587f5e2c/origin-url)

## 二、变化检测触发时机

> 我们了解了什么是变化检测，那何时触发变化检测呢？​我们可以看看下面这两个简单的Demo

Demo1:

一个计数器组件，点击按钮Count会一直加 1

```
@Component({
  selector: "app-counter",
  template: `
    Count：{{ count }}
    <br />
    <button (click)="increase()">Increase</button>
  `,
})
export class CounterComponent {
  count = 0;

  constructor() {}

  increase() {
    this.count = this.count + 1;
  }
}

```

Demo2：

一个Todo List的组件，通过Http获取数据后渲染到页面

```
  @Component({
    selector: "app-todos",
    template: ` <li *ngFor="let item of todos">{{ item.titme }}</li> `,
  })
  export class TodosComponent implements OnInit {
    public todos: TodoItem[] = [];

    constructor(private http: HttpClient) {}

    ngOnInit() {
      this.http.get<TodoItem[]>("/api/todos").subscribe((todos: TodoItem[]) => {
        this.todos = todos;
      });
    }
  }
```

从上面的两个 Demo 中我们发现，在两种情况下触发了变化检测：

* 点击事件发生时
* 通过 http 请求远程数据时

仔细思考下，这两种触发的方式有什么共同点呢? 我们发现这两种方式都是异步操作，所以我们可以得出一个结论： **只要发生了异步操作，Angular 就会认为有状态可能发生变化了，然后就会进行变化检测。**

这个时候可能大家会想到 `setTimeout` ​ `setInterval` ​ ，是的，它们同样也会触发变化检测。

```
@Component({
  selector: "app-counter",
  template: `
    Count：{{ count }}
    <br />
    <button (click)="increase()">Increase</button>
  `,
})
export class CounterComponent implements OnInit {
  count = 0;

  constructor() {}
  
  ngOnInit(){
    setTimeout(()=>{
       this.count= 10;
    });
  }

  increase() {
    this.count = this.count + 1;
  }
}
```

简而言之，如果发生以下事件之一，Angular 将触发变化检测：

* 任何浏览器事件（click、keydown 等）
* `setInterval()`  和  `setTimeout()`
* HTTP 通过  `XMLHttpRequest` ​ 进行请求 ​

## 三、Angular 如何订阅异步事件执行变化检测

> 刚才我们了解到，只要发生了异步操作，Angular 就会进行变化检测，那 Angular 又是如何订阅到异步事件的状态，从而触发变化检测的呢？这里我们就要聊一聊 zone.js 了。

### 1. Zone.js

Zone.js 提供了一种称为 \*\* 区域（Zone） \*\* 的机制，用于封装和拦截浏览器中的异步活动、它还提供 **异步生命周期的钩子** 和 **统一的异步错误处理机制。**

Zone.js 是通过 **Monkey Patching（猴子补丁）** 的方式来对浏览器中的常见方法和元素进行拦截，例如 `setTimeout` 和 `HTMLElement.prototype.onclick` ​。Angular 在启动时会利用 zone.js 修补几个低级浏览器 API，从而实现异步事件的捕获，并在捕获时间后调用变化检测。

下面用一段简化的代码来模拟一下替换 setTimeout 的过程：

```
function setTimeoutPatch() {
  // 存储原始的setTimeout
  var originSetTimeout = window['setTimeout'];
  // 对浏览器原生方法的包裹封装
  window.setTimeout = function () {
      return global['zone']['setTimeout'].apply(global.zone, arguments);
  };
  // 创建包裹方法，提供给上面重写后的setTimeout使用Ï
  Zone.prototype['setTimeout'] = function (fn, delay) {
  	// 先调用原始方法
    originSetTimeout.apply(window, arguments);
    // 执行完原始方法后就可以做其他拦截后需要进行的操作了
    ...
   };
}
```

### 2. NgZone

Zone.js 提供了一个全局区域，可以被 fork 和扩展以进一步封装/隔离异步行为，Angular 通过创建一个fork并使用自己的行为扩展它，通常来说， 在 Angular APP 中，每个 Task 都会在 Angular 的 Zone 中运行，这个 Zone 被称为  `NgZone` 。一个 Angular APP 中只存在一个 Angular Zone， **而变更检测只会由运行于这个 \*\* `**NgZone**` \*\* 中的异步操作触发** 。

简单的理解就是： **Angular 通过 Zone.js 创建了一个自己的区域并称之为 NgZone，Angular 应用中所有的异步操作都运行在这个区域中。**

## 四、变化检测是如何工作的

我们了解 Angular 的核心是 **组件化** ，组件的嵌套会使得最终形成一棵 **组件树** 。

![](https://atlas-rc.pingcode.com/files/public/61c1a1a9e429e861587f5d2a/origin-url)

Angular 在生成组件的同时，还会为每一个组件生成一个变化检测器 `changeDetector` ​，用来记录组件的数据变化状态，由于一个 Component 会对应一个 `changeDetector` ，所以 `changeDetector` 同样也是一个树状结构的组织。

![](https://atlas-rc.pingcode.com/files/public/619f6b4855b01e7f062d4918/origin-url)

在组件中我们可以通过注入 `ChangeDetectorRef` ​ ​来获取组件的 `changeDetector` ​

```
@Component({
  selector: "app-todos",
  ...
})
export class TodosComponent{
  constructor(cdr: ChangeDetectorRef) {}
}
```

我们在创建一个 Angular 应用 后，Angular 会同时创建一个 `ApplicationRef` ​ 的实例，这个实例代表的就是我们当前创建的这个 Angular 应用的实例。 `ApplicationRef` ​创建的同时，会订阅 ngZone 中的 `onMicrotaskEmpty` ​ 事件，在所有的微任务完成后调用所有的视图的 `detectChanges()` ​ 来执行变化检测。

​下是简化的代码：

```
class ApplicationRef {
  // ViewRef 是继承于 ChangeDetectorRef 的
  _views: ViewRef[] = [];
  constructor(private _zone: NgZone) {
    this._zone.onMicrotaskEmpty.subscribe({
      next: () => {
        this._zone.run(() => {
          this.tick();
        });
      },
    });
  }

  // 执行变化检测
  tick() {
    for (let view of this._views) {
      view.detectChanges();
    }
  }
}
```

### 1. 单向数据流

**什么是单向数据流？**

刚才我们说了每次触发变化检测，都会从根组件开始，沿着整棵组件树从上到下的执行每个组件的变更检测，默认情况下，直到最后一个叶子 Component 组件完成变更检测达到稳定状态。在这个过程中，一但父组件完成变更检测以后，在下一次事件触发变更检测之前，它的子孙组件都不允许去更改父组件的变化检测相关属性状态的，这就是单向数据流。

我们看一个示例：

```
@Component({
  selector: "app-parent",
  template: `
    {{ title }}
    <app-child></app-child>
  `, 
})
export class ParentComponent {
  title = "我的父组件";
}

@Component({
  selector: "app-child",
  template: ``, 
})
export class ChildComponent implements AfterViewInit {
  constructor(private parent: ParentComponent) {}

  ngAfterViewInit(): void {
    this.parent.title = "被修改的标题";
  }
}
```

![image.png](https://atlas-rc.pingcode.com/files/public/61c3f0b7e429e861587f5db6/origin-url)

**为什么出现这个错误呢？**

这是因为我们违反了单向数据流，ParentComponent​ 完成变化检测达到稳定状态后，ChildComponent 又改变了 ParentComponent 的数据使得 ParentComponent 需要再次被检查，这是不被推荐的数据处理方式。在开发模式下，Angular 会进行二次检查，如果出现上述情况，二次检查就会报错： ExpressionChangedAfterItHasBeenCheckedError ，在生产环境中，则只会执行一次检查。

并不是在所有的生命周期去调用都会报错，我们把刚才的示例修改一下：

```
@Component({
  selector: "app-child",
  template: ``, 
})
export class ChildComponent implements OnInit {
  constructor(private parent: ParentComponent) {}

  ngOnInit(): void {
    this.parent.title = "被修改的标题";
  }
}
```

修改后的代码运行正常，这是为什么呢？这里要说一下Angular检测执行的顺序：

1. 更新所有子子组件绑定的属性
2. 调用所有子组件生命周期的钩子 OnChanges, OnInit, DoCheck ，AfterContentInit
3. 更新当前组件的DOM
4. 调用子组件的变换检测
5. 调用所有子组件的生命周期钩子 ngAfterViewInit

`ngAfterViewInit` 是在变化检测之后执行的，在执行变化检测后我们更改了父组件的数据，在Angular执行开发模式下的第二次检查时，发现与上一次的值不一致，所以报错，而 `ngOnInit` ​的执行在变化检测之前，所以一切正常。

这里提一下AngularJS，AngularJS采用的是双向数据流，错综复杂的数据流使得它不得不多次检查，使得数据最终趋向稳定。理论上，数据可能永远不稳定。AngularJS的策略是，脏检查超过10次，就认为程序有问题，不再进行检查。

![](https://atlas-rc.pingcode.com/files/public/61c3ede8e429e861587f5db4/origin-url)

## 五、变化检测的性能

刚才我们聊了变化检测的工作流程，接下来我想说的是变化检测的性能， **默认情况下，当我们的组件中某个值发生了变化触发了变化检测，那么Angular会从上往下检查所有的组件。** 不过Angular对每个组件进行更改检测的速度非常快，因为它可以使用 [内联缓存](https://link.juejin.cn/?target=https%3A%2F%2Fmrale.ph%2Fblog%2F2012%2F06%2F03%2Fexplaining-js-vms-in-js-inline-caches.html) 在几毫秒内执行数千次检查，其中内联缓存可生成对 VM 友好代码。

尽管 Angular 进行了大量优化，但是遇到了大型应用，变化检测的性能仍然会下降，所以我们还需要用一些其他的方式来优化我们的应用。

## 六、变化检测的策略

Angular 提供了两种运行变更检测的策略：

* `Default`
* `OnPush`

### 1. Default 策略

默认情况下，Angular 使用 `ChangeDetectionStrategy.Default` 变更检测策略，每次事件触发变化检测（如用户事件、计时器、XHR、promise 等）时，此默认策略都会从上到下检查组件树中的每个组件。这种对组件的依赖关系不做任何假设的保守检查方式称为 **脏检查** ，这种策略在我们应用组件过多时会对我们的应用产生性能的影响。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/78e622a66ef74aa2b8bcc0a68e0a7602\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2. OnPush 策略

Angular 还提供了一种 `OnPush` 策略，我们可以修改组件装饰器的 `changeDetection` ​来更改变化检测的策略

```
@Component({
    selector: 'app-demo',
    // 设置变化检测的策略
    changeDetection: ChangeDetectionStrategy.OnPush,
    template: ...
})
export class DemoComponent {
    ...
}
```

设置为 OnPush 策略后，Angular 每次触发变化检测后会跳过该组件和该组件的所以子组件变化检测

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a04c0a1c85194163a34d31c7446e4be6\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

_OnPush模式下变化检测流程_

在 `OnPush` 策略下，只有以下这几种情况才会触发组件的变化检测：

* 输入值（@Input）更改
* 当前组件或子组件之一触发了事件
* 手动触发变化检测
* 使用 async 管道后， observable 值发生了变化

#### 输入值（@Input）更改

在默认的变更检测策略中，Angular 将在 `@Input()` 数据发生更改或修改时执行变化检测，使用该 `OnPush` 时，传入 `@Input()` ​ 的值 **必须是一个新的引用** 才会触发变化检测。

> JavaScript有两种数据类型，值类型和引用类型，值类型包括：number、string、boolean、null、undefined，引用类型包括：Object、Arrary、Function，值类型每次赋值都会分配新的空间，而引用类型比如Object，直接修改属性是引用是不会发生变化的，只有赋一个新的对象才会改变引用。

```
var a= 1;
var b = a;
b = 2;
console.log(a==b); // false

var obj1 = {a:1};
var obj2 = obj1;
obj2.a = 2;
console.log(obj1); // {a:2}
console.log(obj1 === obj2); //true

obj2= {...obj1};
console.log(obj1 === obj2); //false
```

#### 当前组件或子组件之一触发了事件

如果 `OnPush` 组件或其子组件之一触发事件，例如 click，则将触发变化检测（针对组件树中的所有组件）。

需要注意的是在 `OnPush` 策略中，以下操作不会触发变化检测：

* setTimeout()
* setInterval()
* Promise.resolve().then()
* this.http.get('...').subscribe() &#x20;

#### 手动触发变化检测

有三种手动触发更改检测的方法：

* ​​ \*\*detectChanges(): \*\* 它会触发当前组件和子组件的变化检测
* **markForCheck()：** 它不会触发变化检测，但是会把当前的OnPush组件和所以的父组件为OnPush的组件 \*\* 标记为需要检测状态\*\* ，在当前或者下一个变化检测周期进行检测
* **ApplicationRef.tick()** : 它会根据组件的变化检测策略，触发整个应用程序的更改检测

![](https://atlas-rc.pingcode.com/files/public/61a46b1c55b01e3fe92d49bc/origin-url)

可以通过 [在线Demo](https://link.juejin.cn/?target=https%3A%2F%2Fdanielwiehl.github.io%2Fedu-angular-change-detection%2F) ，更直观的了解这几种触发变化检测的方式。

**如何手动触发变更检测呢？**

通过引用$$变化检测对象ChangeDetectorRef\color{#13c078}{变化检测对象ChangeDetectorRef}变化检测对象ChangeDetectorRef$$，可以手动去操作变化检测。我们可以在组件中的通过依赖注入的方式来获取该对象：

```js
constructor(
   private changeRef: ChangeDetectorRef
){}
复制代码
```

此变更检测对象提供了如下所示的方法

```js
abstract class ChangeDetectorRef {

abstract markForCheck() : void 

abstract detectChanges() : void

abstract detach() : void

abstract reattach() : void 

abstract checkNoChanges() : void

}
复制代码
```

**markForCheck()**

，那么变化检测不会再次执行，除非手动调用该方法, 在程序中使用this.changeRef.markForCheck()$$只能引起一次\color{#13c078}{只能引起一次}只能引起一次$$变化检测，如要想要执行多次多次，则需要多次的运行这句话。具体的执行流程如下：

![屏幕快照 2021-08-02 下午7.40.02.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a9b0b658b4a14619b496796590e017cb\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**detectChanges()**

首先他的使用与是否使用了onPush无关，他是只在当前视图和它的子视图$$只运行一次变更检测\color{#13c078}{只运行一次变更检测}只运行一次变更检测$$，应用场景：明确知道有数据的更新，需要Angular执行变更检测的时候使用。

![屏幕快照 2021-08-03 上午11.21.25.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ea8316d8a8ac43d5a9e8dfbb895ec24b\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**detach()**

首先他的使用与是否使用了onPush无关，他是从变化检测树中$$分离变化检测器\color{#13c078}{分离变化检测器}分离变化检测器$$，该组件的变化检测器将不再执行变化检测，同时其子组件也不会执行检测，除非手动调用 reattach() 方法

**reattach()**

首先他的使用与是否使用了onPush无关，他是，使得该组件及其子组件都能执行变化检测，但是如果当前的组件的父组件$$没有启用变更检测\color{#13c078}{没有启用变更检测}没有启用变更检测$$（脏检查）的话，它将$$不起作用\color{#13c078}{不起作用}不起作用$$。

**使用async pipe**

当父组件的输入属性是用observable,那么除了使用this.changeRef.markForCheck()来进行变更检测，我们还可以在子组件中使用async pipe, 发出一个新值时，异步管道会将组件标记为要检查更改(其实也是调用了 this.changeRef.markForCheck())

```js
<p>{{addCount | async}}</p>
```



#### 使用 async 管道

内置的 `AsyncPipe` 订阅一个 observable 并返回它发出的最新值。

每次发出新值时的内部 `AsyncPipe` 调用 `markForCheck`

```
private _updateLatestValue(async: any, value: Object): void {
  if (async === this._obj) {
    this._latestValue = value;
    this._ref.markForCheck();
  }
}
```



## 七、减少变化检测次数

> 刚才我们聊了变化检测的策略，我们可以使用 `OnPush` ​的策略来优化我们的应用，那么这就够了吗？ 在我们实际的开发中还会有很多的场景，我们需要通过一些其他的方式来继续优化我们的应用。

**场景1：**

假如我们在实现一个回车搜索的功能：

```
@Component({
  selector: "app-enter",
  template: `<input #input type="text" />`,
})
export class EnterComponent implements AfterViewInit {
  @ViewChild("input", { read: ElementRef })
  private inputElementRef: any;

  constructor() {}

  ngAfterViewInit(): void {
    this.inputElementRef.nativeElement.addEventListener(
      "keydown",
      (event: KeyboardEvent) => {
        const keyCode = event.which || event.keyCode;
        if (keyCode === 13) {
          this.search();
        }
      }
    );
  }

  search() {
    // ...
  }
}
```

**大家从上面的示例中可以发现什么问题呢？**

我们知道事件会触发Angular的变化检测，在示例中绑定 keydown 事件后，每一次键盘输入都会触发变化检测，而这些变化检测大多数都是多余的检测，只有当按键为 Enter 时，才需要真正的进行变化检测。

在这种情况下，我们就可以利用 `NgZone.runOutsideAngular()` ​ 来减少变化检测的次数。

```
@Directive({
    selector: '[enter]'
})
export class ThyEnterDirective implements OnInit {
    @Output() enter = new EventEmitter();

    constructor(private ngZone: NgZone, private elementRef: ElementRef<HTMLElement>) {}

    ngOnInit(): void {
        // 包裹代码将运行在Zone区域之外
        this.ngZone.runOutsideAngular(() => {
            this.elementRef.nativeElement.addEventListener('keydown', (event: KeyboardEvent) => {
                const keyCode = event.which || event.keyCode;
                if (keyCode === 13) {
                    this.ngZone.run(() => {
                        this.enter.emit(event);
                    });
                }
            });
        });
    }
}
```

**场景2：**

假如我们使用 WebSocket 将大量数据从后端推送到前端，则相应的前端组件应仅每 10 秒更新一次。在这种情况下，我们可以通过调用 `detach()` 和手动触发它来停用更改检测 `detectChanges()` ：

```
constructor(private cdr: ChangeDetectorRef) {
    cdr.detach(); // 停用变化检测
    setInterval(() => {
      this.cdr.detectChanges(); // 手动触发变化检测
    }, 10 * 1000);
  }
复制代码
```

当然使用 `ngZone.runOutsideAngular()` ​ 也可以处理这种场景。

## 八、脱离 Zone.js 开发

之前我们说了Angular 可以自动帮我们进行变化检测，这主要是基于Zone.js来实现，那么很多人潜意识会任务Zone.js 就是 Angular 是一部分，Angular的 应用程序必须基于Zone.js，其实不然，如果我们对应用有极高的性能要求时，我们可以选择移除 Zone.js，移除Zone.js 将会提升应用的性能和打包的体积，不过带来的后果就是我们需要主要去调用变化检测。

### 1. 如何移除 Zone.js?

![image.png](https://atlas-rc.pingcode.com/files/public/61c16bc2e429e861587f5cef/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c16beae429e861587f5cf0/origin-url)

### 2. 手动调用变化检测

在 Ivy 之后，我们有一些新的API可以更方便的调用变化检测

![image.png](https://atlas-rc.pingcode.com/files/public/61c19e06e429e861587f5d23/origin-url)

\*\*ɵmarkDirty: \*\* 标记一个组件为 dirty 状态 (需要重新渲染) 并将在未来某个时间点安排一个变更检测

**ɵdetectChanges：** 因为某些效率方面的原因，内部文档不推荐使用  `ɵdetectChanges`  而推荐使用  `ɵmarkDirty` ​， `ɵdetectChanges` ​会触发组件以子组件的变更检测。

### 3. 移除后的性能

移除Zone.js后变化检测由应用自己来控制，极大的减少了不必要的变化检测次数，同时打包后的提及也减少了 36k

移除前：

![image.png](https://atlas-rc.pingcode.com/files/public/61c16e43e429e861587f5cf8/origin-url)

移除后：

![image.png](https://atlas-rc.pingcode.com/files/public/61c16e11e429e861587f5cf6/origin-url)

## 九、测试与变化检测

### 1. 组件绑定

我们先来看一个组件绑定的例子：

![image.png](https://atlas-rc.pingcode.com/files/public/61c1a9d1e429e861587f5d39/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c1a9eee429e861587f5d3a/origin-url)

按我们正常开发组件的想法，当看到这个示例的时候一定认为这个Case是Ok的，但是在运行测试后我们发现这个Case失败了。

在生产环境中，当 Angular 创建一个组件，就会自动进行变更检测。 **但是在测试中，** `**TestBed.createComponent()**` **​并不会进行变化检测，需要我们手动触发。**

修改一下上面的Case:

![image.png](https://atlas-rc.pingcode.com/files/public/61c1aa26e429e861587f5d3b/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c1aa33e429e861587f5d3c/origin-url)

从上面的示例中可以了解到，我们必须通过调用 `fixture.detectChanges()` 来告诉 TestBed 执行数据绑定。

如果我们在测试中动态改变了绑定值，同样也需要调用 `fixture.detectChanges()` 。

```
it("should update title", () => {
    component.title = 'Test Title';
    fixture.detectChanges();
    const h1 = fixture.nativeElement.querySelector("h1");
    expect(h1.textContent).toContain('Test Title');
});
复制代码
```

### 2. 自动变更检测

我们发现写测试过程中需要频繁的调用 `fixture.detectChanges()` ​，可能会觉得比较繁琐，那 Angular 可不可以在测试环境中自动运行变化检测呢？​

我们可以通过配置  `ComponentFixtureAutoDetect`  来实现

```
TestBed.configureTestingModule({
  declarations: [ BannerComponent ],
  providers: [
    { provide: ComponentFixtureAutoDetect, useValue: true }
  ]
});
复制代码
```

然后再回头看看刚才的示例：

![image.png](https://atlas-rc.pingcode.com/files/public/61c05327e429e861587f5c8b/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c0533ce429e861587f5c8c/origin-url)

上面的示例我们并没有调用 `fixture.detectChanges()` ​，但是测试依然通过了，这是因为我们开启了自动变化检测。

再看一个示例：

![image.png](https://atlas-rc.pingcode.com/files/public/61c05411e429e861587f5c90/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c0544ee429e861587f5c91/origin-url)

上面的示例中，我们在测试代码中动态修改了 title 的值，测试运行失败，这是因为 Angular 并不知道测试改变了组件， `ComponentFixtureAutoDetect`  只对异步操作进行自动变化检测，例如 Promise、setTimeout、click 等DOM事件等，如果我们手动更改了绑定值，我们依然还需要调用 `fixture.detectChanges()` ​ 来执行变化检测。

### **3. 常见的坑**

#### **ngModel**

![image.png](https://atlas-rc.pingcode.com/files/public/61c06fd5e429e861587f5c9a/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c06fe2e429e861587f5c9b/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c06febe429e861587f5c9c/origin-url)

上面这个示例，绑定值修改后调用了 `fixture.detectChanges()` ​, 但是运行测试后仍然报错，这是为什么呢？

![image.png](https://atlas-rc.pingcode.com/files/public/61c13b35e429e861587f5caf/origin-url)

查看Angular源码后我们发现 \*\* ngModel 的值是通过异步更新的\*\* ，执行 `fixture.detectChanges()` ​后虽然触发了变化检测，但是值还并未修改成功。

修改一下测试：

![image.png](https://atlas-rc.pingcode.com/files/public/61c13cf8e429e861587f5cb5/origin-url)

修改后我们将断言包裹在了 `fixture.whenStable()` ​ 中，然后测试通过，那 `whenStable()` ​ 是什么呢？

> whenStable(): Promise ： 当夹具稳定时解析的承诺 当事件已触发异步活动或异步变更检测后，可用此方法继续执行测试。

当然除了用 `fixture.whenStable()` ​ 我们也可以用 `tick()` ​ 来解决这个问题

![image.png](https://atlas-rc.pingcode.com/files/public/61c13ef4e429e861587f5cb8/origin-url)

> tick() ：为 fakeAsync Zone 中的计时器模拟异步时间流逝 在此函数开始时以及执行任何计时器回调之后，微任务队列就会耗尽

#### **测试 OnPush 组件**

![image.png](https://atlas-rc.pingcode.com/files/public/61c1490de429e861587f5cd3/origin-url)

![image.png](https://atlas-rc.pingcode.com/files/public/61c14956e429e861587f5cd4/origin-url)

上面这个示例，我们在修改属性后调用了 `fixture.detectChanges()` ​ ，但是测试未通过，这是为什么呢？我们发现这个示例与第一个示例唯一的区别就是这个组件是一个 `OnPush` 组件，之前我们说过默认变化检测会跳过 `OnPush` ​组件的，只有在特定的几种情况下才会触发变化检测的，遇到这种情况如何解决呢？​​

![image.png](https://atlas-rc.pingcode.com/files/public/61c14aa5e429e861587f5cdd/origin-url)

我们可以手动获取组件的 `ChangeDetectorRef` ​ 来主动触发变化检测。

## 十、延伸

### 1. 虚拟DOM与增量DOM

> Angular Ivy 是一个新的 Angular 渲染器，它与我们在主流框架中看到的任何东西都截然不同，因为它使用增量 DOM。 增量DOM是什么呢？它与虚拟Dom有什么不同呢？

#### 虚拟 DOM

首先说一下虚拟DOM，我们要了解在浏览器中，直接操作Dom是十分损耗性能的，而虚拟DOM 的主要概念是将 UI的虚拟表示保存在内存中，通过 Diff 操作对比当前内存和上次内存中视图的差异，从而减少不必要的Dom操作，只针对差异的Dom进行更改。

虚拟DOM执行流程：

1. 当 UI 发生变化时，将整个 UI 渲染到 Virtual DOM 中。
2. 计算先前和当前虚拟 DOM 表示之间的差异。
3. 使用更改更新真实的 DOM。

![](https://miro.medium.com/max/700/1\*8OCCATi8\_5HmWI1QpjrRNA.png)

**虚拟 DOM 的优点：**

* 高效的 Diff 算法。
* 简单且有助于提高性能。
* 没有 React 也可以使用
* 足够轻量
* 允许构建应用程序且不考虑状态转换

#### 增量 DOM

增量Dom的主要概念是将组件编译成一系列的指令，这些指令去创建DOM树并在数据更改时就地的更新它们。

![image.png](https://atlas-rc.pingcode.com/files/public/61c03e85e429e861587f5c69/origin-url)

例如：

```
@Component({
  selector: 'todos-cmp',
  template: `
    <div *ngFor="let t of todos|async">
        {{t.description}}
    </div>
  `
})
class TodosComponent {
  todos: Observable<Todo[]> = this.store.pipe(select('todos'));
  constructor(private store: Store<AppState>) {}
}
```

编译后：

```
var TodosComponent = /** @class */ (function () {
  function TodosComponent(store) {
    this.store = store;
    this.todos = this.store.pipe(select('todos'));
  }

  TodosComponent.ngComponentDef = defineComponent({
    type: TodosComponent,
    selectors: [["todos-cmp"]],
    factory: function TodosComponent_Factory(t) {
      return new (t || TodosComponent)(directiveInject(Store));
    },
    consts: 2,
    vars: 3,
    template: function TodosComponent_Template(rf, ctx) {
      if (rf & 1) { // create dom
        pipe(1, "async");
        template(0, TodosComponent_div_Template_0, 2, 1, null, _c0);
      } if (rf & 2) { // update dom
        elementProperty(0, "ngForOf", bind(pipeBind1(1, 1, ctx.todos)));
      }
    },
    encapsulation: 2
  });

  return TodosComponent;
}());
```

**增量DOM的优点：**

1. 渲染引擎可以被Tree Shakable，降低编译后的体积
2. 占用较低的内存

**为什么可渲染引擎可以被 Tree Shakable？**

**Tree Shaking 是指在编译目标代码时移除上下文中未引用的代码** ，增量 DOM 充分利用了这一点，因为它使用了基于指令的方法。正如示例所示，增量 DOM 在编译之前将每个组件编译成一组指令，这有助于识别未使用的指令。在 Tree Shakable 过程中，可以将这些未使用的的指令删除掉。

**减少内存的使用**

与虚拟 DOM 不同，增量 DOM 在重新呈现应用程序 UI 时不会生成真实 DOM 的副本。此外，如果应用程序 UI 没有变化，增量 DOM 就不会分配任何内存。大多数情况下，我们都是在没有任何重大修改的情况下重新呈现应用程序 UI。因此，按照这种方法可以极大的减少设备内存使用。

## 十一、参考

* [Angular 变化检测详解](https://juejin.cn/post/7046262799323889700)
* [深入理解 Angular 的变更检测](https://juejin.cn/post/6992113040560750623)
