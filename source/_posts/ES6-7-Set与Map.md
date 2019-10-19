---
title: ES6-7-Set与Map
date: 2019-10-16 11:38:13
tags: ES6
categories: ES6
---

## ES6中的Set

创建Set并添加项
Set中存储的是不重复的值，但是Set会保存类型不等的两个值  两个空对象也不会被判断成为同一个值
```
let set = new Set();
set.add(5);
set.add('5');
set.size;//2

//可以使用set来初始化一个数组 来保证数组中没有重复的值
let arr = [1,1,1,2,3,4,4,5,6,6]
let set = new Set(arr);//去重
```
判断一个值是否存在于set中可以用set.has(5) 返回布尔

删除值
```
set.delete(5);//删除5这一项，并且size减1
set.clear();//全部清除可以使用 size为0 set被清空
```

### Set上的forEach()方法
Set上可以使用forEach方法，不过传递的参数与数组中的forEach稍有不同 三个参数分别是 下个位置的值，跟上个参数一样，Set自身

即Set中无法获取对象的index

#### 将Set转换为数组
Set得到的并不是一个真正的数组
```
let arr = [...set];//此种方式是将set修改为数组最简单的方法

let arr = Array.from(arr);//此种方法也可以转换为数组
```
## Weak Set  new WeakSet()
```
1.new WeakSet()中不能传入基本类型
2.不能迭代 不能用在 for-of循环中
3.没有forEach()方法
4.没有size属性
```

## ES6的Map
Map类型是键值对的有序列表,可以调用set()方法用来传递一个关联的值。之后调用get()来获取
```
let map = new Map();
let key1 = {};
map.set('key','dd');
map.set(key1,{});

map.get('key');//dd
map.get(key1);//{}
```
PS:Map中可以使用对象来作为键 map的存储跟LocalStorage有些类似
Map中同样有size属性，其它与Set中类似
map.has(<属性名称>);//用来表示Map中是否有此属性
```
map.delete(<属性名>);//删除某个属性，size减1
map.clear();//全部清除
```
### Map的初始化
Map的初始化与Set类似，通过传入一个数组给Map构造器 并且数组的每一项也需要是数组。并且每一项数组中的第一项作为键，第二项作为值

### Map上的forEach方法
回调的三个参数 下个位置的值，该值所对应的键，Map本身


