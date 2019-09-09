![image](../img/timg.jpg)
<br>

## 前言

events 模块是 node 的核心模块之一，几乎所有常用的 node 模块都继承了 events 模块，比如 http、fs 等。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [基本例子](#一基本例子)
- [error 事件](#二error-事件)
- [EventEmitter类相关方法](#三eventemitter类相关方法)

### 一、基本例子

- [单个事件监听器](#11-单个事件监听器)
- [同个事件，多个事件监听器](#12-同个事件多个事件监听器)
- [只运行一次的事件监听器](#13-只运行一次的事件监听器)
- [注册事件监听器前，事件先触发](#14-注册事件监听器前事件先触发)
- [异步执行，还是顺序执行](#15-异步执行还是顺序执行)
- [移除事件监听器](#16-移除事件监听器)

#### 1.1 单个事件监听器

```
const EventEmitter = require('events');

class MyEmitter extends EventEmitter{}

const myEmitter = new MyEmitter();
myEmitter.on('event',()=>{
    console.log('触发事件');
});
myEmitter.emit('event');
```

上面代码是一个简单的`EventEmitter`实例，绑定了一个监听器，`eventEmitter.on()`用于注册监听器，`eventEmitter.emit()`用于触发事件。

#### 1.2 同个事件，多个事件监听器

```
var EventEmitter = require('events');

class Man extends EventEmitter{}

var man = new Man();

man.on('wakeup',function(){
    console.log('man has woken up');
})

man.on('wakeup',function(){
    console.log('man has woken up agin');
});

man.emit('wakeup');

// 输出如下：
// man has woken up
// man has woken up again
```

#### 1.3 只运行一次的事件监听器

```
var EventEmitter = require('events');

class Man extends EventEmitter{}

var man = new Man();

man.on('wakeup',function(){
    console.log('man has woken up');
});

man.once('wakeup',function(){
    console.log('man has woken up again');
});

man.emit('wakeup');
man.emit('wakeup');

// 输出如下：
// man has woken up
// man has woken up again
// man has woken up
```

#### 1.4 注册事件监听器前，事件先触发

```
var EventEmitter = require('events');

class Man extends EventEmitter {}

var man = new Man();

man.emit('wakeup',1);

man.on('wakeup',function(index){
    console.log('man has woken up ->' + index);
});

man.emit('wakeup',2);

// 输出如下：
// man has woken up ->2

```

#### 1.5 异步执行，还是顺序执行

```
var EventEmitter = require('events');

class Man extends EventEmitter {}

var man = new Man();

man.on('wakeup', function(){
    console.log('man has woken up'); // 代码1
});

man.emit('wakeup');

console.log('woman has woken up');  // 代码2

// 输出如下：
// man has woken up
```

#### 1.6 移除事件监听器

```
var EventEmitter = require('events');

function wakeup(){
    console.log('man has woken up');
}

class Man extends EventEmitter{}

var man = new Man();

man.on('wakeup',wakeup);
man.emit('wakeup');

man.removeListener('wakeup',wakeup);
man.emit('wakeup');

// 输出如下：
// man has woken up
```

### 二、error 事件

EventEmitter定义了一个特殊的事件error，它包含了“错误”的语义，我们在遇到异常的时候通常会发射error事件。

```
var events = require('events');

var emitter = new events.EventEmitter();

emitter.emit('error');
```

### 三、EventEmitter类相关方法

- **addListener(event,listener)：** 指定事件绑定事件处理函数。
- **on(event,listener)：** addListener方法的别名
- **once(event,listener)：** 对指定事件解除事件的处理函数。
- **removeListener(event,listener)：** 对指定事件解除事件的处理函数。
- **removeAllListeners([event])：** 对指定的事件解除所有的事件处理函数。
- **setMaxListeners(n)：** 指定事件处理函数的最大数量。
- **listeners(event)：** 获取指定事件的所有事件处理函数
- **emit(event,[arg1],[arg2])：** 手工触发指定事件

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
