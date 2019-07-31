高阶函数英文叫 Higher-order function，它的定义很简单，就是至少满足下列一个条件的函数：

接受一个或多个函数作为输入

输出一个函数

也就是说高阶函数是对其他函数进行操作的函数，可以将它们作为参数传递，或者是返回它们。 简单来说，高阶函数是一个接收函数作为参数传递或者将函数作为返回值输出的函数。

#### 1) 函数作为参数传递

JavaScript 语言中内置了一些高阶函数，比如 Array.prototype.map，Array.prototype.filter 和 Array.prototype.reduce，它们接受一个函数作为参数，并应用这个函数到列表的每一个元素。我们来看看使用它们与不使用高阶函数的方案对比

> Array.prototype.map
>
> map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果，原始数组不会改变。传递给 map 的回调函数（callback）接受三个参数，分别是 currentValue、index（可选）、array（可选），除了 callback 之外还可以接受 this 值（可选），用于执行 callback 函数时使用的 this 值。

> 来个简单的例子方便理解，现在有一个数组 [1, 2, 3, 4]，我们想要生成一个新数组，其每个元素皆是之前数组的两倍，那么我们有下面两种使用高阶和不使用高阶函数的方式来实现。

```
// 不使用高阶函数
const arr1 = [1, 2, 3, 4];
const arr2 = [];
for (let i = 0; i < arr1.length; i++) {
  arr2.push( arr1[i] * 2);
}

console.log( arr2 );
// [2, 4, 6, 8]
console.log( arr1 );
// [1, 2, 3, 4]
```

```
// 使用高阶函数
const arr1 = [1, 2, 3, 4];
const arr2 = arr1.map(item => item * 2);

console.log( arr2 );
// [2, 4, 6, 8]
console.log( arr1 );
// [1, 2, 3, 4]
```

> Array.prototype.filter
>
> filter() 方法创建一个新数组, 其包含通过提供函数实现的测试的所有元素，原始数组不会改变。接收的参数和 map 是一样的，其返回值是一个新数组、由通过测试的所有元素组成，如果没有任何数组元素通过测试，则返回空数组。

> 来个例子介绍下，现在有一个数组 [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4]，我们想要生成一个新数组，这个数组要求没有重复的内容，即为去重。

```
//不使用高阶函数
const arr1 = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
const arr2 = [];
for (let i = 0; i < arr1.length; i++) {
  if (arr1.indexOf( arr1[i] ) === i) {
    arr2.push( arr1[i] );
  }
}

console.log( arr2 );
// [1, 2, 3, 5, 4]
console.log( arr1 );
// [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4]
```

```
//使用高阶函数
const arr1 = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
const arr2 = arr1.filter( (element, index, self) => {
    return self.indexOf( element ) === index;
});

console.log( arr2 );
// [1, 2, 3, 5, 4]
console.log( arr1 );
// [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4]
```

> Array.prototype.reduce
>
> reduce() 方法对数组中的每个元素执行一个提供的 reducer 函数(升序执行)，将其结果汇总为单个返回值。传递给 reduce 的回调函数（callback）接受四个参数，分别是累加器 accumulator、currentValue、currentIndex（可选）、array（可选），除了 callback 之外还可以接受初始值 initialValue 值（可选）。
>
> 如果没有提供 initialValue，那么第一次调用 callback 函数时，accumulator 使用原数组中的第一个元素，currentValue 即是数组中的第二个元素。 在没有初始值的空数组上调用 reduce 将报错。
>
> 如果提供了 initialValue，那么将作为第一次调用 callback 函数时的第一个参数的值，即 accumulator，currentValue 使用原数组中的第一个元素。
>
> 来个简单的例子介绍下，现在有一个数组 [0, 1, 2, 3, 4]，需要计算数组元素的和，需求比较简单，来看下代码实现。

```
//不使用高阶函数
const arr = [0, 1, 2, 3, 4];
let sum = 0;
for (let i = 0; i < arr.length; i++) {
  sum += arr[i];
}

console.log( sum );
// 10
console.log( arr );
// [0, 1, 2, 3, 4]
```

```
//使用高阶函数  无initialValue
const arr = [0, 1, 2, 3, 4];
let sum = arr.reduce((accumulator, currentValue, currentIndex, array) => {
  return accumulator + currentValue;
});

console.log( sum );
// 10
console.log( arr );
// [0, 1, 2, 3, 4]
```

```
//使用高阶函数  有initialValue
const arr = [0, 1, 2, 3, 4];
let sum = arr.reduce((accumulator, currentValue, currentIndex, array) => {
  return accumulator + currentValue;
}, 10);

console.log( sum );
// 20
console.log( arr );
// [0, 1, 2, 3, 4]
```

#### 2) 函数作为返回值输出

> isType 函数

我们知道在判断类型的时候可以通过 Object.prototype.toString.call 来获取对应对象返回的字符串，比如：

```

let isString = obj => Object.prototype.toString.call( obj ) === '[object String]';

let isArray = obj => Object.prototype.toString.call( obj ) === '[object Array]';

let isNumber = obj => Object.prototype.toString.call( obj ) === '[object Number]';

```

可以发现上面三行代码有很多重复代码，只需要把具体的类型抽离出来就可以封装成一个判断类型的方法了，代码如下。

```
let isType = type => obj => {
  return Object.prototype.toString.call( obj ) === '[object ' + type + ']';
}

isType('String')('123');		// true
isType('Array')([1, 2, 3]);	// true
isType('Number')(123);			// true
```

这里就是一个高阶函数，因为 isType 函数将 obj => { ... } 这一函数作为返回值输出。

> add 函数
> 我们看一个常见的面试题，用 JS 实现一个无限累加的函数 add，示例如下

```
add(1); // 1
add(1)(2);  // 3
add(1)(2)(3)； // 6
add(1)(2)(3)(4)； // 10
```

我们可以看到结构和上面代码有些类似，都是将函数作为返回值输出，然后接收新的参数并进行计算。

我们知道打印函数时会自动调用 toString()方法，函数 add(a) 返回一个闭包 sum(b)，函数 sum() 中累加计算 a = a + b，只需要重写 sum.toString()方法返回变量 a 就可以了。

```
function add(a) {
    function sum(b) { // 使用闭包
    	a = a + b; // 累加
    	return sum;
    }
    sum.toString = function() { // 重写toString()方法
        return a;
    }
    return sum; // 返回一个函数
}

add(1); // 1
add(1)(2);  // 3
add(1)(2)(3)； // 6
add(1)(2)(3)(4)； // 10
```
