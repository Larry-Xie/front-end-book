# JS 深浅拷贝

## 一、引言

在代码中经常会有赋值的地方，而伴随着赋值就会带来拷贝的问题，在 JS 中经常被提及的就是浅拷贝和深拷贝，我们需要了解他们的使用场景以及在写代码的时候注意。

## 二、浅拷贝

### 1. 原理

浅拷贝是指，一个新的对象对原始对象的属性值进行精确地拷贝，如果拷贝的是基本数据类型，拷贝的就是基本数据类型的值；如果拷贝的是引用数据类型，拷贝的就是内存地址。如果其中一个对象的引用内存地址发生改变，另一个对象也会发生变化。



![浅拷贝](http://resource.muyiy.cn/image/2019-07-24-060221.png)

例如上图中，`SourceObject` 是原对象，其中包含基本类型属性 `field1` 和引用类型属性 `refObj`。浅拷贝之后基本类型数据 `field2` 和 `filed1` 是不同属性，互不影响。但引用类型 `refObj` 仍然是同一个，改变之后会对另一个对象产生影响。

简单来说可以理解为浅拷贝只解决了第一层的问题，拷贝第一层的**基本类型值**，以及第一层的**引用类型地址**。

### 2. 实现

浅拷贝通常可以有几下几种实现方法：

#### 2.1 Object.assign()

`object.assign` 是 ES6 中 object 的一个方法，该方法可以用于 JS 对象的合并。我们可以使用它来实现浅拷贝。

该方法的参数 target 指的是目标对象，sources指的是源对象。使用形式如下：

```typescript
Object.assign(target, ...sources)
```

使用示例：

```javascript
let target = {a: 1};
let object2 = {b: {d : 2}};
let object3 = {c: 3};
Object.assign(target, object2, object3);  
console.log(target);  // {a: 1, b: {d : 2}, c: 3}
```

这里通过 Object.assign 将 object2 和 object3 拷贝到了 target 对象中，下面来尝试将 object2 对象中的 b 属性中的d属性由 2 修改为 666：

```typescript
object2.b.d = 666;
console.log(target); // {a: 1, b: {d: 666}, c: 3}
```

可以看到，target 的 b 属性值的 d 属性值变成了666，因为这个 b 的属性值是一个对象，它保存了该对象的内存地址，当原对象发生变化时，引用他的值也会发生变化。 ​

**注意：**

* 如果目标对象和源对象有同名属性，或者多个源对象有同名属性，则后面的属性会覆盖前面的属性；
* 如果该函数只有一个参数，当参数为对象时，直接返回该对象；当参数不是对象时，会先将参数转为对象然后返回；
* 因为`null` 和 `undefined` 不能转化为对象，所以第一个参数不能为`null`或 `undefined`，否则会报错；
* 它不会拷贝对象的继承属性，不会拷贝对象的不可枚举的属性，可以拷贝 Symbol 类型的属性。

实际上，Object.assign 会循环遍历原对象的可枚举属性，通过复制的方式将其赋值给目标对象的相应属性。

#### 2.2 扩展运算符

使用扩展运算符可以在构造字面量对象的时候，进行属性的拷贝。使用形式如下：

```typescript
let cloneObj = { ...obj };
```

使用示例：

```javascript
let obj1 = {a:1,b:{c:1}}
let obj2 = {...obj1};
obj1.a = 2;
console.log(obj1); //{a:2,b:{c:1}}
console.log(obj2); //{a:1,b:{c:1}}
obj1.b.c = 2;
console.log(obj1); //{a:2,b:{c:2}}
console.log(obj2); //{a:1,b:{c:2}}
```

扩展运算符 和 object.assign 实现的浅拷贝的功能差不多，如果属性都是基本类型的值，使用扩展运算符进行浅拷贝会更加方便。

#### 2.3 数组浅拷贝

**（1）Array.prototype.slice()**

`slice()`方法是 JavaScript 数组方法，该方法可以从已有数组中返回选定的元素，不会改变原始数组。使用方式如下：

```typescript
array.slice(start, end)
```

该方法有两个参数，两个参数都可选：

* start: 规定从何处开始选取。如果是负数，那么它规定从数组尾部开始算起的位置。也就是说，-1 指最后一个元素，-2 指倒数第二个元素，以此类推。
* end：规定从何处结束选取。该参数是数组片断结束处的数组下标。如果没有指定该参数，那么切分的数组包含从 start 到数组结束的所有元素。如果这个参数是负数，那么它规定的是从数组尾部开始算起的元素。

**如果两个参数都不写，就可以实现一个数组的浅拷贝**：

```javascript
let arr = [1,2,3,4];
console.log(arr.slice()); // [1,2,3,4]
console.log(arr.slice() === arr); //false
```

slice 方法不会修改原数组，只会返回一个浅拷贝了原数组中的元素的一个新数组。原数组的元素会按照下述规则拷贝：

* 如果该元素是个对象引用 （不是实际的对象），slice 会拷贝这个对象引用到新的数组里。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中的这个元素也会发生改变。
* 对于字符串、数字及布尔值来说，slice 会拷贝这些值到新的数组里。在别的数组里修改这些字符串或数字或是布尔值，将不会影响另一个数组。

如果向两个数组任一中添加了新元素，则另一个不会受到影响。

**（2）Array.prototype.concat()**

`concat()` 方法用于合并两个或多个数组，此方法不会更改原始数组，而是返回一个新数组。使用方式如下：

```javascript
arrayObject.concat(arrayX,arrayX,......,arrayX)
```

该方法的参数 arrayX 是一个数组或值，将被合并到 arrayObject 数组中。如果省略了所有 arrayX 参数，则 concat 会返回调用此方法的现存数组的一个浅拷贝：

```javascript
let arr = [1,2,3,4];
console.log(arr.concat()); // [1,2,3,4]
console.log(arr.concat() === arr); //false
```

concat 方法创建一个新的数组，它由被调用的对象中的元素组成，每个参数的顺序依次是该参数的元素（参数是数组）或参数本身（参数不是数组）。它不会递归到嵌套数组参数中。 ​

concat 方法不会改变 this 或任何作为参数提供的数组，而是返回一个浅拷贝，它包含与原始数组相结合的相同元素的副本。 原始数组的元素将复制到新数组中，如下所示：

* 对象引用（而不是实际对象）：concat 将对象引用复制到新数组中。 原始数组和新数组都引用相同的对象。 也就是说，如果引用的对象被修改，则更改对于新数组和原始数组都是可见的。 这包括也是数组的数组参数的元素。
* 数据类型如字符串，数字和布尔值：concat 将字符串和数字的值复制到新数组中。

### 3. 手写浅拷贝

根据以上对浅拷贝的理解，实现浅拷贝的思路：

* 对基础类型做最基本的拷贝；
* 对引用类型开辟新的存储，并且拷贝一层对象属性。

代码实现：

```javascript
// 浅拷贝的实现;
function shallowCopy(object) {
  // 只拷贝对象
  if (!object || typeof object !== "object") return;
  // 根据 object 的类型判断是新建一个数组还是对象
  let newObject = Array.isArray(object) ? [] : {};
  // 遍历 object，并且判断是 object 的属性才拷贝
  for (let key in object) {
    if (object.hasOwnProperty(key)) {
      newObject[key] = object[key];
    }
  }
  return newObject;
}
```

这里用到了 `hasOwnProperty()` 方法，该方法会返回一个布尔值，指示对象自身属性中是否具有指定的属性。所有继承了 Object 的对象都会继承到 `hasOwnProperty()` 方法。这个方法可以用来检测一个对象是否是自身属性。

可以看到，所有的浅拷贝都只能拷贝一层对象。如果存在对象的嵌套，那么浅拷贝就无能为力了。深拷贝就是为了解决这个问题而生的，它能解决多层对象嵌套问题，彻底实现拷贝。

## 三、深拷贝

### 1. 原理

深拷贝是指，对于简单数据类型直接拷贝他的值，对于引用数据类型，在堆内存中开辟一块内存用于存放复制的对象，并把原有的对象类型数据拷贝过来，这两个对象相互独立，属于两个不同的内存地址，修改其中一个，另一个不会发生改变。

![深拷贝](http://resource.muyiy.cn/image/2019-07-24-060222.png)

### 2. 实现

#### 2.1 JSON.stringify()

`JSON.parse(JSON.stringify(obj))`是比较常用的深拷贝方法之一，它的原理就是利用`JSON.stringify` 将`JavaScript`对象序列化成为 JSON 字符串），并将对象里面的内容转换成字符串，再使用`JSON.parse`来反序列化，将字符串生成一个新的 JavaScript 对象。 ​

这个方法是开发中使用最多的深拷贝的方法，也是最简单的方法。

使用示例：

```javascript
let obj1 = {  
  a: 0,
  b: {
    c: 0
  }
};
let obj2 = JSON.parse(JSON.stringify(obj1));
obj1.a = 1;
obj1.b.c = 1;
console.log(obj1); // {a: 1, b: {c: 1}}
console.log(obj2); // {a: 0, b: {c: 0}}
```

这个方法虽然简单粗暴，但也存在一些问题，在使用该方法时需要注意：

* 拷贝的对象中如果有函数，undefined，symbol，当使用过`JSON.stringify()`进行处理之后，都会消失。
* 无法拷贝不可枚举的属性；
* 无法拷贝对象的原型链；
* 拷贝 Date 引用类型会变成字符串；
* 拷贝 RegExp 引用类型会变成空对象；
* 对象中含有 NaN、Infinity 以及 -Infinity，JSON 序列化的结果会变成 null；
* 无法拷贝对象的循环应用，即对象成环 (`obj[key] = obj`)。

在日常开发中，上述几种情况一般很少出现，所以这种方法基本可以满足日常的开发需求。如果需要拷贝的对象中存在上述情况，还是要考虑使用下面的几种方法。

#### 2.2 函数库lodash

该函数库也有提供`_.cloneDeep`用来做深拷贝，可以直接引入并使用：

```javascript
var _ = require('lodash');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f);// false
```

这里附上 lodash 中深拷贝的源代码供大家学习：

```javascript
/**
* value：需要拷贝的对象
* bitmask：位掩码，其中 1 是深拷贝，2 拷贝原型链上的属性，4 是拷贝 Symbols 属性
* customizer：定制的 clone 函数
* key：传入 value 值的 key
* object：传入 value 值的父对象
* stack：Stack 栈，用来处理循环引用
*/

function baseClone(value, bitmask, customizer, key, object, stack) {
    let result
 
    // 标志位
    const isDeep = bitmask & CLONE_DEEP_FLAG		// 深拷贝，true
    const isFlat = bitmask & CLONE_FLAT_FLAG		// 拷贝原型链，false
    const isFull = bitmask & CLONE_SYMBOLS_FLAG	// 拷贝 Symbol，true
 
    // 自定义 clone 函数
    if (customizer) {
        result = object ? customizer(value, key, object, stack) : customizer(value)
    }
    if (result !== undefined) {
        return result
    }
 
    // 非对象  
    if (!isObject(value)) {
        return value
    }
    
    const isArr = Array.isArray(value)
    const tag = getTag(value)
    if (isArr) {
        // 数组
        result = initCloneArray(value)
        if (!isDeep) {
            return copyArray(value, result)
        }
    } else {
        // 对象
        const isFunc = typeof value == 'function'
 
        if (isBuffer(value)) {
            return cloneBuffer(value, isDeep)
        }
        if (tag == objectTag || tag == argsTag || (isFunc && !object)) {
            result = (isFlat || isFunc) ? {} : initCloneObject(value)
            if (!isDeep) {
                return isFlat
                    ? copySymbolsIn(value, copyObject(value, keysIn(value), result))
                	: copySymbols(value, Object.assign(result, value))
            }
        } else {
            if (isFunc || !cloneableTags[tag]) {
                return object ? value : {}
            }
            result = initCloneByTag(value, tag, isDeep)
        }
    }
    // 循环引用
    stack || (stack = new Stack)
    const stacked = stack.get(value)
    if (stacked) {
        return stacked
    }
    stack.set(value, result)
 
    // Map
    if (tag == mapTag) {
        value.forEach((subValue, key) => {
            result.set(key, baseClone(subValue, bitmask, customizer, key, value, stack))
        })
        return result
    }
 
    // Set
    if (tag == setTag) {
        value.forEach((subValue) => {
            result.add(baseClone(subValue, bitmask, customizer, subValue, value, stack))
        })
        return result
    }
 
    // TypedArray
    if (isTypedArray(value)) {
        return result
    }
 
    // Symbol & 原型链
    const keysFunc = isFull
    	? (isFlat ? getAllKeysIn : getAllKeys)
    	: (isFlat ? keysIn : keys)
 
    const props = isArr ? undefined : keysFunc(value)
    
    // 遍历赋值
    arrayEach(props || value, (subValue, key) => {
        if (props) {
            key = subValue
            subValue = value[key]
        }
        assignValue(result, key, baseClone(subValue, bitmask, customizer, key, value, stack))
    })
    
    // 返回结果
    return result
}
```

### 3. 手写深拷贝

#### **3.1 基础递归实现**

实现深拷贝的思路就是，使用 for in 来遍历传入参数的属性值，如果值是基本类型就直接复制，如果是引用类型就进行递归调用该函数，实现代码如下：

```javascript
function deepClone(source) {
      //判断source是不是对象
      if (source instanceof Object == false) return source;
      
  		//根据source类型初始化结果变量
      let target = Array.isArray(source) ? [] : {}; 
      for (let i in source) {
        // 判断是否是自身属性
        if (source.hasOwnProperty(i)) {
          //判断数据i的类型
          if (typeof source[i] === 'object') {
            target[i] = deepClone(source[i]);
          } else {
            target[i] = source[i];
          }
        }
      }
      return target;
    }
   
console.log(clone({b: {c: {d: 1}}}));  // {b: {c: {d: 1}}})
```

这样虽然实现了深拷贝，但也存在一些问题：

* 不能复制不可枚举属性以及 Symbol 类型；
* 只能对普通引用类型的值做递归复制，对于 Date、RegExp、Function 等引用类型不能正确拷贝；
* 可能存在循环引用问题。

#### **3.2 优化递归实现**

上面只是实现了一个基础版的深拷贝，对于上面存在的几个问题，可以尝试去解决一下：

1. 使用 `Reflect.ownKeys()` 方法来解决不能复制不可枚举属性以及 Symbol 类型的问题。 `Reflect.ownKeys()` 方法会返回一个由目标对象自身的属性键组成的数组。它的返回值等同于: `Object.getOwnPropertyNames(target).concat(Object.getOwnPropertySymbols(target))`;
2. 当参数值为 Date、RegExp 类型时，直接生成一个新的实例并返回；
3. 利用 `Object.getOwnPropertyDescriptors()` 方以获得对象的所有属性以及对应的特性。简单来说，这个方法返回给定对象的所有属性的信息，包括有关getter和setter的信息。它允许创建对象的副本并在复制所有属性（包括getter和setter）时克隆它。
4. 使用 `Object.create()` 方法创建一个新对象，并继承传入原对象的原型链。`Object.create()`方法会创建一个新对象，使用现有的对象来提供新创建的对象的`__proto__`。
5. 使用 WeakMap 类型作为 Hash 表，WeakMap 是弱引用类型，可以防止内存泄漏，所以可以用来检测循环引用，如果存在循环，则引用直接返回 WeakMap 存储的值。WeakMap 的特性就是，保存在其中的对象不会影响垃圾回收，如果 WeakMap 保存的节点，在其他地方都没有被引用了，那么即使它还在 WeakMap 中也会被垃圾回收回收掉了。在深拷贝的过程当中，里面所有的引用对象都是被引用的，为了解决循环引用的问题，在深拷贝的过程中，希望有个数据结构能够记录每个引用对象有没有被使用过，但是深拷贝结束之后这个数据能自动被垃圾回收，避免内存泄漏。

代码实现：

```javascript
function deepClone (obj, hash = new WeakMap()) {
  // 日期对象直接返回一个新的日期对象
  if (obj instanceof Date){
  	return new Date(obj);
  } 
  //正则对象直接返回一个新的正则对象     
  if (obj instanceof RegExp){
  	return new RegExp(obj);     
  }
  //如果循环引用,就用 weakMap 来解决
  if (hash.has(obj)){
  	return hash.get(obj);
  }
  // 获取对象所有自身属性的描述
  let allDesc = Object.getOwnPropertyDescriptors(obj);
  // 遍历传入参数所有键的特性
  let cloneObj = Object.create(Object.getPrototypeOf(obj), allDesc)
  
  hash.set(obj, cloneObj)
  for (let key of Reflect.ownKeys(obj)) { 
    if(typeof obj[key] === 'object' && obj[key] !== null){
    	cloneObj[key] = deepClone(obj[key], hash);
    } else {
    	cloneObj[key] = obj[key];
    }
  }
  return cloneObj
}
```

可以使用以下数据进行测试：

```javascript
let obj = {
  num: 1,
  str: 'str',
  boolean: true,
  und: undefined,
  nul: null,
  obj: { name: '对象', id: 1 },
  arr: [0, 1, 2],
  func: function () { console.log('函数') },
  date: new Date(1),
  reg: new RegExp('/正则/ig'),
  [Symbol('1')]: 1,
};
Object.defineProperty(obj, 'innumerable', {
  enumerable: false, value: '不可枚举属性' 
});
obj = Object.create(obj, Object.getOwnPropertyDescriptors(obj))
obj.loop = obj    // 将loop设置成循环引用的属性
let cloneObj = deepClone(obj)

console.log('obj', obj)
console.log('cloneObj', cloneObj)
```

运行结果如下：  可以看到，这样基本就实现了多数数据类型的深拷贝，不过也还存在一些缺陷，比如 Map 和 Set 结构在这个方法中无法进行拷贝。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/848ca283e5de4206b7809e5c018c64f1\~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## 四、参考

* [如何实现一个深浅拷贝](https://juejin.cn/post/7003514213658263588)
* [详细解析赋值、浅拷贝和深拷贝的区别](https://muyiy.cn/blog/4/4.1.html)
