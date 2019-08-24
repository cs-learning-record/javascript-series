![image](../img/timg.jpg)
<br>

## 前言

遍历器是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据只要部署 Iterator 接口，就可以完成遍历操作。

Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

Iterator 的遍历过程是这样的。

1. 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
2. 第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。
3. 第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。
4. 不断调用指针对象的`next`方法，走到它指向数据结构的结束位置。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [默认 Iterator 接口](#一默认-iterator-接口)
- [调用 Iterator 接口的场合](#二调用-iterator-接口的场合)
- [Iterator 接口与 Generator 函数](#三iterator-接口与-generator-函数)
- [遍历器对象的 return()，throw()](#四遍历器对象的-returnthrow)
- [for...of 循环](#五forof-循环)
- [与其他遍历语法的比较](#六与其他遍历语法的比较)

### 一、默认 Iterator 接口

原生具备 Iterator 接口的数据结构如下：

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

### 二、调用 Iterator 接口的场合

- [解构赋值](#21-解构赋值)
- [扩展运算符](#22-扩展运算符)
- [yield\*](#23-yield)
- [其他场合](#24-其他场合)

#### 2.1 解构赋值

```
let set = new Set().add('a').add('b').add('c');

let [x,y] = set;
// x='a'; y='b'

let [first,...rest] = set;
// first='a';rest=['b','c'];
```

#### 2.2 扩展运算符

扩展运算符(...)也会调用默认的 Iterator 接口。

```
// 例一
var str = 'hello';
[...str]    // ['h','e','l','l','o']

// 例二
let arr = ['b','c'];

['a',...arr','d']
```

#### 2.3 yield\*

`yield*`后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。

```
let generator = function*(){
    yield 1;
    yield* [2,3,4];
    yield 5;
}

var iterator = generator();

iterator.next();    // {value:1,done:false}
iterator.next();    // {value:2,done:false}
iterator.next();    // {value:3,done:false}
iterator.next();    // {value:4,done:false}
iterator.next();    // {value:5,done:false}
iterator.next();    // {value:undefined,done:true}

```

#### 2.4 其他场合

由于数组的遍历会调用遍历器接口，所以任何接受数组作为参数的参数的场合。其实都调用了遍历器接口。下面是一些例子。

- for...of
- Array.from()
- Map(),Set(),WeakMap(),WeakSet()（比如`new Map([['a',1],['b',2]])`）
- Promise.all()
- Promise.race()

### 三、Iterator 接口与 Generator 函数

```
var myIterable = {}

myIterable[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
    yield 3;
};
[...myIterable]     // [1,2,3]

```

### 四、遍历器对象的 return()，throw()

遍历器对象除了具有`next`方法，还可以具有`return`方法和`throw`方法。`return`方法的使用场合是，如果`for...of`循环提前退出，就会调用`return`方法。如果一个对象在完成遍历前，需要清理或释放资源，就可以部署`return`方法

```
function readLineSync(file){
    return {
        next(){
            return {done:false};
        },
        return(){
            file.close();
            return {done:true};
        }
    }
}
```

### 五、for...of 循环

- [数组](#51-数组)
- [Set 和 Map 结构](#52-set-和-map-结构)
- [计算生成的数据结构](#53-计算生成的数据结构)
- [类似数组的对象](#54-类似数组的对象)
- [对象](#55-对象)

#### 5.1 数组

数组原生具备`iterator`接口，`for...of`循环本质上就是调用这个接口产生的遍历器。

```
const str = ['red','green','blue'];

for(let v of arr){
    console.log(v);     // red green blue
}

```

#### 5.2 Set 和 Map 结构

Set 和 Map 结构也原生具有 Iterator 接口，可以直接使用`for...of`循环。

```
var engines = new Set(['Gecko','Trident','Webkit']);

for(var e of engines){
    console.log(e);
}
//  Gecko
//  Trident
//  Webkit

var es6 = new Map();
es6.set("edition", 6);
es6.set("committee", "TC39");
es6.set("standard", "ECMA-262");
for (var [name, value] of es6) {
  console.log(name + ": " + value);
}
// edition: 6
// committee: TC39
// standard: ECMA-262
```

#### 5.3 计算生成的数据结构

ES6 的数组、Set、Map 都部署了以下三个方法，调用后都返回遍历器对象。

- `entries()`返回一个遍历对象，用来遍历`[键名，键值]`组成的数组。对于数组，键名就是索引值；对于 Set，键名与键值相同。Map 结构的 Iterator 接口，默认就是调用`entries`方法。
- `keys()`返回一个遍历器对象，用来遍历所有的键名。
- `values()`返回一个遍历器对象，用来遍历所有的键值。

```
let arr = ['a','b','c'];
for(let pair of arr.entries()){
    console.log(pair);
}
// [0, 'a']
// [1, 'b']
// [2, 'c']
```

#### 5.4 类似数组的对象

类似数组的对象包括好几类，分别为字符串、DOM NodeList 对象、`arguments`对象

```
// 字符串
let str = "hello";

for (let s of str) {
  console.log(s); // h e l l o
}

// DOM NodeList对象
let paras = document.querySelectorAll("p");

for (let p of paras) {
  p.classList.add("test");
}

// arguments对象
function printArgs() {
  for (let x of arguments) {
    console.log(x);
  }
}
printArgs('a', 'b');
```

#### 5.5 对象

对于普通的对象，`for...of`结构不能直接使用，会报错，必须部署了 Iterator 接口后才能使用。

```
function* entries(obj){
    for(let key of Object.keys(obj)){
        yield [key,obj[key]];
    }
}

for(let [key,value] of entries(obj)){
    console.log(key,'->',value);
}
// a->1
// b->2
// c->3
```

### 六、与其他遍历语法的比较

`for...in`循环有几个缺点

- 数组的键名是数字，但是`for...in`循环是以字符串作为键名“0”、“1”、“2”等等。
- `for...in`循环不仅遍历数字键名，还会遍历手动添加的其他健，甚至包括原型链上的键。
- 某些情况下，`for...in`循环会以做生意顺序遍历键名。

`for...of`循环相比上面几种做法，有以下几个优点

- 有着同`for...in`一样的简洁语法，但是没有`for...in`那些缺点。
- 不同于`forEach`方法，它可以与`break`、`continue`和`return`配合使用。
- 提供了遍历所有数据结构的统一操作接口。

### 参考资料

- [ES6 系列之迭代器与 for of](https://github.com/mqyqingfeng/Blog/issues/90)
- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/iterator)
- [ECMAScript 6 入门 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/iterator.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
```
