![image](../img/timg.jpg)
<br>

## 前言

Node.js 标准库提供了 http 模块，其中封装了一个高效的 HTTP 服务器和一个简易的 HTTP 客户端。Http.Server 是一个基于事件的 HTTP 服务器，它的核心由 Node.js 下层 C++部分实现，而接口由 JavaScript 封装，兼顾了高性能与简易性。

## 关于作者

一个工作八年的草根程序员。

## 内容

- HTTP 服务器
- HTTP 客户端

### 一、HTTP 服务器

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

上面代码中，http.createServer 创建了一个 http.Server 的实例，将一个函数作为 HTTP 请求处理函数。最后该实例还调用了 listen 函数，启动服务器并监听 3000 端口。

- http.Server 的事件
- http.ServerRequest
- 获取 GET 请求内容
- 获取 POST 请求内容
- http.ServerResponse

#### 1.1 http.Server 的事件

http.Server 是一个基于事件的 HTTP 服务器，所有的请求都被封装为独立的事件，开发者只需要对它的事件编写响应函数即可实现 HTTP 服务器的所有功能。

```
var http = require('http');

var server = new http.Server();
server.on('request',function(req,res){
    res.writeHead(200,{'Content-Type':'text/html'});
    res.write('<h1>Node.js</h1>');
    res.end('<p>Hello World</p>')
});
server.listen(3000);

console.log('HTTP server is  listening at port 3000.');
```

#### 1.2 http.ServerRequest

http.serverRequest 提供了以下 3 个事件用于控制请求体

- data:当请求体数据到来时，该事件被触发。
- end:当请求体数据传输完成时，该事件被触发，此后将不会再有数据到来。
- close:用户当前请求结束时，该事件被触发。

http.serverRequest 相关属性

| 名称        | 含义                                                          |
| ----------- | ------------------------------------------------------------- |
| complete    | 客户端请求是否已经发送完成                                    |
| httpVersion | HTTP 协议版本，通常是 1.0 或 1.1                              |
| method      | HTTP 请求方法，如 GET、POST、PUT、DELETE 等                   |
| url         | 原始的请求路径，例如 /static/image/x.jpg 或 /user?name=byvoid |
| headers     | HTTP 请求头                                                   |
| trailers    | HTTP 请求尾(不常见)                                           |
| connection  | 当前 HTTP 连接套接字，为 net.Socket 的实例                    |
| socket      | connection 属性的别名                                         |
| client      | client 属性的别名                                             |

#### 1.3 获取 GET 请求内容

```
var http = require('http');
var url = require('url');
var util = require('util');
http.createServer(function(req,res){
    res.writeHead(200,{'Content-Type':'text/plain'});
    res.end(util.inspect(url.parse(req.url,true)));
}).listen(3000);
```

通过 url.parse1，原始的 path 被解析为一个对象，其中 query 就是我们所谓的 GET 请求的内容，而路径则是 pathname。

#### 1.4 获取 POST 请求内容

```
var http = require('http');
var querystring = require('querystring');
var util = require('util');
http.createServer(function(req,res){
    var post = '';

    req.on('data',function(chunk){
        post +=chunk;
    });

    req.on('end',function(){
        post = querystring.parse(post);
        res.end(util.inspect(post);)
    })
}).listen(3000);
```

通过 req 的 data 事件监听函数，每当接受到请求体的数据， 就累加到 post 变量中。

#### 1.5 http.ServerResponse

http.ServerResponse 是返回给客户端的信息，决定了用户最终能看到的结果。

http.ServerResponse 有三个重要的成员函数，用于返回响应头、响应内容以及结束 请求。

- response.writeHead(statusCode, [headers]):向请求的客户端发送响应头。 statusCode 是 HTTP 状态码，如 200 (请求成功)、404 (未找到)等。headers 是一个类似关联数组的对象，表示响应头的每个属性。该函数在一个请求内最多只 能调用一次，如果不调用，则会自动生成一个响应头。
- response.write(data, [encoding]):向请求的客户端发送响应内容。data 是 一个 Buffer 或字符串，表示要发送的内容。如果 data 是字符串，那么需要指定 encoding 来说明它的编码方式，默认是 utf-8。在 response.end 调用之前， response.write 可以被多次调用。
- response.end([data], [encoding]):结束响应，告知客户端所有发送已经完 成。当所有要返回的内容发送完毕的时候，该函数 必须 被调用一次。它接受两个可 选参数，意义和 response.write 相同。如果不调用该函数，客户端将永远处于 等待状态。

### 二、HTTP 客户端

向服务器发起请求，并将服务器返回的内容打印到控制台。

http 模块提供了两个函数 http.request 和 http.get，功能是作为客户端向 HTTP 服务器发起请求。

- http.request
- http.get
- http.ClientRequest
- http.ClientResponse

#### 2.1 http.request(options, callback)

接受两个参数，option 是 一个类似关联数组的对象，表示请求的参数，callback 是请求的回调函数。option 常用的参数如下所示。

- host :请求网站的域名或 IP 地址。
- port :请求网站的端口，默认 80。
- method :请求方法，默认是 GET。
- path :请求的相对于根的路径，默认是“/”。QueryString 应该包含在其中。
  例如 /search?query=byvoid。
- headers :一个关联数组对象，为请求头的内容。

```
var http = require('http');
var querystring = require('querystring');

var contents = querystring.stringfy({
    name:'tony',
    email:`fftony@163.com`,
    address: 'Zijing 2#,Tsinghua University'
});

var options = {
    host:'www.fttony.com',
    path:'/application/node/post.php',
    method:'post',
    headers:{
        'Content-Type':'application/x-www-form-urlencoded',
        'Content-Length':contents.length
    }
};

var req = http.request(options,function(res){
    res.setEncoding('utf8');
    res.on('data',function(data){
        console.log(data);
    })
})

req.write(contents);
req.end();
```

#### 2.2 http.get(options, callback)

http.get(options, callback) http 模块还提供了一个更加简便的方法用于处 理 GET 请求:http.get。它是 http.request 的简化版，唯一的区别在于 http.get 自动将请求方法设为了 GET 请求，同时不需要手动调用 req.end()。

```

var http = require('http');
http.get({host: 'www.byvoid.com'}, function(res) { res.setEncoding('utf8');
res.on('data', function (data) {
           console.log(data);
         });
});

```

#### 2.3 http.ClientRequest

http.ClientRequest 是由 http.request 或 http.get 返回产生的对象，表示一
个已经产生而且正在进行中的 HTTP 请求。

```
var http = require('http');
var req = http.get({host: 'www.byvoid.com'});
req.on('response', function(res) { res.setEncoding('utf8'); res.on('data', function (data) {
        console.log(data);
      });
});
```

http.ClientRequest 还提供了以下函数。

- request.abort():终止正在发送的请求。
- request.setTimeout(timeout, [callback]):设置请求超时时间，timeout 为
  毫秒数。当请求超时以后，callback 将会被调用。

#### 2.4 http.ClientResponse

http.ClientResponse 与 http.ServerRequest 相似，提供了三个事件 data、end
和 close，分别在数据到达、传输结束和连接结束时触发，其中 data 事件传递一个参数 chunk，表示接收到的数据。

ClientResponse 的属性

| 名称        | 含义                             |
| ----------- | -------------------------------- |
| statusCode  | HTTP 状态码，如 200、404、500    |
| httpVersion | HTTP 协议版本，通常是 1.0 或 1.1 |
| headers     | HTTP 请求头                      |
| trailers    | HTTP 请求尾(不常见)              |

http.ClientResponse 还提供了以下几个特殊的函数。

- response.setEncoding([encoding]):设置默认的编码，当 data 事件被触发
  时，数据将会以 encoding 编码。默认值是 null，即不编码，以 Buffer 的形式存储。常用编码为 utf8。

- response.pause():暂停接收数据和发送事件，方便实现下载功能。
- response.resume():从暂停的状态中恢复。

### 参考资料

- 《nodejs 开发指南》
- [http 模块概览](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/http.md)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
