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

- 默认 Iterator 接口
- 调用 Iterator 接口的场合
- Iterator 接口与 Generator 函数
- 遍历器对象的 return()，throw()
- for...of 循环
- 与其他遍历语法的比较

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

- 解构赋值
- 扩展运算符
- yield\*
- 其他场合

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

### 六、与其他遍历语法的比较

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
