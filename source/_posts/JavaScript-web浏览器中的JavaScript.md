---
title: JavaScript-web浏览器中的JavaScript
date: 2019-05-29 09:47:14
tags: JavaScript
categories: JavaScript
---
## 客户端JavaScript
Window对象是所有客户端JavaScript特性和API的主要接入点，他表示浏览器的一个窗口或者窗体，用window来引用
window对象定义的一些方法 location、alert、setTimeout等这些属性，不需要显示使用window属性，因为它是全局对象

window对象最重要的一个属性是document，它引用Document对象，表示在窗口中的文档，有一些重要方法 如 getElementById
```
    var timestamp = document.getElementById('timestamp');//查找id=timestamp元素
    /**
    * getElementById()返回的Element对象也有其他的重要属性和方法；如允许脚本获取它的内容，设置属性值等
    */

    timestamp.style.backgroundColor='red';//设置背景色
    timestamp.className = 'hightlight';//设置一个class类名
    if(timestamp.firstChid == null){//如果元素为空，往里面插入当前日期和时间
        timestamp.appendChild(document.creatTextNode(new Date().toString())
    }
```

## 在HTML中嵌入JavaScript
4种方式
使用最多的是src 加载

## JavaScript程序的执行
JavaScript 程序的执行有2个阶段，一，载入文档内容，并加载脚本的阶段。二，载入文档完成，并且所有脚本加载完成。
在所有脚本执行完成就进入第二阶段，这个阶段是异步的，并且由事件驱动。

事件驱动阶段发生的第一个事件就是 onload 事件 表示文档已经加载完成，可以操作

核心的JavaScript和客户端的JavaScript都有一个单线程执行模型。脚本和事件处理程序只能同时执行一个。没有并发性。

###同步异步延时脚本
在加载文档时候如何遇到了script会把这部分内容先加载出来，阻塞了页面的渲染。
可以使用defer async属性来改变脚本的执行方式
defer和async都是告诉浏览器可以继续解析渲染文档，同事执行下载脚本
defer使得浏览器延迟脚本的执行
async属性使得浏览器可以尽快的执行脚本，而不在下载时阻塞文档渲染解析
如果<\script>同时有两个2个属性，如果支持的浏览器会遵循async属性并且忽略defer属性

注意：延迟脚本会按照文档顺序执行，异步脚本在载入后执行，可能会无序
不支持async异步执行脚本
```
function loadasync(url){
    var head = document.getElementByTagName('head)[0];//找到head元素
    var s = document.createElement('script');//创建一个script元素
    s.src = url;//设置script src的属性值
    head.appendChild(s);//讲script元素加入到head标签中
}
//此函数会异步加载脚本
```
### 事件驱动的JavaScript
需要程序响应一个事件叫 事件处理程序  事件监听器  回调
有些事件的目标是文档元素，他们会向上进行 冒泡 因此有时候需要处理这种冒泡

如果需要为一个事件注册多个处理程序函数，需要用到 addEventListenter('事件名称','方法','是否冒泡') ie9以下（不包括ie9）不支持addEventListenter需要用到的是attachEvent()
```
window.addEventListener()
window.attachEvent()
```

### 客户端JavaScript线程模型
JavaScript核心语言并不包含任何线程机制。html5定义了一种新的后台线程'Web Worker'
web worker 是为了执行密集任务不冻结用户界面的后台线程，并且运行在web worker线程代码不能访问文档内容，不能和主线程共享状态

### 客户端JavaScript时间线
```
1.web浏览器创建document对象，并开始解析web页面，解析html元素和他们的文本内容后 添加Element对象和Text节点到文档中

  此阶段document.readystate = 'loading';

2.当html解析器遇到没有async和defer属性的script元素时，把这些元素添加到文档中，然后执行内部或者外部的脚本。并且在下载或者执行脚本时候浏览器会停止渲染页面，也就是html解析器会停止执行。脚本可以遍历已经执行完毕的dom树

3.当解析器设置或遇到了async属性的script元素时，它开始下载脚本，并继续解析文档，脚本下载完不会等待加载完毕尽快执行。此时脚本能够访问在它之前的所有元素。

4.当文档解析完成，document.readystate = 'interactive';

5.所有defer脚本会按照在文档里面出现的顺序执行，异步脚本也可能在这个时间段执行。延迟脚本能够访问完整的dom树，禁止使用document.write()方法

6.浏览器在Document对象上触发DOMContentLoaded事件。这标志程序从同步到异步的驱动阶段。此时可能还有异步脚本没有完成

7.文档已经解析完成，浏览器可能还有其他内容载入（比如图片）当所有内容完成载入，并且所有异步脚本完成载入和执行
  此阶段document.readystate = 'complete',web浏览器触发Window对象上的load事件

8.调用异步事件，以异步响应用户输入，网络事件，计时器过期等

```

## 兼容性和互用性
### IE里面的条件注释
```
//html中的条件注释
<!--[if IE 6]>
兼容ie6的内容
<![endif]-->

<!--[if lte IE 7]>
兼容ie7的内容
<![endif]-->

<!--[if !IE]>
兼容非ie内容
<![endif]-->

<!--[if IE]>
兼容所有ie
<![endif]-->

//IE的JavaScript解释器也支持条件注释，仅仅在ie浏览器中执行
//以下代码仅仅在ie中执行
/*@cc_on
    @if(@_jscript)
    //该代码位于一条js注释内但在ie中执行
    alert('IN IE');
    @end
@*/

//以下代码除了在ie中能够执行，其他浏览器也可以
/*@cc_on
    @if(@_jscript)
    //该代码位于一条js注释内但在ie中执行
    alert('IN IE');
    @else*/
    //这里面写的代码除ie外都可以执行
    alert("not ie")
/*@end
@*/
```

## 可访问性
