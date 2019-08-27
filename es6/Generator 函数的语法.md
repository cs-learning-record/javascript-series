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

- 基本概念
- next 方法的参数
- for...of 循环
- Generator.prototype.throw()
- Generator.prototype.return()
- next()、throw()、return() 的共同点
- yield* 表达式
- 作为对象属性的Generator函数
- Generator 函数的this
- 含义
- 应用

### 一、基本概念

- yield表达式
- 与Iterator接口的关系

#### 1.1 yield表达式

```
```

#### 1.2 与Iterator接口的关系

```
```

### 二、`next`方法的参数

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

```
```

### 三、`for...of`循环

```
```

### 四、Generator.prototype.throw()

```
```

### 五、Generator.prototype.return()

```
```

### 六、`next()、throw()、return()`的共同点

```
```

### 七、`yield*`表达式

```
```

### 八、作为对象属性的Generator函数

```
```

### 九、`Generator`函数的`this`

```
```

### 十、含义

协程是一种程序运行的方式，可以理解成“”

- Generator与状态机
- Generator与协程

#### 10.1 Generator与状态机

传统的“子例”

#### 10.2 Generator与协程

### 十一、应用

`Generator`可以暂停函数执行，返回任意表达式的值。这种特点使得Generator有多种应用场景。

- 异步操作的同步化表达
- 控制流程管理
- 部署Iterator接口
- 作为数据结构

#### 11.1 异步操作的同步化表达

#### 11.2 控制流程管理

#### 11.3 部署Iterator接口

#### 11.4 作为数据结构

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