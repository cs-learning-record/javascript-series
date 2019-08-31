![image](../img/timg.jpg)
<br>

## 前言

Class 可以通过`extends`关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

```
class ColorPoint extends Point{
    constructor(x,y,color){
        super(x,y);     // 调用父类的constructor(x,y)
        this.color = color;
    }

    toString(){
        return this.color + ' '+ super.toString();  //  调用父类的toString()
    }
}
```

ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`）方法，然后再用子类的构造函数修改`this`。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [Object.getPrototypeOf()](#一objectgetprototypeof)
- [super 关键字](#二super-关键字)
- [类的 prototype 属性和**proto**属性](#三类的-prototype-属性和proto属性)
- [原生构造函数的继承](#四原生构造函数的继承)
- [Mixin 模式的实现](#五mixin-模式的实现)

### 一、Object.getPrototypeOf()

`Object.getPrototypeOf`方法可以用来从子类上获取父类。可以使用这个方法判断，一个类是否继承了另一个类。

```
Object.getPrototypeOf(ColorPoint) === Point
// true
```

### 二、super 关键字

`super`，既可以当作函数使用，也可以当作对象作用。

第一种情况，`super`作为函数调用时，代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次`super`函数。

```
class A{
    constructor(){
        console.log(new.target.name);
    }
}
class B extends A{
    constructor(){
        super();
    }
}
new A()     // A
new B()     // B
```

注意：`super()`只能用在子类的构造函数之中，用在其他地方就会报错。

第二种情况，`super`作为对象时，在普通方法中，指向父类的原型对象。在静态方法中，指向父类。

```
class A{
    p(){
        return 2;
    }
}

class B extends A{
    constructor(){
        super();
        console.log(super.p());     //2
    }
}

```

注意：`super`指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过`super`调用的。

```
class A{
    constructor(){
        this.p = 2;
    }
}

class B extends A{
    get m(){
        return super.p;
    }
}

let b = new B();
b.m     // undefined
```

上面代码中，`p`是父类`A`实例的属性，`super.p`就引用不到它。

在子类型普通方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类实例。

```
class A {
  constructor() {
    this.x = 1;
  }
  print() {
    console.log(this.x);
  }
}

class B extends A {
  constructor() {
    super();
    this.x = 2;
  }
  m() {
    super.print();
  }
}

let b = new B();
b.m() // 2
```

如果`super`作为对象，用在静态方法之中，这时`super`将指向父类，而不是父类的原型对象。

### 三、类的 prototype 属性和**proto**属性

在 ES5 中，每一个对象都有`__proto__`属性，指向对应的构造函数的`prototype`属性。Class 作为构造函数的语法糖，同时有`prototype`属性和`__proto__`属性，因此同时存在两条继承链。

- 子类的`__proto__`属性，表示构造函数的继承，总是指向父类。
- 子类的`prototype`属性的`__proto__`属性，表示方法的继承，总是指向父类的`prototype`属性。

```
class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

模式实现

```
class A{

}

class B{

}

// B的实例继承A的实例
Object.setPrototypeOf(B.prototype,A.prototype);

// B 继承 A 的静态属性
Object.setPrototypeOf(B, A);

const b = new B();
```

### 四、原生构造函数的继承

ES6 允许继承原生构造函数定义子类，因为 ES6 是先新建父类的实例对象`this`，然后再用子类的构造函数修饰`this`，使得父类的所有行为都可以继承。

```
class MyArray extends Array {
  constructor(...args) {
    super(...args);
  }
}

var arr = new MyArray();
arr[0] = 12;
arr.length // 1

arr.length = 0;
arr[0] // undefined
```

### 五、Mixin 模式的实现

Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口。

```
function mix(...mixins){
    class Mix{
        constructor(){
            for(let mixn of mixins){
                copyProperties(this,new mixin());   // 拷贝实例属性
            }
        }
    }

    for(let mixin of mixins){
        copyProperties(Mix,mixin);      // 拷贝静态属性
        copyProperties(Mix.prototype,mixin.prototype);      // 拷贝原型属性
    }

    return Mix;
}

function copyProperties(target,source){
    for(let key of Reflect.ownKeys(source)){
        if(key !== 'constructor'
      && key !== 'prototype'
      && key !== 'name'){
            let desc = Object.getOwnPropertyDescriptor(source, key);
            Object.defineProperty(target, key, desc);
      }
    }
}

class DistributedEdit extends mix(Loggable, Serializable) {
  // ...
}
```

### 参考资料

- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/class-extends)
- [《ECMAScript 6 入门》 第三版]()

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
