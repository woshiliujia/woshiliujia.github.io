---
title: JavaScript-14-window对象
date: 2019-05-30 14:49:02
tags: JavaScript
categories: JavaScript
---
 ## 计时器
 setTimeout()
 setInterval()
  实现一个函数在指定之后的时间运行。不同的是setTimeout只执行一次，setInterval在指定的毫秒数里面重复调用
  都可以返回一个值可以传递给clearTimeout()用于取消函数执行
 都属于全局方法
 如果这两个方法第一个参数传入字符串，那么字符串会在间隔时间求值 'eval()'
 
 ## 浏览器定位和导航
 window对象的location属性引用的是Location对象，表示该窗口中当前显示文档的URL，并且定义了方法使窗口载入新的文档

 Document对象的location属于也引用到Location对象；
 window.location === document.location;
 document.URL 在Location对象发生更新后属性不会改变

 ### 解析url
 location 对象的其他属性 常用的有 hash、search分别表示url中的片段表示和?后面的内容
 ```
 //提取url中搜索字符串参数
 function urlArgs(){
     var arg = {};
     var query = location.search.substring(1);//查询到字符串并且去除'?'号
     var paris = query.split('&');//根据'&'符号将查询字符串分隔开
     for(let i=0;i<paris.length;i++){
         var pos = paris[i].indexOf('=');//查找有没有那种name=value
         if(pos == -1) continue;//如果字符串查找值为-1 表示没有找到匹配的字符串 跳过
         var name = paris[i].substring(0,pos);//pos找到的是第一次 = 号的位置，substring没有改变原字符串，返回新的字符串，并且不包括指定的第二个位置 所以提取name
         var value = paris[i].substring(pos+1);//提取value，从 = 号后一位开始，直到结束
         value = decodeURIComponent(value);//对value进行解码，url上的中文获取会乱码，需要进行这一步解码
         arg[name] = value; //存储为arg的属性
         return arg;//返回arg对象为字符串中的参数
     }
 }
 ```

### 载入新的文档
location.assign()//此方法可以使窗口载入并显示指定的url文档，只能修改search部分

location.replace()//此方法结果和上面类似，只是会删除当前文档的历史，无法返回

location.reload();//重载页面即刷新页面

浏览器跳转新页面的方法
location = 'www.baidu.com';//使得浏览器跳转到百度
location = '/index';//是浏览器跳转到当前目录下的index目录
location = '#top';//使得浏览器滚动到当前页面的某个位置 ，如果设置的片段标识不存在，则会滚动到顶部

Location对象的URL分解属性是可写的，重新赋值会改变当前URL的位置，会导致浏览器载入新的文档，如果改变的是hash属性则在文档中滚动，不会跳转新页面
location.search = '?page=1&pagesize=3';//载入下个页面

## 浏览历史
window对象的history属性引用的是该窗口History对象。
History对象的length属性表示浏览历史列表的元素数量，处于安全原因脚本不能访问已保存的历史url
History对象的back()和forward()方法与浏览器后退前进的按钮一样。
第三个方法go()方法接收一个整数参数表示前进或者后退几页

## 浏览器和屏幕信息
window对象的navigator和screen属性分别表示浏览器和屏幕信息

### Navigator对象
此对象中包括
```
appName:web浏览器全称，IE中'Miscrosoft Internet Explorer' FireFox和其他浏览器通常取值'Netscape'

appVersion:此属性通常以数字开始，字符串前面数字通常为'4.0/5.0'表示第四代或者第五代兼容浏览器。后面字符没有标准无法作为判断依据

userAgent:浏览器在发送http请求时，USE-AGENT 字段所携带的值。

platform:在其上运行的操作系统的字符串

//以下是一些杂项或者为标准化的方法属性
onLine:此属性值为Boolen表示浏览器是否联网。
geoloaction:用于确定用户地理位置的接口
javaEnable:非标准方法,当浏览器可以运行java小程序返回true 谷歌浏览器没有此属性
cookieEnable:非标准方法，当浏览器可以永久保存cookie时，返回true 
```
### Screen对象
screen属性引用的是Screen对象
提供的信息包括以下:
```
width/heigt:以像素为单位的窗口大小
availWidth/availHeight:指的是实际可用的显示大小，排除桌面任务栏这类的占用空间

//可用以上属性确定web应用是否运行在一个小的屏幕上
```

## 对话框
window提供了3个向用户提供简单的对话框
```
alert();

//以下2个方法会阻止程序的运行，很少使用
confirm();//此方法会返回一个布尔值，表示用户点击取消还是确定按钮 let tip = confirm('确认关闭吗');//返回true/false
prompt();//此方法也可以传入字符串表示提示语 如果点击的是取消返回null否则返回输入内容
```

## 错误处理
Window对象的onerror属性是一个事件处理程序. 
window.onerror = function(msg,url,line){
    return true;//火狐浏览器中需要返回true来表示已经处理了错误
    return false;//其他浏览器返回false来表示处理了错误
};//msg:错误信息;url:发生错误文档所在位置;line:发生的行数

## 作为Window对象属性的文档元素
打开/关闭窗口
```
window.open(url,_blank,'width=100,height:300,status=yes,resizable=yse',);//
window.close();//关闭当前窗口
```

