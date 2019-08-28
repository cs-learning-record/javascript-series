![image](../img/timg.jpg)
<br>

## 前言

ES6 的类，完全可以看作构造函数的另一种写法。

```
class Ponit{
   constructor(){
       // ...
   }

   toString(){
       // ...
   }

   toValue(){

   }
}

// 等同于
Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

## 关于作者

一个工作八年的草根程序员。

## 内容

- [简介](#一简介)
- [静态方法](#二静态方法)
- [实例属性的新写法](#三实例属性的新写法)
- [静态属性](#四静态属性)
- [私有方法和私有属性](#五私有方法和私有属性)
- [new.target属性](#六newtarget属性)

### 一、简介

- [constructor方法](#11-constructor方法)
- [类的实例](#12-类的实例)
- [取值函数和存值函数](#13-取值函数和存值函数)
- [属性表达式](#14-属性表达式)
- [Class表达式](#15-class表达式)

#### 1.1 constructor方法

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

```
class Point{

}

// 等同于
class Point[
    construct(){}
]
```

类必须使用`new`调用，否则会报错。

#### 1.2 类的实例

生成类的实例的写法，与 ES5 完全一样，也是使用`new`命令。

```
class Point{
    // ...
}

var point = new Point(2,3);
```

#### 1.3 取值函数和存值函数

与ES5一样，在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

```
class MyClass{
    constructor(){
        // ...
    }
    get prop(){
        return 'getter';
    }
    set prop(value){
        console.log('setter'+value);
    }
}

let inst = new MyClass();

inst.prop = 123;
// setter:123

inst.prop
// 'getter'
```

#### 1.4 属性表达式

类的属性名，可以采用表达式。

```
let methodName = 'getArea';

class Square{
    constructor(length){
        //  ...
    }
    [methodName](){
        // ...
    }
}
```

#### 1.5 Class表达式

与函数一样，类也可以使用表达式的形式定义。

```
const MyClass = class Me{
    getClassName(){
        return Me.name;
    }
}
```

需要注意的是，这个类的名字是`MyClass`而不是`Me`，`Me`只在Class的内部代码可用，指代当前类。

**注意点**

- 严格模式
- 不存在提升
- name属性
- Generator方法
- this的指向

### 二、静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```
class Foo{
    static classMethod(){
        return 'hello';
    }
}

Foo.classMethod()   // hello
```

### 三、实例属性的新写法

实例属性除了定义在`constructor()`方法里面的`this`上面，也可以定义在类的最顶层。

```
class IncreasingCounter {
  constructor() {
    this._count = 0;
  }
  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}
```

另一种写法是，这个属性也可以定义在类的最顶层，其他都不变。

```
class IncreasingCounter{
    _count = 0;
    get value(){
        console.log('Getting the current value!');
        return this._count;
    }
    increment(){
        this._count++;
    }
}
```

### 四、静态属性

静态属性指的是Class本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性。

```
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```

### 五、私有方法和私有属性

- [私有方法](#51-私有方法)
- [私有属性](#511-命名上加以区别)

#### 5.1 私有方法

- [命名上加以区别](#511-命名上加以区别)
- [将私有方法移出模块](#512-将私有方法移出模块)
- [利用`Symbol`](#513-利用symbol)

#### 5.1.1 命名上加以区别

```
class Widget{

    // 公有方法
    foo(baz){
        this._bar(baz);
    }

    // 私有方法
    _bar(baz){
        return this.snaf = baz;
    }
}
```

#### 5.1.2 将私有方法移出模块

模块内部的所有方法都是对外可见的

```
class Widget{
    foo (baz){
        bar.call(this,baz);
    }
}

function bar(baz){
    return this.snaf = baz;
}
```

#### 5.1.3 利用`Symbol`

利用`Symbol`值的唯一性，将私有方法的名字命名为一个`Symbol`值。

```
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass{

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  // ...
};
```

#### 5.2 私有属性

方法是在属性名之前，使用`#`表示。

```
class IncreasingCounter{
    #count = 0;
    get value(){
        console.log('Getting the current value!');
        return this.#count;
    }
    increment(){
        this.#count++;
    }
}
```

### 六、new.target属性

`new`是从构造函数生成实例对象的命令，ES6为`new`命令引入了一个`new.target`属性，该属性一般用在构造函数之中，返回`new`命令作用于的那个构造函数。

```
function Person(name){
    if(new.target !== undefined){
        this.name = name;
    }else{
        throw new Error('必须使用new命令生成实例');
    }
}

```

### 参考资料

- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/class)
- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/content/docs/class.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>