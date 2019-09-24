![image](../img/timg.jpg)
<br>

## 前言

单个`Node.js`实例运行在单个线程中。 为了充分利用多核系统，有时需要启用一组 Node.js 进程去处理负载任务。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [多个node实例+多个端口](#一多个node实例+多个端口)
- [主进程向子进程转发请求](#二主进程向子进程转发请求)
- [实例代码](#三实例代码)
- [实现原理](#四实现原理)

### 一、多个node实例+多个端口

集群内的node实例，各自监听不同的端口，再由反向代理实现请求到多个端口的分发。

- 优点：实现简单，各实例相对独立，这对服务稳定性有好处。
- 缺点：增加端口战胜，进程之间通信比较麻烦。

### 二、主进程向子进程转发请求

集群内，创建一个主进程（master），以及若干个子进程。由master监听客户连接请求，并根据特定的策略，转发worker。

- 优点：通常只占用一个端口，通信相对简单，转发策略更灵活。
- 缺点：实现相对复杂，对主进程的稳定性要求较高。

### 三、实例代码

```
const cluster = require('cluster');

if(cluster.isMaster){
    const cpuNum = require('os').cpus().length;
    for(let i=0;i<cpuNum;++i){
        cluster.fork();
    }

    // 创建进程完成后输出信息
    cluster.on('online',(worker)=>{
        console.log('Create worker-' + worker.process.pid);
    });

    // 监听子进程退出后重启事件
    cluster.on('exit',(worker,code,signal)=>{
        console.log('[Master] worker ' + worker.process.pid + ' died with code:' + code + ', and' + signal);
        cluster.fork();     // 重启子进程
    });
}else{
    const net = require('net');
    net.createServer().on('connection',(socket)=>{
        setTimeout(()=>{
            socket.end('Request handled by worker-'+process.pid);
        },10);
    }).listen(8989);
}
```

输出结果

```
Create worker- 23735
Create worker- 23731
Create worker- 23729
Create worker- 23730
```

### 四、实现原理

#### 4.1 master、worker如何通信

> master进程通过cluster.fork()来创建worker进程。cluster.fork()内部是通过child_process.fork()来创建子进程。

#### 4.2 如何实现端口共享

net模块中，对 listen() 方法进行了特殊处理。根据当前进程是master进程，还是worker进程：

1. master进程：在该端口上正常监听请求。（没做特殊处理）
2. worker进程：创建server实例。然后通过IPC通道，向master进程发送消息，让master进程也创建 server 实例，并在该端口上监听请求。当请求进来时，master进程将请求转发给worker进程的server实例。

#### 4.3 如何将请求分发到多个worker

每当worker进程创建server实例来监听请求，都会通过IPC通道，在master上进行注册。当客户端请求到达，master会负责将请求转发给对应的worker。

具体转发给哪个worker？这是由转发策略决定的。可以通过环境变量NODE_CLUSTER_SCHED_POLICY设置，也可以在cluster.setupMaster(options)时传入。

当有客户请求到达，master会轮询一遍worker列表，找到第一个空闲的worker，然后将该请求转发给该worker。

### 参考资料

- 《深入浅出 Nodejs》
- [cluster模块](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/cluster.md)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
