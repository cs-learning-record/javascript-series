![image](../img/timg.jpg)
<br>

## 前言

Node.js标准库提供了http模块，其中封装了一个高效的HTTP服务器和一个简易的HTTP客户端。Http.Server是一个基于事件的HTTP服务器，它的核心由Node.js下层C++部分实现，而接口由JavaScript封装，兼顾了高性能与简易性。

## 关于作者

一个工作八年的草根程序员。

## 内容

- HTTP 服务器
- HTTP 客户端

### 一、HTTP服务器

接收来自客户端的请求，并将客户端请求的地址返回给客户端。

```
var http = require('http');

http.createServer(function(req,res){
    res.writeHead(200,{'Content-Type':'text/html'});
    res.write('<h1>Node.js</h1>');
    res.write('<h1>Hello World</h1>');
}).listen(3000);

console.log('HTTP server is listenging at port 3000.');
```

上面代码中，http.createServer创建了一个http.Server的实例，将一个函数作为HTTP请求处理函数。最后该实例还调用了listen函数，启动服务器并监听3000端口。

- http.Server的事件
- http.ServerResponse

#### http.Server的事件

#### http.ServerResponse

### 二、HTTP客户端

向服务器发起请求，并将服务器返回的内容打印到控制台。

```
```

### 参考资料

- 《nodejs开发指南》
- [http模块概览](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/http.md)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>