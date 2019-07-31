# 节流和防抖

#### 节流

> 思路：在规定时间内只触发一次
>
> 高频事件触发，但在 n 秒内只会执行一次，所以节流会稀释函数的执行频率
>
> 每次触发事件时都判断当前是否有等待执行的延时函数
>
> 应用场景：可以将一些事件降低触发频率。比如懒加载时要监听计算滚动条的位置，但不必每次滑动都触发，可以降低计算的频率，而不必去浪费资源；另外还有做商品预览图的放大镜效果时，不必每次鼠标移动都计算位置。

```
function throttle (fn, delay) {
  // 利用闭包保存时间
  let prev = Date.now()
  return function () {
    let context = this
    let arg = arguments
    let now = Date.now()
    if (now - prev >= delay) {
      fn.apply(context, arg)
      prev = Date.now()
    }
  }
}

function fn () {
  console.log('节流')
}
addEventListener('scroll', throttle(fn, 1000))
```

#### 15.实现一个防抖函数

> 思路:在规定时间内未触发第二次，则执行
>
> 触发高频事件后 n 秒内函数只会执行一次，如果 n 秒内高频事件再次被触发，则重新计算时间
>
> 每次触发事件时都取消之前的延时调用方法

> 应用场景：一般可以使用在用户输入停止一段时间过后再去获取数据，而不是每次输入都去获取

```
function debounce (fn, delay) {
  // 利用闭包保存定时器
  let timer = null
  return function () {
    let context = this
    let arg = arguments
    // 在规定时间内再次触发会先清除定时器后再重设定时器
    clearTimeout(timer)
    timer = setTimeout(function () {
      fn.apply(context, arg)
    }, delay)
  }
}

function fn () {
  console.log('防抖')
}
addEventListener('scroll', debounce(fn, 1000))
```
