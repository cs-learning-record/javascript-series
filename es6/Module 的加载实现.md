![image](../img/timg.jpg)
<br>

## 前言

本人平时学习及收集内容，欢迎参入一起讨论。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 模块化的理解
- AMD
- CMD
- CommonJS
- ES6模块

### 一、模块化的理解

- 什么是模块
- 模块化的进化过程
- 模块化的好处
- 引入多个`<script>`后出现出现问题

#### 1.1 什么是模块

- 将一个复杂的程序依据一定的规则(规范)封装成几个块(文件)，并进行组合在一起
- 块的内部数据与实现是私有的，只是向外部暴露一些接口(方法)与外部其它模块通信

#### 1.2 模块化的进化过程

- **全局function模式：将不同的功能封装成不同的全局函数**
  - 编码：将不同的功能封装不同的全局函数
  - 问题：污染全局命名空间，容易引起命名冲突或数据不安全，而且模块成员之间看不出直接关系

```
function m1(){
  //...
}
function m2(){
  //...
}
```
- **namespace模式 : 简单对象封装**
  - 作用：减少了全局变量，解决命名冲突
  - 问题：数据不安全（外部可以直接修改模块内部的数据）

```
let myModule = {
    data:'www.baidu.com',
    foo(){
        console.log(`foo() ${this.data}`)
    },
    bar(){
        console.log(`bar() ${this.data}`)
    }
}

myModule.data = 'other data';       // 能直接修改模块的数据
myModule.foo()
```

这样的写法会暴露所有模块成员，内部状态可以被外部改写。

- **IIFE模式：匿名函数自调用(闭包)**
  - 作用：数据是私有的，外部只能通过暴露的方法操作
  - 编码：将数据和行为封装到一个函数内部，通过给window添加属性来向外暴露接口
  - 问题：如果当前这个模块依赖另一个模块怎么办？

```
// index.html文件
<script type="text/javascript" src="module.js"></script>
<script type="text/javascript">
    myModule.foo();
    myModule.bar();
    console.log(myModule.data)  // undefined 不能访问模块内部数据
    myModule.data = 'xxx';      // 不是修改的模块内部的data
    myModule.foo(); // 没有改变
</script>
```

```
// module.js文件
(function(window){
    let data = 'www.baidu.com'
    // 操作数据的函数
    function foo(){
        // 用于暴露有函数
        console.log(`foo() ${data}`)
    }
    function bar(){
        // 用于暴露有函数
        console.log(`bar() ${data}`)
        otherFun()  // 内部调用
    }
    function otherFun(){
        // 内部私有函数
        console.log('otherFun()')
    }
    // 暴露行为
    window.myModule = {foo,bar} // ES6 写法
})(window)
```

- **IIFE模式增强 : 引入依赖**

这就是现代模块实现的基石

```
// module.js文件
(function(window,$){
    let data = 'window.baidu.com'
    // 操作数据的函数
    function foo(){
        // 用于暴露函数
        console.log(`foo() ${data}`)
        $('body').css('background','red')
    }
    function bar(){
        // 用于暴露有函数
        console.log(`bar() ${data}`)
        otherFun()  // 内部调用
    }
    function otherFun(){
        // 内部私有的函数
        console.log('otherFun()')
    }
    // 暴露行为
    window.myModule = {foo,bar}
})(window.jQuery)
```

```
    // index.html文件
  <script type="text/javascript" src="jquery-1.10.1.js"></script>
  <script type="text/javascript" src="module.js"></script>
  <script type="text/javascript">
    myModule.foo()
  </script>
```

上例子通过jquery方法将页面的背景颜色改成红色，所以必须先引入jQuery库，就把这个库当作参数传入。**这样做除了保证模块的独立性，还使得模块之间的依赖关系变得明显**。

#### 1.3 模块化的好处

- 避免命名冲突(减少命名空间污染)
- 更好的分离，按需加载
- 更高复用性
- 高可维护性

#### 1.4 引入多个`<script>`后出现出现问题

- 请求过多

首先我们要依赖多个模块，那样就会发送多个请求，导致请求过多。

- 依赖模糊

我们不知道他们的具体依赖关系是什么，也就是说很容易因为不了解他们之间的依赖关系导致加载先后顺序出错。

- 难以维护

以上两种原因就导致了很难维护，很可能出现牵一发而动全身的情况导致项目出现严重的问题。
模块化固然有多个好处，然而一个页面需要引入多个js文件，就会出现以上这些问题。而这些问题可以通过模块化规范来解决，下面介绍开发中最流行的commonjs, AMD, ES6, CMD规范。

### 二、CommonJS

- 概述
- 特点
- 基本语法
- 模块的加载机制
- 服务器端实现

#### 2.1 概述

Node 应用由模块组成，采用CommonJS模块规范。每个文件就是一个模块，有自己的作用域。在一个文件里定义的变量、函数、类，都是私有的，对其他文件不可见。**在服务器端，模块的加载是运行时同步加载的；在浏览器端，模块需要提前编译打包处理**。

#### 2.2 特点

- 所有代码都运行在模块作用域，不会污染全局作用域。
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
- 模块加载的顺序，按照其在代码中出现的顺序。

#### 2.3 基本语法

- 暴露模块：`module.exports = value`或`expors.xxx=value`
- 引入模块：`require(xxx)`如果是第三方模块，xxx为模块名；如果是自定义模块，xxx为模块文件路径

#### 2.4 模块的加载机制

**CommonJS模块的加载机制是，输入的是被输出的值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值**。

### 三、AMD

### 四、CMD

CMD 规范专门用于浏览器端，模块的加载是异步的，模块使用时会加载执行。CMD规范整合了CommonJS和AMD规范的特点。在Sea.js中所有JavaScript模块都遵循CMD模块定义规范。

### 五、ES6模块

参考上一篇文章[Module 的语法](https://github.com/cs-learning-record/javascript-series/blob/master/es6/Module%20%E7%9A%84%E8%AF%AD%E6%B3%95.md)

### 六、几种模块间的区别

- AMD 与 CMD 的区别
- CommonJS与AMD
- Commonjs与ES6的区别

#### 6.1 AMD 与 CMD 的区别

- CMD 推崇**依赖就近**，AMD推崇**依赖前置**
- AMD是**提前执行**，CMD是**延迟执行**

#### 6.2 CommonJS与AMD

- CommonJS规范加载模块是同步，也就是说，只有加载完成，才能执行后面的操作。
- AMD规范则是非同步加载模块，允许指定回调函数。

#### 6.3 Commonjs与ES6的区别

- CommonJS模块输出的是一个值的拷贝，ES6模块输出的是值的引用。
- CommonJS模块是运行时加载，ES6模块是编译时输出接口。

### 总结

- CommonJS规范主要用于服务端编程，加载模块是同步的，这并不适合在浏览器环境，因为同步意味着阻塞加载，浏览器资源是异步加载的，因此有了AMD、CMD解决方案。
- AMD规范在浏览器环境中异步加载模块，而且可以并行加载多个模块。不过，AMD规范开发成本高，代码的阅读和书写比较困难，模块定义方式的主义不顺畅。
- CMD规范与AMD规范很相似，都用于浏览器编程，依赖就是近，延迟执行，可以很容易在Node.js中运行。不过，依赖SPM打包，模块的加载逻辑偏重
- **ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。**

### 参考资料

- [前端模块化详解(完整版)](https://github.com/ljianshu/Blog/issues/48)
- [ES6 系列之模块加载方案](https://github.com/mqyqingfeng/Blog/issues/108)
- [前端模块化：CommonJS,AMD,CMD,ES6](https://juejin.im/post/5aaa37c8f265da23945f365c)
- [《JavaScript 标准参考教程（alpha）》](http://javascript.ruanyifeng.com/nodejs/module.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>