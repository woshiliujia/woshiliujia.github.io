---
title: JavaScript-7-数组
date: 2018-12-28 23:47:25
tags: JavaScript
categories: JavaScript
---

## 数组读写
数组是对象的特殊形式,只是将对象的属性变成索引并省略了
    
    var o = {}
    o[1] = 'one'
    //输入是{1:'one'}

数组可以从原型中继承元素。es5中数组可以定义元素的getter和setter方法

## 数组元素的添加和删除
```
<!-- 向数组尾部追加元素 -->
var a = [];
a.push('last');等价于 a[length] = 'last';//向数组末尾添加一个元素;

<!-- 向数组首部添加元素 -->
a.unshift('first')

<!-- 删除数组元素 -->
a.pop();//删除最后一位元素，返回新的长度（与push一组）
a.shift();//删除第一位元素，返回新的长度（与unshift一组）
delete a[0];//此操作会只会将a[0]赋值为undefined,数组长度不会改变
```

## 数组遍历
数组遍历最常用的就是for循环
```
var keys = Object.keys(o);//此方法可以获得对象o的属性名所组成的数组

```