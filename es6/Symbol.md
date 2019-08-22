![image](../img/timg.jpg)
<br>

## 前言

ES5 的对象属性名都是字符串，这容易造成属性名的冲突。ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 作为属性名的 Symbol
- 属性名的遍历
- Symbol.for()，Symbol.keyFor()
- 内置的Symbol值

### 一、作为属性名的 Symbol

由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。

```
var mySymbol = Symbol();

// 第一种写法
var a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
var a = {
    [mySymbol]:'Hello!';
};

// 第三种写法
var a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

```

### 二、属性名的遍历

Symbol 作为属性名，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols`方法，可以获取指定对象的所有 Symbol 属性名。

`Object.getOwnPropertySymbols`方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

```
var obj = {};
var a = Symbol('a');
var b = Symbol('b');

obj[a] = 'Hello';
obj[b] = 'World';

var objectSymbols = Object.getOwnPropertySymbols(obj);

objectSymbols
// [Symbol(a), Symbol(b)]

```

### 三、Symbol.for()，Symbol.keyFor()

有时，我们希望重新使用同一个Symbol值，Symbol.for方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的Symbol值。如果有，就返回这个Symbol值，否则就新建并返回一个以该字符串为名称的Symbol值。

```
var s1 = Symbol.for('foo');
var s2 = Symbol.for('foo');

s1 === s2 // true
```

### 四、内置的Symbol值

除了定义自己使用的Symbol值以外，ES6还提供了11个内置的Symbol值，指向语言内部使用的方法。

- `Symbol.hasInstance`：指向一个内部方法。当其他对象使用`instanceof`运算符，判断是否为该对象的实例时，会调用这个方法。
- `Symbol.isConcatSpreadable`：属性等于一个布尔值，表示该对象用于`Array.prototype.concat()`时，是否可以展开。
- `Symbol.species`：指向当前对象的构造函数。创造实例时，默认会调用这个方法，即使用这个属性返回的函数当作构造函数，来创造新的实例对象。
- `Symbol.match`：指向一个函数。当执行str.match(myObject)时，如果该属性存在，会调用它，返回该方法的返回值。
- `Symbol.replace`：指向一个方法，当该对象被String.prototype.replace方法调用时，会返回该方法的返回值。
- `Symbol.search`：指向一个方法，当该对象被String.prototype.search方法调用时，会返回该方法的返回值。
- `Symbol.split`：指向一个方法，当该对象被String.prototype.split方法调用时，会返回该方法的返回值。
- `Symbol.iterator`：指向该对象的默认遍历器方法。
- `Symbol.toPrimitive`：指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。

  Symbol.toPrimitive被调用时，会接受一个字符串参数，表示当前运算的模式，一共有三种模式

  - Number：该场合需要转成数值
  - String：该场合需要转成字符串
  - Default：该场合可以转成数值，也可以转成字符串

- `Symbol.toStringTag`：指向一个方法。在该对象上面调用Object.prototype.toString方法时，如果这个属性存在，它的返回值会出现在toString方法返回的字符串之中，表示对象的类型。
- `Symbol.unscopables`：指向一个对象。该对象指定了使用with关键字时，哪些属性会被with环境排除。

### 参考资料

- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/symbol.html)
- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/symbol)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>