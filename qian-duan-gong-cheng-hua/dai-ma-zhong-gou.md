# 代码重构

## 一、引言

> 所谓重构（refactoring）是这样一个过程：在不改变代码外在行为的前提下，对代码做出修改，以改进程序的内部结构。重构是一种经千锤百炼形成的有条不紊的程序整理方法，可以最大限度地减小整理过程中引入错误的概率。本质上说，重构就是在代码写好之后改进它的设计。

重构和性能优化有相同点，也有不同点。

相同的地方是它们都在不改变程序功能的情况下修改代码；不同的地方是重构为了让代码变得**更加容易理解、易于修改**，性能优化则是为了**让程序运行得更快**。这里还得重点提一句，由于侧重点不同，**重构可能使程序运行得更快，也可能使程序运行得更慢**。

重构可以一边写代码一边重构，也可以在程序写完后，拿出一段时间专门去做重构。没有说哪个方式更好，视个人情况而定。如果你专门拿一段时间来做重构，则建议在重构一段代码后，立即进行测试。这样可以避免修改代码太多，在出错时找不到错误点。

## 二、重构的原则 <a href="#zhong-gou-de-yuan-ze" id="zhong-gou-de-yuan-ze"></a>

1. 事不过三，三则重构。即不能重复写同样的代码，在这种情况下要去重构。
2. 如果一段代码让人很难看懂，那就该考虑重构了。
3. 如果已经理解了代码，但是非常繁琐或者不够好，也可以重构。
4. 过长的函数，需要重构。
5. 一个函数最好对应一个功能，如果一个函数被塞入多个功能，那就要对它进行重构了。（4 和 5 不冲突）
6. 重构的关键在于运用大量微小且保持软件行为的步骤，一步步达成大规模的修改。每个单独的重构要么很小，要么由若干小步骤组合而成。

## 三、重构的手法 <a href="#zhong-gou-de-shou-fa" id="zhong-gou-de-shou-fa"></a>

重构本身有很多方法，以下八种是比较常用的：

1. 提取重复代码，封装成函数
2. 拆分功能太多的函数
3. 变量/函数改名
4. 替换算法
5. 以函数调用取代内联代码
6. 移动语句
7. 折分嵌套条件表达式
8. 将查询函数和修改函数分离

### 1. 提取重复代码，封装成函数 <a href="#ti-qu-zhong-fu-dai-ma-feng-zhuang-cheng-han-shu" id="ti-qu-zhong-fu-dai-ma-feng-zhuang-cheng-han-shu"></a>

假设有一个查询数据的接口 `/getUserData?age=17&city=beijing`。现在需要做的是把用户数据：`{ age: 17, city: 'beijing' }` 转成 URL 参数的形式：

```
let result = ''
const keys = Object.keys(data)  // { age: 17, city: 'beijing' }
keys.forEach(key => {
    result += '&' + key + '=' + data[key]
})

result.substr(1) // age=17&city=beijing
```

如果只有这一个接口需要转换，不封装成函数是没问题的。但如果有多个接口都有这种需求，那就得把它封装成函数了：

```
function JSON2Params(data) {
    let result = ''
    const keys = Object.keys(data)
    keys.forEach(key => {
        result += '&' + key + '=' + data[key]
    })

    return result.substr(1)
}
```

### 2. 拆分功能太多的函数 <a href="#chai-fen-gong-neng-tai-duo-de-han-shu" id="chai-fen-gong-neng-tai-duo-de-han-shu"></a>

下面是一个打印账单的程序：

```
function printBill(data = []) {
    // 汇总数据
    const total = {}
    data.forEach(item => {
        if (total[item.department] === undefined) {
            total[item.department] = 0
        }

        total[item.department] += item.value
    })
    // 打印汇总后的数据
    const keys = Object.keys(total)
    keys.forEach(key => {
        console.log(`${key} 部门：${total[key]}`)
    })
}

printBill([
    {
        department: '销售部',
        value: 89,
    },
    {
        department: '后勤部',
        value: 132,
    },
    {
        department: '财务部',
        value: 78,
    },
    {
        department: '总经办',
        value: 90,
    },
    {
        department: '后勤部',
        value: 56,
    },
    {
        department: '总经办',
        value: 120,
    },
])
```

可以看到这个 `printBill()` 函数实际上包含有两个功能：汇总和打印。我们可以把汇总数据的代码提取出来，封装成一个函数。这样 `printBill()` 函数就只需要关注打印功能了。

```
function printBill(data = []) {
    const total = calculateBillData(data)
    const keys = Object.keys(total)
    keys.forEach(key => {
        console.log(`${key} 部门：${total[key]}`)
    })
}

function calculateBillData(data) {
    const total = {}
    data.forEach(item => {
        if (total[item.department] === undefined) {
            total[item.department] = 0
        }

        total[item.department] += item.value
    })

    return total
}
```

### 3. 变量/函数改名 <a href="#bian-liang-han-shu-gai-ming" id="bian-liang-han-shu-gai-ming"></a>

无论是变量命名，还是函数命名，都要尽量让别人明白你这个变量/函数是干什么的。变量命名的规则着重于描述“是什么”，函数命名的规则着重于描述“做什么”。

#### **3.1 变量**

```
const a = width * height
```

上面这个变量就不太好，`a` 很难让人看出来它是什么。

```
const area = width * height
```

改成这样就很好理解了，原来这个变量是表示面积。

#### **3.2 函数**

```
function cache(data) {
    const result = []
    data.forEach(item => {
        if (item.isCache) {
            result.push(item)
        }
    })

    return result
}
```

这个函数名称会让人很疑惑，`cache` 代表什么？是设置缓存还是删除缓存？再一细看代码，噢，原来是获取缓存数据。所以这个函数名称改成 `getCache()` 更加合适。

### 4. 替换算法 <a href="#ti-huan-suan-fa" id="ti-huan-suan-fa"></a>

```
function foundPersonData(person) {
    if (person == 'Tom') {
        return {
            name: 'Tom',
            age: 18,
            id: 21,
        }
    }

    if (person == 'Jim') {
        return {
            name: 'Jim',
            age: 20,
            id: 111,
        }
    }

    if (person == 'Lin') {
        return {
            name: 'Lin',
            age: 19,
            id: 10,
        }
    }

    return null
}
```

上面这个函数的功能是根据用户姓名查找用户的详细信息，可以看到这个函数做了三次 `if` 判断，如果没找到数据就返回 `null`。这个函数不利于扩展，每多一个用户就得多写一个 `if` 语句，我们可以用更方便的“查找表”来替换它。

```
function foundPersonData(person) {
    const data = {
        'Tom': {
            name: 'Tom',
            age: 18,
            id: 21,
        },
        'Jim': {
            name: 'Jim',
            age: 20,
            id: 111,
        },
        'Lin': {
            name: 'Lin',
            age: 19,
            id: 10,
        },
    }

    return data[person] || null
}
```

修改后代码结构看起来更加清晰，也方便未来做扩展。

### 5. 以函数调用取代内联代码 <a href="#yi-han-shu-tiao-yong-qu-dai-nei-lian-dai-ma" id="yi-han-shu-tiao-yong-qu-dai-nei-lian-dai-ma"></a>

如果一些代码所做的事情和已有函数的功能重复，那就最好用函数调用来取代这些代码。

```
let hasApple = false
for (const fruit of fruits) {
    if (fruit == 'apple') {
        hasApple = true
        break
    }
}
```

例如上面的代码，可以用数组的 `includes()` 方法代替：

```
const hasApple = fruits.includes('apple')
```

修改后代码更加简洁。

### 6. 移动语句 <a href="#yi-dong-yu-ju" id="yi-dong-yu-ju"></a>

让存在关联的东西一起出现，可以使代码更容易理解。如果有一些代码都是作用在一个地方，那么最好是把它们放在一起，而不是夹杂在其他的代码中间。最简单的情况下，只需使用移动语句就可以让它们聚集起来。就像下面的示例一样：

```
const name = getName()
const age = getAge()
let revenue
const address = getAddress()
// ...
```

```
const name = getName()
const age = getAge()
const address = getAddress()

let revenue
// ...
```

由于两块数据区域的功能是不同的，所以除了移动语句外，我还在它们之间空了一行，这样让人更容易区分它们之间的不同。

### 7. 折分嵌套条件表达式 <a href="#zhe-fen-qian-tao-tiao-jian-biao-da-shi" id="zhe-fen-qian-tao-tiao-jian-biao-da-shi"></a>

当很多的条件表达式嵌套在一起时，会让代码变得很难阅读：

```
function getPayAmount() {
    if (isDead) {
        return deadAmount()
    } else {
        if (isSeparated) {
            return separatedAmount()
        } else if (isRetired) {
            return retireAmount()
        } else {
            return normalAmount()
        }
    }
}
```

```
function getPayAmount() {
    if (isDead) return deadAmount()
    if (isSeparated) return separatedAmount()
    if (isRetired) return retireAmount()
    return normalAmount()
}
```

将条件表达式拆分后，代码的可阅读性大大增强了。

### 8. 将查询函数和修改函数分离 <a href="#jiang-cha-xun-han-shu-he-xiu-gai-han-shu-fen-li" id="jiang-cha-xun-han-shu-he-xiu-gai-han-shu-fen-li"></a>

一般的查询函数都是用于取值的，例如 `getUserData()`、`getAget()`、`getName()` 等等。有时候，我们可能为了方便，在查询函数上附加其他功能。例如下面的函数：

```
function getValue() {
    let result = 0
    this.data.forEach(val => result += val)
    // 这里插入了一个奇怪的操作
    sendBill()
    return result
}
```

千万不要这样做，函数很重要的功能是职责分离。所以我们要将它们分开：

```
function getValue() {
    let result = 0
    this.data.forEach(val => result += val)
    return result
}

function sendBill() {
	// ...
}
```

这样函数的功能就很清晰了。

## 四、参考 <a href="#xiao-jie" id="xiao-jie"></a>

* [带你入门前端工程 - 重构](https://woai3c.gitee.io/introduction-to-front-end-engineering/10.html#%E9%87%8D%E6%9E%84%E7%9A%84%E5%8E%9F%E5%88%99)
