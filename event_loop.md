#### [练习题 1](/EventLoop/demo1.md)

### 事件循环机制![Image text](https://upload-images.jianshu.io/upload_images/6100502-7831389bec37fe52.png?imageMogr2/auto-orient/strip|imageView2/2/w/1024/format/webp)

> 同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入 Event Table 并注册函数

> 当指定的事情完成时，Event Table 会将这个函数移入 Event Queue

> 当栈中的代码执行完毕，执行栈（call stack）中的任务为空时，就会读取任务队列（Event quene）中的事件，去执行对应的回调

> 如此循环，形成 js 的事件循环机制（Event Loop）

#### 1、JS 中异步任务分为两种任务类型：宏任务和微任务

#### 宏任务(task)，可以理解是每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）

> 1、每一个 task 会从头到尾将这个任务执行完毕，不会执行其它

> 2、浏览器为了能够使得 JS 内部 task 与 DOM 任务能够有序的执行，会在一个 task 执行结束后，在下一个 task 执行开始前，对页面进行重新渲染 （task->渲染->task->...）

#### 微任务(jobs)，可以理解是在当前 task 执行结束后立即执行的任务

> 1、也就是说，在当前 task 任务后，下一个 task 之前，在渲染之前

> 2、所以它的响应速度相比 setTimeout（setTimeout 是 task）会更快，因为无需等渲染

> 3、也就是说，在某一个宏任务执行完后，就会将在它执行期间产生的所有微任务都执行完毕（在渲染前）

#### 总结下运行机制

- 执行一个宏任务（栈中没有就从事件队列中获取）
- 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
- 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
- 当前宏任务执行完毕，开始检查渲染，然后 GUI 线程接管渲染
- 渲染完毕后，JS 线程继续接管，开始下一个宏任务（从事件队列中获取）

#### 分别什么样的场景会形成宏任务和微任务呢？

- 宏任务：主代码块，setTimeout，setInterval 等
- 微任务：Promise，process.nextTick 等

#### [练习题 2](/EventLoop/demo2.md)

#### 2、setTimeout、Promise、Async/Await 区别

- setTimeout

```
console.log('script start')
setTimeout(function(){
  console.log('settimeout')
})
console.log('script end')
// 输出顺序：script start->script end->settimeout
```

- Promise
  > Promise 本身是同步的立即执行函数

```
console.log('script start')
let promise1 = new Promise(function (resolve) {
console.log('promise1')
resolve()
console.log('promise1 end')
}).then(function () {
console.log('promise2')
})
setTimeout(function(){
console.log('settimeout')
})
console.log('script end')
// 输出顺序: script start->promise1->promise1 end->script end->promise2->settimeout
```

- async/await

```
async function async1(){
  console.log('async1 start');
  await async2();
  console.log('async1 end')
}
async function async2(){
  console.log('async2')
}
console.log('script start');
async1();
console.log('script end')
```

// 输出顺序：script start->async1 start->async2->script end->async1 end

> async 函数返回一个 Promise 对象，当函数执行的时候，一旦遇到 await 就会先返回，等到触发的异步操作完成，再执行函数体内后面的语句。可以理解为，是让出了线程，跳出了 async 函数体。

#### 3、[用 setTimeout()方法来模拟 setInterval()与 setInterval()之间的什么区别](https://blog.csdn.net/baidu_24024601/article/details/51862488)

#### 思考题

- [如何实现一个睡眠函数？](https://www.cnblogs.com/muzidaitou/p/12734914.html)
- [promise 使用过什么方法？以及区别？](https://es6.ruanyifeng.com/#docs/promise)
- react 和 vue 对于事件是如何处理的？
