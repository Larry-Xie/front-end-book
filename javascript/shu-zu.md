# 数组

### 前言

数组是JS最常见的一种数据结构，我们在开发中会经常用到，并且JS中数组操作函数还是非常多的，很容易出现弄混或者概念不清晰，写下这篇文章就是想来总结一下，也算是对细节的一个知识大扫盲！！！

JS中的Array方法

新建数组

常规方式

let temp = new Array(); temp\[0] = 123; temp\[1] = "string"; temp\[2] = true; console.log(temp\[0]); // 123 console.log(temp\[3]); // undefined

let temp2 = new Array(2);// 规定了数组的长度为2 temp2\[0] = 123; temp2\[1] = "string"; temp2\[2] = true; //虽然规定了数组长度，但仍可以再次添加元素，定义的数组大小对此没有影响 简洁方式

let temp = new Array(123, "string", true); 数组文本方式

let temp = \[123, "string", true]; 注意！

出于简洁、可读性和执行速度的考虑，推荐使用最后一种数组文本方法。 数组内可以存放任意类型的数据 数组元素不赋值，则为undefined 访问数组范围以外的元素时，不会出现越界异常，为undefined 定义了数组大小，依然可以添加更多的元素 增删改查

栈方法push & pop增删数组元素

这两个方式都是对数组尾部进行压入和弹出操作。

push(arg1, arg2, …)可以每次压入一个或多个元素，并返回更新后的数组长度。

pop()函数则每次只会弹出尾部的元素，并返回弹出的元素，若对空数组调用pop()则返回undefined。会更改源数组。

let temp = \[123, "string", true];

let push1 = temp.push(456); // 插入一个元素 console.log(push1, temp); // 4, \[123, "string", true, 456]

let push2 = temp.push(789, "string2", false); // 插入多个元素 console.log(push2, temp); // 7, \[123, "string", true, "456", 789, "string2", false]

let temp2 = \[123];

let pop1 = temp2.pop(); console.log(pop1, temp2);// true, \[];

let pop2 = temp2.pop(); console.log(pop2, temp2); // undefined, \[]; 队列方法shift & unshift增删数组元素

这两个方式都是对数组头部进行位移与弹出操作。

unshift(arg1, arg2, …)可以像数组的头部添加一个或多个元素，并返回更新后的数组长度，并把所有其他元素位移到更高的索引。

shift()方法会删除首个数组元素，并返回弹出的元素，并把所有其他元素位移到更低的索引。会更改源数组。

let temp = \[123, 456, 789]; let unshift1 = temp.unshift("abc", "efg"); console.log(unshift1, temp); // 5, \["abc", "efg", 123, 456, 789]

let temp2 = \[123, 456, 789]; let shift1 = temp2.shift(); console.log(shift1, temp2); // 123, \[456, 789] delete删除数组元素

JS数组属于对象，所以可以使用 JavaScript delete 运算符来删除，但这种方式会在数组留下未定义的空洞，所以很少使用。 会更改源数组。

let temp = \[123, 456, 789]; delete temp\[1]; console.log(temp.length, temp\[1]); // 3, undefined splice增删数组元素

splice(arg1, arg2, arg3, …)第一个参数定义了新添加元素的位置，第二个参数定义应删除多少元素，其余参数定义要添加的元素，并返回一个包含已删除项的数组。

splice还可以在数组不留空洞的情况下删除元素。会更改源数组。

let temp1 = \[1, 2, 3, 4, 5, 6]; let splice1 = temp1.splice(1, 2, 7, 8, 9); console.log(splice1, temp1); //\[2, 3],\[1, 7, 8, 9, 4, 5, 6]

let temp2 = \[1, 2, 3, 4, 5, 6]; let splice2 = temp2.splice(1, 0, 7, 8); console.log(splice2, temp2);//\[], \[1, 7, 8, 2, 3, 4, 5, 6]

let temp3 = \[1, 2, 3, 4, 5, 6]; let splice3 = temp3.splice(1, 2); console.log(splice3, temp3);//\[2, 3], \[1, 4, 5, 6] concat合并数组

合并现有数组来创建一个新的数组，concat(arg1, arg2, …)不会更改现有数组，总是返回一个新的数组，可以使用任意数量的数组参数。不会更改源数组。

let temp1 = \[1, 2]; let temp2 = \[3, 4]; let temp3 = \[5]; let concat1 = temp1.concat(temp2, temp3); console.log(concat1, temp1, temp2); // \[1,2,3,4,5], \[1,2],\[3,4] 操作符合并数组，不会更改源数组。

let temp1 = \[1,2,3]; let temp2 = \[4,5,6]; let arr = \[...temp1, ...temp2]; console.log(arr); //\[1,2,3,4,5,6] slice裁剪数组

slice(arg1, arg2) 方法用数组的某个片段切出新数组，不会修改源数组中的任何元素，返回一个新数组，第一个参数为元素选取开始位置，第二个参数为元素选取结束位置，如果第二个参数被省略则会切除数组的剩余部分。不会更改源数组。

let temp = \[1,2,3,4,5,6,7,8]; let slice1 = temp.slice(2, 4); console.log(slice1, temp); //\[3, 4],\[1,2,3,4,5,6,7,8];

let slice2 = temp.slice(4); console.log(slice2); //\[5,6,7,8] Math.max查数组最大值

Math.max.apply(arg1,arg2)参数不支持数组

可以用Math.max.apply(null,arr)来获取数组中的最大值

let temp = \[1,3,6,2,8,4]; let max1 = Math.max.apply(null, temp); console.log(max1); // 8 Math.min查数组最小值

Math.min.apply(arg1,arg2)参数不支持数组

可以用Math.min.apply(null,arr)来获取数组中的最小值

let temp = \[1,3,6,2,8,4]; let min1 = Math.min.apply(null, temp); console.log(min1); // 1 JavaScript方法查数组最大值

function getArrayMaxValue(arr) { var len = arr.length var max = -Infinity; while (len--) { if (arr\[len] > max) { max = arr\[len]; } } return max; } let temp = \[1,3,6,2,8,4]; let max1 = getArrayMaxValue(temp); console.log(max1);// 8 JavaScript方法查数组最小值

function getArrayMinValue(arr) { var len = arr.length var min = Infinity; while (len--) { if (arr\[len] < min) { min = arr\[len]; } } return min; } let temp = \[1,3,6,2,8,4]; let min1 = getArrayMinValue(temp); console.log(min1);// 1 查找索引

indexOf(arg1, arg2)方法在数组中搜索元素值并返回其位置。arg1为搜索元素，arg2可选从哪里开始搜索。负值将从结尾开始的给定位置开始，并搜索到结尾。

lastIndexOf(arg1,arg2) 与 indexOf() 类似，但是从数组结尾开始搜索。arg2可选从哪里开始搜索。负值将从结尾开始的给定位置开始，并搜索到开头。

findIndex() 方法返回通过测试函数的第一个数组元素的索引。

let temp = \[1,2,3,4,5,4,3,2]; let pos1 = temp.indexOf(4); console.log(pos1); //3

let pos2 = temp.lastIndexOf(4); console.log(pos2);//5

let temp = \[1,35,67,8];

let findIndex1 = temp.findIndex(function(value){ return value > 10; }) console.log(findIndex1); //1 查找值

find(function(arg1,arg2,arg3)) 方法返回通过测试函数的第一个数组元素的值。arg1为数组元素值， arg2为数组元素索引，arg3为数组本身。

let temp = \[1,35,67,8];

let find1 = temp.find(function(value){ return value > 10; }); console.log(find1);//35 注意！

splice和slice容易混淆，记住splice翻译是拼接，slice翻译是切片。

splice 会返回被删除元素组成的数组，或者为空数组

pop,shift会返回那个被删除的元素

push,unshift 会返回新数组长度

pop,push,shift,unshift,splice会改变源数组

indexOf,lastIndexOf,concat,slice 不会改变源数组

数组转换

toString数组转字符串

toString()方法把每个元素转换为字符串，然后以逗号连接输出显示， 不会更改源数组。

let temp = \[1,2,3,4]; let toString1 = temp.toString(); console.log(toString1, temp);//1,2,3,4 \[1,2,3,4] join数组转字符串

join()方法可以把数组转换为字符串，不过它可以指定分隔符。

在调用 join() 方法时，可以传递一个参数作为分隔符来连接每个元素。

如果省略参数，默认使用逗号作为分隔符，这时与toString()方法转换操作效果相同。不会更改源数组。

let temp = \[1,2,3,4]; let join1 = temp.join(); let join2 = temp.join("_"); console.log(join1, join2); //1,2,3,4 1_2_3_4 split字符串转数组

split(arg1, arg2)方法是 String 对象方法，与join()方法操作正好相反。该方法可以指定两个参数，第一个参数为分隔符，指定从哪儿进行分隔的标记；第二个参数指定要返回数组的长度。

let temp = "1-2-3-4-5"; let split1 = temp.split("-"); let split2 =temp.split("-", 2); console.log(split1, split2);//\[1,2,3,4,5],\[1,2] 对象转数组

let temp = {key1: "value1", key2: "value2"};

let trans1 = Object.keys(temp); console.log(trans1, temp);//\["key1", "key2"], {key1: "value1", key2: "value2"}

let trans2 = Object.values(temp); console.log(trans2); //\["value1", "value2"]

let trans3 = Object.entries(temp); console.log(trans3); //\[\["key1", "key2"], \["value1", "value2"]] 数组转对象

let temp = \["a","b"]; let trans1 = {...temp}; console.log(trans1, temp); // {0: "a", 1: "b"}, \["a","b"]

let trans2 = Object.assign({}, temp);; console.log(trans2, temp);//{0: "a", 1: "b"}, \["a","b"]

let trans3 = Object.assign(temp, {}); console.log(trans3, temp); // \["a","b"], \["a","b"] 注意！

toString, join不会改变源数组 数组排序

sort数组排序

sort按照字母顺序对数组进行排序, 直接修改了源数组，所以可以不用再将返回值赋给其他变量。

该函数很适合字符串排序，如果数字按照字符串来排序，则 "25" 大于 "100"，因为 "2" 大于 "1"，正因如此，sort()方法在对数值排序时会产生不正确的结果。

可以通过修正比值函数对数值进行排序。比较函数的目的是定义另一种排序顺序。比较函数应该返回一个负，零或正值，当 sort() 函数比较两个值时，会将值发送到比较函数，并根据所返回的值（负、零或正值）对这些值进行排序。会更改源数组。

let temp = \["bi", "ci", "ai", "di"]; let sort1 = temp.sort(); // 可以不用复制给sort1, 直接执行temp.sort()这里是为方便大家直观比较 console.log(sort1, temp); // \["ai","bi","ci","di"], \["ai","bi","ci","di"]

let temp = \[40, 100, 1, 5, 25, 10]; temp.sort(function(a, b){ return b-a }); console.log(temp); // \[100, 40, 25, 10, 5, 1]

let temp = \[40, 100, 1, 5, 25, 10]; temp.sort(function(a, b){ return a-b; }); console.log(temp); // \[1, 5, 10, 25, 40, 100] reverse数组反转

reverse()反转数组中的元素，直接修改了源数组。

let temp = \[1,3,2,4]; temp.reverse(); console.log(temp); //\[4,2,3,1]; 注意！

sort,reverse会更改源数组 数组迭代

数组迭代即对每个数组项进行操作

Array.forEach()

forEach(function(arg1,arg2,arg3){})方法为每个数组元素调用一次函数（回调函数），arg1为数组元素值， arg2为数组元素索引，arg3为数组本身， 此方法不会更改源数组，也不会创建新数组。

let temp = \[1,3,5]; temp.forEach(function(value, index, array){ console.log(value, index, array); }); /\*\*\* \*1 0 \[1,3,5] \*3 1 \[1,3,5] \*5 2 \[1,3,5] \*\*\*/ Array.map()

map(function(arg1,arg2,arg3){})方法通过对每个数组元素执行函数来创建新数组，arg1为数组元素值， arg2为数组元素索引，arg3为数组本身，方法不会对没有值的数组元素执行函数，不会更改源数组，创建一个新数组。

let temp = \[1, 3, 5, , 9];

//index， value不用时可以省略 let map1 = temp.map(function(value, index, array){ return value\*2 }); console.log(map1);// \[2, 6, 10, empty, 18] Array.filter()

filter(function(arg1,arg2,arg3){})方法创建一个包含通过指定条件的数组元素的新数组, arg1为数组元素值， arg2为数组元素索引，arg3为数组本身，不会更改源数组。

let temp = \[1, 3, 5, 7, 9]; let filter1 = temp.filter(function(value){ return value > 5; }); console.log(filter1); // \[7, 9] Array.every()

every(function(arg1,arg2,arg3){})方法测试数组的所有元素是否通过了置顶条件。arg1为数组元素值， arg2为数组元素索引，arg3为数组本身。不会更改源数组。

let temp = \[3, 5, 7, 9];

let every1 = temp.every((value) => { return value > 1; }); console.log(every1); //true Array.some()

some(function(arg1,arg2,arg3){})方法测试数组中是否有元素通过了指定条件的测试。不会更改源数组。

let temp = \[3, 5, 7, 9];

let some1 = temp.some(function(value){ return value > 6; }); console.log(some1); // true Array.reduce()

reduce(function(arg1,arg2,arg3,arg4){})接收一个函数作为累加器（accumulator），数组中的每个值（从左到右）开始缩减，最终为一个值。

arg1上一次调用回调返回的值，或者是提供的初始值（initialValue）,arg2为数组元素值， arg3为数组元素索引，arg4为数组本身。不会更改源数组。

let temp = \[3, 5, 7, 9]; let reduce1 = temp.reduce(function(a,b){ console.log(a,b); return a+b; }); console.log(reduce1); /\*\*\* \*3 5 \*8 7 \*15 9 \*24 \*\*\*/ 普通for循环

性能比较好

let temp = \[1,2,3,4]; for(let i=0,len=temp.length;i\<len;i++) { console.log(temp\[i]); } //1 //2 //3 //4 for…in

let temp = \[1,22,33,4]; for(let i in temp){ console.log(i, temp\[i]); } //0 1 //1 22 //3 22 //4 4 for…of

for..of 是es6中引进的循环，主要是为了补全之前for循环中的以下不足

let temp = \[1,2,3,4]; for(let i of temp){ console.log(i); } //1 //2 //3 //4 跳出循环

forEach跳出循环

let temp = \[1,2,3,4]; let arr = \[]; temp.forEach((value, index) =>{ if(index === 1){ // break; // continue; // return; // return true; return false; } arr.push(value); }); console.log(arr); /\*\*\*

* break: Uncaught SyntaxError: Illegal break statement 不合法
* continue: Uncaught SyntaxError: Illegal continue statement 不合法
* return: \[1,3,4] 跳出本次循环
* return true: \[1,3,4] 跳出本次循环
* return false: \[1,3,4] 跳出本次循环 \*\*\*/ map跳出循环

let temp = \[1,2,3,4]; let arr = temp.map((value, index) =>{ if(index === 1){ // break; // continue; // return; // return true; // return false; } return value; }); console.log(arr); /\*\*\*

* break: Uncaught SyntaxError: Illegal break statement 不合法
* continue: Uncaught SyntaxError: Illegal continue statement 不合法
* return: \[1,undefined,3,4] 跳出本次循环
* return true: \[1,true,3,4] 跳出本次循环
* return false: \[1,false,3,4] 跳出本次循环 \*\*\*/ filter跳出循环

let temp = \[1,9,2,3,4]; let arr = temp.filter((value, index) =>{ if(index === 1){ // break; // continue; // return; // return true; // return false; } return value > 2; }); console.log(arr); /\*\*\*

* break: Uncaught SyntaxError: Illegal break statement 不合法
* continue: Uncaught SyntaxError: Illegal continue statement 不合法
* return: \[3,4] 跳出本次循环
* return true: \[9,3,4] 跳出本次循环, 返回了9是因为return true符合条件
* return false: \[3,4] 跳出本次循环 \*\*\*/ every跳出循环

let temp = \[1,9,2,3,4]; let arr = \[]; temp.every((value, index) =>{ if(index === 1){ // break; // continue; // return; // return true; // return false; } arr.push(value); return true; // every遇到return false会跳出循环,方便测试这里返回return true }); console.log(arr); /\*\*\*

* break: Uncaught SyntaxError: Illegal break statement 不合法
* continue: Uncaught SyntaxError: Illegal continue statement 不合法
* return: \[1] 成功跳出循环
* return true: \[1,2,3,4] 跳出本次循环
* return false: \[1] 成功跳出循环 \*\*\*/ some跳出循环

let temp = \[1,9,2,3,4]; let arr = \[]; temp.some((value, index) =>{ if(index === 1){ // break; // continue; // return; // return true; // return false; } arr.push(value); return false; // every遇到return true会跳出循环,方便测试这里返回return false }); console.log(arr); /\*\*\*

* break: Uncaught SyntaxError: Illegal break statement 不合法
* continue: Uncaught SyntaxError: Illegal continue statement 不合法
* return: \[1,2,3,4] 跳出本次循环
* return true: \[1] 成功跳出循环
* return false: \[1,2,3,4]跳出本次循环 \*\*\*/ for跳出循环

let temp = \[1,9,2,3,4]; let arr = \[]; for(let i = 0; i < temp.length; i++){ if(i === 1){ // break; // continue; // return; // return true; // return false; } arr.push(temp\[i]); } console.log(arr); /\*\*\*

* break: \[1] 成功跳出循环
* continue: \[1,2,3,4] 跳出本次循环
* return: Uncaught SyntaxError: Illegal return statement 不合法
* return true: Uncaught SyntaxError: Illegal return statement 不合法
* return false: Uncaught SyntaxError: Illegal return statement 不合法 \*\*\*/ for…in跳出循环

let temp = \[1,9,2,3,4]; let arr = \[]; for(let index in temp){ if(index === 1){ // break; // continue; // return; // return true; // return false; } arr.push(temp\[index]); } console.log(arr); /\*\*\*

* break: \[1,9,2,3,4] 无效
* continue: \[1,9,2,3,4] 无效
* return: Uncaught SyntaxError: Illegal return statement 不合法
* return true: Uncaught SyntaxError: Illegal return statement 不合法
* return false: Uncaught SyntaxError: Illegal return statement 不合法 \*\*\*/ for…of跳出循环

let temp = \[1,9,2,3,4]; let arr = \[]; let index =1 for(let value of temp){ if(value === 9){ // break; // continue; // return; // return true; // return false; } arr.push(value); } console.log(arr); /\*\*\*

* break: \[1] 成功跳出循环
* continue: \[1,2,3,4] 跳出本次循环
* return: Uncaught SyntaxError: Illegal return statement 不合法
* return true: Uncaught SyntaxError: Illegal return statement 不合法
* return false: Uncaught SyntaxError: Illegal return statement 不合法 \*\*\*/ 汇总表格:

Image 注意！

forEach,map,filter,every,some,reduce这些迭代方法不会改变源数组 some 在有true的时候停止 every 在有false的时候停止 其他常用操作

检测数组

let temp1 = \[1,2,4]; let temp2 = 5;

console.log(temp1 instanceof Array); // true console.log(temp2 instanceof Array); //false

console.log(Array.isArray(temp1)); //true console.log(Array.isArray(temp2)); //false

console.log(Object.prototype.toString.call(temp1)); //\[object Array] console.log(Object.prototype.toString.call(temp2));// \[object Number] 洗牌算法

将一个数组打乱,返回一个打乱的数组

let temp = \[1,3,5,6,7,2,4]; temp.sort(() => { return Math.random() - 0.5; }) console.log(temp); 数组去重

let temp = \[1,3,5,6,7,9,4,3,1,6];

let unique1 = Array.from(new Set(temp)); console.log(unique1);//\[1, 3, 5, 6, 7, 9, 4]

let unique2 = \[...new Set(temp)]; console.log(unique2);//\[1, 3, 5, 6, 7, 9, 4]

let newArr = \[]; for(let i=0; i\<temp.length;i++){ if(newArr.indexOf(temp\[i]) === -1){ newArr.push(temp\[i]); } } console.log(newArr);//\[1, 3, 5, 6, 7, 9, 4]

let newArr = \[]; for(let i=0; i\<temp.length;i++){ if(temp.indexOf(temp\[i]) === i){ newArr.push(temp\[i]); } } console.log(newArr);//\[1, 3, 5, 6, 7, 9, 4]

let arr = \[1,3,5,6,7,9,4,3,1,6]; arr.sort(); let newArr = \[arr\[0]]; for(let i=1; i\<arr.length;i++){ if(arr\[i] !== newArr\[newArr.length - 1]){ newArr.push(arr\[i]); } } console.log(newArr); //\[1, 3, 4, 5, 6, 7, 9]

let arr = \[1,3,5,6,7,9,4,3,1,6]; for(let i=0; i\<arr.length;i++){ for(let j=i+1; j\<arr.length; j++){ if(arr\[i] === arr\[j]){ arr.splice(j,1); j--; } } } console.log(arr);// \[1, 3, 5, 6, 7, 9, 4] 数组去虚值

虚值有 false, 0，''， null, NaN, undefined

let temp = \[1,2,"",4,undefined,5, false,0,null,NaN]; let newArr = temp.filter((value) => { return value; }) console.log(newArr); //\[1,2,4,5] 用数据填充数组

let arr = new Array(5).fill(1); console.log(arr);//\[1,1,1,1,1] 数组中获取随机值

根据数组长度获取一个随机索引。

let arr = \['a','b','c','d']; let randomIndex = Math.floor(Math.random()\*arr.length); let randomValue = arr\[randomIndex]; console.log(randomIndex, randomValue); // 2 c

硬核的文章像极了爱情，刚开始兴奋就要结束了，非常感谢各位小伙伴能够看到这里，如果觉得文章还有点东西，求点赞👍 求关注❤️ 求分享👥！ 微信搜索公众号【接水怪】，查看更多接水怪原创\~
