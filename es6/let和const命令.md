![image](../img/timg.jpg)
<br>

## 前言

本人平时学习及收集内容，欢迎参入一起讨论。

## 关于作者

一个工作八年的草根程序员。

## 内容

- let 和 const 基本用法
- 不存在变量提升
- 暂时性死区
- 不允许重复声明
- 不绑定全局作用域
- 块级作用域

### 一、let 和 const 基本用法

- let 基本用法
- const 基本用法

#### 1.1 let 基本用法

ES6 新增了 let 命令，用来声明变量。它的用法类似于 var，但是所声明的变量，只在 let 命令所在的代码块内有效。

```
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```

#### 1.2 const 基本用法

`const`声明一个只读的常量。一旦声明，常量的值就不能改变。

```
const PI = 3.1415;
PI // 3.1415

PI = 3;
// TypeError: Assignment to constant variable.
```

### 二、不存在变量提升

`var`命令会发生”变量提升“现象，即变量可以在声明之前使用，值为`undefined`。这种现象多多少少是有些奇怪的，按照一般的逻辑，变量应该在声明语句之后才可以使用。

为了纠正这种现象，`let`命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。

```
if (false) {
    let value = 1;
}
console.log(value); // Uncaught ReferenceError: value is not defined

```

### 三、不绑定全局作用域

```
let value = 1;
console.log(window.value); // undefined
```

### 三、暂时性死区

let 和 const 声明的变量不会被提升到作用域顶部，如果在声明之前访问这些变量，会导致报错：

```
console.log(typeof value); // Uncaught ReferenceError: value is not defined
let value = 1;
```

### 四、不允许重复声明

`let`不允许在相同作用域内，重复声明同一个变量。

```
var value = 1;
let value = 2; // Uncaught SyntaxError: Identifier 'value' has already been declared
```

### 五、块级作用域

`let`实际上为 JavaScript 新增了块级作用域。

```
function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5
}
```

### 六、let 和 const 的区别

const 用于声明常量，其值一旦被设定不能再被修改，否则会报错。

### 参考资料

- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/let.html)
- [ES6 系列之 let 和 const](https://github.com/mqyqingfeng/Blog/issues/82)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
