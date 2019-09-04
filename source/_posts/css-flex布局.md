---
title: css-flex布局
date: 2019-09-04 23:09:39
tags: CSS
categories: CSS
---
## 理解主轴与辅轴

默认的排列方向为主轴
```
//默认设置display:flex;相当于默认水平方向即flex-direction:row;
```

### 对齐与空间
主轴的排列叫排布，辅轴叫对齐
用于排布的属性是justify-content: 不能指定单独的个别项的排布

PS:对flexbox子项指定外边距值为auto，并且那一侧还有空间。那么该外边距就会占据所有剩余空间

用于布局的也就是子项的排布属性为 align-items: 此为对齐所有项
```
//给一个元素设置上下左右居中
//父元素设置display:flex;子元素设置margin:auto;
//这样会自动填充上下左右边距
```
#### 对齐个别项
```
//如果需要更改个别元素的对齐方式可以使用
align-self://可跟align-items属性差不多
```

### 可伸缩的尺寸
```
//flex-basis 该项是控制在主轴上的，也就是需要在父级设置
//默认值为 auto，表示从width height获取尺寸
//如果设置为百分比或者长度值则相对于主轴即容器而言
//如果没有设置主尺寸则根据内容确定大小

//如果设置为content则会忽略width height 根据项目内容确定大小 此项支持度不一致
```

```
//flex-grow 弹性系数 可以放大每一项如果还有剩余空间
//默认值为 0 表示从flex-basis取得尺寸后就不再扩展 即代表不放大
```
```
//flex-shrink 弹性系数 表示空间不够该如何收缩 默认值为1，表示每项按照自己首选尺寸等比例缩放
```

```
//flex简写属性
flex:1 0 0%;//分别表示flex-grow flex-shrink flex-basis
//ps:最后一个值必须加上百分号 简写flex-basis必须加单位 或0px
```

### 列布局和个别排序
order:-1;