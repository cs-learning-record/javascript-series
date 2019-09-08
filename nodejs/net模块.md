![image](../img/timg.jpg)
<br>

## 前言

Node 提供了 net、dgram、http、https 这 4 个模块分别处理 TCP、UDP、HTTP、HTTPS，适用于服务器端和客户端。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 网络模型
- 输入 url 到页面加载都发生了什么事情？
- 构建 TCP 服务
- 构建 UDP 服务
- 构建 HTTP 服务
- 构建 WebSocket 服务
- 总结

### 一、网络模型

七层网络分别是：应用层、表示层、会话层、传输层、网络层、数据链路层、物理层

[](./img/net01.png)
[](./img/net02.png)

### 二、输入 url 到页面加载都发生了什么事情？

- 3 次握手
- 4 次挥手

#### 2.1 3 次握手

- 客户端-发送带有 SYN 标志的数据包-一次握手-服务端
- 服务端-发送带有 SYN/ACK 标志的数据包-二次握手-客户端
- 客户端-发送带有 ACK 标志的数据包-三次握手-服务端

#### 2.2 4 次挥手

- 客户端-发送一个 FIN，用来关闭客户端到服务器的数据传送
- 服务器-收到这个 FIN，它发回一个 ACK，确认序号为收到的序号加 1.和 SYN 一样，一个 FIN 将占用一个序号
- 服务器-关闭与客户端的连接，发送一个 FIN 给客户端
- 客户端-发回 ACK 报文确认，并将确认序号设置为收到序号加 1

```
输入地址
浏览器查找域名的 IP 地址
这一步包括 DNS 具体的查找过程，包括：浏览器缓存->系统缓存->路由器缓存...
浏览器向 web 服务器发送一个 HTTP 请求
服务器的永久重定向响应（从 http://example.com 到 http://www.example.com）
浏览器跟踪重定向地址
服务器处理请求
服务器返回一个 HTTP 响应
浏览器显示 HTML
浏览器发送请求获取嵌入在 HTML 中的资源（如图片、音频、视频、CSS、JS等等）
浏览器发送异步请求
```

参考资料：[从 URL 输入到页面展现到底发生什么？](https://github.com/ljianshu/Blog/issues/24)

### 三、构建 TCP 服务

```
var net = require('net');

var server = net.createServer(function(socket){
    // 新的连接
    socket.on('data',function(data){
        socket.write('你好');
    });

    socket.on('end',function(){
        console.log('连接断开');
    });
    socket.write('欢迎光临《深入浅出Node.js》示例：\n');
});

server.listen(8124,function(){
    console.log('server bound';)
})
```

#### 3.1 TCP 服务的事件

- 服务器事件
- 连接事件

**服务器事件**

通过 net.createServer()创建的服务器而言，它是一个 EventEmitter 实例。

- listening:在调用 server.listen()绑定端口或者 Domain Socket 后触发，简洁写法为 server.listen(port,listeningListener)，通过 listent()方法的第二个参数传入。
- connection：每个客户端套接字
- close：
- error：当服务器发生异常时，将会触发该事件。比如侦听一个使用中的端口，将会触发一个异常，如果不侦听 error 事件，服务器将会抛出异常。

**连接事件**

- data:当
- end:当连接中的做生意一端发送了 FIN

### 四、构建 UDP 服务

#### 7.1 创建 UDP 服务器端

```
var dgram = require('dgram');

var server = dgram.createSocket('udp4');

server.on('message',function(msg,rinfo){
    console.log('server got:'+ msg + 'from '+ rinfo.address + ':'+rinfo.port);
})
```

#### 7.2 创建 UDP 客户端

```

```

#### 7.3 UDP 套接字事件

- message：
- listening：
- close：
- error：

### 五、构建 HTTP 服务

略，参考上一篇[文章](https://github.com/cs-learning-record/javascript-series/blob/master/nodejs/http%E6%A8%A1%E5%9D%97.md)

### 六、构建 WebSocket 服务

```

```

### 七、总结

### 参考资料

- [net 模块](https://github.com/wscats/node-tutorial/tree/master/tutorial/net)
- 《深入浅出 nodejs》

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
