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

    delete 运算符可以删除对象的属性.但只是断开属性和宿主对象的联系,不会操作属性中的属性（即，如果这个属性是一个对象，并不会对这个对象有操作）

    delete 运算符只能删除自有属性，不能删除继承属性

    delete 表达式删除成功或者没有副作用时，返回true 无意义的删除也返回true

    delete 也不能删除可配置属性为false的对象 某些内置对象的属性是不可配置的，通过变量声明和和函数创建的全局对象的属性（比如通过 var a = 1;delete this.a ;返回false）严格模式下报错

## 检测属性

判断某个属性是否存在某个对象中可以使用：in运算符、hasOwnProperty()和propetyIsEnumerable() 方法来完成,或者通过属性查询

    in运算符左侧是属性名(必须是字符串,否则会被当成一个变量)，右侧是对象.如果对象的自有属性或者继承属性中包含此属性则返回true

    hasOwnProperty()方法用来检测给定的名字是否是对象的自有属性，如果是继承属性返回false

    propeIsEnumerable()方法是hasOwnProperty()的升级版本，只有是自有属性，且这个属性的可枚举性（enumerable attribute）为true 才会返回true

    !== 符号有种情况不能使用，当属性存在但值为undefined和不存在的属性可以使用 in运算符


## 枚举属性
    1. for/in 循环遍历 只能遍历可枚举的属性
    2. Object.keys(); 返回一个对象中属性名称组成的数组 （自有，且可枚举的属性）
    3. Object.getOwnPropetyNames();返回所有自有属性名称包括不可枚举的


## 属性getter和setter

    es5中属性值可以是一或者两个方法代替;这两个方法是getter和setter，这两个方法定义的叫存取器属性

    程序查询存取器属性时，即调用getter方法时返回就是表达式的值 

    程序要赋值的时候就是将赋给的值当做参数传入setter方法

    存取器属性不具有可写性（writable attribute）如果属性同时具有getter和setter则是一个读写属性，如果只有setter属性则读取时候返回undefined

 定义存取器属性 例子：
    ```

    var o = {
        //普通的数据属性
        data_prop:value,
        
        //存取器属性,成对出现 属性名称一样的
        get accessor_prop(){},
        set accessor_prop(){}

    }

    ```