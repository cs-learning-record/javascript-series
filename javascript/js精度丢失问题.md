![image](../img/timg.jpg)
<br>

## 前言

众所周知，JavaScript浮点数运算时经常遇到会`0.000000001`和`0.999999999`这样奇怪的结果，如`0.1+0.2=0.30000000000000004`、`1-0.9=0.09999999999999998`，出现这种问题到底是为什么？

## 关于作者

一个工作八年的草根程序员。

## 内容

- JavaScript是如何表示数字的？
- 运算时发生了什么？
- 怎么解决精度问题？

### 一、JavaScript是如何表示数字的？

JavaScript使用Number类型表示数字（整数和浮点数），遵循[IEEE 754](https://zh.wikipedia.org/wiki/IEEE_754)标准通过64位来表示一个数字

通过

### 二、运算时发生了什么？

### 三、怎么解决精度问题？

### 参考资料

- [0.1 + 0.2不等于0.3？为什么JavaScript有这种“骚”操作？](https://juejin.im/post/5b90e00e6fb9a05cf9080dff)
- [揭秘 0.1 + 0.2 != 0.3](https://www.barretlee.com/blog/2016/09/28/ieee754-operation-in-js/)
- [JavaScript 浮点数陷阱及解法](https://github.com/camsong/blog/issues/9)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>