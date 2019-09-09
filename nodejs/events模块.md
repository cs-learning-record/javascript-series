![image](../img/timg.jpg)
<br>

## 前言

events 模块是 node 的核心模块之一，几乎所有常用的 node 模块都继承了 events 模块，比如 http、fs 等。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 基本例子
- 事件发射器
- error 事件
- 继承 EventEmitter

### 一、基本例子

```
const EventEmitter = require('events');

class MyEmitter extends EventEmitter{}

const myEmitter = new MyEmitter();
myEmitter.on('event',()=>{
    console.log('触发事件');
});
myEmitter.emit('event');
```

### 二、事件发射器

```
```

### 三、error 事件

EventEmitter定义了一个特殊的事件error，它包含了“错误”的语义，我们在遇到异常的时候通常会发射error事件。

```
var events = require('events');

var emitter = new events.EventEmitter();

emitter.emit('error');
```

### 四、继承 EventEmitter

### 参考资料

- 《nodejs 开发指南》
- [event 模块](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/events.md)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
