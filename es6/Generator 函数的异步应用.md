![image](../img/timg.jpg)
<br>

## 前言

本人平时学习及收集内容，欢迎参入一起讨论。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 传统方法
- 基本概念
- Generator 函数
- Thunk 函数
- co 函数

### 一、传统方法

ES6 诞生以前，异步编程的方法，大概有下面四种。

- 回调函数
- 事件监听
- 发布/订阅
- Promise 对象

### 二、基本概念

- 异步
- 回调函数
- Promise

#### 2.1 异步

所谓“异步”，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务第二段（处理文件）。这种不连续的执行，就叫做异步。

相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

#### 2.2 回调函数

js 语言对异步编程的实现，就是回调函数。所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。回调函数的英语名字`callback`，直译过来就是“重新调用”。

读取文件进行处理，是这样写的。

```
fs.readFile('/etc/passwd','utf-8',function(err,data){
    if(err) throw err;
    console.log(data);
})
```

上面代码中，`readFile` 函数的第三个参数，就是回调函数，也就是任务的第二段。等到操作系统返回了`/etc/passwd`这个文件以后，回调函数才会执行。

#### 2.3 Promise

回调函数本身并没有问题，它的问题出现在多个回调函数嵌套。就会出现多重嵌套。代码不是纵向发展，而是横向发展，很快就会乱成一团，无法管理。因为多个异步操作形成了强耦合，只要有一个操作需要修改，它的上层回调函数和下层回调函数，可能都要跟着修改。

Promise 对象就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套，改成链式调用。

```
var readFile = require('fs-readfile-promise');

readFile(fileA)
.then(function (data) {
  console.log(data.toString());
})
.then(function () {
  return readFile(fileB);
})
.then(function (data) {
  console.log(data.toString());
})
.catch(function (err) {
  console.log(err);
});
```

可以看到，Promise 的写法只是回调函数的改进，使用`then`方法以后，异步任务的两段执行看得更清楚了，除此以外，并无新意。

Promise 的最大问题是代码冗余，原来的任务被 Promise 包装了一下，不管什么操作，一眼看去都是一堆`then`，原来的语义变得很不清楚。

### 三、Generator 函数

- 协程
- 协程的 Generator 函数实现
- Generator 函数的数据交换和错误处理
- 异步任务的封装

#### 3.1 协程

协程，是多个线程互相协作，完成异步任务。

协程有点像函数，又有点像线程。它的运行流程大致如下。

- 第一步，协程`A`开始执行。
- 第二步，协程`A`执行到一半，进入暂停，执行权转移到协程`B`。
- 第三步，（一段时间后）协程`B`交还执行权。
- 第四步，协程`A`恢复执行。

读取文件的协程写法如下。

```
function * asyncJob(){
    //  ...其他代码
    var f = yield readFile(fileA);
}
```

上面代码的函数 `asyncJob` 是一个协程，它的奥妙就在其中的`yield`命令。它表示执行到此处，执行权将交给其他协程。也就是说，`yield`命令是异步两个阶段的分界线。

协程遇到`yield`命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作，如果去除`yield`命令，简直一模一样。

#### 3.2 协程的 Generator 函数实现

Gernerator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用`yield`语句注明。

```
function * get(x){
    var y = yield x+2;
    return y;
}

var g = gen(1);
g.next();   // {value:3,done:false}
g.next();   // {value:undefined,done:true}
```

#### 3.3 Generator 函数的数据交换和错误处理

Generator 函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。险些之外，它还有两个特性，使它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制。

`next`返回值的 value 属性，是 Generator 函数向外输出数据；`next`方法还可以接受参数，向 Generator 函数体内输入数据。

```
function * gen(x){
    var y = yield x+2;
    return y;
}

var g = gen(1);
g.next()    // {value:3,done:false}
g.next(2)   // {value:2,done:true}
```

Generator 函数内部还可以部署错误处理代码，捕获函数体外抛出的错误。

```
function * gen(x){
    try{
        var y = yield x+2;
    }catch(e){
        console.log(e);
    }
    return y;
}

var g = gen(1);
g.next();
g.throw('出错了');
// 出错了
```

Generator 函数体外，使用指针对象的`throw`方法抛出的错误，可以被函数体内的`try...catch`代码块捕获。

#### 3.4 异步任务的封装

```
var fetch = require('node-fetch');

function* gen(){
    var url = 'https://api.github.com/users/github';
    var result = yield fetch(url);
    console.log(result.bio)
}

var g = gen();
var result = g.next();
result.value.then(function(data){
    return data.json();
}).then(function(data){
    g.next(data);
})
```

首先执行 Generator 函数，获取遍历器对象，然后使用 `next` 方法（第二行），执行异步任务的第一阶段。

### 四、Thunk 函数

Thunk 函数是自动执行 Generator 函数的一种方法。

- Thunk 函数的含义
- js 语言的 Thuck 函数
- Generator 函数的流程管理
- Thunk 函数的自动流程管理

#### 4.1 Thunk 函数的含义

编译器的“传名调用”实现，往往是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做 Thunk 函数。

#### 4.2 js 语言的 Thuck 函数

经过转换器处理，它变成了一个单参数函数，只接受回调函数作为参数。这个单参数版本，就叫做 Thunk 函数。

Thunk 函数转换器。

```
// ES5版本
var Thunk = function(fn){
    return function(){
        var args = Array.prototype.slice.call(arguments);
        return function (callback){
            args.push(callback);
            return fn.apply(this,args);
        }
    };
}

// ES6版本
const Thunk = function(fn){
    return function(...args){
        return function(callback){
            return fn.call(this,...args,callback);
        }
    }
};

```

#### 4.3 Generator 函数的流程管理

Thunk 函数可以用于 Generator 函数的自动流程管理。

```
var fs = requires('fs');
var thunkify = require('thunkify');
var readFileThunk = thunkify(fs.readFile);

var gen = function* (){
    var r1 = yield readFileThunk('/etc/fstab');
    console.log(r1.toString());
    console.log(r2.toString())
}
```

#### 4.4 Thunk 函数的自动流程管理

基于 Thunk 函数的 Generator 执行器。

```
function run(fn){
    var gen = fn();

    function next(err,data){
        var result = gen.next(data);
        if(result.done)  return;
        result.value(next);
    }
    next();
}

function* g() {
  // ...
}

run(g);
```

### 五、co 函数

- 基本用法
- co 模块的原理
- co 模块的源码
- 处理并发的异步操作

#### 5.1 基本用法

`co`函数返回一个`Promise`对象，因此可以用`then`方法添加回调函数。

```
co.(gen).then(function(){
    console.log('Generator 函数执行完成');
})
```

#### 5.2 co 模块的原理

Generator 是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。

两种方法可以做到这一点

1. 回调函数。将异步操作包装成 Thunk 函数，在回调函数里面交回执行权。
2. Promise 对象。将异步操作包装成 Promise 对象，用`then`方法交回执行权。

co 模块其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个模块。

#### 5.3 co 模块的源码

1. `co` 函数接受 Generator 函数作为参数，返回一个 Promise 对象。
2. `co` 先检查参数 gen 是否为 Generator 函数。如果是，就执行该函数，得到一个内部指针对象；如果不是就返回，并将 Promise 对象的状态改为 resolved。
3. `co` 将 Generator 函数的内部指针对象的 next 方法，包装成 `onFulfilled` 函数。这主要是为了能够捕捉抛出的错误。
4. next 函数，它会反复调用自身。

```
function co(gen){
    var ctx = this;
    return new Promise(function(resolve,reject){
        if( type gen === 'function') gen = gen.call(ctx);
        if(!gen || typeof gen.next !=='function') return resolve(gen);

        onFulfilled();
        function onFulfilled(res){
            var ret;
            try{
                ret = gen.next(res);
            }catch(e){
                return reject(e);
            }
            next(ret);
        }

        function next(ret){
            if (ret.done) return resolve(ret.value);
            var value = toPromise.call(ctx, ret.value);
            if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
            return onRejected(
                new TypeError(
                'You may only yield a function, promise, generator, array, or object, '
                + 'but the following object was passed: "'
                + String(ret.value)
                + '"'
                )
            );
        }
    });
}
```

#### 5.4 处理并发的异步操作

co 支持迸发的异步操作，即允许某些操作同时进行，等到它们全部完成，才进行下一步。

```
// 数组的写法
co(function* (){
    var res = yield[
        Promise.resolve(1),
        Promise.resolve(2)
    ];
    console.log(res);
}).catch(onerror);


// 对象的写法
co(function*() {
    var res = yield{
        1:Promise.resolve(1),
        2:Promise.resolve(2)
    };
    console.log(res);
}).catch(onerror);

```

### 参考资料

- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/generator-async)
- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/generator-async.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
