![image](../img/timg.jpg)
<br>

## 前言

在异步编程中，Promise 扮演了举足轻重的角色，它解决了 ajax 请求过程中的回调地狱的问题，令代码更具可读性。

`Promise`对象有以下两处特点：

- 对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。如果改变已经发生了，你再对`Promise`对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错误了它，再去监听，是得不到结果的。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 基本用法
- then()
- Promise 的异常捕获方式
- finally()
- all()
- ace()
- resolve()
- reject()
- try()
- 错误用法及误区
- promise 你可能不知道的 6 件事
- 手写 Promise

### 一、基本用法

ES6 规定，`Promise`对象是一个构造函数，用来生成`Promise`实例。

```
const promise = new Promise(function(resolve,reject){

    if(/* 异步操作成功*/){
        resolve(value);
    }else{
        reject(error);
    }
})
```

`Promise` 构造函数接受一个函数作为参数，该函数的两个参数分别是 `resolve` 和 `reject`。

`resolve`函数的作用是，将`Promise`对象的状态从“未完成”变成“成功”（即从 pending 变成 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；

`reject`函数的作用是，将`Promse`对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

### 二、then()

`then`方法是定义在原型对象`Promise.prototype`上的。`then`方法可以接受两个回调函数作为参数。第一个回调函数是`Promise`对象的状态变为`resolved`时调用。其中，第二个函数是可选的，不一定要提供。`then`方法返回的是一个新`Promise`实例。

```
getJSON("/post/1.json").then(post=>getJSON(post.commentURL)).then(comments=>console.log("resolved:",comments),err=>console.log("rejected:",err))

```

### 三、Promise 的异常捕获方式

- 在 Promise 的构造体内进行错误处理
- 通过 Promise.prototype.catch 来进行错误处理
- Promise.all 中的异常捕获
- Promise.try 中的异常捕获
- 在 Promise 中无法被捕获的错误

#### 3.1 在 Promise 的构造体内进行错误处理

```
var promise = new Promise(function(resolve,reject){
    try{
        throw new Error('test');
    }catch(e){
        reject(e)
    }
})
```

在 Promise 的构造体内进行错误处理，类似于我们在 ES5 中的错误处理方式。

#### 3.2 通过 Promise.prototype.catch 来进行错误处理

生成**Promise**实例后，我们可以通过**Promise**原型上的 catch 方法来捕获 Promise 实例内部的错误。`catch`方法返回的还是一个 Promise 对象。

```
var promise = new Promise(function(resolve,reject){
    reject(new Error('test'));
})

promise.catch(function(e){
    // something to deal with the error
    console.log(e);
})
```

此外 catch 方法还可以处理链式调用中的错误，比如：

```
var promise = new Promise(function(resolve,reject){
    resolve();
})
promise.then(function(){
    // if some error throw
}).then(function(){
    // if some error throw
}).catch(function(e){
    // something to deal with the error
    console.log(e)
})

// Error:test1

```

上述的代码，最后一个 catch 方法可以捕获前面链式调用过程中任何一步 then 方法里面所抛出的错误。catch 方面里面还可以再抛错误，这个错误会被后面的 catch 捕获

```
var promise = new Promise(function(resolve,reject){
    reject(new Error('test1'))
})
promise.catch(function(e){
    console.log(e);
    throw new Error('test2')
}).catch(function(e){
    console.log(e)
})

// Error:test1
// Error:test2
```

#### 3.3 Promise.all 中的异常捕获

如果组成**Promise.all**的**promise**有自己的错误捕获方法，那么**Promise.all**中的 catch 就不能捕获该错误。

```
var p1= new Promise(function(resolve,reject){
    reject(new Error('test1'));
}).catch(function(e){
    console.log('由p1自身捕获')
});

var p2= new Promise(function(resolve,reject){
    resolve();
})
var p = Promise.all([p1,p2]);
p.then(function(){

}).catch(function(e){
    // 在此处捕获不到p1中的error
    console.log(e)
})
// 由p1贴身捕获Error:test1

```

#### 3.4 Promise.try 中的异常捕获

ES2018 中可以通过**Promise.try**来同步处理，可能是异步也可能是同步的函数。

```
function f(){}
Promise.try(f);
console.log(2);

```

上述的方法，不管是同步还是异步，都会执行该方法，再输出 2.

在**Promise.try**的错误处理中，通过 catch 方法既可以捕获 f 是同步函数情况下的错误，也可以捕获 f 是异步函数下的错误。

```
function f(){

}

Promise.try(f).then(function(){

}).catch(function(e){

})
```

#### 3.5 在 Promise 中无法被捕获的错误

在**promise**实例**resolve**之后，错误无法被捕获。

```
var promise = new Promise(function(resolve,reject){
    resolve();
    throw new Error('test');    // 该错误无法被捕获
})
promise.then(function(){
//
}).then(function(e){
    console.log(e)
})
```

该错误可以用尾调用 resolve 来避免。

### 四、finally()

`finally`方法用于指定不管`Promise`对象最后状态如何，都会执行的操作。

```
promise.then(result=>{...}).catch(error=>{...}).finally(()=>{...});
```

不管`promise`最后的状态，在执行完`then`或`catch`指定的回调函数以后，都会执行`finally`方法指定的回调函数。

简单实现 finally

```
    Promise.prototype.finally = function(callback){
        let P = this.contructor;
        return this.then(
            value => P.resolve(callback()).then(()=>value),
            reason => P.resolve(callback()).then(()=>{throw reason})
        )
    }
```

### 五、all()

`Promise.all`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。`Promise.all`方法接受一个数组作为参数，`p1`、`p2`、`p3`都是`Promise`实例，如果不是就会先调`Promise.resolve`方法，再进一步处理。

```
var p = Promise.all([p1,p2,p3]);
```

p 的状态由`p1`、`p2`、`p3`决定，分成两种情况。

1. 只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`返回值组成一个数组，传递给`p`的回调函数。
2. 只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

```
// 生成一个Promise对象的数组
var promises = [2, 3, 5, 7, 11, 13].map(function (id) {
  return getJSON('/post/' + id + ".json");
});

Promise.all(promises).then(function (posts) {
  // ...
}).catch(function(reason){
  // ...
});

```

### 六、ace()

`Promise.race`方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

```
var p = Promise.race([p1,p2,p3]);
```

只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给`p`的回调函数。

`Promise.race`方法的参数与`Promise.all`方法一样，如果不是`Promise`实例，就会先调用`Promise.resolve`方法，将参数转为`Promise`实例，再进一步处理。

```
const p = Promise.race([
  fetch('/resource-that-may-take-a-while'),
  new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('request timeout')), 5000)
  })
]);
p.then(response => console.log(response));
p.catch(error => console.log(error));
```

### 七、resolve()

有时需要将现有对象转为 Promise 对象，`Promise.resolve`方法就赶到这个作用。

`Promise.resolve`等价于下面的写法。

```
Promise.resolve('foo')
//  等价于
new Promise(resolve=>resolve('foo'))
```

`resolve`方法的参数分成四种情况

1. 参数是一个 `Promise`实例
2. 参数是一个`thenable`对象
3. 参数不是具有`then`方法的对象，或根本就不是对象
4. 不带有任何参数

#### 7.1 参数是一个 `Promise`实例

如果参数是 Promise 实例，那么`Promise.resolve`将不做任何修改、原封不动地返回这个实例。

#### 7.2 参数是一个`thenable`对象

`thenable`对象指的是具有`then`方法的对象，`Promise.resolve`方法会将这个对象转为 Promise 对象，然后就立即执行`thenable`对象的`then`方法。

```
let thenable = {
    then: function(resolve,reject){
        resolve(42);
    }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value){
    console.log(value);     // 42
})
```

`thenable`对象的`then`方法执行后，对象`p1`的状态就变为`resolved`，从而立即执行最后那个`then`方法指定的回调函数，输出 42。

#### 7.3 参数不是具有`then`方法的对象，或根本就不是对象

`Promise.resolve`方法允许调用时不带参数，直接返回一个`resolved`状态的`Promise`对象。

#### 7.4 不带有任何参数

### 八、reject()

`reject(reason)`方法也会返回一个新的`Promise`实例，该实例的状态为`rejected`。

```
var p = Promise.reject('出错了');

// 等同于
var p = new Promise((resolve,reject)=> reject('出错了'))

p.then(null,function(s){
    console.log(s)
})
// 出错了
```

### 九、try()

### 十、错误用法及误区

- 当作回调来用 Callback Hell
- 怎样用 forEach() 处理 promise
- 没有返回值
- 没有 Catch
- catch()与 then(null, fn)
- 断链 The Broken Chain
- 穿透 Fall Through

### 十一、promise 你可能不知道的 6 件事

- `then()`返回一个 forked Promise(分叉的 Promise)
- 回调函数应该传递结果
- 只能捕获来自上一级的异常
- 错误能被恢复
- Promise 能被暂停
- resolved 状态的 Promise 不会立即执行

### 十二、手写 Promise

```
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

function Promise(excutor){
    let that = this;        // 缓存当前promise实例对象
    that.status = PENDING;  // 初始状态
    that.value = undefined;     // fulfilled状态时 返回的信息
    that.reason = undefined;    // rejected状态时，拒绝的原因
    that.onFulfilledCallbacks = []; // 存储fulfilled状态对应的onFulfilled函数
    that.onRejectedCallbacks = [];      //存储rejected状态对应的onRejected函数

    function resolve(value){    // value成功态时接收的终值
        if(value instanceof Promise){
            return value.then(resolve,reject);
        }
        //  实践中要确保onFulfilled 和 onRejected方法异步执行，且应该在then方法被调用的那一轮事件循环之后的新执行栈中执行。
        setTimeout(()=>{
            //调用resolve回调对应onFulfilled函数
            if(that.status === PENDING){
                // 只能由pending状态=> fullfilled状态（避免调用多次resolve reject）
                that.status = FULFILLED;
                that.value = value;
                that.onFulfilledCallbacks.forEach(cb=>cb(that.value));
            }
        })
    }
    function reject(reason){    // reason失败态时接收的拒因
        setTimeout(()=>{
            // 调用reject 回调对应onRejected函数
            if (that.status === PENDING){
                // 只能由pending状态 => rejected状态（避免调用多次resolve reject）
                that.status = REJECTED;
                that.reason = reason;
                that.onRejectedCallbacks.forEach(cb=>cb(that.reason));
            }
        });
    }

    // 捕获在excutor执行器中抛出异常
    // new Promise((resolve,reject)=>{
        //   throw new Error('error in excutor')
    // })

    try{
        excutor(resolve,reject);
    }catch(e){
        reject(e);
    }
}

function resolvePromise(promise2,x,resolve,reject){
    var then;
    var thenCalledThrow = false;

    if(promise2 === x){
        return reject(new TypeError('Chaining cycle detected for promise!'))
    }

    if( x instanceof Promise){
        if(x.status === 'pending'){
            x.then(function(v){
                resolvePromise(promise2,v,resolve,reject)
            },reject)
        }else{
            x.then(resolve,reject)
        }
        return
    }

    if((x!==null) && ((typeof x=== 'object') || (typeof x === 'function'))){
        try{
            then = x.then   // because x.then could be a getter
            if(typeof then === 'function'){
                then.call(x,function rs(y){
                    if(thenCalledOrThrow) return
                    thenCalledOrThrow = true
                    reutrn resolvePromise(promise2,y,resolve,reject)
                },function rj(r){
                    if(thenCalledOrThrow) return
                    thenCalledOrThrow = true
                    return reject(r)
                })
            }else{
                resolve(x)
            }
        }catch(e){
            if(thenCalledOrThrow) return
            thenCalledOrThrow = true
            return reject(e)
        }
    }else{
        resolve(x)
    }
}

Promise.prototype.then = function(onFulfilled,onRejected){
    const that = this;
    let newPromise;
    // 处理参数默认值  保证参数后续能够继续执行
    onFulfilled = typeof onFulfilled === "function" ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : reason => {
        throw reason;
    }
    if ( that.status === FULFILLED){    //成功态
        return newPromise = new Promise((resolve,reject) =>{
            setTimeout(()=>{
                try{
                    let x = onFulfilled(that.value);
                    resolvePromise(newPromise,x,resolve,reject);
// 新的promise resolve 上一个onFulfilled的返回值
                }catch(e){
                    reject(e);  //  捕获前面onFulfilled中抛出的异常 then(onFulfilled, onRejected);
                }
            })
        })
    }

    if(that.status === REJECTED){   // 失败态
        return newPromise = new Promise((resolve,reject)=>{
            setTimeout(()=>{
                try{
                    let x = onRejected(that.reason);
                    resolvePromise(newPromise,x,resolve,reject);
                }catch(e){
                    reject(e);
                }
            })
        })
    }

    if(that.status === PENDING){    // 等待态
        // 当异步调用resolve/rejected时 将onFulfilled/onRejected收集暂存到集合中
        return newPromise = new Promise((resolve,reject)=>{
            that.onFulfilledCallbacks.push((value)=>{
                try{
                    let x = onFulfilled(value);
                    resolvePromise(newPromise,x,resolve,reject);
                }catch(e){
                    reject(e);
                }
            });
            that.onRejectedCallbacks.push((reason)=>{
                try{
                    let x = onRejected(reason);
                    reasolvePromise(newPromise,x,resolve,reject);
                }catch(e){
                    reject(e);
                }
            })
        })
    }
}

Promise.prototype.catch = function(onRejected){
    return thsi.then(null,onRejected)
}

Promise.deferred = Promise.defer = function(){
    var dfd ={}
    dfd.promise = new Promise(function(resolve,reject){
        dfd.resolve = resolve
        dfd.reject = reject
    })
    return dfd;
}

Promise.all = function(promises){
    return new Promise((resolve,reject){
        const result = []
        let cnt = 0
        for (let i = 0; i < promises.length;++i){
            promises[i].then(value=>{
                cnt++
                result[i]=value
                if(cnt  === promises.length) resolve(result)
            },reject)
        }
    })
}

Promise.race = function(promises){
    return new Promise((resolve,reject)=>{
        promises[i].then(resolve,reject)
    })
}

```

参考资料：[手写 promise](https://github.com/xieranmaya/blog/issues/3)

### 十三、Promise 几道面试题

> 问：下面四个使用 promise 的语句之间的不同点在哪儿？

```
doSomething().then(function () {
    return doSomethingElse();
})；

doSomethin().then(functiuoin () {
    doSomethingElse();
});

doSomething().then(doSomethingElse());

doSomething().then(doSomethingElse);

```

### 参考资料

- [Promise A+ 规范](https://malcolmyu.github.io/2015/06/12/Promises-A-Plus/)
- [JavaScript Promise 迷你书](http://liubin.org/promises-book/)
- [你了解 Promise 吗？](https://mp.weixin.qq.com/s/mvqR4oEq1VcTYB57QqGIvQ)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
