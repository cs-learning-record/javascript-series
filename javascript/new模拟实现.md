![image](../img/timg.jpg)
<br>

## 前言

本人平时学习及收集内容，欢迎参入一起讨论。

## 关于作者

一个工作八年的草根程序员。

## 内容

- new几个特点
- 代码实现

> new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。 ——（来自于MDN）


### 一、new几个特点

- 创建（或者说构造）一个全新的对象
- 这个新对象会被执行\[\[原型\]\]连接。
- 这个新对象会绑定到函数调用的this。
- 如果函数没返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

### 二、代码实现

```
    function objectFactory(){
        var obj = new Object(),
        Constructor = [].shift.call(arguments);
        obj.__proto__ = Constructor.prototype;
        var ret = Constructor.apply(obj,arguments);
        return typeof ret==='object' ? ret : obj;
    }

```

### 参考资料

- [JavaScript深入之new的模拟实现](https://github.com/mqyqingfeng/Blog/issues/13)
- [深度解析 new 原理及模拟实现](https://github.com/yygmind/blog/issues/24)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
