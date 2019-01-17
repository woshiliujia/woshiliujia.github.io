---
title: Javascript-9.类和模块
date: 2019-01-17 14:12:46
tags: JavaScript
categories: JavaScript
---
## 类和原型
类的所有实例对象都从同一个原型对象上继承属性。因此原型对象时类的核心。

例：一个简单的JavaScript类  使用的是工厂方法
```
function inherit(p){//此方法传入个对象，并返回一个新对象，这个新对象继承自传入的对象
    if(p == null) throw TypeError();
    if(Object.create) return Object.create(p);
    var t = typeof p;
    if(t !== 'function' && t !== 'object') throw TypeError();
    function f(){};
    f.prototype = p;
    return new f();
}

//实现一个range.js 用于表示值的范围的类  下面这种方法并不常用
function range(from,to){
    //使用inherit()方法生成新的对象，并且这个对象继承自己定义的原型对象
    var r = inherit(range.methods);

    //存储新的对象开始和结束位置
    r.from = from;
    r.to = to;
    //返回这个新的对象
    return r;
}

//这个原型对象自己的方法,这些方法会被每个实例所继承
range.methods = {
    includes:function(x){
        return this.from <= x && x <= this.to;
    },
    toString:function(){
        return "范围从"+this.from+"到"+this.to;
    }
}

//使用新的对象实现的例子
var r = range(1,3); //创建一个范围对象
r.includes(2);//调用这个新对象并传入参数
r.toString();
```

## 类和构造函数
构造函数是用来初始化新创建的对象使用的，使用new调用构造函数会创建一个新的函数，并且这个新的函数调用的上下文就是这个新创建的对象，即this指向这个新创建的函数。
调用构造函数的一个重要特征就是构造函数的原型会直接被用作新创建的函数的原型即prototype属性
所以所有通过构造函数创建的对象都继承自一个对象,因此他们都是同一个类的原型
例：对上面方法的优化
```
//创建构造函数
function Range(from,to){
    //new关键字调用构造函数里面的调用上下文就是当前对象，this指向这个新创建的对象
    this.from = from;
    this.to = to;
}
//所有范围对象都继承这个对象
Range.prototype = {
    includes:function(x){
        return this.from <= x && x<= this.to;
    },
    toString:function(){
        return this.from+','+this.to;
    }
}
//之前我的错误理解和错误写法
//var range = new Range(); //此处已经表示创建并初始化对象了
//var r = range(2,3);r.include(2);不知道我这个搞的什么玩意

//调用这个构造函数，并将参数传递进去
var r = new Range(1,3);
r.includes(2);
r.toString();
```
PS:
1. 构造函数约定首字母大写
2. 构造函数通过new关键字调用的，并且this指向这个新创建的函数
3. 构造函数不必返回新的对象，通过new关键字调用构造函数本来就是个新的
4. 原型对象必须是prototype
