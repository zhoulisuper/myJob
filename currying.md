> 定义
>
> 函数柯里化又叫部分求值，维基百科中对柯里化 (Currying) 的定义为：
>
> > 在数学和计算机科学中，柯里化是一种将使用多个参数的函数转换成一系列使用一个参数的函数，并且返回接受余下的参数而且返回结果的新函数的技术。
>
> 用大白话来说就是只传递给函数一部分参数来调用它，让它返回一个新函数去处理剩下的参数。使用一个简单的例子来介绍下，最常用的就是 add 函数了。

```
// 木易杨
const add = (...args) => args.reduce((a, b) => a + b);

// 传入多个参数，执行 add 函数
add(1, 2) // 3

// 假设我们实现了一个 currying 函数，支持一次传入一个参数
let sum = currying(add);
// 封装第一个参数，方便重用
let addCurryOne = sum(1);
addCurryOne(2) // 3
addCurryOne(3) // 4
```

> 实际应用

#### 1）延迟计算

```
// 木易杨
const add = (...args) => args.reduce((a, b) => a + b);

// 简化写法
function currying(func) {
    const args = [];
    return function result(...rest) {
        if (rest.length === 0) {
          return func(...args);
        } else {
          args.push(...rest);
        	return result;
        }
    }
}

const sum = currying(add);

sum(1,2)(3); // 未真正求值
sum(4); 		 // 未真正求值
sum(); 			 // 输出 10
```

上面的代码理解起来很容易，就是「用闭包把传入参数保存起来，当传入参数的数量足够执行函数时，就开始执行函数」。上面的 currying 函数是一种简化写法，判断传入的参数长度是否为 0，若为 0 执行函数，否则收集参数。

```
// 木易杨
let obj = {
  name: 'muyiy'
}
const fun = function () {
  console.log(this.name);
}.bind(obj);

fun(); // muyiy
```

#### 2) 动态创建函数

有一种典型的应用情景是这样的，每次调用函数都需要进行一次判断，但其实第一次判断计算之后，后续调用并不需要再次判断，这种情况下就非常适合使用柯里化方案来处理。即第一次判断之后，动态创建一个新函数用于处理后续传入的参数，并返回这个新函数。当然也可以使用惰性函数来处理，本例最后一个方案会有所介绍。

我们看下面的这个例子，在 DOM 中添加事件时需要兼容现代浏览器和 IE 浏览器（IE < 9），方法就是对浏览器环境进行判断，看浏览器是否支持，简化写法如下。

```
// 简化写法
function addEvent (type, el, fn, capture = false) {
    if (window.addEventListener) {
        el.addEventListener(type, fn, capture);
    }
    else if(window.attachEvent){
        el.attachEvent('on' + type, fn);
    }
}
```

但是这种写法有一个问题，就是每次添加事件都会调用做一次判断，那么有没有什么办法只判断一次呢，可以利用闭包和立即调用函数表达式（IIFE）来处理。

```
const addEvent = (function(){
    if (window.addEventListener) {
        return function (type, el, fn, capture) {
            el.addEventListener(type, fn, capture);
        }
    }
    else if(window.attachEvent){
        return function (type, el, fn) {
            el.attachEvent('on' + type, fn);
        }
    }
})();
```

上面这种实现方案就是一种典型的柯里化应用，在第一次的 if...else if... 判断之后完成部分计算，动态创建新的函数用于处理后续传入的参数，这样做的好处就是之后调用就不需要再次计算了。

#### 3)参数复用 -- 参考 isType 函数

### 实现 currying 函数

我们可以理解所谓的柯里化函数，就是封装「一系列的处理步骤」，通过闭包将参数集中起来计算，最后再把需要处理的参数传进去。那如何实现 currying 函数呢？

实现原理就是「用闭包把传入参数保存起来，当传入参数的数量足够执行函数时，就开始执行函数」。上面延迟计算部分已经实现了一个简化版的 currying 函数。

下面我们来实现一个更加健壮的的 currying 函数。

```
function currying(fn, length) {
  length = length || fn.length; 	// 注释 1
  return function (...args) {			// 注释 2
    return args.length >= length	// 注释 3
    	? fn.apply(this, args)			// 注释 4
      : currying(fn.bind(this, ...args), length - args.length) // 注释 5
  }
}

// Test
const fn = currying(function(a, b, c) {
    console.log([a, b, c]);
});

fn("a", "b", "c") // ["a", "b", "c"]
fn("a", "b")("c") // ["a", "b", "c"]
fn("a")("b")("c") // ["a", "b", "c"]
fn("a")("b", "c") // ["a", "b", "c"]

//注释 1：第一次调用获取函数 fn 参数的长度，后续调用获取 fn 剩余参数的长度

//注释 2：currying 包裹之后返回一个新函数，接收参数为 ...args

//注释 3：新函数接收的参数长度是否大于等于 fn 剩余参数需要接收的长度

//注释 4：满足要求，执行 fn 函数，传入新函数的参数

//注释 5：不满足要求，递归 currying 函数，新的 fn 为 bind 返回的新函数（bind 绑定了 ...args 参数，未执行），新的 length 为 fn 剩余参数的长度
```
