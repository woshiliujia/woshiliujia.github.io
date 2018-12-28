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
```
概括：如果要查找一个对象的某个属性 先是查找此对象本身是否有这个属性，如果没有
继续查找此对象的原型对象，如果原型对象没有找到并且此原型对象也有原型则继续查找
直到查到某个原型对象的原型为null; 这就构成了一个 “链” 通过这个实现属性继承
属性赋值
```

 1. 属性赋值首先检查原型链判断是否允许赋值，如果此对象继承一个只读属性，那么赋值操作是不被允许的
 2. 如果允许赋值，也只是在此对象上修改或者新增属性，而不会去修改原型链;

### 属性访问错误
    如果对象存在，访问的一个属性此对象自身和继承属性均未找到此属性。返回undefined。

    如果对象不存在，那么查找这个不存在对象的属性就会报错

    设置属性失败的场景
        1. o中的p属性是只读的,不能给只读属性赋值（defineProperty()方法,可以对可配置的只读属性修改）
        2. o中的属性p是继承来的,并且p是只读的，不能通过同名自有属性覆盖只读的继承属性
        3. 如果对象o的可扩展属性（extensible attribute）为false 并且o中没有属性p，则也不能添加属性p。

## 删除属性

    delete 运算符可以删除对象的属性.但只是断开属性和宿主对象的联系,不会操作属性中的属性（即，如果这个属性是一个对象，并不会对这个对象有操作）

    delete 运算符只能删除自有属性，不能删除继承属性

    delete 表达式删除成功或者没有副作用时，返回true 无意义的删除也返回true

    delete 也不能删除可配置属性为false的对象 某些内置对象的属性是不可配置的，通过变量声明和和函数创建的全局对象的属性（比如通过 var a = 1;delete this.a ;返回false）严格模式下报错

## 检测属性

判断某个属性是否存在某个对象中可以使用：in运算符、hasOwnProperty()和propetyIsEnumerable() 方法来完成,或者通过属性查询

    in运算符左侧是属性名(必须是字符串,否则会被当成一个变量)，右侧是对象.如果对象的自有属性或者继承属性中包含此属性则返回true

    hasOwnProperty()方法用来检测给定的名字是否是对象的自有属性，如果是继承属性返回false

    propeIsEnumerable()方法是hasOwnProperty()的升级版本，只有是自有属性，且这个属性的可枚举性（enumerable attribute）为true 才会返回true

    !== 符号有种情况不能使用，当属性存在但值为undefined和不存在的属性可以使用 in运算符


## 枚举属性
    1. for/in 循环遍历 只能遍历可枚举的属性
    2. Object.keys(); 返回一个对象中属性名称组成的数组 （自有，且可枚举的属性）
    3. Object.getOwnPropetyNames();返回所有自有属性名称包括不可枚举的


## 属性getter和setter

    es5中属性值可以是一或者两个方法代替;这两个方法是getter和setter，这两个方法定义的叫存取器属性

    程序查询存取器属性时，即调用getter方法时返回就是表达式的值 

    程序要赋值的时候就是将赋给的值当做参数传入setter方法

    存取器属性不具有可写性（writable attribute）如果属性同时具有getter和setter则是一个读写属性，如果只有setter属性则读取时候返回undefined

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

## 属性的特性
        属性的特性包含 可写，可枚举，可配置，名字，值的特性。
        即（value,writable,enumerable,configurable）
        es3中无法设置这些，es5中可以修改
        存取器属性不具有值和可写性，因此存取器的四个特性是 读取 get 写入 set 可枚举，可配置性 即（get,set,enumerable,configurable）

通过调用Object.getOwnPropertyDescriptor() 可以获得某个对象的特定描述符
对于继承属性和不存在的属性返回undefined,只能获取自有属性的描述符

### 设置属性的特性，或者让新建属性具有，某种特性 需要调用Object.definePrototypeOf()
传入修改的对象、要创建的或者修改的属性名称以及描述符对象

例：
```
var o = {};
Object.definePrototypeOf(o,'x',{
    value:1,
    writable:true,
    enumerable:false,
    configurable:true
})
```
    1. 如果对象是不可扩展的,可以编辑已有的自有属性,但是不能添加新的属性
    2. 如果属性是不可配置的
        a. 不能从false修改为true (可以true修改为false)
        b. 不能修改属性的可枚举性
        c. 存取器属性和数据属性也不能互转

## getter 和 setter老式api
没看

## 对象的三个属性

### 原型属性
    对象的原型属性使用来继承的，即可以继承其他对象里面的属性和方法 （通俗来讲就是叫爸爸，有了原型属性你才能叫别人爸爸，获得你爸爸的属性和方法。不然别人不给你）



```
这里有个问题以后目前还不是很理解透彻：以对象字面量创建的对象为什么给原型赋值无用

可能是因为以对象字面量形式创建的对象会将原型链切断吗？
```


### 类属性 （class attribute）

    对象的类属性是一个字符串，可通过默认的toString()方法访问到
    因为很对对象继承的toString()被重写了因此需要调用Function.call()方法
    Object.prototype.toString.call(o) //o 是传入的对象

### 可扩展性
    对象的可扩展性用以表示是否可以给对象添加新的属性，所有的内置对象和自定义对象都是显式
    可扩展的。宿主对象的可扩展性是由JavaScript引擎决定的


查询是否可扩展：Object.isExtensible()传入要查询的对象

将对象转换为不可扩展: Object.preventExtensions() 传入转换对象 
```
ps:
    1.一旦将对象设置为不可扩展后，将不可逆
    2.preventExtensions()只会影响对象本身的可扩展性，并不能影响到原型对象
      如果给不可扩展对象的原型添加属性，不可扩展对象一样可以继承这些新属性
```

更暴力的方式 seal  封闭

```
    Object.seal();
        1.将对象设置为不可扩展，还能将新对象的所有自有属性都设置为不可配置
        2.已有的属性也不能删除和配置 可写性可以设置 
        3.这个操作依然不可逆

检测对象是否封闭 Object.isSealed();
```


更更暴力的方式 冻结（freeze）

```

    Object.freeze();
    1.不可扩展，自有属性不可配置
    2.所有数据属性都会被设置为只读（存取器属性除外）
    3.这个操作依然不可逆

检测对象是否被冻结 Object.isFrozen()
```

## 序列化对象
对象序列化是指将对象的状态转换为字符串,也可以将字符串还原成对象
方法
```
JSON.stringify();对象转字符串，传入对象

JSON.parse();字符串转对象，传入字符串
```

## 对象方法
    1.toString() 
        此方法没有参数，返回一个调用这个方法的字符串，
        在需要将字符串转换成对象时候都需要调用
        不过默认的toString()方法返回的是[object Object] 一般都会自定义类，重写

    2.toLocalString()
        此方法返回一个表示这个对象的本地化字符串,跟上面差不多

    3. toJSON()
        JSON.stringify() 会调用此方法

    4.valueOf()
        ...