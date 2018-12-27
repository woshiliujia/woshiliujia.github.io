---
title: JavaScript-6.对象
date: 2018-12-26 22:31:57
tags: JavaScript
---

## 创建对象
创建对象三种方法 

1. 对象直接量 即 var obj = {};

2. new 关键字来创建 即 var obj = new Object();

3. ES5 Object.create() 来创建 即 var obj = Object.create(); 传入参数为对象的原型，空对象传入Object.prototype;

第三种创建方法的模拟
```
function inherit(p){
    //首先判断p是否为null 传入必须是个对象不能是null
    if(p == null) throw TypeError();

    //如果支持Object.create;则直接使用
    if(Object.create) return Object.create(p);

    //如果不支持则使用模拟方法 判断是否为对象和function
    var t = typeof p; //判断传入对象p的类型是否为对象或者function
    if(t !== 'object' && t !== 'function') throw TypeError(); //不是抛出错误
    //成立 则定义构造函数，将原型属性赋值为 p
    function f(){};
    f.prototype = p;
    //最后返回此构造函数
    return new f();
    
 //此方法不完全等于 Object.create(); 没有第二个参数，和没法传入null;
}
```

## 访问/查询 对象
访问对象可以通过 . 的方式 和 [] 的方式 

如果使用 . 运算符 属性名是一个表示符 不是一个数据类型因此程序无法修改。

如果使用 [] 运算符 属性名通过字符串来表示，因此可以修改和创建。



## 继承

### 概括：如果要查找一个对象的某个属性 先是查找此对象本身是否有这个属性，如果没有

### 继续查找此对象的原型对象，如果原型对象没有找到并且此原型对象也有原型则继续查找

### 直到查到某个原型对象的原型为null; 这就构成了一个 “链” 通过这个实现属性继承

### 属性赋值
    1. 属性赋值首先检查原型链判断是否允许赋值，如果此对象继承一个只读属性，那么赋值操作是不被允许的
    2. 如果允许赋值，也只是在此对象上修改或者新增属性，而不会去修改原型链;

### 属性访问错误
    如果对象存在，访问的一个属性此对象自身和继承属性均未找到此属性。返回undefined。

    如果对象不存在，那么查找这个不存在对象的属性就会报错

    设置属性失败的场景
        1. o中的p属性是只读的,不能给只读属性赋值（defineProperty()方法,可以对可配置的只读属性修改）
        2. o中的属性p是继承来的,并且p是只读的，不能通过同名自有属性覆盖只读的继承属性
        3. 如果对象o的可扩展属性（extensible attribute）为false 并且o中没有属性p，则也不能添加属性p。

## 删除属性
