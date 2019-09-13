![image](../img/timg.jpg)
<br>

## 前言

Buffer 是 node 的核心模块，开发者可以利用它 来处理二进制数据，比如文件流的读写、网络请求数据的处理等。

Buffer 的 API 非常多，本文仅挑选 比较常用/容易理解 的 API 进行讲解，包括 Buffer 实例的创建、比较、连接、拷贝、查找、遍历、类型转换、截取、编码转换等。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [Buffer 结构](#一buffer-结构)
- [Buffer 的转换](#二buffer-的转换)
- [Buffer 的拼接](#三buffer-的拼接)
- [Buffer 相关方法](#四buffer-相关方法)

### 一、Buffer 结构

Buffer 是一个像 Array 的对象，但它主要用于操作字节。

- [模块结构](#11-模块结构)
- [Buffer 对象](#12-buffer-对象)
- [Buffer 内存分配](#13-buffer-内存分配)

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

- [字符串转 Buffer](#21-字符串转-buffer)
- [Buffer 转字符串](#22-buffer-转字符串)
- [Buffer 不支持的编码类型](#23-buffer-不支持的编码类型)

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

- [乱码是如何产生的](#31-乱码是如何产生的)
- [setEncoding()与 string_decoder()](#32-setencoding与-string_decoder)
- [正确拼接 Buffer](#33-正确拼接-buffer)

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

### 四、Buffer 相关方法

- **buf.equals(otherBuffer)：** 判断两个 buffer 实例存储的数据是否相同，如果是，返回 true，否则返回 false。
- **buf.compare(target[, targetStart[, targetEnd[, sourceStart[, sourceEnd]]]])：** 也判断两个 buffer 实例进行比较，不同的是：可以指定比较的范围；返回值为整数，达标 buf、target 的大小关系
- **Buffer.from([62])：** 每个 array 的元素对应 1 个字节（8 位），取值从 0 到 255。
- **Buffer.concat(list[, totalLength])：** 返回一个合并了`list`中所有`Buffer`实例的新`Buffer`。
- **buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])：** 拷贝`buf`中某个区域的数据到`target`中的某个区域，即使`target`的内存区域与`buf`的重叠。
- **buf.indexOf(value[, byteOffset][, encoding])：** 跟数组的查找差不多，需要注意的是，value 可能是 String、Buffer、Integer 中的任意类型。
- **buf.write(string[, offset[, length]][, encoding])：** 将 string 写入 buf 实例，同时返回写入的字节数。
- **buf.fill(value[, offset[, end]][, encoding])：** 用`value`填充 buf，常用于寝化 buf。
- **buf.toString([encoding[, start[, end]]])：** 把 buf 解码成字符串
- **buf.toJSON()：** 转成 JSON 字符串
- **buf.values()、buf.keys()、buf.entries()：** 用于对 `buf`进行 `for...of`遍历
- **buf.slice([start[, end]])：** 用于截取 buf，并返回一个新的 Buffer 实例。

### 参考资料

- 《深入浅出 Nodejs》
- [buffer 模块](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/buffer.md)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>
