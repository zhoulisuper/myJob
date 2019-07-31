JavaScript 中的 this 含义要丰富得多，它可以是全局对象、当前对象或者任意对象，这完全取决于函数的调用方式。JavaScript 中函数的调用有以下几种方式：
作为对象方法调用
作为函数调用
作为构造函数调用
使用 apply 或 call,bind 调用
下面我们将按照调用方式的不同，分别讨论 this 的含义。

> 1、作为对象方法调用
>
> 在 JavaScript 中，函数也是对象，因此函数可以作为一个对象的属性，此时该函数被称为该对象的方法，在使用这种调用方式时，this 被自然绑定到该对象

```

var test = {
a:1,
b:0,
get:function(){
return this.a;
}
}
test.get()//1

```

> 2.作为函数调用
>
> 函数也可以直接被调用，此时 this 绑定到全局对象。在浏览器中，window 就是该全局对象。比如下面的例子：函数被调用时，this 被绑定到全局对象，接下来执行赋值语句，相当于隐式的声明了一个全局变量，这显然不是调用者希望的

```

function makeNoSense(x) {
this.x = x;
}

```

> 3.作为构造函数调用
>
> javaScript 支持面向对象式编程，与主流的面向对象式编程语言不同，JavaScript 并没有类（class）的概念，而是使用基于原型（prototype）的继承方式。相应的，JavaScript 中的构造函数也很特殊，如果不使用 new 调用，则和普通函数一样。作为又一项约定俗成的准则，构造函数以大写字母开头，提醒调用者使用正确的方式调用。如果调用正确，this 绑定到新创建的对象上。

```

function Point(x, y){
this.x = x;
this.y = y;
}

```

> 当应用于内部函数，即声明在另外一个函数体内的函数，这种绑定到全局对象的方式会产生另外一个问题。我们仍然以前面提到的 point 对象为例，这次我们希望在 moveTo 方法内定义两个函数，分别将 x，y 坐标进行平移。结果可能出乎大家意料，不仅 point 对象没有移动，反而多出两个全局变量 x，y。

```

var point = {
x : 0,
y : 0,
moveTo : function(x, y) {
// 内部函数
var moveX = function(x) {
this.x = x;//this 绑定到了哪里？
};
// 内部函数
var moveY = function(y) {
this.y = y;//this 绑定到了哪里？
};

    moveX(x);
    moveY(y);
    }

};
point.moveTo(1, 1);
point.x; //==>0
point.y; //==>0
x; //==>1
y; //==>1

```

> 这属于 JavaScript 的设计缺陷，正确的设计方式是内部函数的 this 应该绑定到其外层函数对应的对象上，为了规避这一设计缺陷，聪明的 JavaScript 程序员想出了变量替代的方法，约定俗成，该变量一般被命名为 that。

```

var point = {
x : 0,
y : 0,
moveTo : function(x, y) {
var that = this;
// 内部函数
var moveX = function(x) {
that.x = x;
};
// 内部函数
var moveY = function(y) {
that.y = y;
}
moveX(x);
moveY(y);
}
};
point.moveTo(1, 1);
point.x; //==>1
point.y; //==>1

```

> 4.在 call 或者 apply，bind 中调用
>
> 让我们再一次重申，在 JavaScript 中函数也是对象，对象则有方法，apply 和 call 就是函数对象的方法。这两个方法异常强大，他们允许切换函数执行的上下文环境（context），即 this 绑定的对象。很多 JavaScript 中的技巧以及类库都用到了该方法。让我们看一个具体的例子
>
> 当一个函数被 call、apply 或者 bind 调用时，this 的值就取传入的对象的值。

```

var obj = {
x: 10
}
function foo(){
console.log(this); //{x: 10}
console.log(this.x); //10
}
foo.call(obj);
foo.apply(obj);
foo.bind(obj)();

```

> 5.箭头函数中的 this
>
> 当使用箭头函数的时候，情况就有所不同了：箭头函数内部的 this 是词法作用域，由上下文确定。
>
> 现在，箭头函数完全修复了 this 的指向，this 总是指向词法作用域，也就是外层调用者 obj。

```

var obj = {
x: 10,
foo: function() {
var fn = () => {
return () => {
return () => {
console.log(this); //Object {x: 10}
console.log(this.x); //10
}
}
}
fn()()();
}
}
obj.foo();

```
