![image](../img/timg.jpg)
<br>

## 前言

querystring 模块是用来处理 query 字符串的简单辅助模块。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [字符串转对象](#一字符串转对象)
- [对象转字符串](#二对象转字符串)
- [其它方法](#三其它方法)

### 一、字符串转对象

```
var querystring = require('querystring');
var str = 'firstname=dk&url=http%3A%2F%2Fdk-lan.com&lastname=tom&passowrd=123456';
var param = querystring.parse(param);
//结果
//{firstname:"dk", url:"http://dk-lan.com", lastname: 'tom', passowrd: 123456};
```

### 二、对象转字符串

```
var querystring = require('querystring');

var obj = {firstname:"dk", url:"http://dk-lan.com", lastname: 'tom', passowrd: 123456};
//将对象转换成字符串
var param = querystring.stringify(obj);
//结果
//firstname=dk&url=http%3A%2F%2Fdk-lan.com&lastname=tom&passowrd=123456
```

### 三、其它方法

- **decode()：** 解码，对应的别名是`parse()`
- **encode()：** 编码，对应的别名是`stringify()`
- **escape(str)：** 对字符串进行编码
- **parse(str[, sep[, eq[, options]]])：** 将字符串解析成对象
- **stringify(obj[, sep[, eq[, options]]])：** 将对象解析成字符串
- **unescape(str)：** 编码的字符串进行解码。

### 参考资料

- 《Nodejs 即学即用》
- [nodejs v12.1 文档](https://nodejs.org/api/querystring.html)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
