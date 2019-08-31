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

- Object.getPrototypeOf()
- super 关键字
- 类的 prototype 属性和**proto**属性
- 原生构造函数的继承
- Mixin 模式的实现

### 一、Object.getPrototypeOf()

`Object.getPrototypeOf`方法可以用来从子类上获取父类。可以使用这个方法判断，一个类是否继承了另一个类。

```
Object.getPrototypeOf(ColorPoint) === Point
// true
```

### 二、super 关键字

```

```

### 三、类的 prototype 属性和**proto**属性

```

```

### 四、原生构造函数的继承

```

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

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
