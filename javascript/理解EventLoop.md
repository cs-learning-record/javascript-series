![image](../img/timg.jpg)
<br>

## 前言

`Event Loop`即事件循环，是指浏览器或`Node`的一种解决`javaScript`单线程运行时不会阻塞的一种机制，也就是我们经常使用**异步**的原理。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 堆，栈、队列
- 线程与进程
- [Event Loop](#三event-loop)
- 浏览器中的Event Loop
- 例子
- NodeJS的Event Loop

### 一、堆，栈、队列

![](./img/eventloop.png)

- 堆
- 栈
- 队列

#### 1.1 堆

**堆**是一种数据结构，是利用完全二叉树维护的一组数据，堆分为两种，一种为最大**堆**，一种为最小**堆**，将根节点**最大**的**堆**叫做**最大堆**或**大根堆**，根节点**最小**的**堆**叫做**最小堆**或**小根堆**。

**堆**是**线性数据结构**，相当于**一维数组**，有唯一后继。

如最大堆

![](./img/eventloop01.png)

#### 1.2 栈

**栈**在计算机科学中是限定仅在**表尾**进行**插入**或**删除**操作的线性表。**栈**是一种数据结构，它按照**后进先出**的原则存储数据，**先进入**的数据被压入**栈底**，**最后的数据在栈顶**，需要读数据的时候从**栈顶**开始**弹出数据**。

**栈**是只能在**某一端插入**和**删除**的**特殊线性表**。

![](./img/eventloop02.png)

#### 1.3 队列

特殊之处在于它只允许在表的前端进行**删除**操作，而在表的后端进行**插入**操作，和**栈**一样，**队列**是一种操作受限制的线性表。

进行**插入**操作的端称为**队尾**，进行**删除**操作的称为**队头**。队列中没有元素时，称为**空队列**。

**队列**的数据元素又称为**队列元素**。在队列中插入一个队列元素称为**入队**，从**队列**中一个队列歹毒称为**出队**。因为队列**只允许**在一端**插入**，在另一端**删除**，所以只有**最早**进入**队列**的元素**才能最先从队列中**删除，故队列又称为**先进先出**。

### 二、线程与进程

官方的说法是：进程是 CPU资源分配的最小单位；线程是 CPU调度的最小单位。

这两句话并不好理解，我们先来看张图：

![](./img/eventloop03.png)

- 进程好比图中的工厂，有单独的专属自己的工厂资源。
- 线程好比图中的工人，多个工人在一个工厂中协作工作，工厂与工人是1:n的关系。也就是说**一个进程由一个或多个线程组成，线程是一个进程中代码的不同执行路线**；
- 工厂空间是工厂共享的，这象征一个进程的内在空间是共享的，每个线程都可用这些共享内存。
- 多个工厂之间独立存在。

### 三、Event Loop

在`JavaScript`中，任务被分为两种，一种宏任务（`MacroTask`）也叫`Task`，一种叫微任务（`MicroTask`）。

- MacroTask（宏任务）
- MicroTask（微任务）

#### 3.1 MacroTask（宏任务）

- `script`全部代码、`setTimeout`、`setInterval`、`setImmediate`（浏览器暂时不支持，只有IE10支持，具体可见MDN）、`I/O`、`UI Rendering`。

#### 3.2 MicroTask（微任务）

- `Process.nextTick(node独有)`、`Promise`、`Object.observe(废弃)`、`MutationObserver`（具体使用方式查看[这里](http://javascript.ruanyifeng.com/dom/mutationobserver.html）

### 四、浏览器中的Event Loop

`Javascript`有一个`main thread`主线程和`call-stack`调用栈(执行栈)，所有的任务都会被放到调用栈等待主线程执行。

- JS调用栈
- 同步任务和异步任务
- Event Loop 过程解析

#### 4.1 JS调用栈

JS调用栈采用的是后进先出的规则，当函数执行的时候，会被添加到栈的顶部，当执行完后，就会从栈顶移出，直到栈内被清空。

#### 4.2 同步任务和异步任务

`JavaScript`单线程任务被分为**同步任务**和**异步任务**，同步任务会在调用栈中按照顺序等待主线程依次执行，异步任务会在异步任务有了结果后，将注册的回调函数放入任务队列中等待主线程空闲的时候（调用栈被清空），被读取到栈内等待主线程的执行。

![](./img/eventloop05.jpg)

任务队列`Task Queue`，即队列，是一种先进先出的一种数据结构。

#### 4.3 Event Loop 过程解析

![](./img/eventloop04.png)

- 一开始执行栈空我们可以把**执行栈认为是一个存储函数调用的栈结构，遵循先进后出的原则。**micro队列空，macro队列里有且只有一个script脚本（整体代码）。
- 全局上下文（script标签）被推入执行栈，同步代码执行。在执行的过程中，会判断是同步任务还是异步任务，通过对一些接口的调用，可以产生新的 macro-task 与 micro-task，它们会分别被推入各自的任务队列里，同步代码执行完了，script脚本会被移出macro队列，这个过程本质上是队列的macro-task的执行和出队的过程。
- 上一步我们出队的是一个macro-task,这一步我们处理的是micro-task。但需要注意的是：当macro-task出队时，任务是**一个一个**执行的；而micro-task出队时，任务是**一队一队**执行的。因此我们处理micro队列这一步，会逐个执行队列中的任务并把它出队，直到队列被清空。
- **执行渲染操作，更新界面**
- 检查是否存在Web worker任务，如果有，则对其进行处理
- 上述过程循环往复，直到两个队列都清空

我们总结一下，每一次循环都是一个这样的过程：

![](./img/eventloop07.png)

**当某个宏任务执行完后，会查看是否有微任务队列，如果有，先执行微任务队列中的所有任务，如果没有，会读取宏任务队列中排在最前的任务，执行宏任务的过程中，遇到微任务，依次加入微任务队列。栈空后，再依次读取微任务队列里的任务，依次类推。**

**mactotask & microtask的执行顺序**

![](./img/eventloop06.png)

### 五、例子

#### 5.1 例一

```
    console.log('start');
    
    setTimeout(function(){
        console.log('setTimeout')
    },0)

    Promise.resolve().then(function(){
        console.log('promise1')
    }).then(function(){
        console.log('promise2')
    })

    console.log('end')

```

打印台输出的log顺序是什么？结合上述的步骤分析。

![](./img/browser-deom1-excute-animate.gif)

- 首先，全局代码（main()）压入调用栈执行，打印`start`;
- 接下来`setTimeout`压入`macrotask`队列，`promise.then`回调放入`microtask`队列，最后执行`console.log('end')`，打印出`end`;
- 至此，调用栈中的代码被执行完成，回顾`macrotask`的定义，我们知道全局代码属于`macrotask`，`macrotask`执行完，那接下来就是执行`microtask`队列的任务了，执行`promise`回调打印`promise1`;
- `promise`回调函数默认返回`undefined`，promise状态变为`fullfill`触发接下来的`then`架设，继续压入`microtask`队列，**event loop会把当前的microtask队列一直执行完**，此时执行第二个`promise.then`回调打印出`promise2`;
- 这时`microtask`队列已经为空，从上面的流程图可以知道，接下来主线程会去做一些UI渲染工作，然后开始下一轮`event loop`,执行`setTimeout`的回调，打印出`setTimeout`;

#### 5.2 例二

```
Promise.resolve().then(()=>{
    console.log('Promise1')
    setTimeout(()=>{
        console.log('setTimeout2');
    },0)
});
setTimeout(()=>{
    console.log('setTimeout1');
    Promise.resolve().then(()=>{
        console.log('Promise2')
    })
},0)

```

最后输出结果是Promise1，setTimeout1，Promise2，setTimeout2

- 一开始执行栈的同步任务（这属于宏任务）执行完毕，会去查看是否有微任务队列，上题中存在（有且只有一个），然后执行微任务队列中的所有任务输出`Promise1`，同时会生成一个宏任务`setTimeout2`
- 然后去查看宏任务队列，宏任务`setTimeout1`在`setTimeout2`之前，先执行宏任务`setTimeout1`，输出`setTimeout1`
- 在执行宏任务`setTimeout1`时会生成微任务`Promise2`，放入微任务队列中，接着先去清空微任务队列中的所有任务，输出`Promise2`
- 清空完微任务队列中的所有任务后，就又会去宏任务队列取一个，这回执行的是`setTimeout2`

### 六、NodeJS的Event Loop

- 6.1 Node简介
- 6.2 六个阶段
- 6.3 Micro-Task 与 Macro-Task
- 6.4 注意点

#### 6.1 Node简介

Node中Event Loop和浏览器中的是完全不相同的东西。Nodejs采用V8作为js的解析引擎，而I/O处理方面使用了自己设计的libuv，libuv是一个基于事件驱动的跨平台抽象层，封装了不同操作系统一些底层特性，对处提供统一的API，事件循环机制也是它里面的实现。

![](./img/eventloop08.png)

Node.js的运行机制如下：

- V8引擎解析JavaScript脚本。
- 解析后的代码，调用Node API。
- libuv库负责Node API的执行。它将不同的任务分配给不同的线程，形成一个Event Loop(事件循环)，以异步的方式将任务的执行结果返回给V8引擎。
- V8引擎再将结果返回给用户。

#### 6.2 六个阶段

其中libuv引擎中的事件循环分为6个阶段，它们会按照顺序反复运行。每当进入某一个阶段的时候，都会从对应的架设队列中取出函数去执行。当队列为空或者执行的回调函数数量达到系统设定的阈值，就会进入下一阶段。

![](./img/eventloop09.png)

从上图中，大致看出node中的事件循环的顺序：

外部输入数据——>轮询阶段(poll)——>检查阶段(check)——>关闭事件回调阶段(close callback)——>定时器检测阶段(timer)——>I/O事件回调阶段(I/O callbacks)——>闲置阶段(idle,prepare)——>轮询阶段(按照该顺序反复运行)

- timers阶段：这个阶段执行timer(setTimeout、setInterval)的回调
- I/O callbacks阶段：处理一些上一轮循环中的少数未执行的I/O回调
- idle,prepare阶段：仅node内部使用
- poll阶段：获取新的I/O事件，适当的条件下node将阻塞在这里
- check阶段：执行setImmediate()的回调
- close callbacks阶段：执行socket的close事件回调

注意：**上面六个阶段都不包括**process.nextTick()

接下去我们详细介绍`timers`、`poll`、`check`这3个阶段，因为日常开发中的绝大部分异步任务都是在这3个阶段处理的。

**(1) timer**

timers阶段会执行setTimeout和setInterval回调，并且是由poll阶段控制的。同样，**在Node中定时器指定的时间也不是准确时间，只能是尽快执行**。

**(2) poll**

poll是一个至关重要的阶段，这一阶段中，系统会做两件事情

1. 回到timer阶段执行回调
2. 执行I/O回调

并且在进入该阶段时如果没有设定了timer的话，会发生以下两件事情

- 如果poll队列不为空，会遍历回调队列并同步执行，直到队列为空或者达到系统限制
- 如果poll队列为空时，会有两件事发生
  - 如果有setImmediate回调需要执行，poll阶段会停止并且进入到check阶段执行回调
  - 如果没有setImmediate回调需要执行，会先行回调被加入到队列中并立即执行回调，这里同样会有个超时时间设置防止设置防止一直先行下去

当然设定了timer的话且poll队列为空，则会判断是否有timer超时，如果有的话会回到timer阶段执行回调。

**(3) check阶段**

setImmediate()的回调会被加入check队列中，从event loop的阶段图可以知道，check阶段的执行顺序在poll阶段之后。

我们先来看个例子：

```
console.log('start')
setTimeout(()=>{

},0);
setTimeout(()=>{
    console.log('timer2')
    Promise.resolve().then(function(){
        console.log('promise2')
    },0)
})
Promise.resolve().then(function(){
    console.log('promise3')
})
console.log('end');
// start=>end=>promise3=>timer1=>timer2=>promise1=>promise2
```

- 一开始执行栈的同步任务(这属于宏任务)执行完毕后(依次打印出start end,并将2个timer依次放入timer队列)，会先去执行微任务(**这点跟浏览器端的一样**)，所以打印出promise3
- 然后进入timer阶段，执行timer1的回调函数，打印timer1，并将promise.then回调放入microtask队列，同样的步骤执行timer2，打印timer2；这点跟浏览器端相差比较大，**timer阶段有几个setTimeout/setInterval都会依次执行**，并不像浏览器端，每执行一个宏任务后就支执行一个微任务。

#### 6.3 Micro-Task 与 Macro-Task

Node端事件循环中的异步队列也是这两种：macro（宏任务）队列和 micro（微任务）队列。

- 常见的macro-task比如：setTimeout、setInterval、setImmediate、script(整体代码)、I/O操作等。
- 常见的micro-task比如：process.nextTick、new Promise().then(回调)等。

#### 6.4 注意点

**(1) setTimeout 和 setImmediate**

二者非常相似，区别主要在于调用时间时机不同。

- setImmediate设计在poll阶段完成时执行，即check阶段。
- setTimeout设计在poll阶段为空闲时，且设定到达后执行，但它的timer阶段执行

```
setTimeout(function timeout(){
    console.log('timeout');
},0);
setImmediate(function immediate(){
 console.log('immediate');
})

```

- 对于以上代码来说，setTimeout可能执行在前，也可能执行在后。
- 首先setTimeout(fn,0)===setTimeout(fn,1)这是由源码决定的进入事件循环也是需要成本的，如果在准备时候花费了大于1ms的时间，那么在timer阶段就会直接执行setTimeout回调
- 如果准备时间花费小于1ms，那么就setImmediate回调先执行了

但当二者在异步i/o callback内部调用时，总是先执行setImmediate，再执行setTimeout

```
const fs = require('fs');

fs.readFile(__filename,()=>{
    setTimeout(()=>{
        console.log('timeout')
    },0)
    setImmediate(()=>{
        console.log('immedidate')
    })
})

// immediate
// timeout

```

在上述代码中，setImmediate永远先执行。因为两个代码写在IO回调中，IO回调是在poll阶段执行，当回调执行完毕后队列为空，发现存在setImmediate回调，所以就直接跳转到check阶段去执行回调了。

**(2) process.nextTick**

这个函数其实是独立于Event Loop之外的，它有一个自己的队列，当每个阶段完成后，如果存在nextTick队列，就会清空队列中的所有回调函数，并且优先于其他microtask执行。

```
setTimeout(()=>{
    console.log('timer1')
    Promise.resolve().then(function(){
        console.log('promise1')
    })
},0)
process.nextTick(()=>{
    console.log('nextTick')
    process.nextTick(()=>{
        console.log('nextTick');
        process.nextTick(()=>[
            console.log('nextTick')
            process.nextTick(()=>[
                console.log('nextTick')
            ])
        ])
    })
})

```

### 七、Node与浏览器的 Event Loop 差异

**浏览器环境下，microtask的任务队列是每个macrotask执行完之后执行。而在Node.js中，microtask会在事件循环的各个阶段之间执行，也就是一个阶段执行完毕，就会去执行microtask队列的任务。**

![](./img/eventloop10.png)

接下我们通过一个例子来说明两者区别：

```
setTimeout(()=>{
    console.log('timer1')
    Promise.resolve().then(function(){
        console.log('promise1')
    })
},0)

setTimeout(()=>{
    console.log('timer2')
    Promise.resolve().then(function(){
        console.log('promise2')
    })
},0)

```

浏览器端运行结果：`timer1=>promise1=>timer2=>promise2`

浏览器的处理过程如下:

![](./img/browser-deom2-excute-animate.gif)

Node端运行结果分两种情况：

- 如果node11版本一量执行一个阶段里的一个宏任务(setTimeout.setInterval和setImmediate)就立刻执行微任务队列，这就跟浏览器端运行一致，最后的结果为`timer1=>promise1=>timer2=>promise2`
- 如果是node10及其之前版本：要看第一个定时器执行完，第二个定时器是否在完成队列中。
  - 如果是第二个定时器还未在完成队列中，最后的结果为`timer1=>promise1=>timer2=>promise2`
  - 如果是第二个定时器已经完成队列中，则最后的结果为`timer1=>timer2=>promise1=>promise2`

1. 全局脚本(main())执行，将2个timer依次放入timer队列，main()执行完毕，调用栈空闲，任务队列开始执行；
2. 首先进入timer阶段，执行timer1的回调函数，打印timer1，并将promise1.then回调放入microtas队列，同样的步骤执行timer2,打印timer2;
3. 至此，timer阶段执行结束，event loop进入下一个阶段之前，执行microtask队列的所有任务，依次打印promise1、promise2

Node端的处理过程如下：

![](./img/nodejs-deom-excute-animate.gif)


### 总结

浏览器和Node 环境下，microtask 任务队列的执行时机不同

- Node端，microtask在事件循环的各个阶段之间执行
- 浏览器端，microtask在事件循环的macrotask执行完之后执行

### 参考资料

- [一次弄懂Event Loop（彻底解决此类面试问题）](https://juejin.im/post/5c3d8956e51d4511dc72c200)
- [Event Loop](https://juejin.im/book/5bdc715fe51d454e755f75ef/section/5be04a8e6fb9a04a072fd2cd)
- [从浏览器多进程到JS单线程，JS运行机制最全面的一次梳理](https://juejin.im/post/5a6547d0f265da3e283a1df7)
- [浏览器与Node的事件循环(Event Loop)有何区别?](https://github.com/ljianshu/Blog/issues/54)
- [这一次，彻底弄懂 JavaScript 执行机制](https://juejin.im/post/59e85eebf265da430d571f89)
- [详解JavaScript中的Event Loop（事件循环）机制](https://zhuanlan.zhihu.com/p/33058983)
- [Event Loop](https://juejin.im/book/5bdc715fe51d454e755f75ef/section/5be04a8e6fb9a04a072fd2cd)


## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
