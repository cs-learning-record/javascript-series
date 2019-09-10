![image](../img/timg.jpg)
<br>

## 前言

fs模块是文件操作的封装，它提供了文件的读取、写入、更名、删除、遍历目录、链接等POSIX文件系统操作。

## 关于作者

一个工作八年的草根程序员。

## 内容

- 文件读取
- 文件写入
- 文件是否存在
- 创建目录
- 删除文件
- 遍历目录
- 文件重命名
- 监听文件修改
- 获取文件状态
- 访问/权限检测
- 文件打开/关闭
- 文件路径

### 一、文件读取

**普通读取**

```
var fs = require('fs');
var data;

try{
    data = fs.readFileSync('./fileForRead.txt','utf8');
    console.log('文件内容：' + data);
}cat(err){
    console.error('读取文件出错：' + err.message);
}

```

**异步读取**

```
var fs = require('fs');

fs.readFile('./fileForRead.txt','uft8',function(err,data){
    if(err){
        return console.error('读取文件出错：' + err.message);
    }
    console.log('文件内容：' + data);
})
```

**通过文件流读取**

适合读取大文件

```
var fs = require('fs');
var readStream = fs.createReadStream('./fileForRead.txt','utf8');

readStream.on('data',function(chunk){
    console.log('读取数据：'+chunk);
}).on('error',function(err){
    console.log('出错：'+err.message);
}).on('end',function(){ // 没有数据了
    console.log('没有数据了');
}).on('close',function(){   // 已经关闭，不会再有事件抛出
    console.log('已经关闭');
})
```

### 二、文件写入

备注：以下代码，如果文件不存在，则创建文件；如果文件存在，则覆盖文件内容；

**异步写入**

```
var fs = require('fs');

fs.writeFile('./fileForWrite.txt','hello world','utf8',function(err){
    if(err) throw err;
    console.log('文件写入成功');
});

```

**同步写入**

```
var fs = require('fs');

try{
    fs.writeFileSync('./fileForWrite1.txt','hello world','utf8');
    console.log('文件写入成功');
}catch(err){
    throw err;
}
```

**通过文件流写入**

```
var fs = require('fs');
var writeStream = fs.createWriteStream('./fileForWrite1.txt','utf8');

writeStream.on('close',function(){  // 已经关闭，不会再有事件抛出
    console.log('已经关闭');
});

writeStream.write('hello');
writeStream.write('world');
writeStream.end('');
```

### 三、文件是否存在

`fs.exists()`已经是`deprecated`状态，现在可以通过下面代码判断文件是否存在。

```
var fs = require('fs');

fs.access('./fileForRead.txt',function(err){
    if(err) throw err;
    console.log('fileForRead.txt存在');
});

fs.access('./fileForRead2.txt',function(err){
    if(err) throw err;
    console.log('fileForRead2.txt存在');
});
```

`fs.access()`除了判断文件是否存在（默认模式），还可以用来判断文件的权限。

### 四、创建目录

异步版本（如果目录已存在，会报错）

```
var fs = require('fs');

fs.mkdir('./hello',function(err){
    if(err) throw err;
    console.log('目录创建成功');
})
```

同步版本

```
var fs = require('fs');

fs.mkdirSync('./hello');
```

### 五、删除文件

异步方式

```
var fs = require('fs');

fs.unlink('',function(err){
    if(err) throw err;
    console.log('文件删除成功');
})
```

同步方式

```
var fs = require('fs');

fs.unlinkSync('./fileForUnlink.txt');
```

### 六、遍历目录

```
var fs = require('fs');
```

### 七、文件重命名

```
var fs = require('fs');
```

### 八、监听文件修改

```
var fs = require('fs');
```

### 九、获取文件状态

```
var fs = require('fs');
```

### 十、访问/权限检测

```
var fs = require('fs');
```

### 十一、文件打开/关闭

```
var fs = require('fs');
```

### 十二、文件路径

```
var fs = require('fs');
```

### 参考资料

- 《nodejs开发指南》
- [fs模块](https://github.com/chyingp/nodejs-learning-guide/blob/master/%E6%A8%A1%E5%9D%97/fs.md)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>