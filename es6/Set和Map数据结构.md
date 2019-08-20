![image](../img/timg.jpg)
<br>

## 前言

ES6 中新几种数组结构分别是 Set、WeakSet、Map、WeakMap 数组结构。

## 关于作者

一个工作八年的草根程序员。

## 内容

- Set
- WeakSet
- Map
- WeakMap

### 一、Set

- 基本用法
- Set 实例的属性和方法
- 遍历操作

#### 1.1 基本用法

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。Set 本身是一个构造函数，用来生成 Set 数据结构。

```
// 例一
const set = new Set([1,2,3,4]);
[...set]
// [1,2,3,4]

// 例二
const items = new Set([1,2,3,4,5,5,5]);
items.size      //5

// 例三
function divs(){
    return [...document.querySelectorAll('div')]
}

const set = new Set(divs());
set.size // 56

```

#### 1.2 Set 实例的属性和方法

Set 结构的实例有以下属性。

- `Set.prototype.constructor`：构造函数，默认就是`Set`函数。
- `Set.prototype.size`：返回`Set`实例的成员总数。

Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

- `add(value)`：添加某个值，返回 Set 结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `clear()`：清除所有成员，没有返回值。

```
s.add(1).add(2).add(2);

s.has(1);       // true

s.delete(2);
s.has(2) // false
```

#### 1.3 遍历操作

- `keys()`：返回键名的遍历器
- `values()`：返回键名的遍历器
- `entries()`：返回键名的遍历器
- `forEach()`：返回键名的遍历器

```
let set = new Set(['red','green','blue']);

for(let item of set.keys()){
    console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]

let set = new Set([1, 2, 3]);
set.forEach((value, key) => console.log(value * 2) )

```

**遍历的应用**

扩展运算符（`...`）内部使用`for...of`循环，所以也可以用于 Set 结构。

```
let set = new Set(['red', 'green', 'blue']);
let arr = [...set];
// ['red', 'green', 'blue']

```

扩展运算符和 Set 结构相结合，就可以去除数组的重复成员。

```
let arr = [3, 5, 2, 2, 5, 5];
let unique = [...new Set(arr)];
// [3, 5, 2]
```

使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。

```
let a = new Set([1,2,3]);
let b = new Set([4,3,2]);

// 并集
let union = new Set([...a,...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x=>b.has(x)));
// set {2, 3}

// 差集
let difference = new Set([...a].filter(x=>!b.has(x)));
```

### 二、WeakSet

- 含义
- 语法

### 三、Map

- 基本用法
- 实例的属性和操作方法
- 遍历方法

### 3.1 基本用法

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。

```
const m = new Map();
const o = {p:'Hello World'};

m.set(o,'content');
m.get(o)    // "content"

m.has(o)    // true
m.delete(o) // true
m.has(o)       // false
```

### 3.2 实例的属性和操作方法

- size 属性
- set(key,value)
- get(key)
- has(key)
- delete(key)
- clear()

#### 3.2.1 size 属性

`size`属性返回 Map 结构的成员总数。

```
const map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
```

#### 3.2.2 set(key,value)

`set` 方法设置键名 `key` 对应的键值为 `value`，然后返回整个 `Map` 结构。如果 `key` 已经有值，则键值会被更新，否则就新生成该键。

```
const m = new Map();

m.set('edition', 6)        // 键是字符串
m.set(262, 'standard')     // 键是数值
m.set(undefined, 'nah')    // 键是 undefined
```

#### 3.2.3 get(key)

`get`方法读取`key`对应的键值，如果找不到`key`，返回`undefined`。

```
const m = new Map();

const hello = function() {console.log('hello');};
m.set(hello, 'Hello ES6!') // 键是函数

m.get(hello)  // Hello ES6!
```

#### 3.2.4 has(key)

`has`方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。

```
const m = new Map();

m.set('edition', 6);
m.set(262, 'standard');
m.set(undefined, 'nah');

m.has('edition')     // true
m.has('years')       // false
m.has(262)           // true
m.has(undefined)     // true
```

#### 3.2.5 delete(key)

`delete`方法删除某个键，返回`true`。如果删除失败，返回`false`。

```
const m = new Map();
m.set(undefined,'nah');
m.has(undefined)

m.delete(undefined)
m.has(undefined)       // false

```

#### 3.2.6 clear()

`clear`方法清除所有成员，没有返回值。

```
let map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
map.clear()
map.size // 0
```

### 3.3 遍历方法

Map 结构原生提供三个遍历器生成函数和一个遍历方法。

- `keys()`：
- `values()`：
- `entries()`：
- `forEach()`：

### 四、WeakMap

- 含义
- 用途

### 参考资料

- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/set-map.html)
- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/set-map)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
