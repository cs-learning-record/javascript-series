![image](../img/timg.jpg)
<br>

## 前言

Buffer 是 node 的核心模块，开发者可以利用它 来处理二进制数据，比如文件流的读写、网络请求数据的处理等。

Buffer 的 API 非常多，本文仅挑选 比较常用/容易理解 的 API 进行讲解，包括 Buffer 实例的创建、比较、连接、拷贝、查找、遍历、类型转换、截取、编码转换等。

## 关于作者

一个工作八年的草根程序员。

## 内容

- Buffer 结构
- Buffer 的转换
- Buffer 的拼接
- Buffer 相关方法

### 一、Buffer 结构

Buffer 是一个像 Array 的对象，但它主要用于操作字节。

- 模块结构
- Buffer 对象
- Buffer 内存分配

#### 1.1 模块结构

Buffer 是一个典型的 JavaScript 与 C++结合的模块，它将性能相关部分用 C++实现，将非性能相关的部分用 JavaScript 实现。

#### 1.2 Buffer 对象

Buffer 对象类似于数组，它的元素为 16 进制的两位数，即 0 到 255 的数值。

```
var str = '深入浅出node.js';
var buf = new Buffer(str,'utf-8');
console.log(buf);
```

#### 1.3 Buffer 内存分配

Buffer 对象的内存分配不是在 V8 的堆内存中，而是在 Node 的 C++层面实现内存的申请的。因为处理大量的字节数据不能采用需要一点内存就向操作系统申请一点内存的方式，这可能千万大量内存申请的系统调用。Node 采用了 slab 分配机制，slab 具有 3 种状态

- full：完全分配状态
- partial：部分分配状态
- empty：没有被分配状态

### 二、Buffer 的转换

- 字符串转 Buffer
- Buffer 转字符串
- Buffer 不支持的编码类型

#### 2.1 字符串转 Buffer

字符串转 Buffer 对象主要是通过构造函数完成的：

```
new Buffer(str,[encoding]);
```

#### 2.2 Buffer 转字符串

实现 Buffer 向字符串转换，主要是通过 Buffer 对象的 toString()可以将 Buffer 对象转换为字符串。

```
buf.toString([encoding],[start],[end])

```

#### 2.3 Buffer 不支持的编码类型

Node 的 Buffer 对象支持的编码类型有限，可以使用 isEncoding()函数来判断编码是否支持转换，iconv 和 iconv-lite 两个模块可以支持更多的编码类型转换。

### 三、Buffer 的拼接

- 乱码是如何产生的
- setEncoding()与 string_decoder()
- 正确拼接 Buffer

#### 3.1 乱码是如何产生的

原因在于文件可读流在读取时会逐个读取 Buffer。中文字在 UTF-8 下占 3 个字节。

#### 3.2 setEncoding()与 string_decoder()

- `setEncoding()`：设置编码
- `string_decoder()`：设置解码

#### 3.3 正确拼接 Buffer

```
Buffer.concat = function(list,length){
    if(!Array.isArray(list)){
        throw new Error('Usage:Buffer.concat(list,[length])');
    }

    if(list.length ===0){
        return new Buffer(0);
    }else if(list.length ===1){
        return list[0];
    }

    if(typeof length !== 'number'){
        length =0;
        for(var i=0;i<list.length;i++){
            var buf = list[i];
            length+=buf.length;
        }
    }

    var buffer = new Buffer(length);
    var pos=0;
    for(var i=0;i<list.length;i++){
        var buf = list[i];
        buf.copy(buffer.pos);
        pos+= buf.length;
    }
    return buffer;
};
```

### Buffer 相关方法

- **buf.equals(otherBuffer)：**
- **buf.compare(target[, targetStart[, targetEnd[, sourceStart[, sourceEnd]]]])：**
- **Buffer.compare(buf1, buf2)：**
- **Buffer.from([62])：**
- **Buffer.concat(list[, totalLength])：**
- **buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])：**
- **buf.indexOf(value[, byteOffset][, encoding])：**
- **buf.write(string[, offset[, length]][, encoding])：**
- **buf.fill(value[, offset[, end]][, encoding])：**
- **buf.toString([encoding[, start[, end]]])：**
- **buf.toJSON()：**
- **buf.values()、buf.keys()、buf.entries()：**
- **buf.slice([start[, end]])：**

### 参考资料

- 《深入浅出 Nodejs》

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
