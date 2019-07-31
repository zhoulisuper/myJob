# EventBus

EventBus 是消息传递的一种方式，基于一个消息中心，订阅和发布消息的模式。这种方式的实现不仅仅局限于前端，在 iOS 中的消息中心也是如此实现。

设计模式：订阅者发布者模式，这种设计模式在前端很常见。

API 的设计：

2.1 只能构造一个消息对象

2.2 on(‘msgName’, func)订阅消息，msgName:订阅的消息名称 func: 订阅的消息

2.3 one(‘msgName’, func)仅订阅一次消息，后订阅的会替换前面订阅的消息

2.4 emit(‘msgName’, msg)发布消息 msgName:消息名称 msg：发布的消息

2.5 off(‘msgName’)移除消息

> 实现 EventBus

```
// 构造EventBus
function EventBusClass() {
    this.msgQueues = {}
}

EventBusClass.prototype = {
    // 将消息保存到当前的消息队列中
    on: function(msgName, func) {
        if (this.msgQueues.hasOwnProperty(msgName)) {
            if (typeof this.msgQueues === 'function') {
                this.msgQueues[msgName] = [this.msgQueues[msgName], func]
            } else {
                this.msgQueues[msgName] = [...this.msgQueues[msgName], func]
            }
        } else {
            this.msgQueues[msgName] = func;
        }
    },
    // 消息队列中仅保存一个消息
    one: function(msgName, func) {
        // 无需检查msgName是否存在
        this.msgQueues[msgName] = func;
    },
    // 发送消息
    emit: function(msgName, msg) {
        if (!this.msgQueues.hasOwnProperty(msgName)) {
            return
        }
        if (typeof this.msgQueues[msgName] === 'function') {
            this.msgQueues[msgName](msg)
        } else {
            this.msgQueues[msgName].map((fn) => {
                fn(msg)
            })
        }
    },
    // 移除消息
    off: function(msgName) {
        if (!this.msgQueues.hasOwnProperty(msgName)) {
            return
        }
        delete this.msgQueues[msgName]
    }
}

// 将EventBus放到window对象中
const EventBus = new EventBusClass()
window.EventBus = EventBus
```

> 使用 EventBus

```
// 订阅消息
function subscribe() {
    EventBus.on('first-event', function(msg) {
        alert(`订阅的消息是：${msg}`);
    });
}

// 发送消息
function emit() {
    const msgInput = document.getElementById("msgInputId")
    EventBus.emit('first-event', msgInput.value)
}

// 移除消息
function off(msgName) {
    EventBus.off(msgName)
}
```

> 总结
>
> 整个 EventBus 主要部分是分为三个部分。消息中心，订阅事件方法，发布消息方法。虽然不能和 Vue 中实现的那么全面，但麻雀虽小，五脏俱全。缺少的部分在于对数据安全性的校验。
