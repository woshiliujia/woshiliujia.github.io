---
title: JavaScript-10-正则表达式的定义
date: 2019-04-15 21:05:11
tags: JavaScript
categories: JavaScript
---

## 正则表达式的定义
正则表达式使用RegExp对象表示，可以使用构造函数new RegExp()来创建RegExp对象，更多是使用对象直接量来定义
```
var pattern = /s$/;//包裹在一堆斜杠之间的字符  表示所有已字母s结尾的字符串

//构造函数也可以定义等价的表达式
var pattern = new RegExp("s$")
```
