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
- catch()
- finally()
- all()
- ace()
- resolve()
- reject()
- try()
- promise 你可能不知道的 6 件事
- 手写 Promise

### 一、基本用法

### 二、then()

### 三、catch()

### 四、finally()

### 五、all()

### 六、ace()

### 七、resolve()

### 八、try()

### 九、promise 你可能不知道的 6 件事

### 十、手写 Promise

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

### 十一、Promise 几道面试题

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
