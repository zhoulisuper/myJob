# 继承

> 原型链是实现继承最原始的模式，即通过 prototype 属性实现继承。
>
> 弊端
>
> 原型链中引用类型的属性会被所有实例共享的，即所有实例对象使用的是同一份数据，会相互影响。
>
> 无法向父级构造函数传参

```
//父级-构造函数
function Father() {
 this.fatherProp = true
}

//父级-原型属性
Father.prototype.getFatherValue = function() {
 return this.fatherProp
}

//子级-构造函数
function Son() {
 this.sonProp = false
}

//子级-原型属性：继承父级
//即__proto__指向父级的prototype
//若不理解请阅读《一张图彻底KO原型链(prototype,__proto__》
Son.prototype = new Father()

//子级-添加原型方法
Son.prototype.getSonValue = function() {
 return this.sonProp
}

//创建子级的实例对象
var son = new Son()
console.log(son.getFatherValue()) //true
```

> 借用构造函数继承实例属性
>
> 原型链的继承带来的问题可借用构造函数的方式解决。其核心思想是：在子级构造函数中调用父级构造函数。
>
> 如何实现在一个构造函数中调用另一个函数？——call()和 apply()
> 弊端：
>
> 这种方式是通过构造函数实现的，当然也把构造函数自身的问题带过来了——破坏了复用性。因为每个实例都创建了一份副本。

```
//核心思想
function Child () {
  Parent.call(this)
}

//例子
function Father() {
 this.arr = [1,2,3]
}

function Son() {
 //call的第一个函数是this指向的对象,即构造函数的实例对象
 Father.call(this)

 /*上面代码等同于下面这段代码：
 (function() {
  this.arr = [1,2,3]
 }).call(this)
 */
}

var son1 = new Son()
console.log(son1.arr) //1,2,3

var son2 = new Son()
son2.arr.push(4)

console.log(son2.arr) //1,2,3,4
console.log(son1.arr) //1,2,3

//解决传参问题：
function Father(name) {
 this.name = name
}

function Son(name) {
 Father.call(this, name)
}

var son1 = new Son("小名")
console.log(son1.name)  //小名

var son2 = new Son("一灯")
console.log(son2.name)  //一灯
```

> 组合继承 = 原型链 + 借用构造函数。取其长避其短：共享的用原型链，各自的借用构造函数

```
function Father(name) {
 this.name = name
 this.arr = [1,2,3]
}

Father.prototype.getName = function() {
 console.log(this.name)
}

function Son(name, age) {
 Father.call(this, name)
 this.age = age
}

Son.prototype = new Father()
Son.prototype.constructor = Son
Son.prototype.getAge = function() {
 console.log(this.age)
}

var son1 = new Son("小名", 23)
son1.arr.push(4)
console.log(son1.arr) //1,2,3,4
son1.getName()    //小名
son1.getAge()     //23

var son2 = new Son("一灯", 24)
console.log(son2.arr) //1,2,3
son1.getName()    //一灯
son1.getAge()     //24
```

> Object.create()方法

```
var person = {
 name: 'Jiang',
 friends: ['Shelby', 'Court']
}
var anotherPerson = Object.create(person)
console.log(anotherPerson.friends) // ['Shelby', 'Court']
```

> 寄生继承原型属性

```
(function () {
  let Super = function () {}
  Super.prototype = Parent.prototype
  Child.prototype = new Super()
})()
```
