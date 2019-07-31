### 一、单例模式

定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点

核心：确保只有一个实例，并提供全局访问

实现：假设要设置一个管理员，多次调用也仅设置一次，我们可以使用闭包缓存一个内部变量来实现这个单例

```
function SetManager(name) {
    this.manager = name;
}

SetManager.prototype.getName = function() {
    console.log(this.manager);
};

var SingletonSetManager = (function() {
    var manager = null;

    return function(name) {
        if (!manager) {
            manager = new SetManager(name);
        }

        return manager;
    }
})();

SingletonSetManager('a').getName(); // a
SingletonSetManager('b').getName(); // a
SingletonSetManager('c').getName(); // a
```

### 二、策略模式

1. 定义

定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

2. 核心

将算法的使用和算法的实现分离开来。

一个基于策略模式的程序至少由两部分组成：

第一个部分是一组策略类，策略类封装了具体的算法，并负责具体的计算过程。

第二个部分是环境类 Context，Context 接受客户的请求，随后把请求委托给某一个策略类。要做到这点，说明 Context 中要维持对某个策略对象的引用

3. 实现

策略模式可以用于组合一系列算法，也可用于组合一系列业务规则

假设需要通过成绩等级来计算学生的最终得分，每个成绩等级有对应的加权值。我们可以利用对象字面量的形式直接定义这个组策略

在组合业务规则方面，比较经典的是表单的验证方法。

```
// 加权映射关系
var levelMap = {
    S: 10,
    A: 8,
    B: 6,
    C: 4
};

// 组策略
var scoreLevel = {
    basicScore: 80,

    S: function() {
        return this.basicScore + levelMap['S'];
    },

    A: function() {
        return this.basicScore + levelMap['A'];
    },

    B: function() {
        return this.basicScore + levelMap['B'];
    },

    C: function() {
        return this.basicScore + levelMap['C'];
    }
}

// 调用
function getScore(level) {
    return scoreLevel[level] ? scoreLevel[level]() : 0;
}

console.log(
    getScore('S'),
    getScore('A'),
    getScore('B'),
    getScore('C'),
    getScore('D')
); // 90 88 86 84 0
```

4. 优缺点

优点

可以有效地避免多重条件语句，将一系列方法封装起来也更直观，利于维护

缺点

往往策略集会比较多，我们需要事先就了解定义好所有的情况

### 三、代理模式

1. 定义

为一个对象提供一个代用品或占位符，以便控制对它的访问

2. 核心

当客户不方便直接访问一个 对象或者不满足需要的时候，提供一个替身对象 来控制对这个对象的访问，客户实际上访问的是 替身对象。

替身对象对请求做出一些处理之后， 再把请求转交给本体对象

代理和本体的接口具有一致性，本体定义了关键功能，而代理是提供或拒绝对它的访问，或者在访问本体之前做一 些额外的事情

3. 实现

代理模式主要有三种：保护代理、虚拟代理、缓存代理

> 保护代理主要实现了访问主体的限制行为，以过滤字符作为简单的例子

```
// 主体，发送消息
function sendMsg(msg) {
    console.log(msg);
}

// 代理，对消息进行过滤
function proxySendMsg(msg) {
    // 无消息则直接返回
    if (typeof msg === 'undefined') {
        console.log('deny');
        return;
    }

    // 有消息则进行过滤
    msg = ('' + msg).replace(/泥\s*煤/g, '');

    sendMsg(msg);
}


sendMsg('泥煤呀泥 煤呀'); // 泥煤呀泥 煤呀
proxySendMsg('泥煤呀泥 煤'); // 呀
proxySendMsg(); // deny
```

它的意图很明显，在访问主体之前进行控制，没有消息的时候直接在代理中返回了，拒绝访问主体，这数据保护代理的形式

> 虚拟代理在控制对主体的访问时，加入了一些额外的操作

在滚动事件触发的时候，也许不需要频繁触发，我们可以引入函数节流，这是一种虚拟代理的实现

```
// 函数防抖，频繁操作中不处理，直到操作完成之后（再过 delay 的时间）才一次性处理
function debounce(fn, delay) {
    delay = delay || 200;

    var timer = null;

    return function() {
        var arg = arguments;

        // 每次操作时，清除上次的定时器
        clearTimeout(timer);
        timer = null;

        // 定义新的定时器，一段时间后进行操作
        timer = setTimeout(function() {
            fn.apply(this, arg);
        }, delay);
    }
};

var count = 0;

// 主体
function scrollHandle(e) {
    console.log(e.type, ++count); // scroll
}

// 代理
var proxyScrollHandle = (function() {
    return debounce(scrollHandle, 500);
})();

window.onscroll = proxyScrollHandle;
```

> 缓存代理可以为一些开销大的运算结果提供暂时的缓存，提升效率

```
/ 主体
function add() {
    var arg = [].slice.call(arguments);

    return arg.reduce(function(a, b) {
        return a + b;
    });
}

// 代理
var proxyAdd = (function() {
    var cache = [];

    return function() {
        var arg = [].slice.call(arguments).join(',');

        // 如果有，则直接从缓存返回
        if (cache[arg]) {
            return cache[arg];
        } else {
            var ret = add.apply(this, arguments);
            return ret;
        }
    };
})();

console.log(
    add(1, 2, 3, 4),
    add(1, 2, 3, 4),

    proxyAdd(10, 20, 30, 40),
    proxyAdd(10, 20, 30, 40)
); // 10 10 100 100
```

### 四、迭代器模式

1. 定义

迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

2. 核心

在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素

3. 实现

JS 中数组的 map forEach 已经内置了迭代器
