![image](../img/timg.jpg)
<br>

## 前言

本人平时学习及收集内容，欢迎参入一起讨论。

## 关于作者

一个工作八年的草根程序员。

## 内容

- [前言](#%e5%89%8d%e8%a8%80)
- [关于作者](#%e5%85%b3%e4%ba%8e%e4%bd%9c%e8%80%85)
- [内容](#%e5%86%85%e5%ae%b9)
  - [一、canvas](#%e4%b8%80canvas)
  - [二、svg](#%e4%ba%8csvg)
  - [三、requestAnimationFrame()](#%e4%b8%89requestanimationframe)
  - [四、File API](#%e5%9b%9bfile-api)
    - [4.1 File 对象](#41-file-%e5%af%b9%e8%b1%a1)
    - [4.2 FileList 对象](#42-filelist-%e5%af%b9%e8%b1%a1)
    - [4.3 FileReader 对象](#43-filereader-%e5%af%b9%e8%b1%a1)
  - [五、Web Workers](#%e4%ba%94web-workers)
  - [六、Geolocation API](#%e5%85%adgeolocation-api)
  - [七、History](#%e4%b8%83history)
  - [八、WebSocket](#%e5%85%abwebsocket)
  - [九、postMessage](#%e4%b9%9dpostmessage)
- [联系作者](#%e8%81%94%e7%b3%bb%e4%bd%9c%e8%80%85)

### 一、canvas

关于canvas用法，请关注[相应文章](https://github.com/cs-learning-record/javascript-series/blob/master/canvas.md)。

### 二、svg

关于svg用法，请关注后续相应文章。

### 三、requestAnimationFrame()

`window.requestAnimationFrame()`告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行

```
window.requestAnimFrame=(function(){
    return window.requestAnimationFrame ||
           window.webkitRequestAnimationFrame ||
           window.mozRequestAnimationFrame  ||
           window.oRequestAnimationFrame ||
           window.msRequestAnimationFrame ||
           function(callback){
               window.setTimeout(callback,1000/60);
           };
})();

```

### 四、File API

- File 对象
- FileList 对象
- FileReader 对象

#### 4.1 File 对象

`File`对象代表一个文件，用来读写文件信息。它继承了`Blob`对象，或者说是一种特殊的`Blob`对象，所有可以使用`Blob`对象的场合都可以使用它。

- 构造函数
- 实例属性和实例方法

**构造函数**

`File()`构造函数接受三个参数。

- array:一个数组，成员可以是二进制对象或字符串，表示文件的内容。
- name:字符串，表示文件名或文件路径。
- options:配置对象，设置实例的属性。可以设置两个属性。type:字符串，表示实例对象的MIME类型，默认值为空字符串。lastModified：时间戳，表示上次修改的时间，默认为`Date.now()`。

```
var file = new File(['foo'],'foo.txt',{type:'text/plain'})
```

**实例属性和实例方法**

File 对象有以下实例属性。

- File.lastModified:最后修改时间
- File.name:文件名或文件路径
- File.size:文件大小（单位字节）
- File.type:文件的MIME类型

```
var myFile = new File([],'file.bin',{
    lastModified: new Date(2018, 1, 1)
});
myFile.lastModified
myFile.name // "file.bin"
myFile.size // 0
myFile.type // ""

```

#### 4.2 FileList 对象

`FileList`对象是一个类似数组的对象，代表一组选中的文件，每个成员都是一个 File 实例。它主要出现在两个场合。

- 文件控件节点（`<input type="file">`）的`files`属性，返回一个 FileList 实例。
- 拖拉一组文件时，目标区的`DataTransfer.files`属性，返回一个 FileList 实例。


#### 4.3 FileReader 对象

FileReader提供以下的实例属性：

- FileReader.error：读取文件时产生的错误对象
- FileReader.readyState：整数，表示读取文件时的当前状态。一共有三种可能的状态，0表示尚未加载任何数据，1表示数据正在加载，2表示加载完成。
- FileReader.result：读取完成后的文件内容，有可能是字符串，也可能是一个ArrayBuffer实例。
- FileReader.onabort：`abort`事件（用户终止读取操作）的监听函数。
- FileReader.onerror：`error`事件（读取错误）的监听函数。
- FileReader.onload：`load`事件（读取操作完成）的监听函数，通常在这个函数里面使用`result`属性，拿到文件内容。
- FileReader.onloadstart：`loadstart`事件（读取操作开始）的监听函数。
- FileReader.onloadend：`loadend`事件（读取操作结束）的监听函数。
- FileReader.onprogress：`progress`事件（读取操作进行中）的监听函数。


FileReader提供了如下几个实例方法：

- readAsText(file,encoding):以纯文本形式读取文件，将读取到的文本保存在result属性中。第二个参数用于指定编码类型，是可选的。
- readAsDataURL(file):读取文件并将文件以数据URI的形式保存在result属性中。
- readAsBinaryString(file):读取文件并将一个字符串保存在result属性中，字符串中的每个字符表示一字节。
- readAsArrayBuffer(file):读取文件并将一个包含文件内容的

### 五、Web Workers


### 六、Geolocation API

### 七、History

### 八、WebSocket

### 九、postMessage

## 联系作者

<div align="center">
    <p>
        在颠覆世界的同时，也要好好关照自己。
    </p>
    <img src="../img/contact.png" />
</div>