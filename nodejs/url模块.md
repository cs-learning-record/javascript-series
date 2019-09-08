![image](../img/timg.jpg)
<br>

## 前言

URL 模块提供了解析和处理 URL 字符串的便利工具，当你需要和 URL 打交道时会非常有用。

- 模块方法
- url 解析：url.parse()
- 关于 urlObject
- url 拼接：url.format(urlObject)
- url.resolve(from, to)

### 一、模块方法

url 模块三个方法分别是：

- **.parse(usrString)：**将 url 字符串，解析成 object，便于开发者进行操作。
- **.format(urlObj)：**.parse()方法的反向操作。
- **.resove(from,to)：**以 from 作为起始地址，解析出完整的目标地址

### 二、url 解析：url.parse()

> 完整语法：url.parse(urlString[, parseQueryString[, slashesDenoteHost]])

- **urlString：**url 链接。
- **parseQueryString：**（默认为 false）如为 false，则 `urlObject.query`为未解析的字符串，比如`nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1`，且对应的值不会 `decode`；如果 `parseQueryString` 为 true，则 `urlObject.query` 为 object，比如{ nick: '程序猿小卡' }，且值会被 decode；
- **slashesDenoteHos：**（默认为 false）如果为`true`，那么类似`//foo/bar` 里的 foo 就会被认为是`hostname`；如果为 false，则 foo 被认为是 pathname 的一部分。

#### 2.1 参数值不进行解析

代码如下：

```
var url = require('url');
var str = 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1';

var obj = url.parse(str);
console.log(obj);
```

输出如下：

```
Url {
  protocol: 'http:',
  slashes: true,
  auth: 'Chyingp:HelloWorld',
  host: 'ke.qq.com:8080',
  port: '8080',
  hostname: 'ke.qq.com',
  hash: '#part=1',
  search: '?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  query: 'nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  pathname: '/index.html',
  path: '/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  href: 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1' }
```

#### 2.2 对参数值进行 decode

代码如下：

```

```

输出结果如下：

```

```

#### 2.3 针对路径 //foo/bar 的处理

代码如下：

```

```

输出如下：

```

```

### 三、关于 urlObject

- protocol：协议，需要注意的是包含了:，并且是小写的。
- slashes：如果:后面跟了两个//，那么为 true。
- auth：认证信息，如果有密码，为 usrname:passwd，如果没有，则为 usrname。注意，这里区分大小写。
- host：主机名。注意包含了端口，比如 ke.qq.com:8080，并且是小写的。
- hostname：主机名，不包含端口，并且是小写的。
- hash：哈希部分，注意包含了#。
- search：查询字符串，注意，包含了?，此外，值是没有经过 decode 的。
- query：字符串 或者 对象。如果是字符串，则是 search 去掉?，其余一样；如果是对象，那么是 decode 过的。
- path：路径部分，包含 search 部分。
- pathname：路径部分，不包含 search 部分。
- href：原始的地址。不过需要注意的是，protocol、host 会被转成小写字母。

```
{
  protocol: 'http:',
  slashes: true,
  auth: 'Chyingp:HelloWorld',
  host: 'ke.qq.com:8080',
  port: '8080',
  hostname: 'ke.qq.com',
  hash: '#part=1',
  search: '?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  query: { nick: '程序猿小卡' },
  pathname: '/index.html',
  path: '/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  href: 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1' }
  }
```

### url 拼接：url.format(urlObject)

> 完整语法：url.format(urlObject)

`url.parse(str)`的反向操作，没什么好说的。`urlObject` 包含了很多字段，比如 protocol、slashes、protocol 等，且不一定需要全部传，所以有一套解析逻辑。

### url.resolve(from, to)

```
url.resolve('/one/two/three', 'four')         // '/one/two/four'
url.resolve('http://example.com/', '/one')    // 'http://example.com/one'
url.resolve('http://example.com/one', '/two') // 'http://example.com/two'
```

## 关于作者

一个工作八年的草根程序员。

## 内容

### 参考资料

- 《Nodejs 即学即用》
- [url](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/url.md)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
