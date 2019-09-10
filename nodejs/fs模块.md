![image](../img/timg.jpg)
<br>

## 前言

fs模块是文件操作的封装，它提供了文件的读取、写入、更名、删除、遍历目录、链接等POSIX文件系统操作。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [文件读取](#一文件读取)
- [文件写入](#二文件写入)
- [文件是否存在](#三文件是否存在)
- [创建目录](#四创建目录)
- [删除文件](#五删除文件)
- [遍历目录](#六遍历目录)
- [文件重命名](#七文件重命名)
- [监听文件修改](#八监听文件修改)
- [获取文件状态](#九获取文件状态)
- [访问/权限检测](#十访问权限检测)
- [文件打开/关闭](#十一文件打开关闭)
- [文件读取](#十二文件读取)
- [追加文件内容](#十三追加文件内容)

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

**同步方式**

```
var fs = require('fs');
var path = require('path');

var getFileInDir = function (dir) {
    var results = [path.resolve(dir)];
    var files = fs.readdirSync(dir, 'utf8');

    files.forEach(function (file) {

        file = path.resolve(dir, file);

        var stats = fs.statSync(file);

        if (stats.isFile()) {
            results.push(file);
        } else if (stats.isDirectory()) {
            results = results.concat(getFileInDir(file));
        }
    });

    return results;
};
var files = getFileInDir('../');
console.log(files);
```

**异步方式**

```
// 异步方式遍历目录

var fs = require('fs');
var path = require('path');

var getFileInDir = function (dir) {
    var results = [path.resolve(dir)];
    fs.readdir(dir, { encoding: 'utf8' }, function (err, files) {
        files.forEach(function (file) {

            file = path.resolve(dir, file);

            var stats = fs.statSync(file);

            if (stats.isFile()) {
                results.push(file);
            } else if (stats.isDirectory()) {
                results = results.concat(getFileInDir(file));
            }
        });
    });
    return results;
};
var files = getFileInDir('../');
console.log(files);
```

### 七、文件重命名

**异步**

```
var fs = require('fs');
fs.rename('./hello', './world', function(err){
    if(err) throw err;
    console.log('重命名成功');
});
```

**同步**

```
fa.renameSync(oldPath,newPath);
var fs = require('fs');

fs.renameSync('./world','./hello');
```

### 八、监听文件修改

`fs.watch()`比`fs.watchFile`高效很多

#### fs.watchFile()

实现原理：轮询。每隔一段时间检查文件是否发生变化。所以在不同平台上表现基本是一致的。

```
var fs = require('fs');

var options ={
    persistent:true,    // 默认就是true
    interval:2000       // 多久检查一次
};

fs.watchFile('./fileForWatch.txt',options,function(curr,prev){
    console.log('修改时间为：'+curr.mtime);
})
```

```
var fs = require('fs');

var options = {
    persistent: true,
    recursive: true,
    encoding: 'utf8'
};

fs.watch('../', options, function(event, filename){
    console.log('触发事件:' + event);
    if(filename){
        console.log('文件名是: ' + filename);
    }else{
        console.log('文件名是没有提供');
    }
});
```

### 九、获取文件状态

相关方法如下：

- stats.isFile()——是否文件
- stats.isDirectory()——是否目录
- stats.isSocket() -- 是不是socket文件
- atime：Access Time // 访问时间
- mtime:: Modified Time // 文件内容修改时间
- ctime: Changed Time. // 文件状态修改时间，比如修改文件所有者、修改权限、重命名等
- birthtime: Birth Time // 创建时间。在某些系统上是不可靠的，因为拿不到。

**异步方式**

```
var fs = require('fs');

var getTimeDesc = function (d) {
    return [d.getFullYear(), d.getMonth() + 1, d.getDate()].join('-') + ' ' + [d.getHours(), d.getMinutes(), d.getSeconds()].join(':');
}

fs.stat(__dirname + '\\file.txt', function (err, stats) {
    if (err) {
        console.error(err.message)
        return
    }
    console.log('文件大小：' + stats.size);
    console.log('创建时间：' + getTimeDesc(stats.birthtime));
    console.log('访问时间：' + getTimeDesc(stats.atime));
    console.log('修改时间：' + getTimeDesc(stats.mtime));
})
```

**同步方式**

```
var fs = require('fs');

var getTimeDesc = function(d){

}
```

### 十、访问/权限检测

**异步版本**

```
var fs = require('fs');

fs.access('./fileForAccess.txt',function(err){
    if(err) throw err;
    console.log('可以访问');
})
```

**同步版本**

```
var fs = require('fs');

// 如果成功，则返回undefined，如果失败则抛出错误
try{
    fs.accessSync('./fileForAccess.txt');
}catch(e){
    throw(e);
}
```

### 十一、文件打开/关闭

`fs.open(path[, flags[, mode]], callback)`异步地打开文件。
- `path`文件路径。
- `flags`可以是以下值。
  - r：以读取模式打开文件。
  - r+：以读写模式打开文件。
  - w：以写入模式打开文件，如果文件不存在则创建。
  - w+：以读写模式打开文件，如果文件不存在则创建。
  - a：以追加模式打开文件，如果文件不存在则创建。
  - a+：以读取追加模式打开文件，如果文件不存在则创建。
- `mode`：用于创建文件时给文件指定权限。
- `callback`：回调函数

`fs.close(fd, callback)`异步地关闭文件。

### 十二、文件读取

`fs.read(fd, buffer, offset, length, position, callback)`从指定的文件中读取数据。

- `fd`：文件的句柄。
- `buffer`：将读取的文件内容写到buffer里。
- `offset`：buffer开始写入的位置。（在offset开始写入，还是offset+1？）
- `length`：要读取的字节数
- `position`：文件从哪个位置开始读取。如果是null，那么就从当前位置开始读取。
- `callback`：回调函数，其参数为`(err,bytesRead,buffer)`

### 十三、追加文件内容

`fs.appendFile(file, data[, options], callback)`异步地将数据追加到文件，如果文件尚不存在则创建该文件。`data`可以是字符串或`buffer`。

- `file`:可以是文件路径，也可以是文件句柄
- `data`:要追加的内容。string或者buffer
- `options`
  - `encoding`：编码，默认是`utf8`
  - `mode`：默认是`0o666`
  - `flag`：默认是`a`
- `callback`:回调函数

```
var fs = require('fs');

fs.appendFile('./extra/fileForAppend.txt','hello','utf8',function(err){
    if(err) throw err;
    console.log('append成功');
})
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