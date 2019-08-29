![image](../img/timg.jpg)
<br>

## 前言

在 ES6 之前，社区制定了一些模块加载方案，最主要的有 CommonJS 和 AMD 两种。前者用于服务器，后者用于浏览器。ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

## 关于作者

一个工作八年的草根程序员。

## 内容

- export 命令
- import 命令
- 模块的整体加载
- export default 命令
- export 与 import 的复合写法
- import()
- 跨模块常量

### 一、export 命令

`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

```
export var firstName = 'Tony';
export var lastName = 'Jackson';
export var year = 1958;

export {firstName,lastName,year};
```

`export`输出的变量就是本来名字，但是可以使用`as`关键字重命名。

```
function v1(){...}
function v2(){...}

export{
    v1 as streamV1,
    v2 as streamV2,
    v2 as streamLatestVersion
}
```

`function`和`class`的输出

```
export function f(){};

function f(){

}
export {f};

```

### 二、import 命令

使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块。

```
import {firstName,lastName,year} from './profile.js';

function setName(element){
    element.textContent = firstName + ' '+lastName;
}
```

上面代码的`import`命令，用于加载`profile.js`文件，并从中输入变量。

注意，`import`命令具有提升效果，会提升到整个模块的头部，首先执行。

```
foo();

import {foo} from 'my_module';
```

上面的代码不会报错，因为`import`的执行早于`foo`的调用。

`import`命令输入的变量都是只读，因为它的本质是输入接口。

```
import {a} from './xxx.js'

a = {}; // Syntax Error : 'a' is read-only;
```

由于`import`是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

```
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

### 三、模块的整体加载

除了指定加载某个输出值，还可以使用整体加载，即用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面

```
// circle.js

export function area(radius){
    return  Math.PI * radius * radius;
}

export function circumference(radius){
    return 2* Math.PI*radius;
}

// main.js
import * as circle from './circle';

console.log('圆面积：'+circle.area(4);)
console.log('圆周长：'+circle.circumference(14);)
```

### 四、export default 命令

为模块指定默认输出，就要用到`export.default`命令。

```
// export-default.js
export default function () {
  console.log('foo');
}
```

`export default`命令用于指定模块的默认输出。显然，一个模块只能有一个默认输出，因此`export default`命令只能使用一次。

正是因为 `export default` 命令其实只是输出一个叫做 `default` 的变量，所以它后面不能跟变量声明语句。

### 五、export 与 import 的复合写法

如果在一个模块之中，先输入后输出同一个模块，`import`语句可以与`export`语句写在一起。

```
export {foo,bar} from 'my_module';

//  可以简单理解为
import { foo,bar} from 'my_module';
export {foo,bar};
```

### 六、import()

`import()`类似于 Node 的`require`方法，区别主要是前者是异步加载，后者是同步加载。`import()`函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。

**适用场合**

- 按需加载
- 条件加载
- 动态的模块路径

### 七、跨模块常量

`const`声明的常量只在当前代码块有效。如果想设置跨模块的常量（即跨多个文件），或者说一个值要被多个模块共享，可以采用下面的写法。

```
export const db = {
  url: 'http://my.couchdbserver.local:5984',
  admin_username: 'admin',
  admin_password: 'admin password'
};

// constants/user.js
export const users = ['root', 'admin', 'staff', 'ceo', 'chief', 'moderator'];
```

### 参考资料

- [import、require、export、module.exports 混合使用详解](https://juejin.im/post/5a2e5f0851882575d42f5609)
- [前端模块化：CommonJS,AMD,CMD,ES6](https://juejin.im/post/5aaa37c8f265da23945f365c)
- [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/module)
- [《ECMAScript 6 入门》 第三版](https://yjhenan.gitbooks.io/-ecmascript-6/docs/module.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
