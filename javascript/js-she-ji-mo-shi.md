# JS 设计模式

## 一、引言

在学习封装时，我们知道，封装很重要的一个核心就是要学会提炼可复用的公共逻辑。

而设计模式，就是大佬们在实践过中，逐渐提炼出来的一系列可复用的公共逻辑。设计模式是在运用面向对象思维解决实际问题时的解决方案，他们对于提高代码的可读性和可维护性具有重要的意义。因此，掌握设计模式，是对面向对象的进一步深入学习。

## 二、设计模式原则

提炼公共逻辑是一个非常大的概念，提炼出来的东西是否合理，是否具备可维护性，每个人对公共逻辑的理解不同，结果也会产生变化。特别是在对象与对象要进行复杂的交互时，对于公共逻辑的理解会产生更大的偏差。因此，对于面向对象的逻辑封装，前辈们总结出来了六大设计原则，来指导大家更优雅的使用设计模式。

![](https://images.xiaozhuanlan.com/photo/2020/1443f8fe6108a1af57da453f32382a09.image)

### 1. 单一职责原则 <a href="#section" id="section"></a>

一个类，只负责一项职责。一个方法尽量只干一件事。

例如，一个非常简单的案例，人要打球。合理的封装应该是，分别封装一个人的类，与球的类。这是我们很容易就能够区分出来的事情。单一原则的运用往往在潜移默化中，虽然你可能还没有听过这个原则，但是你已经在使用它了。

```javascript
class Person {}
class Ball {}
```

不合理的封装，就是把球的属性，放在 Person 对象里

```javascript
class Person {
  constructor(name, ballType) {
    this.name = name
    this.ball = ballType
  }
}
```

但是这样封装的扩展性就非常差，因为在实践中，人有许多类别，例如学生，教师，医生等，可能会设计不同的类。球也有羽毛球，篮球，乒乓球等等。人与球是一个多对多的关系，如果封装在一个类里，后续的扩展就会变得非常困难。

### 2. 里氏替换原则 <a href="#section-1" id="section-1"></a>

该原则针对的是父类与子类的替换关系。

最早是在 1988 年，由麻省理工学院的一位女士（Barbara Liskov）提出。该原则以 liskov 的名字命名。

任何使用父类实例的地方，能够使用子类实例完美替换。这就是里氏替换原则的核心。

例如，父类为 Person， 子类为学生 Student

```javascript
class Person {
  constructor(name) {
    this.name = name
  }
  run(t) {
    console.log(`${this.name} 跑了 ${t} 公里`);
  }
}

class Student extends Person {
  constructor(name, grade) {
    super(name)
    this.grade = grade
  }
}

const p1 = new Person('Tom')
p1.run(20)
const s1 = new Student('Tom')
s1.run(20)
```

父类实例 p1 调用 run 方法，与子类实例 s1 调用 run 方法，他们的效果是一模一样的。我们能够用 s1 替换掉 p1 而不会引发什么额外的问题。

我们知道，继承的本质是代码的复制，也就是说，只要我们不在子类中，重写父类已经完整实现的方法，就能够达到里氏替换原则想要的效果。

这里强调的是，子类一定只能是父类的扩展，而非在父类的基础上进行畸变和魔改。

如果一定需要重写父类的方法，那么也一定是在父类方法上进行扩展，要确保原有逻辑的稳定性。

很明显，该原则也是确保我们写的程序能具备强大的扩展性。

### 3. 依赖倒置原则 <a href="#section-2" id="section-2"></a>

依赖倒置原则的原始定义如下：

High-level modules should not depend on low-level modules. Both should depend on abstractions.\
**高层模块不应该依赖低层模块，两者都应该依赖其抽象**

Abstractions should not depend upon details. Details should depend upon abstractions.\
**抽象不应该依赖细节，细节应该依赖抽象**

这个理解起来有点困难，但是掌握之后威力巨大。

理解这个定义，首先需要理解三个概念，高层模块，底层模块，以及抽象。

有一个非常简单的场景，我看书。

这个场景里，高层模块就是 “我”，低层模块就是 “书”。

我们来使用代码定义这个场景，大概就是

```javascript
myself.read(book)
```

这里的 myself 与 book，就是我们要封装的两个类。

```javascript
// 低层模块
class Book {
  // 定义一个非常简单的方法
  getContent() {
    return '书籍的内容'
  }
}
```

低层模块是一个非常简单的类，他不依赖任何其他的类来实现自己的功能。

```javascript
import Book from './Book'

// 高层模块
class Person {
  // 此时需要传入一个 Book 的实例，用于获取书籍的内容
  read(book: Book) {
    console.log('我开始看书了')
    console.log(`书的内容是：${book.getContent()}`)
  }
}
```

关注此处 read 方法的实现。如果我们要做类型约束，那么期望传入的参数，就最好只能是 Book 类的实例，这样我们可以进一步确认运用时的准确性。于是，Person 类在封装时，就不可避免的依赖了底层模块 Book 类。

在实践中，这样的方式其实没有什么问题。

```javascript
const myself = new Person()
const book = new Book()

myself.read(book)
```

真实场景中，这样的实现，扩展性受到了限制。

我可能不仅仅只是想读书，还想要读报纸，读手机内容等等。那此时，read 方法的类型限制就很难受了

```javascript
import Book from './Book'
import Newspaper from './NewsPaper'
import Phone from './Phone'

// 高层模块
class Person {
  // 此时需要传入一个 Book 的实例，用于获取书籍的内容
  read(book: Book | Newspaper | Phone) {
    console.log('我开始看书了')
    console.log(`书的内容是：${book.getContent()}`)
  }
}
```

这里的改动，我们做了一件成本偏高的事情，就是修改了 Person 类。实践场景远不止如此简单。 Person 类的修改，也就意味着存在未知风险，相关的功能都需要重新验证一次。而 Person 类的依赖也越来越多，后期的调整成分本在逐渐变得很高。

那么有没有更好的方案，能够在扩展的时候，不改动 Person 类呢？当然有

这里我们需要引入抽象的概念。书、报纸、手机，他们虽然都是不同的类，有非常多的不一样，但是对于 Person 类来说，他们可以提炼出来一个抽象概念：读物。通俗一点说，就是**依据他们的共性，给他们分个类**。

有了读物这个抽象概念，事情就好办多了

先定义抽象类

```javascript
interface Reader {
  getContent: () => string
}
```

然后定义各自具体的读物

```javascript
class Book impements Reader {
  getContent() {
    return '返回具体的内容'
  }
}
```

最后定义 Person 类

```javascript
import Reader from './Reader'

// 高层模块
class Person {
  // 此时需要传入一个 Book 的实例，用于获取书籍的内容
  read(reader: Reader) {
    console.log('我开始看书了')
    console.log(`书的内容是：${reader.getContent()}`)
  }
}
```

此时我们发现，如果我们想要新增报纸这种读物时，就只需要新增报纸类「实现 Reader」即可。Person 类完全不需要任何改动，因为 Person 依赖的是读物。

于是 高层组件「Person」仅仅只依赖了抽象类「Reader」。Person，Book，Newspaper，Phone 得以解耦。

这其实也是面向接口编程。

在实践中，我们可以大量运用依赖倒置原则来提高代码的扩展性。

例如，将盒子、盆子、桶，提炼出一个抽象概念：容器，用于装水。

又例如，封装的地图组件，不明确依赖到底是百度地图还是高德地图或者是谷歌地图。而是将这些地图提炼出来一个抽象概念：地图。无论是什么地图，只需要提供我们组建需要的方法即可

### 4. 接口隔离原则 <a href="#section-3" id="section-3"></a>

定义：一个类，不应该依赖它不需要的接口。

一本书，可以抽象为读物，这个是基于能够提供内容的角度去做的分类。\
一本书，可以抽象为可燃物，这个是基于能够纸张能够燃烧的角度做的分类。

同样的对象，从不同的角度去分类，可以提炼出不同的抽象，也就是说可以提炼出不同的接口。

```javascript
interface Book {
  // 提供内容
  getContent: () => string,

  // 燃烧
  burn: () => void
}
```

但是对于 Person 对象来说，并不关注书的可燃烧属性。因此，当我们定义 Person 类的时候，依赖的抽象类，就不应该包含此处的 burn 方法。

虽然读物和可燃物，都是从书籍里提炼出来的，但是他们针对的场景不一样，因此就不应该把他们的抽象类合并在一起，而应该拆分开。

```javascript
// 读物
interface Reader {
  getContent: () => string
}
```

```javascript
可燃物
interface CombustibleMaterial {
  burn: () => void
}
```

然后根据不同的场景选择依赖即可。

当然，接口隔离原则的使用，一定要适度，要结合场景进行合理的拆分，如果拆分得过细，也就造成接口过多而维护困难的局面。

### 5. 迪米特法则 <a href="#section-4" id="section-4"></a>

迪米特法则要求的是：一个对象应该对其他的对象保持最少的了解。

通俗来说，就是要想办法降低类与类之间的耦合。如果类与类之间的关系太过于密切，那么，一个类发生了变化，就会对另外一个类造成更大的影响。

例如这样一个场景，一个集团下有许多分公司。

从需求上来说，集团需要计算公司每个员工的薪资情况，包括集团直属员工与各分公司员工。

那么我们在设计类的时候，这里的就包括两个类，一个集团公司 HeadOffice，一个分公司 BranchOffice。

对于集团公司来说，至少需要一个方法，就是该场景需要的计算薪资 calculate。

但是计算薪资，还需要获取到非常多的信息才能够正确的计算，例如员工基本信息，岗位信息，基本工资，提成方式等。

不合理的设计方式，就是把分公司员工的薪资计算交给集团公司类来做

```javascript
// 定义分公司类
class BranchOffice {
  // 获取员工列表，包含员工基本信息
  getEmployeeList() {
    return []
  }

  // 通过员工id进一步获取员工更多的信息，例如基本工资，绩效信息，提成方式等
  getEmployeeInfo(id) {
    return '员工信息'
  }
}
```

定义集团公司，还需要分公司薪资，因此此时必须依赖分公司，

```javascript
import BranchOffice from './BranchOffice'

// 定义集团公司类
class HeadOffice {
  // 获取集团公司员工列表，包含员工基本信息
  getEmployeeList() {}

  // 进一步获取员工信息
  getEmployeeInfo(id) {}

  // 设置员工薪资 -> 计算单个员工的信息
  setEmployeeSalary(useInfo) {}

  // 传入分公司对象
  calculate(office: BranchOffice) {
    // 计算集团的员工薪资
    const headEmployees = this.getEmployeeList()
    headEmployees.forEach((emp, i) => {
      const info = this.getEmployeeInfo(emp.id)
      this.setEmployeeSalary({...emp, ...info})
    })

    // 需要通过分公司，获取到分公司的员工信息，以及员工的详细信息
    const employees = office.getEmployeeList()
    employees.forEach((emp, i) => {
      const info = office.getEmployeeInfo(emp.id)
      this.setEmployeeSalary({...emp, ...info})
    })
  }
}
```

此时我们发现，在集团公司中，为了计算子公司员工的薪资，获取了很多只属于子公司的信息，例如子公司的员工列表，子公司的员工详情，那么集团公司与子公司就变得非常密切。如果子公司发生变化，有极大的风险会影响到集团公司的正常运行。

遵守迪米特法则的设计，是应该尽量少的让集团公司获取到子公司的信息。我们只需要在设计方法时，让子公司自己计算自己员工的薪资即可。

代码如下：

```javascript
// 定义分公司类
class BranchOffice {
  // 获取员工列表，包含员工基本信息
  getEmployeeList() {
    return []
  }

  // 通过员工id进一步获取员工更多的信息，例如基本工资，绩效信息，提成方式等
  getEmployeeInfo(id) {
    return {}
  }
  // 计算某一个员工的薪资
  setEmployeeSalary(useInfo) {}

  // 计算所有员工的薪资
  calAllEmployeeSalary() {
    const employees = this.getEmployeeList()
    employees.forEach(employee => {
      const info = this.getEmployeeInfo(employee.id)
      this.setEmployeeSalary({...employee, ...info})
    })
  }
}
```

```javascript
import BranchOffice from './BranchOffice'

// 定义集团公司类
class HeadOffice {
  // 获取集团公司员工列表，包含员工基本信息
  getEmployeeList() {}

  // 进一步获取员工信息
  getEmployeeInfo(id) {}

  // 设置员工薪资 -> 计算单个员工的信息
  setEmployeeSalary(useInfo) {}

  // 计算集团公司所有员工的薪资
  calAllEmployeeSalary() {
    const employees = this.getEmployeeList()
    employees.forEach(employee => {
      const info = this.getEmployeeInfo(employee.id)
      this.setEmployeeSalary({ ...employee, ...info })
    })
  }

  // 传入分公司对象
  // 计算所有员工，包括集团总部和分公司
  calculate(office: BranchOffice) {
    this.calAllEmployeeSalary()
    office.calAllEmployeeSalary()
  }
}
```

我们可以看到，优化之后，集团公司与分公司的耦合降低了，对于集团公司来说，只需要调用一下分公司计算薪资的方法即可。至于分公司如何计算，是否与集团公司的计算方式保持一致，这些都是集团公司并不关心的问题。因此，这样的设计也极大的提高了扩展性。

当然，我们还可以利用依赖倒置原则进一步优化。这里就不再扩展。

### 6. 开闭原则 <a href="#section-5" id="section-5"></a>

开闭原则是面向对象程序设计的终极目标。

上诉我们提到的所有的设计原则，以及之后我们还会学习到的各种设计模式，都在遵循开闭原则。

当需求变化时，如果直接修改旧代码，一个是成本很高，我们无法确保修改不会对之前的功能造成影响，甚至可能会对已有的功能造成破坏性修改，二个是风险很高，我们无法确保在改动过程中，是否会造成了一些错误。不好的设计，在软件迭代一年两年，就可能不得不进行重构。这在实践中很常见。

因此开闭原则的理想情况就是，对已有功能闭合，对扩展开放。也就是说，功能的扩展，都通过设计的扩展来实现，而不是通过修改来实现。

当然，开闭原则是一个非常抽象和理想的情况，实践中很难完全的达到这样的理想效果。我们只需要在实践时，对程序的扩展性和可维护性保持足够的重视，就自然会遵循开闭原则。

## 三、单例模式 <a href="#section-6" id="section-6"></a>

单例模式是最简单的一种设计模式。同时也是使用最多的设计模式。我们在无意识中可能会大量使用它。

顾名思义，单例模式强调的就是**一个类只有一个实例**。

> JavaScript 中去思考单例，要比在其他开发语言里思考单例要简单很多。因为 JavaScript 本身就是单线程语言，不需要考虑多线程的情况下的同步锁问题。

例如，我们随便使用对象字面量创建一个对象，这就是最简单的单例。

```javascript
const p1 = {
  token: '1xd33xsk21',
  time: 20201219
}
```

在实践中，某些场景只需创建一次实例，而不用反复创建实例。

例如，单页应用中的登陆弹窗组件。该组件可能会出现多次，但我们完全没有必要在他每次出现时都创建一个实例。而只需在项目中创建一个实例就可以满足要求。

![](https://images.xiaozhuanlan.com/photo/2020/0fd3aa975e3781ce6c3d4b41d08509e1.image)

那么我们如何才能确保该登陆组件只会创建一个实例呢？

先按照正常的方式创建一个对象

```javascript
class Login {
  constructor() {}
  show() {}
  hide() {}
  // 渲染 DOM 节点
  render() {}
}
```

当前的写法，当我们多次使用 `new` 关键时，就会创建多个实例，还不符合单例模式的要求。那么我们需要思考的问题，就是如何在多次使用 `new` 的时候，不会重复创建实例呢？

我们知道，创建实例的关键就在于构造函数，因此，只需要在构造函数中做一个重复的判断，就可以达到要求。在一个可以在内存中持久存在的地方添加一个引用，用于存储已经创建好的实例，在构造函数中判断，如果该引用已经存在了一个实例，那么就不再返回新的实例。

可以使用静态属性来在内存中存储已创建好的实例

```javascript
class Login {
  // 使用静态属性在内存存储实例
  static instance = null
  constructor(parentNode) {
    // 判断，如果已经存在实例，直接返回该实例
    if (Login.instance) {
      return Login.instance
    }
    this.parentNode = parentNode
    this.render()
    Login.instance = this
    return this
  }

  show() { }
  hide() { }
  // 渲染 DOM 节点
  render() { }
}

const p2 = new Login()
const p1 = new Login()

console.log(p1 === p2) // true
```

当然，也可以使用闭包在内存存储实例，只是写法不一样，思路与上面的方案无差别

```javascript
const Login = (function () {
  // 使用 闭包 在内存存储实例
  let instance = null
  class LoginComponent {
    constructor(parentNode) {
      // 判断，如果已经存在实例，直接返回该实例
      if (instance) {
        return instance
      }
      this.parentNode = parentNode
      this.render()
      instance = this
      return this
    }

    show() { }
    hide() { }
    // 渲染 DOM 节点
    render() { }
  }
  return LoginComponent
})()

const p2 = new Login()
const p1 = new Login()

console.log(p1 === p2) // true
```

仔细观察用闭包存储实例，我们使用函数自执行的方式构建了闭包环境，如果我们换一种方式，假设我们的开发环境已经支持了模块化「import方式」，那么我们就可以将这种方式进行一个简单的调整

首先定义

```javascript
// components/login.js
let instance = null
class LoginComponent {
  constructor(parentNode) {
    // 判断，如果已经存在实例，直接返回该实例
    if (instance) {
      return instance
    }
    this.parentNode = parentNode
    this.render()
    instance = this
    return this
  }

  show() { }
  hide() { }
  // 渲染 DOM 节点
  render() { }
}

export default LoginComponent
```

在其他模块中使用

```javascript
import Login from 'components/login'

const p1 = new Login()
const p2 = new Login()

console.log(p1 === p2)  // true
```

基于这个例子，大家可以体会一下：模块、闭包、单例 之间的关系。

进一步思考，如果我想要把已经封装好的类，转化为单例模式应该怎么办？前提是我期望之前的代码不会更改，或者是第三方依赖无法更改。

此时我们可以借助代理的思维方式来处理。

```javascript
class Login {
  constructor() { }
  show() { }
  hide() { }
  // 渲染 DOM 节点
  render() { }
}

const LoginProxy = (function () {
  let instance = null
  return function() {
    if (!instance) {
      instance = new Login()
    }
    return instance
  }
})()

const p1 = new LoginProxy()
const p2 = new LoginProxy()

console.log(p1 === p2)
```

我们可以将这种方式进行一个简单的调整，让其扩展性变得更强。

核心的思想是创建一个单例创建方法 singleCreator。

singleCreator 借助高阶函数的思想，用于扩展构造函数的逻辑。

```javascript
class Login {
  constructor() { }
  show() { }
  hide() { }
  // 渲染 DOM 节点
  render() { }
}

// 该方法可以将其他的任何类改造成为单例模式
function singleCreator(constructor) {
  let instance = null
  return function() {
    if (!instance) {
      instance = new constructor()
    }
    return instance
  }
}

const _Login = singleCreator(Login)

const p1 = new _Login()
const p2 = new _Login()
console.log(p1)
console.log(p1 === p2)
```

## 四、工厂模式

要准确的理解工厂模式并不简单。

> JavaScript 中没有接口和抽象类的概念，因此基于 JavaScript 理解工厂模式，在实现上与其他语言有所不同。因此学习时要注意区分

假设我有一个手机工厂，工厂里能生产各种手机。小米、苹果、华为等。

每一种手机的生产流程基本相同，但是需要的原材料不一样。

于是我们按照普通的思维定义类时，就会出现一种情况，他们只是在创建时传入的参数不同，但是其他的方法都相同。

```javascript
class Xiaomi {
  constructor() {
    this.materials = {
      1: 'xiaomi_material1',
      2: 'xiaomi_material2',
      3: 'xiaomi_material3',
    }
  }
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}

class IPhone {
  constructor() {
    this.materials = {
      1: 'iphone_material1',
      2: 'iphone_material2',
      3: 'iphone_material3',
    }
  }
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}

class Huawei {
  constructor() {
    this.materials = {
      1: 'huawei_material1',
      2: 'huawei_material2',
      3: 'huawei_material3',
    }
  }
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}
```

这样封装没什么问题。不过我们在实践时，可能会遇到一些维护上的小问题。

时光飞逝，类 Xiaomi 已经在代码中用了很久，项目中有几十处代码使用 `new Xiaomi()` 创建了大量的实例。可是后来我们发现，`Xiaomi` 已经出了很多种品牌了，例如 小米6，小米7，小米8，而且这些小米手机使用的材料也不一样。而我们最开始使用的 `Xiaomi`，其实是想要声明的是 小米 4。

为了适应场景的变动和调整，我们需要修改代码。但是 Xiaomi 类已经变成了祖传代码，此时如果轻易修改，风险非常大。即使只是改一个类名 Xiaomi -> Xiaomi4，就要改动几十处。因此我们在设计之初，如何避免未来修改代码的风险呢？

工厂模式就是这里提供的一个解决方案。

工厂模式用于封装和管理对象的创建。工厂模式期望我们在创建对象时，不会对外暴露创建逻辑，并且是通过使用一个共同的接口来创建新的对象。

首先，创建一个工厂方法，通过传入不同的参数，然后声明不同的类。

```javascript
function factory(type) {
  if (type == 'xiaomi') {
    return new Xiaomi()
  }
  if (type == 'iphone') {
    return new IPhone()
  }
  if (type == 'huawei') {
    return new Huawei()
  }
}
```

这样，我们就通过工厂方法，使用不同的字符串，与具体的类之间，建立了一个映射关系。

那么，我们在使用时，就不再直接通过 `new Xiaomi()` 的方式直接创建实例了。而是使用 factory 方法进行创建。

```javascript
const xm = factory('xiaomi')
const ip = factory('iphone')
const hw = factory('huawei')
```

未来需要将类名进行更改时，例如将 Xiaomi 修改为 Xiaomi4，那么只需要在类的声明和工厂方法里进行修改即可。而其他使用的地方，可以不做修改。

```javascript
- class Xiaomi {
+ class Xiaomi4 {
  constructor() {
    this.materials = {
      1: 'xiaomi_material1',
      2: 'xiaomi_material2',
      3: 'xiaomi_material3',
    }
  }
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}


function factory(type) {
  if (type == 'xiaomi') {
-    return new Xiaomi()
+    return new Xiaomi4()
  }
  if (type == 'iphone') {
    return new IPhone()
  }
  if (type == 'huawei') {
    return new Huawei()
  }
}
```

这就是简单工厂模式。

这样能够解决一部分问题。

进一步思考，后续手机的品种会越来越多，小米8，小米9， 小米10，华为 mete10，华为 p40 等等。那这个时候，我们会发现，除了要新增一个类之外，工厂方法 factory 也会持续被更改。

> 违背了开闭原则

那我们应该怎么解决这个问题呢？有没有一种方式，能够让工厂方法在后续的迭代过程中，不进行修改？

当然有：最简单的方式如下

```javascript
function factory(type) {
  // window 表示声明的类 挂载的对象，可能是window，可能是global，可能是其他自定义的对象
  return new window[type]()
}
```

这样处理之后，那么传入的 type 字符串，就必须与类名保持一致。因此在使用时会有一些限制

```javascript
const hw = factory('Huawei')
```

当然，我们也可以维护一份配置文件，该配置文件就是显式的标明类型字符串与类名的映射关系。

我们可以将这份配置文件，定义在工厂函数的原型对象中。

于是，上面的工厂函数可以演变成为工厂类。并且具备了自己的方法，config 配置文件维护在工厂对象的原型中，被所有实例共享。

```javascript
function Factory() {}
Factory.prototype.create = function(type) {
  var cur = this.config[type]
  if (cur) {
    return new cur()
  }
}
Factory.prototype.config = {}
Factory.prototype.setConfig = function(type, sub) {
  this.config[type] = sub
}
```

之后，每新增一个类，都需要使用工厂对象修改存储在原型对象中的配置

```javascript
class Xiaomi5 {
  constructor() {
    this.materials = {
      1: 'xiaomi_material1',
      2: 'xiaomi_material2',
      3: 'xiaomi_material3',
    }
  }
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}

new Factory().setConfig('xiaomi5', Xiaomi5)
```

我们也可以专门手动维护一个单独的模块作为配置文件。这样的方式更直观。

```javascript
import Xiaomi from './Xiaomi'
import Xiaomi5 from './Xiaomi5'

export default {
  xiaomi: Xiaomi,
  xiaomi5: Xiaomi5
}
```

```javascript
import config from './config'

export default function factory(type) {
  if (config[type]) {
    return new config[type]()
  }
}
```

很显然，在代码层面，还可以对类型声明进行优化。

我们分析上面三个类的情况，都是生成手机，所以所有的方法都完全相同。但是因为每一种手机的原材料不一样，因此构造函数里会不一样。利用封装的思维，我们可以将这三个类，合并成为一个类，不同的手机在构造函数中进行判断。

```javascript
class PhoneFactory {
  constructor(type) {
    if (type == 'xiaomi') {
      this.materials = {
        1: 'xiaomi_material1',
        2: 'xiaomi_material2',
        3: 'xiaomi_material3',  
      }
    }
    if (type == 'iphone') {
      this.materials = {
        1: 'iphone_material1',
        2: 'iphone_material2',
        3: 'iphone_material3',
      }
    }
    if (type == 'huawei') {
      this.materials = {
        1: 'huawei_material1',
        2: 'huawei_material2',
        3: 'huawei_material3',
      }
    }
  }
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}

const xm = new PhoneFactory('xiaomi')
const ip = new PhoneFactory('iphone')
const hw = new PhoneFactory('huawei')
```

这种方式的底层思维是将所有的手机抽象成为同一种类型，然后在构造函数时针对不同的细节进行区分。之所以能够这样处理的原因，是因为 Xiaomi，IPhone，Huawei 这几个类高度相似，因此可以抽象成为同一种类型。但是如果只有部分相似，就需要区别对待。

在 jQuery 的封装里，也有同样的场景。例如 jQuery 的构造函数 `jQuery.fn.init` 中有这样的逻辑判断

```javascript
init = jQuery.fn.init = function (selector, context, root) {
  var match, elem;

  // $(""), $(null), $(undefined), $(false)
  if (!selector) {
    return this;
  }

  // $('.wrapper')
  if (typeof selector === "string") {

    //...

    // $(DOMElement)
  } else if (selector.nodeType) {

  // $(function)
  // Shortcut for document ready
  } else if (jQuery.isFunction(selector)) {
    //....
  }

  return jQuery.makeArray(selector, this);
};
```

为了扩展时，不直接修改对象而是修改配置文件，可以进一步调整一下

```javascript
const config = {
  xiaomi: {
    1: 'xiaomi_material1',
    2: 'xiaomi_material2',
    3: 'xiaomi_material3',
  },
  iphone: {
    1: 'iphone_material1',
    2: 'iphone_material2',
    3: 'iphone_material3',
  },
  huawei: {
    1: 'huawei_material1',
    2: 'huawei_material2',
    3: 'huawei_material3',
  }
}

class PhoneFactory {
  constructor(type) {
    this.materials = config[type]
  }
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}

const xm = new PhoneFactory('xiaomi')
const ip = new PhoneFactory('iphone')
const hw = new PhoneFactory('huawei')
```

但是如果这几个类只是部分相似，只有部分接口是一样的，那么就需要区别对象，而不能直接合在一起。同样的方法使用继承的方式来简化

```javascript
class Phone {
  step1() {}
  step2() {}
  step3() {}
  step4() {}
}

class Xiaomi extends Phone {
  constructor() {
    this.materials = {
      1: 'xiaomi_material1',
      2: 'xiaomi_material2',
      3: 'xiaomi_material3',
    }
  }
}

class IPhone extends Phone {
  constructor() {
    this.materials = {
      1: 'iphone_material1',
      2: 'iphone_material2',
      3: 'iphone_material3',
    }
  }
}

class Huawei extends Phone {
  constructor() {
    this.materials = {
      1: 'huawei_material1',
      2: 'huawei_material2',
      3: 'huawei_material3',
    }
  }
}

const config = {
  xiaomi: Xiaomi,
  iphone: IPhone,
  huawei: Huawei
}

function factory(type) {
  if (config[type]) {
    return new config[type]()
  }
}

const xm = factory('xiaomi')
const ip = factory('iphone')
const hw = factory('huawei')
```

工厂模式的核心思维在于不直接通过 new 来创建实例，而是使用工厂方法进行一层封装，隐藏实例的创建细节。因此上面提到的许多方式，都是能够基本满足这个特点，那么对应到实践场景中，就需要结合场景选择最适合的方式灵活使用。

## 五、观察者模式

观察者模式也可以称之为监听者模式。

它由观察者「Observer」与被观察者「Subject」共同组成。

![](https://images.xiaozhuanlan.com/photo/2020/6001aeb33595058010bcaf8891cd895a.image)

> 又名发布订阅模式，在复杂场景下，为了适应更多需求，除了观察者与被观察者，还会引入更多的角色，因此复杂场景下，大家更愿意称之为发布订阅模式。

我们会遇到很多这种场景，例如事件监听。当我们点击按钮时，对应按钮如果添加了点击事件监听，那么对应的逻辑就会自动执行。而不需要我们显示的调用该逻辑去执行他。

我们也可以自定义事件。

```javascript
var event = new Event('build')

// 添加观察者
document.addEventListener('build', function () {
  console.log('我是新增的一个观察者1，我现在观察到 document 触发了 build 事件')
})

// 添加观察者
document.addEventListener('build', function () {
  console.log('我是新增的一个观察者2，我现在观察到 document 触发了 build 事件')
})

// 被观察者触发事件
document.dispatchEvent(event)
```

观察者模式解决的就是这样的问题。当某一个条件满足要求或者触发某一种事件时，所有的观察者都会感知到并执行对应的逻辑。

在前端里最常见的就是 React/Vue 的数据思维：当我们改变 state/data 里的数据时，就会自动渲染 UI。

![](https://images.xiaozhuanlan.com/photo/2020/4daf74ff1ecb2f3ed8006b0c533b9676.image)

上图就是 Vue 的内部原理，观察者 Observer 观察数据 Data 的变化，如果一旦发现数据产生了变化就会通知后续的流程，最终完成 View 的更新。

#### 基本实现 <a href="#section" id="section"></a>

代码逻辑比较简单，直接上代码。

```javascript
let subjectid = 0
let observerid = 0

class Subject {
  constructor(name) {
    // 观察者队列
    this.observers = []
    this.id = subjectid++
    this.name = name
  }

  // 添加观察者
  addListener(observer) {
    this.observers.push(observer)
  }

  // 删除观察者
  removeListener(observer) {
    this.observers = this.observers.filter(item => item.id !== observer.id)
  }

  // 通知
  dispatch() {
    this.observers.forEach(item => {
      item.watch(this.name)
    })
  }
}

class Observer {
  constructor() {
    this.id = observerid++
  }
  watch(subjectName) {
    console.log(`观察者${this.id}发现了被观察者 ${subjectName} 产生了变化。`)
  }
}

const sub = new Subject('div元素')
const observer1 = new Observer()
const observer2 = new Observer()

sub.addListener(observer1)
sub.addListener(observer2)

sub.dispatch()
```

被观察者，通过 addListener 添加观察者。

当被观察者发生变化时，调用 `sub.dispatch` 通知所有观察者。

观察者通过 watch 接收到通知之后，执行预备好的逻辑。

以 DOM 元素事件绑定为例，我们进行一个类比

```javascript
div.addEventListener('click', fn, false)
div.addEventListener('mousemove', fn, false)
div.addEventListener('mouseup', fn, false)
```

此时，被观察者，就是 div 元素。

没有明确的观察者，而是直接传入回调函数 fn，事件触发时，回调函数全部执行。因此也可以将此处的回调函数理解为观察者。

这种以回调函数作为观察者的方式更符合事件绑定机制。

此处还需要传入一个字符串用于区分事件类型。这种方式又应该怎么实现呢？

思考一下，直接上代码

```javascript
class Subject {
  constructor(name) {
    // 观察者队列
    // 格式为： { click: [fn1, fn2], scroll: [fn1, fn2] }
    this.events = {}
    this.name = name
  }

  // 添加观察者
  addListener(type, fn) {
    const cbs = this.events[type]
    if (cbs && cbs.length > 0) {
      const _cbs = cbs.filter(cb => cb != fn)
      _cbs.push(fn)
      this.events[type] = _cbs
    } else {
      this.events[type] = [fn]
    }
  }

  // 删除观察者
  removeListener(type) {
    delete this.events[type]
  }

  // 通知
  dispatch(type) {
    this.events[type].forEach(cb => cb())
  }
}

const sub = new Subject('div')

sub.addListener('build', function() {
  console.log('build 事件触发1')
})
sub.addListener('build', function () {
  console.log('build 事件触发2')
})
sub.addListener('click', function() {
  console.log('click 事件触发')
})

sub.dispatch('build')
```

#### Vue 的实现原理 <a href="#sectionvue" id="sectionvue"></a>

![](https://images.xiaozhuanlan.com/photo/2020/e5ae8d295215e9a32b6b4ed56589435e.image)

Vue 的核心是监听 data 中的数据变化，然后让数据的变化自动响应到 View 的变化。

由于 Vue 的场景更为复杂，因此在这个过程中引入了许多其他的角色。

Data：数据，最初的被观察者

Observer：Data 的观察者，数据劫持，监听数据的变化

Dep：订阅器，收集 Watcher。

Watcher：Dep 的订阅者，收到通知之后更新试图

数据的变化，会被 Observer 劫持，并且通知订阅器 Dep。Dep 收到通知之后，又会通知 Watcher，Watcher 收到属性的变化通知并执行响应的函数去更新试图 View。

**Observer**

data 数据的监听机制不需要我们去实现，因为在 JavaScript 中，对象中的每一个属性都具备自身的描述对象 `descriptor`。

当访问数据时，描述对象中 get 方法会执行。

当修改数据时，描述对象中的 set 方法会执行。

因此，我们只需要利用这个机制，去监听数据即可。核心的方法是 JavaScript 提供的 `Object.defineProperty`。

此处我们使用递归的方式，监听数据的所有属性。

与此同时，还需要关注的一个点是，我们需要

在 get 执行时，让 Dep 收集 Watcher。

在 set 执行时，通知 Watcher 执行更新逻辑。

因此，完整的实现如下：

```javascript
class Observer {
  constructor(data) {
    this.data = data;
    this.walk(data);
  }
  walk(data) {
    Object.keys(data).forEach((key) => {
      this.defineReactive(data, key, data[key]);
    });
  }
  defineReactive(data, key, val) {
    const dep = new Dep();
    Object.defineProperty(data, key, {
      enumerable: true,
      configurable: true,
      get: () => {
        if (Dep.target) {
          dep.addSub(Dep.target);
        }
        return val;
      },
      set: function (newVal) {
        if (newVal === val) {
          return;
        }
        val = newVal;
        dep.notify();
      }
    });
  }
}
```

使用时，可以直接 new，也可以增加一个额外的判断

```javascript
function observe(value, vm) {
  if (!value || typeof value !== 'object') {
    return;
  }
  return new Observer(value);
};
```

**Dep**

此处的 Dep，也是一个被观察者，它与 Watcher 也是被观察者与观察者的关系。因此 Dep 的实现也非常简单，作用就是收集观察者 Watcher，以及通知 Watcher 更新。

```javascript
class Dep {
  constructor() {
    this.subs = []
  }
  addSub(sub) {
    this.subs.push(sub);
  }
  notify() {
    this.subs.forEach(function (sub) {
      sub.update();
    });
  }
}
```

**Watcher**

对于订阅者 Watcher 一个非常重要的思考就是何时将自己添加到 Dep 中。

我们刚才在 Observer 的实现中，已经明确好了时机，也就是在访问数据时，可以将 Watcher 添加进去。

因此，我们在初始化 Watcher 时，可以手动去访问一次 data 中的数据，强制触发 get 执行。这样我们就可以在 get 的逻辑中，将 Watcher 添加到 Dep 里了。

```javascript
class Watcher {
  constructor(vm, exp, cb) {
    this.vm = vm;
    this.exp = exp;
    this.cb = cb;
    this.value = this.get();
  }
  update() {
    this.run()
  }
  run() {
    var value = this.vm.data[this.exp];
    var oldVal = this.value;
    if (value !== oldVal) {
      this.value = value;
      this.cb.call(this.vm, value, oldVal);
    }
  }
  get() {
    Dep.target = this;
    // 访问data，触发 get 执行，把当前的 Watcher 实例，添加到 Dep 中
    var value = this.vm.data[this.exp]  
    // 添加成功之后，释放掉自身，其他的实例还需要该引用
    Dep.target = null;
    return value;
  }
}
```

#### Vue <a href="#sectionvue-1" id="sectionvue-1"></a>

最后，我们需要将 data，Observer，Watcher 关联起来，形成一个整体。

```javascript
class Vue {
  constructor(options, el, exp) {
    this.data = options.data;

    // 劫持 data
    observe(this.data);

    // 初始化显示
    el.innerHTML = this.data[exp];

    // 创建 Watcher 实例
    new Watcher(this, exp, function (value) {
      el.innerHTML = value;
    });
    return this;
  }
}
```

这样，我们就可以 new 一个 Vue 对象，然后通过 `vue.data.text = 'xxx'` 去改变 View 的显示了。

我们也可以对 Vue 的数据做一个代理处理，让 `vue.data.text` 与 `vue.text` 的操作是等价的。

```javascript
class Vue {
  constructor(options, el, exp) {
    this.data = options.data;
    Object.keys(this.data).forEach((key) => {
      this.proxyKeys(key);
    });

    // 劫持 data
    observe(this.data);

    // 初始化显示
    el.innerHTML = this.data[exp];  
    new Watcher(this, exp, function (value) {
      el.innerHTML = value;
    });
    return this;
  }
  proxyKeys(key) {
    Object.defineProperty(this, key, {
      enumerable: false,
      configurable: true,
      get: () => {
        return this.data[key];
      },
      set: (newVal) => {
        this.data[key] = newVal;
      }
    });
  }
}
```

封装好之后，最后的使用代码就很简单了。

```javascript
var ele = document.querySelector('#wrap');
var vue = new Vue({
  data: {
    text: 'hello world'
  }
}, ele, 'text');

document.addEventListener('click', function() {
  vue.data.text = `${vue.data.text} vue click.`
}, false)
```

## 六、代理模式

理解代理模式，很形象的例子就是明星的经纪人。

我希望能够让某位明星成为我的代言人，但是一般情况只能跟经纪人洽谈。而不会跟明星直接联系。

经纪人就是明星的代理。

生活中也有许多代理模式的案例。

例如我想购买飞机票，但是我可以不用直接在机场购买，我们小区楼下就有一家代理出售机票火车票的小机构。

解决跨域问题也可以使用代理。

浏览器中某一页面无法直接访问其他域名的接口。那如果就是有需求要访问其他域名的接口怎么办呢？

我在自己域名服务器下，创建一个代理。

浏览器访问自己域名服务器的代理接口，代理接口在服务端，不在浏览器，就不会有跨域限制，于是服务端的代理接口可以去访问其他域名的数据。这样就达到了目的。

![](https://images.xiaozhuanlan.com/photo/2020/0e5c7bed23f235c6485820a83fefbdb6.image)

代理模式的好处，在明星经纪人这里体现的非常明显。

想要找赵丽颖谈商业合作的商家非常多，但是如何对比开价更高的商家，如何淘汰恶意捣乱的商家，如何应付诚意不足的商家，如何安排通告行程等等，这些乱七八糟的事情都由经纪人来处理。

而赵丽颖只需要确定最终选择商家即可。代理模式让她避免了很多杂务。

在我们的代码设计中，代理模式，是为其他对象提供一种代理以控制对该对象的访问。

![](https://images.xiaozhuanlan.com/photo/2020/993e1a77406c8b6ea03b8b8c28782caf.image)

* 代理对象，是对目标对象的一次封装
* 代理对象可预先处理访问请求，再决定是否转交给目标对象
* 代理对象和目标对象，对外提供的可操作性方式保持一致

针对不同的场景，实现代理的方式可能不一样。

在 JavaScript 中，提供了默认的 Proxy 对象，用于创建一个对象的代理。

```javascript
const t = {m: 1}
const p1 = new Proxy(t, {
  get: function(obj, prop) {
    return obj[prop]
  }
})

// 通过代理对象，访问目标对象
console.log(p1.m)  // 1

// 通过代理对象修改目标对象
p1.m = 2
console.log(t) // {m: 2}
```

Proxy 的第一个参数为目标对象，第二参数是监听事件，可以监听代理对象的访问与修改操作。这样的话，我们就可以利用代理对象，对目标对象的访问进行数据的劫持。

Vue3.0 正是利用了 Proxy 这样的特点，才能得以使用 Proxy 替换掉 getter/setter。

我们也可以自己实现一个 Proxy 对象。

> ProxyPolyfill 简化版，仅仅只提供了 Object 的兼容。仅供参考阅读

```javascript
class Internal {
  constructor(target, handler) {
    this.target = target
    this.handler = handler
  }
  get(property, receiver) {
    var handler = this.handler;
    if (handler.get ==undefined) {
      return this.target[property];
    }
    if (typeof handler.get === 'function') {
      return handler.get(this.target, property, receiver);
    }
  }
  set(property, value, receiver) {
    var handler = this.handler;
    if (handler.set == undefined) {
      this.target[property] = value;
    } else if (typeof handler.set === 'function') {
      var result = handler.set(this.target, property, value, receiver);
      if (!result) {
        console.error(`set 异常： ${property}`)
      }
    } else {
      console.error("Trap 'set' is not a function: " + handler.set);
    }
  }
}

function ProxyPolyfill(target, handler) {
  return proxyObject(new Internal(target, handler));
}

/**
 * Proxy object 这里是核心关键，使用 Object.create 的方式与 目标对象建立绑定关系
 * @param {Internal} internal 
 * @returns {object}
 */
function proxyObject(internal) {
  var target = internal.target;
  var descMap, newProto;

  descMap = observeProto(internal);
  newProto = Object.create(Object.getPrototypeOf(target), descMap);

  descMap = observeProperties(target, internal);
  return Object.create(newProto, descMap);
}

/**
 * Observe [[Prototype]]
 * @param {Internal} internal 
 * @returns {object} descriptors
 */
function observeProto(internal) {
  var descMap = {};
  var proto = internal.target;
  while (proto = Object.getPrototypeOf(proto)) {
    var props = observeProperties(proto, internal);
    Object.assign(descMap, props);
  }
  descMap.__PROXY__ = {
    get: function () {
      return internal.target ? undefined : 'REVOKED';
    }
  };
  return descMap;
}

/**
 * Observe properties
 * @param {object} obj
 * @param {Internal} internal 
 * @returns {object} descriptors
 */
function observeProperties(obj, internal) {
  var names = Object.getOwnPropertyNames(obj);
  var descMap = {};
  for (var i = names.length - 1; i >= 0; --i) {
    descMap[names[i]] = observeProperty(obj, names[i], internal);
  }
  return descMap;
}

/**
 * Observe property，让 代理对象的属性操作，映射到目标对象
 * @param {object} obj
 * @param {string} prop
 * @param {Internal} internal 
 * @returns {{get: function, set: function, enumerable: boolean, configurable: boolean}}
 */
function observeProperty(obj, prop, internal) {
  var desc = Object.getOwnPropertyDescriptor(obj, prop);
  return {
    get: function () {
      return internal.get(prop, this);
    },
    set: function (value) {
      internal.set(prop, value, this);
    },
    enumerable: desc.enumerable,
    configurable: desc.configurable
  };
}
```

简单验证一下，发现初步达到了目的

```javascript
const t = {m: 1}
const p1 = new ProxyPolyfill(t, {
  get: function(obj, prop) {
    return obj[prop]
  }
})

p1.m = 2
console.log(t) // {m: 2}
```

**虚拟代理：图片加载**

有的时候图片过大，在页面上不能够快速的完整显示。这个时候体验就非常不好。

当图片还没有完整加载完成时，我们可以使用一个默认图片或者 loading 图片进行占位。目标图片加载完成之后，再将 loading 图片替换成目标图片。

```javascript
var targetImage = (function () {
  var imgNode = document.createElement('img');
  document.body.appendChild(imgNode);
  return {
    setSrc: function (src) {
      imgNode.src = src;
    }
  }
})();

var proxyImage = (function() {
  var img = new Image();
  // 先加载 loading 或者默认图片用于快速显示
  targetImage.setSrc('loading.gif')
  img.onload = img.onerror = function () {
    // 加载完成之后，替换目标图片
    targetImage.setSrc(img.src);
  };

  return {
    setSrc: function (src) {
      // 此时开始加载图片
      img.src = src;
    }
  }
})();

proxyImage.setSrc('https://cn.bing.com/sa/simg/hpb/LaDigue_EN-CA1115245085_1920x1080.jpg');
```

**缓存代理：记忆函数**

有这样一个函数。该函数接收两个整数参数，第一个参数表示开始数字，第二个参数表示结束数字，该函数的功能是计算开始到结束的范围中，所有整数的和。

```javascript
function sum(start, end) {
  let res = 0
  for (let i = start; i <= end; i++) {
    res += i
  }
  return res
}
```

于是问题来了，如果是大额的计算，计算成本很高。例如，我多次调用该方法，计算 1 \~ 100000 的和。

```javascript
sum(1, 100000)
sum(1, 100000)
sum(1, 100000)
sum(1, 100000)
```

我们仔细分析一下，会发现有大量的冗余计算过程。1 \~ 100000 的计算，会被重复计算很多次。

既然都是计算 1 \~ 100000，那能不能优化一下，只计算一次就好了？

当然可以。

我们联想一下纯函数的特点，相同的输入，总能得到相同的输出。因此，如果我们能判断，输入的参数是相同的，那么就可以直接上上一次的计算结果返回出来。

优化方案如下：

```javascript
function withSum(base) {
  const cache = {}

  return (...args) => {
    const str = `${args[0]}-${args[1]}`
    if (!cache[str]) {
      cache[str] = base.apply(null, args)
    }
    return cache[str]
  }
}
```

使用时很简单

```javascript
const _sum = withSum(sum)

_sum(1, 100000)
_sum(1, 100000)
_sum(1, 100000)
_sum(1, 100000)
```

该方案利用了闭包，将计算结果缓存在 `cache` 字段中，当再一次需要计算时，就从闭包中先判断是否已经有计算结果了，如果有就直接返回结果，如果没有才会重新计算。

如果仅仅从计算成本来考虑，新的方案有两个成本，一个是比较的成本，一个是计算结果的成本。

通常情况下，比较成本都是远远低于计算成本的，因此能够达到提高计算速度的效果，但是有的情况下，比较时间可能会大于计算时间。

因此，在实践中运用此方法时，要明确好判断条件，确保判断条件都是简单的，如果太过于复杂的比较条件，也有可能导致没有起到优化的效果，反而消耗更大。

在 React 中，有许多用到了这种方法的场景。

例如 React.memo，useState，useCallback，useMemo 等等。

也就意味着，我们在 React 实际场景中，也要尽量简化比较条件。

代理的方式还有很多，根据不同的场景处理手段也不一样，这里就不再一一列举。

## 七、策略模式

策略模式，是一种封装算法与规则的设计模式。

我们在学习封装的终极奥义时已经知道，封装就是提炼出来相同点，将不同点作为变量。

当不同点是一段逻辑、一个算法、一个规则时，我们就可以进一步将这个不同点进行封装。

这就是策略模式解决问题的思路。

**计算员工奖金**

公司里每位员工的奖金都不一样。

员工的奖金，由员工基本工资与员工等级相关。

奖金 = 基本工资 \* 等级对应的倍数

* A 级 -> 5 倍
* B 级 -> 4 倍
* C 级 -> 3 倍
* D 级 -> 2 倍
* E 级 -> 1 倍

于是，我们就可以封装一个计算奖金的方法如下

```javascript
function getBouns(base, level) {
  if (level == 'A') {
    return base * 5
  }
  if (level == 'B') {
    return base * 4
  }
  if (level == 'C') {
    return base * 3
  }
  if (level == 'D') {
    return base * 2
  }
  if (level == 'E') {
    return base * 1
  }
}
```

有了这个方法，计算奖金就很简单

```javascript
const p1 = {
  name: '张三',
  base: 1000,
  level: 'A'
}
p1.bouns = getBouns(p1.base, p1.level)

console.log(p1)
```

这样封装虽然很简单，但是存在潜在的风险：**奖金的计算方式，随时可能会改变**。

当奖金的计算方式发生了变化，新增了更多的员工等级，我们就不得不修改 `getBouns` 方法。

因此我们需要对该方法进行进一步的提炼，把该方法中的变量：奖金的计算方式提炼出来。这样，我们就不担心变量发生改变了。

此处，我们需要关注两个地方

* 变量：奖金的计算方式，是一段逻辑，可以封装为一个函数
* 奖金的计算方法：与员工等级是一对一的映射关系

因此，我们应该建立一个员工等级与奖金计算方式的隐射关系

```javascript
const map = {
  A: function(base) {
    return base * 5
  },
  B: function (base) {
    return base * 4
  },
  C: function (base) {
    return base * 3
  },
  D: function (base) {
    return base * 2
  },
  E: function (base) {
    return base * 1
  },
}
```

即使以后有所修改，我们只需要修改该配置文件或者配置表即可。

员工的奖金依据该配置进行计算

```javascript
function getBouns(base, level) {
  return map[level](base)
}
```

```javascript
const p2 = {
  name: '李四',
  base: 1200,
  level: 'B'
}
p2.bouns = getBouns(p2.base, p2.level)

console.log(p2)
```

**表单验证**

表单验证是一个非常常见的案例，我们也可以使用策略模式来解决规则的验证问题。

借鉴 antdesign 的表单规则，假设我们从组件中，获取得到的字段数据与其对应的规则如下

```javascript
// 从组件中获取到的字段内容，其中包括了具体的值，与传入的校验规则
const fields = {
  username: {
    value: '张三',
    rules: [{required: true, message: '该字段为必填'}, {max: 10}]
  },
  password: {
    value: '123',
    rules: [{required: true}, {min: 6}]
  },
  phone: {
    value: '123123',
    rules: [{pattern: /(^1[3|5|8][0-9]{9}$)/, message: '手机号码规则不匹配'}]
  }
}
```

分析之后我们发现，表单字段的规则，已经被提炼成为变量，通过在表单组件中配置 `rules` 来对每一个字段设定不同的规则。

同样的道理，每一个规则，都应该有对应的验证函数，该验证函数与 rules 中的字段是一对一的映射关系。因此我们也因为维护一份配置表

```javascript
var strategys = {
  required: function (value, rule) {
    if (value === '') {
      return rule.message || '该字段不能为空';
    }
  },
  // 限制最小长度
  min: function (value, rule) {
    if (value.length < rule.min) {
      return rule.message || `该字段最小长度为${rule.min}`;
    }
  },
  // 限制最小长度
  max: function (value, rule) {
    if (value.length > rule.max) {
      return rule.message || `该字段最大长度为${rule.max}`;
    }
  },
  // 手机号码格式
  pattern: function (value, rule) {
    if (!rule.pattern.test(value)) {
      return rule.message || '正则匹配失败';
    }
  }
};
```

规则作为变量被提炼出来，规则的映射表也有了，那么我们只需要再封装一个验证对象即可。

```javascript
function Validator () {
  // 格式 { username: { value: '张三', rules: [{}, {}] } }
  this.fields = {};
};

// 添加一个字段
Validator.prototype.addField = function (name, value, rules) {
  this.fields[name] = {
    value,
    rules
  }
};

// 添加多个字段
Validator.prototype.addFields = function(fields) {
  this.fields = fields
}

// 验证，并返回验证结果
Validator.prototype.validate = function() {
  // 通过的字段，与错误的字段
  const result = { fields: [], errorFields: [] }
  Object.keys(this.fields).forEach(key => {
    // 错误验证结果
    let errorMessage = ''
    const value = this.fields[key].value
    const rules = this.fields[key].rules

    for (var i = 0; i < rules.length; i++) {
      Object.keys(rules[i]).forEach(validateField => {
        if (strategys[validateField]) {
          const message = strategys[validateField](value, rules[i])
          if (message) {
            errorMessage = message
          }
        }
      })
      if (errorMessage) {
        break
      }
    }

    if (errorMessage) {
      result.errorFields.push({field: key, value, message: errorMessage})
    } else {
      result.fields.push({field: key, value})
    }
  })

  return result
};
```

这样，我们在使用时，就可以通过 `validate` 方法，得到表单验证的结果

```javascript
var validator = new Validator();
validator.addFields(fields)
const result = validator.validate()
console.log(result)

if (result.errorFields.length > 0) {
  console.error('字段验证不通过')
}
```

我们可以字段，来验证一下该封装是否合理。

## 八、装饰者模式

当我们想要扩展一个对象的能力时，可以采用的方案有

* 添加原型方法
* 修改构造函数
* 继承
* **装饰者模式**

前面三种方法，都不可避免的会修改原有对象的代码。

而如果不修改原有对象代码的情况下，装饰者模式是很好的一种解决方案。

#### 案例 <a href="#section" id="section"></a>

首先，我们有设计了几件装备，他们的信息保存在 `config.js` 中

```javascript
// config.js
export const cloth = {
  name: '七彩炫光衣',
  hp: 1000
}
export const weapon = {
  name: '青龙偃月刀',
  attack: 2000
}
export const shoes = {
  name: '神行疾步靴',
  speed: 300
}
export const defaultRole = {
  hp: 100,
  atk: 50,
  speed: 125,
  cloth: null,
  weapon: null,
  shoes: null,
  career: null,
  gender: null
}
```

然后创建一个基础的角色对象，添加基础的属性与方法

```javascript
// 基础角色类
// 有血条，攻击力，速度三个基础属性
// 以及衣服，武器，鞋子三个装备插槽
class Role {
  constructor(role) {
    this.hp = role.hp;
    this.atk = role.atk;
    this.speed = role.speed;
    this.cloth = role.cloth;
    this.weapon = role.weapon;
    this.shoes = role.shoes;
  }
  run() {}
  attack() {}
}
```

然后基于基础角色类创建职业为**战士**的角色类

```javascript
// 战士
class Soldier extends Role {
  constructor(role) {
    const r = Object.assign({}, defaultRole, role)
    super(r)
    this.nickname = r.nickname
    this.gender = r.gender
    this.career = '战士'
    // 战士的基础血条 +20
    if (role.hp == defaultRole.hp) {
      this.hp = defaultRole.hp + 20
    }
    // 战士的基础移动速度 +5
    if (role.speed == defaultRole.speed) {
      this.speed = defaultRole.speed + 5
    }
  }
  run() {
    console.log('战士的奔跑动作')
  }
  attack() {
    console.log('战士的攻击动作')
  }
}
```

接下来，我们要创建装饰类。

装饰类不必知道被装饰类的存在。他是相对独立的。我们可以称之为装饰者。

**装饰类的关键，是以被装饰者的实例，作为初始化参数。**

装饰类可能会有许多，衣服武器鞋子等都可以各设计一个装饰类分别负责不同的行为与变化。因此我们需要设计一个基础装饰类作为各装饰类的父类，用于减少代码量。

装饰类与被装饰类的属性与方法基本保持一致，只是实现上略有差异。

```javascript
// 基础装饰类
class Decorator {
  constructor(role) {
    this.role = role;
    this.hp = role.hp;
    this.atk = role.atk;
    this.speed = role.speed;
    this.cloth = role.cloth;
    this.weapon = role.weapon;
    this.shoes = role.shoes;
    this.career = role.career;
    this.gender = role.gender;
    this.nickname = role.nickname;
  }
  run() {
    this.role.run()
  }
  attack() {
    this.role.attack()
  }
}
```

接下来创建衣服装饰类 ClothDecorator

衣服装饰类继承基础装饰类

衣服只会修改角色的属性，并不会修改角色的行为

```javascript
class ClothDecorator extends Decorator {
  constructor(role, cloth) {
    super(role)
    this.cloth = cloth.name
    this.hp += cloth.hp
  }
}
```

类封装好了之后，我们使用一下，感受一下变化

```javascript
const baseInfo = {...defaultRole, nickname: 'alex', gender: 'man'}
// 创建一个战士角色
const alex = new Soldier(baseInfo)
alex.run()
alex.attack()
console.log(alex)

alex = new ClothDecorator(alex, cloth)
// 查看变化
console.log(alex)
```

![](https://images.xiaozhuanlan.com/photo/2020/aacdb6db8420e235f9ae34d13fbd4400.image)

我们看属性 cloth 与 hp 已经发生了变化。

除此之外，我们还需要创建武器装饰类，鞋子装饰类。

武器与鞋子的穿戴会改变角色的攻击动作与奔跑动作，因此需要做更多的修改

```javascript
class WeaponDecorator extends Decorator {
  constructor(role, weapon) {
    super(role)
    this.weapon = weapon.name
    this.atk += weapon.attack
  }
  attack() {
    console.log('装备了武器，攻击速度变得更快了')
  }
}
```

```javascript
console.log('--------装备武器-----------')
alex = new WeaponDecorator(alex, weapon)
alex.attack()
console.log(alex)
```

![](https://images.xiaozhuanlan.com/photo/2020/2dbff603b6a9913e693c672d53884177.image)

鞋子装饰类

```javascript
class ShoesDecorator extends Decorator {
  constructor(role, shoes) {
    super(role)
    this.shoes = shoes.name
    this.speed += shoes.speed
  }
  run() {
    console.log('穿上了鞋子，奔跑速度变得更快了')
  }
}
```

```javascript
console.log('--------装备鞋子-----------')
alex = new ShoesDecorator(alex, shoes)
alex.run()
console.log(alex)
```

![](https://images.xiaozhuanlan.com/photo/2020/a432df41f4e0f31fab02e88f7e28036b.image)

除此之外，我们玩游戏时，还知道每个角色都会在某些情况下获得不同的 buff，例如大龙 buff，小龙 buff，红蓝 buff 等，这些buff 有的会更改角色属性，例如 cd 更短，攻击更高，有的会更改攻击特性，例如红 buff 会持续掉血，减速等。

我们可以思考一下如何实现这些功能。

#### 装饰语法 decorator <a href="#sectiondecorator" id="sectiondecorator"></a>

> 默认情况下并不支持装饰器语法，因此，在学习该语法之前，你需要找到一个支持该语法的开发环境 [如何在构建环境中支持 decorator](https://technologyadvice.github.io/es7-decorators-babel6/)

ES7 中提供了一个快捷的语法用来解决与装饰者模式一样的问题。这就是 装饰器语法 decorator。

在学习装饰器语法之前，需要先温习一下 ES5 的一些基础知识。

假设有对象如下：(便于理解)

```javascript
var person = {
  name: 'TOM'
}
```

对象中的每个属性都有一个特性值来描述这个属性的特点，他们分别是：

* `configurable`: 属性是否能被 delete 删除，当值为false时，其他特性值也不能被改变，默认值为true
* `enumerable`： 属性是否能被枚举，也就是是否能被 for in 循环遍历。默认为 true
* `writable`: 是否能修改属性值。默认为 true
* `value`：具体的属性值是多少，默认为 undefined
* `get`：当我们通过`person.name`访问 name 的属性值时，get 将被调用。该方法可以自定义返回的具体值是多少。get 默认值为 undefined
* `set`：当我们通过`person.name = 'Jake'`设置 name 属性值时，set 方法将被调用，该方法可以自定义设置值的具体方式，set 默认值为 undefined

> 需要注意的是，不能同时设置`value，writeable`与`get set`。

我们可以通过`Object.defineProperty`(操作单个)与`Object.defineProperties`（操作多个）来修改这些特性值。

```javascript
// 三个参数分别为  target, key, descriptor(特性值的描述对象)
Object.defineProperty(person, 'name', {
  value: "TOM"
})

// 新增
Object.defineProperty(person, 'age', {
  value: 20
})
```

装饰器语法与此类似，当我们想要自定义一个装饰器时，可以这样写：

```javascript
function nameDecorator(target, key, descriptor) {
  descriptor.value = () => {
    return 'jake';
  }
  return descriptor;
}
```

函数 `nameDecorator` 的定义会重写被他装饰的属性(getName)。方法的三个参数与 `Object.defineProperty` 一一对应，分别指当前的对象 `Person`，被作用的属性`getName`，以及属性特性值的描述对象 `descriptor` 。函数最后必须返回`descriptor`。

使用时也很简单，如下：

```javascript
class Person {
  constructor() {
    this.name = 'jake'
  }
  @nameDecorator
  getName() {
    return this.name;
  }
}

let p1 = new Person();
console.log(p1.getName())
```

在 `getName` 方法前面加上 `@nameDecorator`，就是装饰器语法。

自定义函数 `nameDecorator` 的参数中，target，就是装饰的对象Person，key 就是被装饰的具体方法`getName`。

不能使用装饰器对构造函数进行更改，如果要修改构造函数，则可以通过如下的方式来完成

```javascript
function initDecorator(target, key, descriptor) {
  const fn = descriptor.value;
  // 改变传入的参数值
  descriptor.value = (...args) => {
    args[0] = 'TOM';
    return fn.apply(target, args);
  }
  return descriptor;
}

class Person {
  constructor(name, age) {
    this.init(name, age)
  }
  @initDecorator
  init(name, age) {
    this.name = name;
    this.age = age;
  }
  getName() {
    return this.name;
  }
  getAge() {
    return this.age;
  }
}

console.log(new Person('alex', 20).getName()); // TOM
```

如果希望装饰器传入一个指定的参数，可以如下做。

```javascript
// 注意这里的差别
function initDecorator(name) {
  return function (target, key, descriptor) {
    const fn = descriptor.value;
    descriptor.value = (...args) => {
      args[0] = name;
      return fn.apply(target, args);
    }
    return descriptor;
  }
}

class Person {
  constructor(name, age) {
    this.init(name, age)
  }
  @initDecorator('xiaoming')
  init(name, age) {
    this.name = name;
    this.age = age;
  }
  getName() {
    return this.name;
  }
  getAge() {
    return this.age;
  }
}

console.log(new Person('alex', 20).getName());  // xiaoming
```

这里利用了闭包的原理，将装饰器函数外包裹一层函数，以闭包的形式缓存了传入的参数。

我们也可以对整个class添加装饰器

```javascript
function personDecorator(target) {
  // 修改方法
  target.prototype.getName = () => {
    return 'hahahahaha'
  }
  // 新增方法，因为内部使用了this，因此一定不能使用箭头函数
  target.prototype.getAge = function () {
    return this.age
  }
  return target;
}

@personDecorator
class Person {
  constructor(name, age) {
    this.init(name, age)
  }
  init(name, age) {
    this.name = name;
    this.age = age;
  }
  getName() {
    return this.name;
  }
}

var p = new Person('alex', 30);
console.log(p.getName(), p.getAge());  // hahahahaha 30
```

也可以传参数

```javascript
function initDecorator(person) {
  return function (target, key, descriptor) {
    var method = descriptor.value;
    descriptor.value = () => {
      var ret = method.call(target, person.name);
      return ret;
    }
  }
}

@stuDecorator(xiaom)
class Student {
  constructor(name, age) {
    this.init(name, age);
  }
  @initDecorator(xiaom)
  init(name, age) {
    this.name = name;
    this.age = age;
  }
  getAge() {
    return this.age;
  }
  getName() {
    return this.name;
  }
}

var p = new Student('hu', 18);
console.log(p.getAge(), p.getName(), p.getOther()); // 22 "xiaom" "other info."
```

那么用ES7 的decorator来实现最开始的需求，则可以这样做

```javascript
import { cloth, weapon, shoes, defaultRole } from './config';

// 基础角色
class Role {
  constructor(role) {
    this.hp = role.hp;
    this.atk = role.atk;
    this.speed = role.speed;
    this.cloth = role.cloth;
    this.weapon = role.weapon;
    this.shoes = role.shoes;
    this.nickname = role.nickname
    this.gender = role.gender
  }
  run() { }
  attack() { }
}


function ClothDecorator(target) {
  target.prototype.getCloth = function (cloth) {
    this.hp += cloth.hp;
    this.cloth = cloth.name;
  }
}

function WeaponDecorator(target) {
  target.prototype.getWeapon = function (weapon) {
    this.atk += weapon.attack;
    this.weapon = weapon.name;
  }
  target.prototype.attack = function () {
    if (this.weapon) {
      console.log(`装备了${this.weapon}，攻击更强了`);
    } else {
      console.log('战士的基础攻击');
    }
  }
}

function ShoesDecorator(target) {
  target.prototype.getShoes = function (shoes) {
    this.speed += shoes.speed;
    this.shoes = shoes.name;
  }
  target.prototype.run = function () {
    if (this.shoes) {
      console.log(`穿上了${this.shoes}，移动速度更快了`);
    } else {
      console.log('战士的奔跑动作');
    }
  }
}


@ClothDecorator
@WeaponDecorator
@ShoesDecorator
class Soldier extends Role {
  constructor(role) {
    const o = Object.assign({}, defaultRole, role);
    super(o);
    this.career = '战士';
    if (role.hp == defaultRole.hp) {
      this.hp = defaultRole.hp + 20;
    }
    if (role.speed == defaultRole.speed) {
      this.speed = defaultRole.speed + 5;
    }
  }
  run() {
    console.log('战士的奔跑动作');
  }
  attack() {
    console.log('战士的基础攻击');
  }
}

const base = {
  ...defaultRole,
  nickname: 'alex',
  gender: 'man'
}

const s = new Soldier(base);
s.getCloth(cloth);
console.log(s);

s.getWeapon(weapon);
s.attack();
console.log(s);

s.getShoes(shoes);
s.run();
console.log(s);
```

这里需要注意的是，装饰者模式与直接使用浏览器支持的语法在实现上的一些区别。

ES7 Decorator重点在于对装饰器的封装，因此我们可以将上栗中的装饰器单独封装为一个模块。在细节上做了一些调整，让我们封装的装饰器模块不仅仅可以在创建战士对象的时候使用，在我们创建其他职业例如法师，射手的时候也能够正常使用。

```javascript
export function ClothDecorator(target) {
  target.prototype.getCloth = function (cloth) {
    this.hp += cloth.hp;
    this.cloth = cloth.name;
  }
}

export function WeaponDecorator(target) {
  target.prototype.getWeapon = function (weapon) {
    this.atk += weapon.attack;
    this.weapon = weapon.name;
  }
  target.prototype.attack = function () {
    if (this.weapon) {
      console.log(`${this.nickname}装备了${this.weapon}，攻击更强了。职业：${this.career}`);
    } else {
      console.log(`${this.career}的基本攻击`);
    }
  }
}

export function ShoesDecorator(target) {
  target.prototype.getShoes = function (shoes) {
    this.speed += shoes.speed;
    this.shoes = shoes.name;
  }
  target.prototype.run = function () {
    if (this.shoes) {
      console.log(`${this.nickname}穿上了${this.shoes}，移动速度更快了。职业：${this.career}`);
    } else {
      console.log(`${this.career}的奔跑动作`);
    }
  }
}
```

可以利用该例子，感受Decorator与继承的不同。

整理之后，Soldier的封装代码将会变得非常简单

```javascript
import { cloth, weapon, shoes, defaultRole } from './config';
import { ClothDecorator, WeaponDecorator, ShoesDecorator } from './equip';
import Role from './Role';

@ClothDecorator
@WeaponDecorator
@ShoesDecorator
class Soldier extends Role {
  constructor(roleInfo) {
    const o = Object.assign({}, defaultRoleInfo, roleInfo);
    super(o);
    this.career = '战士';
    if (roleInfo.hp == defaultRoleInfo.hp) {
      this.hp = defaultRoleInfo.hp + 20;
    }
    if (roleInfo.speed == defaultRoleInfo.speed) {
      this.speed = defaultRoleInfo.speed + 5;
    }
  }
  run() {
    console.log('战士的奔跑动作');
  }
  attack() {
    console.log('战士的基础攻击');
  }
}
```

## 九、参考

* [JavaScript 核心进阶 - 设计模式](https://xiaozhuanlan.com/advance/7093842615)
