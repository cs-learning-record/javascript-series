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
- **has(target,propKey)**：拦截`propKey in proxy`的操作，返回一个布尔值。
- **deleteProperty(target,propKey)**：拦截`delete proxy[propKey]`的操作，返回一个布尔值。
- **ownKeys(tarte)**：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。
- **getOwnPropertyDescriptor(target,propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy,propKey)`，返回属性的描述对象。
- **defineProperty(target,propKey,propDesc)**：拦截`Object.definedProperty(proxy,propKey,propDesc)`、`Object.definedProperties(proxy,propDescs)`，返回一个布尔值。
- **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
- **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。
- **isExtensible(target)**：拦截`Object.isExtensible(proxy)`,返回一个布尔值。
- **setPrototypeOf(target,proto)**：拦截`Object.setPrototypeOf(proxy,proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **apply(target,object,args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object,...args)`、`proxy.apply(...)`。
- **construct(target,args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new Proxy(...args)`。

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
