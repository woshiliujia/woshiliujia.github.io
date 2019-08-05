---
title: JavaScript-15-脚本化文档
date: 2019-08-05 14:37:19
tags: JavaScript
categories: JavaScript
---

## 选取文档元素

IE8及其较低的版本未实现 getElementsByClassName();方法，可以使用querySelectorAll()方法实现

```
document.querySelectorAll();//传入参数相当于jq类库传入方式，$();基于此封装的
```

## 文档结构和遍历