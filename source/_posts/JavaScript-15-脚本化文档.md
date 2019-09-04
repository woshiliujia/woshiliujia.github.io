---
title: JavaScript-15-脚本化文档
date: 2019-07-04 23:35:25
tags: JavaScript
categories: JavaScript
---

## DOM概览
Document对象可以使用document来获取对它的引用
代表更节点的时Document
代表html元素的节点是Element
代表文本节点是text

Document、Element、Text是Node的子类

##选取文档元素

### 通过ID选取元素
Document对象的getElementById()方法选取一个基于唯一ID的元素
```
document.getElementById('section');
```
### 通过名字选取元素
name属性只会在少部分html元素中有效包括（表单、img、iframe、表单元素等）
基于name属性去选取html元素可以使用Document对象的getElementsByName()方法

```
    document.getElementsByName('sex');
    1.此方法返回一个NodeList对象类似于只读数组，选取所有指定name的元素
    2.不管name属性是否有效，只要设置了name属性的元素都可以被选择到
    3.此方法无法在xml中使用
```

### 通过标签名选择元素
getElementByTagName()通过此方法可以获取指定类型的所有html或者xml元素
```
document.getElementByTagName('span');
1.此方法依旧返回一个NodeList对象 类数组对象
```