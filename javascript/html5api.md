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
    - [7.1 属性](#71-%e5%b1%9e%e6%80%a7)
    - [7.2 方法](#72-%e6%96%b9%e6%b3%95)
      - [7.2.1 History.back()、History.forward()、History.go()](#721-historybackhistoryforwardhistorygo)
      - [7.2.2 History.pushState()](#722-historypushstate)
      - [7.2.3 History.replaceState()](#723-historyreplacestate)
    - [7.3 popstate事件](#73-popstate%e4%ba%8b%e4%bb%b6)
  - [八、WebSocket](#%e5%85%abwebsocket)
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

Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。在主线程运行的同时，Worker 线程在后台运行，两者互不干扰。等到 Worker 线程完成计算任务，再把结果返回给主线程。

Web Worker 有以下几个使用注意点。

- 同源限制
- DOM限制
- 全局对象限制
- 通信联系
- 脚本限制
- 文件限制

### 六、Geolocation API

Geolocation API这套API，JavaScript代码能够访问到用户的当前位置信息。

Geolocation有以下三个方法：

- `getCurrentPosition()`确定设备的位置并返回一个携带位置信息的 Position 对象。
- `watchPosition()`注册一个位置改变监听器，每当设备位置改变时，返回一个 long 类型的该监听器的ID值。
- `clearWatch()`取消由 watchPosition()注册的位置监听器。

### 七、History

`window.history`属性指向 History 对象，它表示当前窗口的浏览历史。

- [属性](#7.1-属性)
- [方法](#7.2-方法)
- [popstate事件](#7.3-popstate事件)

#### 7.1 属性

History 对象主要有两个属性。

- `History.length`:当前窗口访问的网址数量（包括当前网页）
- `History.state`:History堆栈最上层的状态值


#### 7.2 方法

- History.back()、History.forward()、History.go()
- History.pushState()
- History.replaceState()

##### 7.2.1 History.back()、History.forward()、History.go()

- History.back():移动到上一个网址，等同于点击浏览器的后退键。对于第一个访问的网址，该方法无效果。
- History.forward():移动到下一个网址，等同于点浏览器的前进键。对于最后一个访问的网址，方法无效果。
- History.go():接受一个整数作为参数，以当前网址为基准，移动到参数指定的网址，比如`go(1)`相当于`forward()`，`go(-1)`相当于`back()`。如果参数超过实际存在的网址范围，该方法无效果；如果不指定参数，默认参数为0，相当于刷新当前页面。

##### 7.2.2 History.pushState()

`History.pushState()`方法用于在历史中添加一条记录。

`window.history.pushState(state, title, url)`

该方法接受三个参数，依次为：

- `state`:一个与添加的记录相关联的状态对象，主要用于popstate事件。该事件触发时，该对象会传入回调函数。也就是说，浏览器会将这个对象序列化以后保留在本地，重新载入这个页面的时候，可以拿到这个对象。如果不需要这个对象，此处可以填null。
- `title`:新页面的标题。但是，现在所有浏览器都忽视这个参数，所以这里可以填空字符串。
- `url`:新的网址，必须与当前页面处在同一个域。浏览器的地址栏将显示这个网址。

##### 7.2.3 History.replaceState() 

`History.replaceState()`方法用来修改`History`对象的当前记录，其他都与pushState()方法一模一样。

#### 7.3 popstate事件

每当同一个文档的浏览历史（即history对象）出现变化时，就会触发popstate事件。

```
window.onpopstate = function (event) {
  console.log('location: ' + document.location);
  console.log('state: ' + JSON.stringify(event.state));
};

```

### 八、WebSocket

后续文章会详细介绍WebSocket。

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>