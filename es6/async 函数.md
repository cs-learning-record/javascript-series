![image](../img/timg.jpg)
<br>

## 前言

ES2017 标准引入了 async 函数，使得异步操作变得更加方便。

它是Generator函数的语法糖。

`async`函数对Generator函数的改进，体现在以下四点。

- 内置执行器
- 更好的语义
- 更广的适用性
- 返回值是 Promise

## 关于作者

一个工作八年的草根程序员。

## 内容

- [基本用法](#一基本用法)
- [语法](#二语法)
- [async 函数的实现原理](#三async-函数的实现原理)
- [与其他异步处理方法的比较](#四与其他异步处理方法的比较)

### 一、基本用法

`async`函数返回一个Promise对象，可以使用`then`方法添加回调函数，当函数执行的时候，一旦遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

```
// 函数声明
async function foo(){}

// 函数表达式
const foo = async function(){};

// 对象的方法
let obj = {async function(){}};

// Class 的方法
class Storage{
  constructor() {
    this.cachePromise = caches.open('avatars');
  }

  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}

// 箭头函数
const foo = async() =>{};
```

### 二、语法

- [返回Promise对象](#21-返回promise对象)
- [Promise对象的状态变化](#22-promise对象的状态变化)
- [await命令](#23-await命令)
- [错误处理](#24-错误处理)
- [使用注意点](#25-使用注意点)

#### 2.1 返回Promise对象

`async`函数返回一个Promise对象。

`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

```
async function f() {
  return 'hello world';
}

f().then(v => console.log(v))
// hello world
```

#### 2.2 Promise对象的状态变化

`async`函数返回的Promise对象，必须等到内部所有`await`命令后面的Promise对象执行完，才会发生状态改变，除非遇到`return`语句或错误。也就是说，只有async函数内部的异步操作执行完，才会执行then方法指定的回调函数。

#### 2.3 await命令

`await`命令后面是一个 Promise 对象，返回该对象的结果。如果不是 Promise 对象，就直接返回对应的值。

```
async function f() {
  // 等同于
  // return 123;
  return await 123;
}

f().then(v => console.log(v))
// 123
```

#### 2.4 错误处理

如果`await`后面的异步操作出错，那么等同于`async`函数返回的 Promise 对象被`reject`。

```
async function f() {
  await new Promise(function (resolve, reject) {
    throw new Error('出错了');
  });
}

f()
.then(v => console.log(v))
.catch(e => console.log(e))
```

防止出错的方法，也是将其放在`try...catch`代码块之中。

```
async function f(){
    try{
        await new Promise(function(resolve,reject){
            throw new Error('出错了');
        })
    }catch(e){}
    return await('hello world');
}

```

如果有多个await命令，可以统一放在try...catch结构中。

```
async function main(){
    try{
    const val1 = await firstStep();
    const val2 = await secondStep(val1);
    const val3 = await thirdStep(val1, val2);

    console.log('Final: ', val3);
    }catch(err){
        console.log(err);
    }
}

```

#### 2.5 使用注意点

1. `await`命令后面的`Promise`对象，运行结果可能是`rejected`，所以最好把`await`命令放在`try...catch`代码块中。
2. 多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
3. `await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。
4. `async`函数可以保留运行堆栈。

### 三、async 函数的实现原理

async 函数的实现原理，就是将Generator函数和自动执行器，包装在一个函数里。

```
async function fn(args){
    // ...
}

// 等同于

function fn(args){
    return spawn(function*(){
        // ...
    });
}

function spawn(genF){
    return new Promise(function(resolve,reject){
        const gen = genF();
        function step(nextF){
            let next;
            try{
                next = nextF();
            }catch(e){
                return reject(e);
            }
            if(next.done){
                return resolve(next.value);
            }
            Promise.resolve(next.value).then(function(v){
                step(function(){
                    return gen.next(v);
                })
            },function(e){
                step(function(){
                    return gen.throw(e);
                })
            })
        }
        step(function() { return gen.next(undefined); });
    });
}
```

### 四、与其他异步处理方法的比较

某个 DOM 元素上面，部署了一系列的动画，前一个动画结束，才能开始后一个。如果当中有一个动画出错，就不再往下执行，返回上一个成功执行的动画的返回值。

Promise 的写法。

```

function chainAnimationsPromise(elem,animations){
    
    // 变量ret用来保存上一个动画的返回值
    let ret = null;

    // 新建一个空的Promise
    let p = Promise.resolve();

    // 使用then方法，添加所有动画
    for(let anim of animations){
        p = p.then(function(val){
            ret = val;
            return anim(elem);
        })
    }

    // 返回一个部署了错误捕捉机制的Promise
    return p.catch(function(e){
        //  ...
    }).then(function(){
        return ret;
    })
}

```

Generator 函数的写法。

```
function chainAnimationsGenerator(elem,animations){
    return spawn(function*(){
        let ret = null;
        try{
            for(let anim of animations){
                ret = yield anim(elem);
            }
        }catch(e){
            //  ...
        }
        return ret;
    })
}
```

async 函数的写法。

```
async function chainAnimationsAsync(elem,animations){
    let ret = null;
    try{
        for(let anim of animations){
            ret = await anim(elem);
        }
    }catch(e){
        /*忽略错误，继续执行*/
    }
    return ret;
}
```

可以看到 Async 函数的实现最简洁，最符合语义，几乎没有语义不相关的代码。它将 Generator 写法中的自动执行器，改在语言层面提供，不暴露给用户，因此代码量最少。如果使用 Generator 写法，自动执行器需要用户自己提供。

### 参考资料

- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/async)
- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/docs/async.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>