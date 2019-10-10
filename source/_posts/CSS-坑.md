---
title: CSS-坑
date: 2019-09-18 13:36:42
tags: 坑
categories: 坑
---

## css 父元素设置了transform: translate3d()
导致的问题:子元素设置的position:fixed;失效 并且降级成为position:absolute;

解决办法:尽量避免这种操作,可以把需要position:fixed;以body定位;

场景: 下拉刷新插件中一般都会设置 transform