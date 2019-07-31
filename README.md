# 日常笔记

## 一、[JS 原理题](https://juejin.im/post/5d2ee123e51d4577614761f8)

#### 1、[call_apply_bind 手写方法](/call_apply_bind.md)

#### 4.instanceof 的原理

思路：右边变量的原型存在于左边变量的原型链上

```
function instanceOf(left, right) {
  let leftValue = left.__proto__
  let rightValue = right.prototype
  while (true) {
    if (leftValue === null) {
      return false
    }
    if (leftValue === rightValue) {
      return true
    }
    leftValue = leftValue.__proto__
  }
}

let a=[1]
a instanceof Array
```

#### 在 while 循环中，break，continue，return 有什么区别

break;直接结束循环，并且跳出到循环后面语句

continue;继续执行循环体第 1 条指令，跳过 continue 后面的所有语句

return;结束包含 while 语句的整个函数，跳转到调用者

#### 5.new 本质

步骤：

- 创建一个新对象且将其隐式原型指向构造函数原型

- 执行构造函数

- 返回该对象

```
function myNew (fun) {
  return function () {
    // 创建一个新对象且将其隐式原型指向构造函数原型
    let obj = {
      __proto__ : fun.prototype
    }
    // 执行构造函数
    fun.call(obj, ...arguments)
    // 返回该对象
    return obj
  }
}

function person(name, age) {
  this.name = name
  this.age = age
}
let obj = myNew(person)('chen', 18) // {name: "chen", age: 18}
```

#### 6.Object.create 的基本实现原理

思路：将传入的对象作为原型

```
function create(obj) {
  function F() {}
  F.prototype = obj
  return new F()
}
```

#### 7.[实现一个基本的 Promise](/promise_code.md)

#### 8.实现浅拷贝

#### 1) ...实现

```
let copy1 = {...{x:1}}
```

#### 2) Object.assign 实现

```
let copy2 = Object.assign({}, {x:1})
```

#### 9.实现一个基本的深拷贝

#### 1) JOSN.stringify()/JSON.parse()

```
let obj = {a: 1, b: {x: 3}}
JSON.parse(JSON.stringify(obj))
```

> 此方法的弊端
>
> 1、如果 obj 里面有时间对象，则 JSON.stringify 后再 JSON.parse 的结果，时间将只是字符串的形式。而不是时间对象；
>
> 2、如果 obj 里有 RegExp、Error 对象，则序列化的结果将只得到空对象；
>
> 3、如果 obj 里有函数，undefined，则序列化的结果会把函数或 undefined 丢失；
>
> 4、如果 obj 里有 NaN、Infinity 和-Infinity，则序列化的结果会变成 null
>
> 5、JSON.stringify()只能序列化对象的可枚举的自有属性，例如 如果 obj 中的对象是有构造函数生成的， 则使用 JSON.parse(JSON.stringify(obj))深拷贝后，会丢弃对象的 constructor
>
> 6、如果对象中存在循环引用的情况也无法正确实现深拷贝；

#### 2) 递归拷贝

```
function deepClone(obj) {
  let copy = obj instanceof Array ? [] : {}
  for (let i in obj) {
    if (obj.hasOwnProperty(i)) {
      copy[i] = typeof obj[i] === 'object' ? deepClone(obj[i]) : obj[i]
    }
  }
  return copy
}
```

#### 10.使用 setTimeout 模拟 setInterval

可避免 setInterval 因执行时间导致的间隔执行时间不一致

> setInterval 执行时间问题
>
> 如果 setInterval 回调函数的执行时间将足够长（比指定的时间间隔长），它们将连续执行并且彼此之间没有时间间隔。
>
> 当 setInterval 回调函数第二次被触发时（此时 setTimeout 函数仍在执行）setInterval 的第一次触发将被抛弃掉。当一个很长的代码块在执行时，可能把所有的 setInterval 回调函数都排在执行队列的后面，代码块执行完之后，结果便会是一大串的 setInterval 回调函数等待执行，并且这些函数之间没有间隔，直到全部完成。所以，浏览器倾向于的当没有更多 interval 的处理函数在排队时再将下一个处理函数排到队尾(这是由于间隔的问题)。

```
setTimeout (function () {
  // do something
  setTimeout (arguments.callee, 500)
}, 500)
```

#### 11.[js 实现一个继承方法](/prototype.md)

#### 12.实现一个双向数据绑定

```
let obj = {}
let input = document.getElementById('input')
let span = document.getElementById('span')
// 数据劫持
Object.defineProperty(obj, 'text', {
  configurable: true,
  enumerable: true,
  get() {
    console.log('获取数据了')
    return obj['text']
  },
  set(newVal) {
    console.log('数据更新了')
    input.value = newVal
    span.innerHTML = newVal
  }
})
// 输入监听
input.addEventListener('keyup', function(e) {
  obj.text = e.target.value
})
```

#### 13.rem 基本设置

```
// 原始配置
function setRem () {
  let doc = document.documentElement
  let width = doc.getBoundingClientRect().width
  let rem = width / 75
  doc.style.fontSize = rem + 'px'
}
// 监听窗口变化
addEventListener("resize", setRem)
```

#### 14.[实现一个节流函数和防抖函数](/throttle_debounce.md)

#### 15、[8 个问题看你是否真的懂 JS-常见代码题](https://juejin.im/post/5d2d146bf265da1b9163c5c9)

#### Object.setPrototypeOf 方法的使用

将一个指定的对象的原型设置为另一个对象或者 null(既对象的[[Prototype]]内部属性).

obj 将被设置原型的对象.

prototype 该对象新的原型(可以是一个对象或者 null).

#### Object.getPrototypeOf()方法

该方法与 Object.setPrototypeOf 方法配套，用于读取一个对象的原型对象。

```
function Rectangle() {
  // ...
}

const rec = new Rectangle();

Object.getPrototypeOf(rec) === Rectangle.prototype
// true

```

## 二、[变量提升](https://zhuanlan.zhihu.com/p/28140450)

function： 声明、初始化、赋值一开始就全部完成，所以函数的变量提升优先级更高

var、let、const 的区别

#### 1) var

var 命令会发生“变量提升”现象，即变量可以在声明之前使用，值为 undefined 。
内层变量可能覆盖外层变量
用来计数的循环变量泄露为全局变量

#### 2) let

声明的全局变量不会挂在顶层对象下面
所声明的变量一定要在声明后使用，否则报错，报错 ReferenceError
暂时性死区，只要块级作用域内存在 let 命令，它所声明的变量就“绑定”（ binding ）这个区域，不再受外部的影响，在代码块内，使用 let 命令声明变量之前，该变量都是不可用的。
不允许重复声明

#### 3) const

声明的全局变量不会挂在顶层对象下面
const 声明之后必须马上赋值，否则会报错
const 简单类型一旦声明就不能再更改，复杂类型(数组、对象等)指针指向的地址不能更改，内部数据可以更改。
const 一旦声明变量，就必须立即初始化，不能留到以后赋值。
const 命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。

## 三、[制定自己团队的前端开发规范](https://juejin.im/post/5d300e0fe51d4577407b1dff)

## 四、[前端 100 问-面试题](https://juejin.im/post/5d23e750f265da1b855c7bbe)

#### 1.['1', '2', '3'].map(parseInt) what & why ?

map 中这个 callback 一共可以接收三个参数，其中第一个参数代表当前被处理的元素，而第二个参数代表该元素的索引。

而 parseInt 则是用来解析字符串的，使字符串成为指定基数的整数。
parseInt(string, radix)
接收两个参数，第一个表示被处理的值（字符串），第二个表示为解析时的基数。

```
['1', '2', '3'].map(parseInt)等同于
['1', '2', '3'].map((a,b,c)=>{return parseInt(a,b)})
结果为：[1, NaN, NaN]
```

#### 2.es6 中 class 与 es5 中对象有什么却别

class 声明会提升，但不会初始化赋值。Foo 进入暂时性死区，类似于 let、const 声明变量。

class 声明内部会启用严格模式。

class 的所有方法（包括静态方法和实例方法）都是不可枚举的。

class 的所有方法（包括静态方法和实例方法）都没有原型对象 prototype，所以也没有[construct]，不能使用 new 来调用。

必须使用 new 调用 class

class 内部无法重写类名。

#### 3.setTimeout、Promise、Async/Await 的区别

主要是考察这三者在事件循环中的区别，事件循环中分为宏任务队列和微任务队列。

其中 settimeout 的回调函数放到宏任务队列里，等到执行栈清空以后执行；

promise.then 里的回调函数会放到相应宏任务的微任务队列里，等宏任务里面的同步代码执行完再执行；

async 函数表示函数里面可能会有异步方法，await 后面跟一个表达式，async 方法执行时，遇到 await 会立即执行表达式，然后把表达式后面的代码放到微任务队列里，让出执行栈让同步代码先执行。

#### 4.数据扁平化

> 编写一个程序将数组扁平化去并除其中重复部分数据，最终得到一个升序且不重复的数组

```
Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{ return a-b})

或者

[...new Set(String(arr).split(','))].sort((a, b) => a - b).map(Number)
```

> 分步解析

```
var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10]
// 扁平化flat参数为层级
let flatArr = arr.flat(4)
// 去重
let disArr = Array.from(new Set(flatArr))
// 排序
let result = disArr.sort(function(a, b) {
    return a-b
})
console.log(result)
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
```

#### 5.[React 中 setState 什么时候是同步的，什么时候是异步的？](https://github.com/sisterAn/blog)

在 React 中，如果是由 React 引发的事件处理（比如通过 onClick 引发的事件处理），调用 setState 不会同步更新 this.state，除此之外的 setState 调用会同步执行 this.state。所谓“除此之外”，指的是绕过 React 通过 addEventListener 直接添加的事件处理函数，还有通过 setTimeout/setInterval 产生的异步调用。

原因：在 React 的 setState 函数实现中，会根据一个变量 isBatchingUpdates 判断是直接更新 this.state 还是放到队列中回头再说，而 isBatchingUpdates 默认是 false，也就表示 setState 会同步更新 this.state，但是，有一个函数 batchedUpdates，这个函数会把 isBatchingUpdates 修改为 true，而当 React 在调用事件处理函数之前就会调用这个 batchedUpdates，造成的后果，就是由 React 控制的事件处理过程 setState 不会同步更新 this.state。

#### 6.有以下 3 个判断数组的方法，请分别介绍它们之间的区别和优劣 Object.prototype.toString.call() 、 instanceof 以及 Array.isArray()

##### 1) Object.prototype.toString.call()

每一个继承 Object 的对象都有 toString 方法，如果 toString 方法没有重写的话，会返回 [Object type]，其中 type 为对象的类型。但当除了 Object 类型的对象外，其他类型直接使用 toString 方法时，会直接返回都是内容的字符串，所以我们需要使用 call 或者 apply 方法来改变 toString 方法的执行上下文

这种方法对于所有基本的数据类型都能进行判断，即使是 null 和 undefined 。

```
Object.prototype.toString.call('An') // "[object String]"
Object.prototype.toString.call(1) // "[object Number]"
Object.prototype.toString.call(Symbol(1)) // "[object Symbol]"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(undefined) // "[object Undefined]"
Object.prototype.toString.call(function(){}) // "[object Function]"
Object.prototype.toString.call({name: 'An'}) // "[object Object]"
```

##### 2) instanceof

instanceof 的内部机制是通过判断对象的原型链中是不是能找到类型的 prototype。

使用 instanceof 判断一个对象是否为数组，instanceof 会判断这个对象的原型链上是否会找到对应的 Array 的原型，找到返回 true，否则返回 false。

```
[]  instanceof Array; // true
```

但 instanceof 只能用来判断对象类型，原始类型不可以。并且所有对象类型 instanceof Object 都是 true。

```
[]  instanceof Object; // true
```

##### 3) Array.isArray()

功能：用来判断对象是否为数组

#### instanceof 与 isArray

```
当检测Array实例时，Array.isArray 优于 instanceof ，因为 Array.isArray 可以检测出 iframes

var iframe = document.createElement('iframe');
document.body.appendChild(iframe);
xArray = window.frames[window.frames.length-1].Array;
var arr = new xArray(1,2,3); // [1,2,3]

// Correctly checking for Array
Array.isArray(arr);  // true
Object.prototype.toString.call(arr); // true
// Considered harmful, because doesn't work though iframes
arr instanceof Array; // false
```

#### Array.isArray() 与 Object.prototype.toString.call()

```
Array.isArray()是ES5新增的方法，当不存在 Array.isArray() ，可以用 Object.prototype.toString.call() 实现。

if (!Array.isArray) {
  Array.isArray = function(arg) {
    return Object.prototype.toString.call(arg) === '[object Array]';
  };
}
```

### 7.[改造下面的代码，使之输出 0 - 9，写出你能想到的所有解法](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/43)。

理解闭包--常见面试题

```
for (var i = 0; i< 10; i++){
	setTimeout(() => {
		console.log(i);
    }, 1000)
}
```

[答案](/closure.md)

### 8.下面代码中 a 在什么情况下会打印 1？

```
var a = ?;
if(a == 1 && a == 2 && a == 3){
 	console.log(1);
}
```

> 答案解析 因为==会进行隐式类型转换 所以我们重写 toString 方法就可以了

```
var a = {
  i: 1,
  toString() {
    return a.i++;
  }
}

if( a == 1 && a == 2 && a == 3 ) {
  console.log(1);
}
```

### 9.实现一个 sleep 函数，比如 sleep(1000) 意味着等待 1000 毫秒，可从 Promise、Generator、Async/Await 等角度实现

#### promise

```
const sleep = (time) => {
  return new Promise(resolve => setTimeout(resolve, time))
}

sleep(1000).then(() => {
    // 这里写你的骚操作
})
```

#### Async/Await

```
const sleep = (time) => {
  return new Promise(resolve => setTimeout(resolve, time))
}

async function sleepAsync() {
  console.log('fuck the code')
  await sleep(1000)
  console.log('fuck the code again')
}

sleepAsync()
```

#### Generator

```
function* sleepGenerator(time) {
  yield new Promise(function(resolve,reject){
    setTimeout(resolve,time);
  })
}
sleepGenerator(1000).next().value.then(()=>{console.log(1)})
```

### 10.EventBus 代码实现(eventBus.md)

## 五、[css 世界解读](https://juejin.im/post/5ce607a7e51d454f6f16eb3d) 、 [css 的妙用--你未必知道的 49 个 CSS 知识点](https://juejin.im/post/5d3eca78e51d4561cb5dde12)

#### 1.在 css 中，!important 的权重相当的高，但是由于宽高会被 max-width/min-width 覆盖，所以!important 会失效。

```
width: 100px!important;
min-width: 200px;
```

复制代码上面代码计算之后会被引擎解析成：

```
width: 200px;
```

#### 2.盒模型

IE 模型： box-sizing: border-box 此模式下，元素的宽度计算为 border+padding+content 的宽度总和。

w3c 标准模型）： box-sizing: content-box 此模式下，元素的宽度计算为 content 的宽度。

#### 3.[css 实现垂直居中 --继续收集方法 至少收集 6 种](/middle_center.md)

#### 4.float 属性的特性

包裹性：即此时元素 width 会像 height 一样由子元素决定，而不是默认撑满父元素。

块状化并格式化上下文：这个就是后面会讲的 BFC 特性。块状是指元素设置 float: left 之后，其 display 的计算值就成了 block。格式化上下文是指会创建一个 BFC，这个后面会讲。

没有任何 margin 合并；

脱离文档流：float 设计的初衷就是为了“文字环绕”效果，为了让文字环绕图片，就需要具备两个条件。第一是元素高度坍塌，第二是行框盒子不可与浮动元素重叠。而元素高度坍塌就导致元素后面的非浮动块状元素会和其重叠，于是他就像脱离文档流了。

#### 5.BFC：块级格式化上下文

- 根元素；
- 浮动元素 (float 不为 none 的元素)；
- 绝对定位元素 (元素的 position 为 absolute 或 fixed)；
- inline-blocks(元素的 display: inline-block)；
- 表格单元格(元素的 display: table-cell，HTML 表格单元格默认属性)；
- overflow 的值不为 visible 的元素；
- 弹性盒 flex boxes (元素的 display: flex 或 inline-flex)；

#### 6、图片瀑布流效果

#### new Image 用法

创建一个 Image 对象：var a=new Image(); 定义 Image 对象的 src: a.src=”xxx.gif”; 这样做就相当于给浏览器缓存了一张图片。

图像对象：
建立图像对象：图像对象名称=new Image([宽度],[高度])

图像对象的属性： border complete height hspace lowsrc name src vspace width

图像对象的事件：onabort onerror onkeydown onkeypress onkeyup onload

需要注意的是：src 属性一定要写到 onload 的后面，否则程序在 IE 中会出错。

参考代码：

```
var img=new Image();
    img.onload=function(){alert("img is loaded")};
    img.onerror=function(){alert("error!")};
    img.src="http://www.abaonet.com/img.gif";
    function show(){alert("body is loaded");};
    window.onload=show;
```

运行上面的代码后，在不同的浏览器中进行测试，发现 IE 和 FF 是有区别的，在 FF 中，img 对象的加载包含在 body 的加载过程中，既是 img 加载完之后，body 才算是加载完毕，触发 window.onload 事件。在 IE 中，img 对象的加载是不包含在 body 的加载过程之中的，body 加载完毕，window.onload 事件触发时，img 对象可能还未加载结束，img.onload 事件会在 window.onload 之后触发。根据上面的问题，考虑到浏览器的兼容性和网页的加载时间，尽量不要在 Image 对象里放置过多的图片，否则在 FF 下会影响网页的下载速度。当然如果你在 window.onload 之后，执行预加载函数，就不会有 FF 中的问题了。可以通过 Image 对象的 complete 属性来检测图像是否加载完成(每个 Image 对象都有一个 complete 属性，当图像处于装载过程中时，该属性值 false,当发生了 onload、onerror、onabort 中任何一个事件后，则表示图像装载过程结束（不管成没成功），此时 complete 属性为 true)

#### 7、文本控制

#### 1) ::first-letter 应用实例

#### 2) text-transform 应用

假设有个输入框只能输入大写字母，那么如下设置，输入小写字母出现的却是大写字母，可用于身份证输入框或验证码输入框等：

```
input {
    text-transform: uppercase;
  }
```

#### 3) word-spacing 空格间隙

不要被表面意思误导，word-spacing 指的是字符“空格”的间隙。如果一段文字中没有空格，则该属性无效。下面代码设定空格间隙是 20px，也就是说空格现在占据的宽度是原有的空格宽度+20px 的宽度：

```
<p>我有空 格，我该死......</p>
<style>
  p {
    word-spacing: 20px;
  }
</style>
```

#### 4) white-space 空白处理

我们都知道如果在 html 中输入多个空白符，默认会被当成一个空白符处理，实际上就是这个属性控制的

- normal：合并空白符和换行符；
- nowrap：合并空白符，但不许换行；
- pre：不合并空白符，并且只在有换行符的地方换行；
- pre-wrap：不合并空白符，允许换行符换行和文本自动换行；

#### 5) text-align: justify

text-align: justify 为两端对齐。除了实现文字的两端对齐，还能用来做一些两端对齐的布局。

#### 8、position 的值

#### absolute

生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。

元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。

#### fixed

生成绝对定位的元素，相对于浏览器窗口进行定位。

元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。

#### relative

生成相对定位的元素，相对于其正常位置进行定位。

因此，"left:20" 会向元素的 LEFT 位置添加 20 像素。

#### static 默认值。

没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。

#### inherit

规定应该从父元素继承 position 属性的值

#### **sticky**

粘性定位要起作用，需要设置最后滞留位置。chrome 有 bug，firefox 完美

## 六、[前端常用算法](https://juejin.im/post/5d3ea9a4e51d4561f060cd2d#heading-16)

### [笔记](/sort.md)

## 七、[大型项目前端架构浅谈](https://juejin.im/post/5cea1f705188250640005472)

## 八、[高级前端进阶](https://github.com/yygmind/blog)--大量前端技术点

### 1、[高阶函数](/higher_order.md)

高阶函数英文叫 Higher-order function，它的定义很简单，就是至少满足下列一个条件的函数：

接受一个或多个函数作为输入

输出一个函数

也就是说高阶函数是对其他函数进行操作的函数，可以将它们作为参数传递，或者是返回它们。 简单来说，高阶函数是一个接收函数作为参数传递或者将函数作为返回值输出的函数。

### 2、[函数柯里化](https://github.com/yygmind/blog/issues/37)

#### [笔记][/currying.md]

## 九、[一名【合格】前端工程师的自检清单](/web_skills.md)

### 1.[this 的几种使用场景](/this.md)

## 十、[前端面试每日 3+1](https://github.com/haizlin/fe-interview)

## 十一、[webpack 教程](https://juejin.im/user/5ac0e63e6fb9a028cb2dc4c0/posts)--大有收货很详细

[全面的文档](https://juejin.im/post/5d2d336951882543b7223307)

### 目前版本的主要特性

目前最新的版本是 v4.32.2，webpack4 升级之后，增加了很多新特性

- 不在支持 Node.js 4
- 移除 CommonChunkPlugin，增加 optimization
- 支持 WebAssembly
- 支持多种模块类型
- 增加 mode 配置
- 零配置模块打包
- 更快的构建时间，速度提升了 98%

### 配置

1. entry
2. output
3. mode
4. loaders
5. plugins

## 十二、[一次完整的面试过程](https://juejin.im/post/5d282541e51d4577523f2422)

## 十三、[设计模式](https://www.cnblogs.com/imwtr/p/9451129.html)

### [笔记](/design_patterns.md)

# 十四、笔记

## 1、移动端打开指定 App 或者下载 App

```
navToDownApp() {
      let u = navigator.userAgent
      if (/MicroMessenger/gi.test(u)) {
        // 如果是微信客户端打开，引导用户在浏览器中打开
        alert('请在浏览器中打开')
      }
      if (u.indexOf('Android') > -1 || u.indexOf('Linux') > -1) {
        // Android
        if (this.openApp('en://startapp')) {
          this.openApp('en://startapp') // 通过Scheme协议打开指定APP
        } else {
          //跳转Android下载地址
        }
      } else if (u.indexOf('iPhone') > -1) {
        if (this.openApp('ios--scheme')) {
          this.openApp('ios--scheme') // 通过Scheme协议打开指定APP
        } else {
          // 跳转IOS下载地址
        }
      }
    },
    openApp(src) {
      // 通过iframe的方式试图打开APP，如果能正常打开，会直接切换到APP，并自动阻止a标签的默认行为
      // 否则打开a标签的href链接
      let ifr = document.createElement('iframe')
      ifr.src = src
      ifr.style.display = 'none'
      document.body.appendChild(ifr)
      window.setTimeout(function() {
        // 打开App后移出这个iframe
        document.body.removeChild(ifr)
      }, 2000)
    }
```

## 2、如何优雅的处理图片异常

### 监听图片的 error 事件

缺点：确实实现了对异常图片的降级处理，但每张图片都需要通过 JS 进行获取，并且监听 error 事件，对于大量图片的情况并不适用

```
<img id="img" src="//xxx.xxx.xxx/img.png">
```

```
let img = document.getElementById('img');
img.addEventListener('error',function(e){
    e.target.str = '//xxx.xxx.xxx/default.png'; // 为当前图片设定默认图
})
```

### 内联事件来监听 error 事件

缺点：但这种方式还不够好，因为我们仍然需要手动的向 img 标签中添加内联事件，在实际开发过程中，很难保证每张图片都不漏写

```
<img src="//xxx.xxx.xxx/img.png" onerror="this.src = '//xxx.xxx.xxx/default.png'">
```

### 全局监听

我们希望的是，能够在全局监听 error 事件，在实际实现之前，先来看一下浏览器中的事件流
DOM2 级事件规定事件流包含三个阶段：

事件捕获阶段

处于目标阶段

事件冒泡阶段

首先发生的是事件捕获，为截获事件提供了机会。然后是实际的目标接收到的事件。最后一个阶段是冒泡阶段。
我们上文中的监听图片自身的 error 事件，实际上在事件流中是处于目标阶段。
对于 img 的 error 事件来说，是无法冒泡的，但是是可以捕获的，我们的实现如下:

```
window.addEventListener('error',function(e){
    // 当前异常是由图片加载异常引起的
    if( e.target.tagName.toUpperCase() === 'IMG' ){
        e.target.src = '//xxx.xxx.xxx/default.jpg';
    }
},true)
```

最后，我们在思考一个问题，当网络出现异常的时候，必然会出现什么网络图片都无法加载的情况，这样就会导致我们监听的 error 事件
被无限触发，所以我们可以设定一个计数器，当达到期望的错误次数时停止对图片赋予默认图片的操作，改为提供一个 Base64 的图片
实现起来也很简单，如下：

```
window.addEventListener('error',function(e){
    let target = e.target, // 当前dom节点
        tagName = target.tagName,
        times = Number(target.dataset.times) || 0, // 以失败的次数，默认为0
        allTimes = 3; // 总失败次数，此时设定为3
    // 当前异常是由图片加载异常引起的
    if( tagName.toUpperCase() === 'IMG' ){
        if(times >= allTimes){
            target.src = 'data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7';
        }else{
            target.dataset.times = times + 1;
            target.src = '//xxx.xxx.xxx/default.jpg';
        }
    }
},true)
```

## 3、不要给 async 函数写那么多 try/catch 了

在开发中，你是否会为了系统健壮性，亦或者是为了捕获异步的错误，而频繁的在 async 函数中写 try/catch 的逻辑？

```
async function func() {
    try {
        let res = await asyncFunc()
    } catch (e) {
      //......
    }
}
```

实现一个包裹函数

```
async function errorCaptured (asyncFunc) {
  tyr {
    let res = await asyncFunc()
    retutn [null,res]
  }catch(e){
    retutn [e,null]
  }
}
```

这样我们就可以使用一个辅助函数包裹这个 async 函数实现错误捕获

```

async function func() {
let [err, res] = await errorCaptured(asyncFunc)
if (err) {
//... 错误捕获
}
//...
}

```

## 4、异步加载 JS 脚本的方式有哪些？

> script 标签中增加 async(html5) 或者 defer(html4) 属性,脚本就会异步加载。

```
<script src="../XXX.js" defer></script>
```

defer 和 async 的区别在于：

- defer 要等到整个页面在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成），在 window.onload 之前执行；

- async 一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。

- 如果有多个 defer 脚本，会按照它们在页面出现的顺序加载

- 多个 async 脚本不能保证加载顺序

> 动态创建 script 标签

动态创建的 script ，设置 src 并不会开始下载，而是要添加到文档中，JS 文件才会开始下载。

> XHR 异步加载 JS

## 5、http 协议、https 协议

### 描述网页从输入 url 到渲染的过程

1. 首先获取 url 解析出 ip 地址 如果本地 hosts 中有配置优先取出配置 若没有则进行 dns 解析
2. tcp 三次挥手 建立连接
3. 客户端发送 http 请求
4. 服务器处理请求并响应
5. 浏览器处理资源文件进行渲染
6. tcp 的四次挥手

### 简述三次握手和四次挥手

> 三次握手

第一次握手：客户端采用 TCP 协议将带有 SYN 标志的数据包发送给服务器，等待服务器的确认。

第二次握手：服务器端在收到 SYN 的数据包后，必须确认 SYN，并发送的 ACK 标志，同时，自己也将会向客户端发送一个 SYN 标志。

第三次握手：客户端在接收到服务器段的 SYN+ACK 包后，自己会向服务器发送 ACK 包，完成三次握手。那么客户端和服务器正式建立了连接，开始传输数据。

> 四次挥手

1. 当客户端的数据传输到尾部时，客户端向服务器发送带有 FIN 标志的数据包，使其明白自己准备断开通信了。 TCP 规定，FIN 报文段即使不携带数据，也要消耗一个序号。

2. 因为 TCP 的通信是使用全双工通信的，所以在断开连接的时候也应该是双向的；当服务器收到带有 FIN 标志的数据包时，
   其必不会直接发送 FIN 标志断开通信的请求，而是先发送一个带有 ACK 标志的应答信息，使客户端明白服务器还有数据要进行发送。

3. 服务器的数据发送完成后，向客户端发送带有 FIN 标志的数据包，通知客户端断开连接。

4. 当客户端收到 FIN 后，担心某些不可控制的因素导致服务器不知道他要断开连接，会发送 ACK 进行确认，同时把自己设置成 TIME_WAIT 状态并启动定时器，在 TCP 的定时器到达后客户端并没有接收到请求，会重新发送；当服务器收到请求后就断开连接；当客户端等待 2MLS（两倍报文最大生存时间）后，没有收到请求重传的请求后，客户端这边就断开连接，整个 TCP 通信就结束了。

> 为什么握手是三次，挥手是四次

因为当 Server 端收到 Client 端的 SYN 连接请求报文后，可以直接发送 SYN+ACK 报文。其中 ACK 报文是用来应答的，SYN 报文是用来同步的。
但是关闭连接时，当 Server 端收到 FIN 报文时，很可能并不会立即关闭 SOCKET，所以只能先回复一个 ACK 报文。
只有等到我 Server 端所有的报文都发送完了，才能发送 FIN 报文，因此不能一起发送。故需要四步握手。

> 为什么不能进行两次握手链接

如果是两次握手，在第二次结束后，服务器并不能保证客户端已经收到了第二次的请求，如此一来的话，服务器会一直保存着这个通信过程，
因为 TCP 通信都是要占用端口的，造成了一定的资源浪费。所以，就一定要让客户端来发送 ACK 的确认请求。

### https 的传输过程

1. 在服务器端存在一个公钥及私钥

2. 客户端从服务器取得这个公钥

3. 客户端产生一个随机的密钥

4. 客户端通过公钥对密钥加密（非对称加密）

5. 客户端发送到服务器端

6. 服务器端接受这个密钥并且以后的服务器端和客户端的数据全部通过这个密钥加密

### https 和 http 的区别

1. https 需要证书。

2. http 是超文本传输协议，是明文传输，https 则是具有安全性的 ssl 加密传输协议。

3. http 和 https 使用的端口不同，前者是 80，后者是 443。

4. http 的连接很简单，无状态；HTTPS 是由 SSL+HTTP 构建的可进行加密传输、身份认证的网络协议，比 http 协议安全。

### [强制缓存和协商缓存 详情](https://juejin.im/post/5c136bd16fb9a049d37efc47)

强制缓存是我们在第一次请求资源时在 http 响应头设置一个过期时间，在时效内都将直接从浏览器进行获取，
常见的 http 响应头字段如 Cache-Control 和 Expires

协商缓存是我们通过 http 响应头字段 etag 或者 Last-Modified 等判断服务器上资源是否修改，
如果修改则从服务器重新获取，如果未修改则 304 指向浏览器缓存中进行获取。

## 6、[浅谈回流和重绘](/repaint_reflow.md)

## 7、前端安全方面

### xss 攻击原理

第一、XSS 反射型攻击，恶意代码并没有保存在目标网站，通过引诱用户点击一个链接到目标网站的恶意链接来实施攻击的。

第二、XSS 存储型攻击，恶意代码被保存到目标网站的服务器中，这种攻击具有较强的稳定性和持久性。

解决方案 1. url 过滤 2.提交的符号如<>（尖括号）、”（引号）、 ‘（单引号）、%（百分比符号）、;
（分号）、()（括号）、&（& 符号）、+（加号）等转义。严格控制输出

### CSRF 跨站请求伪造

CSRF 指的是攻击者盗用了你的身份,以你的名义发送恶意的请求,给你造成个人隐私泄露及财产安全.通常指携带你的 ck 进行请求一些链接。

解决方案

添加 refer 改用 token

## 8、性能优化方面

### webpack 优化

1. 外部引入模块(CDN)
2. 按需引入
3. tree-shaking
4. 开启 gzip

### 其余的优化

1. 前面提到的强制缓存与协商缓存
2. 减少不必要的 http 请求 对资源进行合并
3. 使用骨架屏提升用户体验
4. 前面提到的 defer 延迟加载
5. 懒加载

## 9、[CSS 开发必知必会的 16 个调试工具技巧](https://juejin.im/post/5d39d27cf265da1bc14b6f47)--很厉害

## 10、[如何优雅处理前端异常？](http://jartto.wang/2018/11/20/js-exception-handling/index.html)

## 11、[webpack 样式 常用包](/webpack_loader.md)
