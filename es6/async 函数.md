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

- 基本用法
- 语法
- async 函数的实现原理
- 与其他异步处理方法的比较

### 一、基本用法

`async`函数返回一个Promise

```
```

### 二、语法

`async`函数返回一个Promise对象，可以使用`then`方法添加回调函数。当函数执行的时候，一旦遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

```
```

### 三、async 函数的实现原理

- 返回Promise对象
- Promise对象的状态变化
- await命令
- 错误处理
- 使用注意点

#### 3.1 返回Promise对象

`async`函数返回一个Promise对象。

`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

```
async function f() {
  return 'hello world';
}

f().then(v => console.log(v))
//
```

#### 3.2 Promise对象的状态变化

#### 3.3 await命令

#### 3.4 错误处理

#### 3.5 使用注意点

1. `await`命令后面的`Promise`对象，运行结果可能是`rejected`，所以最好把`await`命令放在`try...catch`代码块中。
2. 多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
3. `await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。
4. `async`函数可以保留运行堆栈。

### 四、与其他异步处理方法的比较

```
```

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