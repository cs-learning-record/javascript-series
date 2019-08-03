![image](../img/timg.jpg)
<br>

## 前言

本人平时学习及收集内容，欢迎参入一起讨论。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [call 模拟实现](#一call模拟实现)
- [apply 模拟实现](#二apply模拟实现)
- [bind 模拟实现](#三bind模拟实现)

经过[上一篇](https://github.com/cs-learning-record/javascript-series/blob/master/javascript/apply%26bind%26call.md)我知道 call、apply、bind 大概功能以及作用，这篇我来根据实现 all、apply、bind。

### 一、call 模拟实现

> call() 方法在使用一个指定的 this 值和若干个指定的参数值的前提下调用某个函数或方法。

从定义上看 call 有以下两个作用：

1. call 改变了 this 的指向，指向到 foo
2. bar 函数执行了

```

Function.prototype.call2=function(context){
    var context= context || window;
    context.fn=this;

    var args=[];
    for(var i=1,len=arguments.length;i<len;i++){
        args.push('arguments['+i+']');
    }

    var result=eval('context.fn('+args+')');

    delete context.fn;
    return result;
}


```

### 二、apply 模拟实现

apply 功能跟 call 类似，代码实现如下：

```
    Function.prototype.apply=function(context,arr){
        var context=Object(context) || window;

        var result;
        if(!arr){
            result=context.fn();
        }else{
            var args=[];
            for(var i=0,len=arr.length;i<len;i++){
                args.push('arr['+i+')');
            }
            result=eval('context.fn('+args+')')
        }

        delete context.fn
        return result;
    }
```

### 三、bind 模拟实现

> bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。(来自于 MDN )

bind 函数的三个特点：

1. 返回一个函数
2. 可以传入参数
3. 一个绑定函数也能使用 new 操作符创建对象，这种行为就像把原函数当成构造器，提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

模拟实现如下：

```
Function.prototype._bind=function(context){
    var func=this;
    var params=[].slice.call(arguments,1);
    var Fnop=function(){};
    var fbound=function(){
        params=params.concat([].slice.call(arguments));
        return  func.apply(this instanceof Fnop? this:context,params);
    };
    Fnop.prototype=this.prototype;
    fbound.prototype=new Fnop();
    return fbound;
};

```

## 参考资料

- [JavaScript 深入之 bind 的模拟实现](https://github.com/mqyqingfeng/Blog/issues/12)
- [JavaScript 深入之 call 和 apply 的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
