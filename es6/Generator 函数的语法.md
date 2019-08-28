![image](../img/timg.jpg)
<br>

## 前言

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

Generator函数有两个特征：

- `function`关键字与函数名之间有一个星号；
- 函数体内`yield`表达式，定义不同的内部状态；

```
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
```

## 关于作者

一个工作八年的草根程序员。

## 内容

- [基本概念](#一基本概念)
- [next 方法的参数](#二next方法的参数)
- [for...of 循环](#三forof循环)
- [Generator.prototype.throw()](#四generatorprototypethrow)
- [Generator.prototype.return()](#五generatorprototypereturn)
- [next()、throw()、return() 的共同点](#六nextthrowreturn的共同点)
- [yield* 表达式](#七yield表达式)
- [作为对象属性的Generator函数](#八作为对象属性的generator函数)
- [Generator 函数的this](#九generator函数的this)
- [含义](#十含义)
- [应用](#十一应用)

### 一、基本概念

- [yield表达式](#11-yield表达式)
- [与Iterator接口的关系](#12-与Iterator接口的关系)

#### 1.1 yield表达式

由于Generator函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

```
function* gen() {
  yield  123 + 456;
}
```

遍历器对象的`next`方法的运行逻辑如下：

1. 遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。
2. 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。
3. 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的value属性值。
4. 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

#### 1.2 与Iterator接口的关系

任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。

```
var myIterable = {};
myIterable[Symbol.iterator] = function*(){
  yield 1;
  yield 2;
  yield 3;
};
[...myIterable] // [1, 2, 3]
```

### 二、`next`方法的参数

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

```
function * f(){
  for(var i = 0; true;i++){
    var reset = yield i;
    if(reset) { i = -1; }
  }
}

var g = f();

g.next()  // {value:0,done:false}
g.next()  // {value:1,done:false}
g.next(true)  // {value:0,done:false}

```

注意，由于`next`方法的参数表示上一个`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的。V8引擎直接忽略第一次使用`next`方法时的参数，只有从第二次使用`next`方法开始，参数才是有效的。

### 三、`for...of`循环

`for...of`循环可以自动遍历Generator函数运行时生成的`Iterator`对象，且此时不再需要调用`next`方法。

```
function * foo(){
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for(let v of foo()){
  console.log(v);
}
// 1 2 3 4 5
```

除了`for...of`循环以外，扩展运算符(`...`)、解构赋值和`Array.from`方法内部调用的，都是遍历器接口。这意味着，它们都可以将Generator函数返回的Iterator对象，作为参数。


### 四、Generator.prototype.throw()

Generator函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在Generator函数体内捕获。

```
var g = function*(){
  try{
    yield;
  }catch(e){
    console.log('内部捕获',e);
  }
}

var i = g();
i.next();

try{
  i.throw('a');
  i.throw('b');
}catch(e){
  console.log('外部捕获',e);
}

// 内部捕获e
// 外部捕获e

```

### 五、Generator.prototype.return()

`Generator`函数返回的遍历器对象，还有一个`return`方法，可以返回给定的值，并且终结遍历Generator函数。

```
function * gen(){
  yield 1;
  yield 2;
  yield 3;
}

var g = gen();

g.next()  // {value:1,done:false}
g.next('foo')  // {value:'foo',done:true}
g.next()  // {value:undefined,done:true}
```

以面代码中，遍历器对象`g`调用`return`方法后，返回值的`value`属性就是`return`方法的参数`foo`。并且，Generator函数的遍历就终止了，返回值的`done`属性为`true`，以后再调用`next`方法，`done`属性总是返回`true`。

### 六、`next()、throw()、return()`的共同点

`next()`、`throw()`、`return()`这三个方法本质上是同一件事，可以放在一起理解。它们的作用都是让Generator函数恢复执行，并且使用不同的语句替换`yield`表达式。

`next()`是将`yield`表达式替换成一个值。

```
const g = function*(x,y){
  let result = yield x+y;
  return result;
};

const gen = g(1,2);
gen.next();   // Object {value:3,done:false}
gen.next(1);  // Object {value:1,done:true}

// 相当于将 let result = yield x+y;
// 替换成 let result = 1;

```

`throw()`是将`yield`表达式替换成一个`throw`语句。

```
gen.throw(new Error('出错了'));   // Uncaught Error:出错了
// 相当于将 let result = yield x+y
// 替换成 let result = throw(new Error('出错了'));
```

`return()`是将`yield`表达式替换成一个`return`语句。

```
gen.return(2);    // Object {value:2,done:true}
// 相当于将let result = yield x+y
// 替换成 let result = return 2;
```

### 七、`yield*`表达式

`yield*`表达式，用来在一个Generator函数里面执行另一个Generator函数。

```
function * foo(){
  yield 'a';
  yield 'b';
}

function * bar(){
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function * bar(){
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}

```

### 八、作为对象属性的Generator函数

如果一个对象的属性是Generator函数，可以简写成下面的形式。

```
let obj = {
  * myGeneratorMethod(){
    ...
  }
}
```

### 九、`Generator`函数的`this`

ES6规定这个遍历器是Generator函数的实例，也继承了Generator函数的`prototype`对象上的方法。

如果把`Generator`当作普通的构造函数，并不会生效，因为`Generator`返回的总是遍历器对象，而不是`this`对象。

```
function* g(){
  this.a= 11;
}

let obj = g();
obj.a // undefined

function* F() {
  yield this.x = 2;
  yield this.y = 3;
}

new F()
// TypeError: F is not a constructor
```

有一个变通方法，首先，生成一个空对象，使用`call`方法绑定`Generator`函数的内部的`this`。这样，构造函数调用以后，这个空对象就是`Generator`函数的实例对象了。

```
function* F(){

}
var obj = {};
var f = F.call(obj);

f.next();   // Object {value:2,done:false}
f.next();   // Object {value:3,done:false}
f.next();   // Object {value:undefined,done:true}

obj.a // 1
obj.b // 2
obj.c // 3
```

### 十、含义

- [Generator与状态机](#101-generator与状态机)
- [Generator与协程](#102-generator与协程)

#### 10.1 Generator与状态机

Generator 是实现状态机的最佳结构。

```
var clock = function *(){
  while(true){
    console.log('Tick!');
    yield;
    console.log('Tock!');
    yield;
  }
}
```

#### 10.2 Generator与协程

协程是一种程序运行的方式，可以理解成“协作的线程”或“协作的函数”，协程既可以用单线程实现，也可以用多线程实现。前者是一种特殊的子例程，后者是一种特殊的线程。

- [协程与子例程的差异](#1021-协程与子例程的差异)
- [协程与普通线程的差异](#1022-协程与普通线程的差异)

##### 10.2.1 协程与子例程的差异

传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式，只有当调用的子函数完全执行完毕，才会结束执行父函数。协程与其不同，多个线程（单线程情况下，即多个函数）可以并行执行，但是只有一个线程（或函数）处于正在运行的状态，其他线程（或函数）都处于暂停态（suspended），线程（或函数）之间可以交换执行权。也就是说，一个线程（或函数）执行到一半，可以暂停执行，将执行权交给另一个线程（或函数），等到稍后收回执行权的时候，再恢复执行。这种可以并行执行、交换执行权的线程（或函数），就称为协程。

从实现上看，在内存中，子例程只使用一个栈（stack），而协程是同时存在多个栈，但只有一个栈是在运行状态，也就是说，协程是以多占用内存为代价，实现多任务的并行。

##### 10.2.2 协程与普通线程的差异

不难看出，协程适合用于多任务运行的环境。在这个意义上，它与普通的线程很相似，都有自己的执行上下文、可以分享全局变量。它们的不同之处在于，同一时间可以有多个线程处于运行状态，但是运行的协程只能有一个，其他协程都处于暂停状态。此外，普通的线程是抢先式的，到底哪个线程优先得到资源，必须由运行环境决定，但是协程是合作式的，执行权由协程自己分配。

由于 JavaScript 是单线程语言，只能保持一个调用栈。引入协程以后，每个任务可以保持自己的调用栈。这样做的最大好处，就是抛出错误的时候，可以找到原始的调用栈。不至于像异步操作的回调函数那样，一旦出错，原始的调用栈早就结束。
Generator 函数是 ES6 对协程的实现，但属于不完全实现。Generator 函数被称为“半协程”（semi-coroutine），意思是只有 Generator 函数的调用者，才能将程序的执行权还给 Generator 函数。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。

如果将 Generator 函数当作协程，完全可以将多个需要互相协作的任务写成 Generator 函数，它们之间使用yield表示式交换控制权。

### 十一、应用

`Generator`可以暂停函数执行，返回任意表达式的值。这种特点使得Generator有多种应用场景。

- [异步操作的同步化表达](#111-异步操作的同步化表达)
- [控制流程管理](#112-控制流程管理)
- [部署Iterator接口](#113-部署iterator接口)
- [作为数据结构](#114-作为数据结构)

#### 11.1 异步操作的同步化表达

`Generator`函数的暂停执行的效果，意味着可以把异步操作的写在`yield`表达式里面，等到用`next`方法时再往后执行。这实际上等同于不需要写回调函数了，因为异步操作的后续操作可以放在`yield`表达式下面，反正要等到调用`next`方法时再执行。所以，Generator函数的一个重要实际意义就是用来处理异步操作，改写回调函数。

```
function * main(){
  var result = yield request('http://some.url');
  var resp = JSON.parse(result);
  console.log(resp.value);
}

function request(url){
  makeAjaxCall(url,function(response){
    it.next(response);
  });
}

var it = main();
it.next();
```

#### 11.2 控制流程管理

`Generator`函数可以改善代码运行流程。

```
function * longRunningTask(value1){
  try{
    var value2 = yield step1(value1);
    var value3 = yield step1(value2);
    var value4 = yield step1(value3);
    var value5 = yield step1(value4);
  }catch(e){
    // Handle any error from step1 through step4
  }
}

function scheduler(task){
  var taskObj = task.next(task.value);
  // 如果Generator函数未结束，就继续调用
  if(!taskObj.done){
    task.value = taskObj.value;
    scheduler(task);
  }
}

scheduler(longRunningTask(initialValue));
```

#### 11.3 部署Iterator接口

利用 Generator 函数，可以在任意对象上部署 Iterator 接口。

```
function * iterEntries(obj){
  let keys = Object.keys(obj);
  for(let i=0;i<keys.length;i++){
    let key = keys[i];
    yield [key,obj[key]];
  }
}

let myObj = { foo: 3, bar: 7 };

for (let [key, value] of iterEntries(myObj)) {
  console.log(key, value);
}
```

#### 11.4 作为数据结构

Generator可以看作是数据结构，更确切地说，可以看作是一个数组结构，因为 Generator 函数可以返回一系列的值，这意味着它可以对任意表达式，提供类似数组的接口。

```
function *doStuff() {
  yield fs.readFile.bind(null, 'hello.txt');
  yield fs.readFile.bind(null, 'world.txt');
  yield fs.readFile.bind(null, 'and-such.txt');
}

for (task of doStuff()) {
  // task是一个函数，可以像回调函数那样使用它
}
```

### 参考资料

- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/generator)
- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/generator.html)


## 联系作者


<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>