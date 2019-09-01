![image](../img/timg.jpg)
<br>

## 前言

Proxy 用于个性某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”，即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和必定。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 概述
- Proxy 实例的方法
- this 问题

### 一、概述

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```
var proxy = new Proxy(target,handler);
```

`new Proxy()`表示生成一个`Proxy`实例，`target`参数表示所要拦截的目标对象，`handler`参数也是一个对象，用来寶拦截行为。

### 二、Proxy 实例的方法

- **get(target,propKey,receiver)**：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。
- **set(target,propKey,value,receiver)**：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。
- **has(target,propKey)**：
- **deleteProperty(target,propKey)**：
- **ownKeys(tarte)**：
- **getOwnPropertyDescriptor(target,propKey)**：
- **defineProperty(target,propKey,propDesc)**：
- **preventExtensions(target)**：
- **getPrototypeOf(target)**：
- **isExtensible(target)**：
- **setPrototypeOf(target,proto)**：
- **apply(target,object,args)**：
- **construct(target,args)**：

### 三、this 问题

虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。

### 参考资料

- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/proxy)
- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/proxy.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
