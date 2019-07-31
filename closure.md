#### 方法一

> 原理：利用 setTimeout 函数的第三个参数，会作为回调函数的第一个参数传入

```
代码一
for (var i = 0; i < 10; i++) {
  setTimeout(i => {
    console.log(i);
  }, 1000, i)
}
代码二
for (var i = 0; i < 10; i++) {
  setTimeout(console.log, 1000, i)
}
```

#### 方法二

> 原理：利用 let 变量的特性 — 在每一次 for 循环的过程中，let 声明的变量会在当前的块级作用域里面（for 循环的 body 体，也即两个花括号之间的内容区域）创建一个文法环境（Lexical Environment），该环境里面包括了当前 for 循环过程中的 i

```
代码一
for (let i = 0; i < 10; i++) {
  setTimeout(() => {
    console.log(i);
  }, 1000)
}
等价于
for (let i = 0; i < 10; i++) {
  let _i = i;// const _i = i;
  setTimeout(() => {
    console.log(_i);
  }, 1000)
}
```

#### 方法三

> 原理：利用函数自执行的方式，把当前 for 循环过程中的 i 传递进去，构建出块级作用域。IIFE 其实并不属于闭包的范畴。

```
for (var i = 0; i < 10; i++) {
  (i => {
    setTimeout(() => {
      console.log(i);
    }, 1000)
  })(i)
}
```

#### 方法四

> 原理：

```
for (var i = 0; i< 10; i++){
    new Promise((r,ej) => ej(i)).catch(i => setTimeout(() => {
          console.log(i);
      }, 1000))
  }
```
