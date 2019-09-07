![image](../img/timg.jpg)
<br>

## 前言

Node提供了net、dgram、http、https这4个模块分别处理TCP、UDP、HTTP、HTTPS，适用于服务器端和客户端。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 网络模型
- 输入url到页面加载都发生了什么事情？
- 构建TCP服务
- 构建UDP服务
- 构建HTTP服务
- 构建WebSocket服务
- 总结

### 一、网络模型

七层网络分别是：应用层、表示层、会话层、传输层、网络层、数据链路层、物理层

[](./img/net01.png)
[](./img/net02.png)

### 二、输入url到页面加载都发生了什么事情？

- 3次握手
- 4次挥手

#### 2.1 3次握手

- 客户端-发送带有SYN标志的数据包-一次握手-服务端
- 服务端-发送带有SYN/ACK标志的数据包-二次握手-客户端
- 客户端-发送带有ACK标志的数据包-三次握手-服务端

#### 2.2 4次挥手

- 客户端-发送一个FIN，用来关闭客户端到服务器的数据传送
- 服务器-收到这个FIN，它发回一个ACK，确认序号为收到的序号加1.和SYN一样，一个FIN将占用一个序号
- 服务器-关闭与客户端的连接，发送一个FIN给客户端
- 客户端-发回ACK报文确认，并将确认序号设置为收到序号加1

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

参考资料：[从URL输入到页面展现到底发生什么？](https://github.com/ljianshu/Blog/issues/24)

### 三、构建TCP服务

```
```

### 四、构建UDP服务

```

```

### 五、构建HTTP服务

略，参考上一篇[文章](https://github.com/cs-learning-record/javascript-series/blob/master/nodejs/http%E6%A8%A1%E5%9D%97.md)

### 六、构建WebSocket服务

```

```

### 七、总结


### 参考资料

- [net 模块](https://github.com/wscats/node-tutorial/tree/master/tutorial/net)
- 《深入浅出nodejs》

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>