## 主要内容

- 默认绑定
- 隐式绑定
- 显式绑定
- new绑定

### 一、默认绑定

首先要介绍的是最常用的函数调用类型:独立函数调用。可以把这条规则看作是无法应用其他规则时的默认规则。来自《你不知道的javascript(上卷)》

例如：

```
function foo() {
	 console.log( this.a );
}
var a=2; foo(); // 2

```

那么我们怎么知道这里应用了默认绑定呢?可以通过分析调用位置来看看 foo() 是如何调用的。

如果使用严格模式(strict mode)，那么全局对象将无法使用默认绑定，因此this会绑定 到 undefined:

```
function foo() { "use strict";
         console.log( this.a );
     }
var a=2;
foo(); // TypeError: this is undefined

```

这里有一个微妙但是非常重要的细节，虽然 this 的绑定规则完全取决于调用位置，但是只 有foo()运行在非strict mode下时，默认绑定才能绑定到全局对象;严格模式下与foo() 的调用位置无关:

```
function foo() { console.log( this.a );
}
vara=2;
(function(){ "use strict";
         foo(); // 2
})();
```
### 二、隐式绑定

隐式绑定需要考虑的是调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含。

```
function foo() { 
	console.log( this.a );
}
var obj={ 
	a: 2,
	foo: foo 
};
obj.foo(); // 2
```
     
对象属性引用链中只有最顶层或者说最后一层会影响调用位置。举例来说：

```
function foo() { 
	console.log( this.a );
}
var obj2={ 
	a: 42,
	foo: foo
	};
var obj1={ 
	a: 2,
	obj2: obj2 
};
obj1.obj2.foo(); // 42
```
     
###  三、隐式丢失

 this 绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把 this 绑定到全局对象或者 undefined 上，取决于是否是严格模式。
 
```
 function foo() { console.log( this.a );
}
varobj={ a: 2,
foo: foo };
var bar = obj.foo; // 函数别名!
var a = "oops, global"; // a 是全局对象的属性 bar(); // "oops, global"
```
另一种形式

```
function box(fn){
	var a='2';
	fn();
}
var a=1;
function b(){
	console.log(this.a);
}
box(b);
\\ 1
```
```
var boss1 = {
  name: 'boss1',
  returnThis () {
    return this
  }
}
var boss2 = {
  name: 'boss2',
  returnThis () {
    return boss1.returnThis()
  }
}
var boss3 = {
  name: 'boss3',
  returnThis () {
    var returnThis = boss1.returnThis
    return returnThis()
  }
}

boss1.returnThis() // boss1
boss2.returnThis() // ? boss1
boss3.returnThis() // window
```



### 四、显式绑定

可以直接指定this的绑定对象，我们称之为显式绑定。

```
function foo() { 
	console.log( this.a );
 }
 var obj={ 
	a:2
 };
 foo.call( obj ); // 2
```

通过 foo.call(..)，我们可以在调用 foo 时强制把它的 this 绑定到 obj 上。

### 五、new绑定

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

1. 创建(或者说构造)一个全新的对象。
2. 这个新对象会被执行[[原型]]连接。
3. 这个新对象会绑定到函数调用的this。
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

```
function foo(a) { this.a = a;
}
var bar = new foo(2); console.log( bar.a ); // 2
```
使用 new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this 上。


### 六、总结：

可以根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照下面的顺序来进行判断:

1. 函数是否在new中调用(new绑定)?如果是的话this绑定的是新创建的对象。 var bar=new foo();
2. 函数是否通过call、apply（显示绑定）或硬绑定调用？如果是的话，this绑定的是指定的对象。var bar=foo.call(obj2);
3. 函数是否在某个上下文对象中调用(隐式绑定)？如果是的话，this绑定的是那个上下文对象。
4. 如果都不是的话。使用默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到全局对象。var bar=foo();

### 参考资料

《你不知道的javascript(上)》

[this与对象原型 第二章：this豁然开朗](http://www.jianshu.com/p/fcbc21a2c507)

[this与对象原型 第一章：this是什么？](http://www.jianshu.com/p/11d84af237c0)

[深入理解JavaScript系列（13）：This? Yes,this!](http://www.cnblogs.com/TomXu/archive/2012/01/17/2310479.html)

[全面理解 JavaScript 中的 this](https://juejin.im/entry/5a29df626fb9a045211e9c2a?utm_source=gold_browser_extension)

[JavaScript This 的六道坎](https://mp.weixin.qq.com/s/b_SojysoGA_Z7WLJrilizg)