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
var r = new Range(1,3);//此处权威指南的中文版上有错误，英文原本正确的
r.includes(2);
r.toString();
```
PS:
1. 构造函数约定首字母大写
2. 构造函数通过new关键字调用的，并且this指向这个新创建的函数
3. 构造函数不必返回新的对象，通过new关键字调用构造函数本来就是个新的
4. 原型对象必须是prototype
5. 构造函数就是用来构造新对象的，所以调用构造函数直接传递参数，如果将构造函数用做普通函数不会正常工作，因为没有返回值，所以调用得到的结果是undefined

### 构造函数和类的标识
原型对象时类的唯一标识，当且仅当两个对象继承自同一个原型对象时，才属于一个类的实例，而初始化对象状态的构造函数不能当做类的标识。如果两个构造函数的prototype属性指向同一个原型对象，那么这么两个构造函数创建的实例属于同一个类。

构造函数是类的外在表现，构造函数的名字通常用作类名。
例：检查一个实例是否由属于某个类会用instanceof
```
function Range(){};
var r = new Range();
r instanceof Range; //返回true

//继续定义两个构造函数，不过这两个构造函数的原型对象都指向于同一个对象
//定义两个构造函数
function Range1(){}
function Range2(){}
var b = {};//定义一个对象，让上面2个构造函数的原型属性都指向于这个对象
var r1 = Range1();//调用构造函数

//通过instanceof检查实例是否是上面构造函数初始化而来，实际上检查的是Range1.prototype和Range2.prototype
r1 instanceof Range1//返回true
r1 instanceof Range2//返回true
```
### constructor属性
任何JavaScript函数都能用作构造函数，并且调用构造函数需要用一个prototype属性。因此每个JavaScript函数都自动拥有一个prototype属性除Function.bind()方法之外。
并且prototype属性值是一个对象，这个对象包含唯一一个不可枚举的属性constructor。constructor属性值是一个函数对象。
例：
```
var F = function(){};//定义个函数对象F
var p = F.prorotype;//将F的原型对象赋值给p
var c = F.prototype.constructor;//将F的原型对象的constructor属性赋值给c
c === F;//返回true  说明原型对象的constructor属性值指向定义构造函数
```
由于构造函数的原型中预先就有constructor属性，并且这个属性指向于构造函数，由于构造函数是类的公共标识，因此constructor属性为新创建的对象提供了类
例：由于构造函数的实例基础继承的构造函数的原型,因此实例对象直接继承构造函数的原型对象，获得了构造函数constructor属性
```
var F = function(){}
var f = new F();
f.constructor === F; //返回true，constructor属性指代这个类;
```
#### 如果将构造函数的原型对象重新定义后，这个新的原型对象就不会含有constructor属性。因为通过这个构造函数构造出来的对象也不会含有constructor属性，所以可以显示给原型添加一个构造属性

例:给重新赋值后的原型对象显示赋值constructor属性
```
//按照上面Range构造函数的例子
Range.prototype = {
    construcroe:Range,
    ...
}
```
另外一个种方式：不给原来的原型对象重新赋值，直接使用对象字面量的方式以此给原型对象添加方法.如下：
```
Range.prototype.toString = function(){};
```

## JavaScript中的Java式的类继承
JavaScript中定义类的步骤可分为3步。
1. 定义一个构造函数，并设置初始化对象的实例属性。
2. 给构造函数的原型属性prototype对象定义实例的方法。
3. 给构造函数定义类字段和类属性。

以上三步可以封装成一个方法
```
//用于定义简单类的一个方法
var extend = (function(){
    for(var p in {toString:null}){
        return function(o){
            for(var i = 1;i<arguments.length;i++){
                var source = arguments[i];
                for(var prop in source) o[prop] = source[prop];
            }
            return o;
        }
    };
    return function(o){
        for(var i = 1;i<arguments.length;i++){
            
        }
    }
    var protoprops = ["toString","valueOf","constructor","hasOwnProperty","isPrototypeOf","prototypeIsEnumerable","toLocalString"];
}())
```