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
1. 定义一个构造函数，并设置初始化对象的实例属性。
2. 给构造函数的原型属性prototype对象定义实例的方法。
3. 给构造函数定义类字段和类属性。

以上三步可以封装成一个方法
```
//用于定义简单类的一个方法
//以下方法属于8-3的例子，传入第一个参数为复制后的对象，其他参数需要为对象
var extend = (function(){//使用匿名函数返回一个函数
    //for in循环判断浏览器兼容性
    for(var p in {toString:null}){//可以循环出不可枚举属性则返回一个函数  非ie9以下
        return function(o){//此处的o是个对象，传入的其他参数也需要是个对象，如果不是无法赋值到o
            for(var i = 1;i<arguments.length;i++){//循环传入的实参
                var source = arguments[i];//把实参赋值给变量source
                for(var prop in source) o[prop] = source[prop];//因为传入的其他参数是对象，所以当做属性赋值到第一个参数
            }
            return o;
        }
    };
    return function(o){//如何没有循环出不可枚举属性 //ie9以下进行的特殊处理
        for(var i = 1;i<arguments.length;i++){//循环可枚举的属性
            var source = arguments[i];
            for(var prop in source) o[prop] = source[prop];
            for(var j = 0;j<protoprops.length;j++){//不可枚举的属性在下方数组中，遍历下方数组
                prop = protoprops[j];
                
                //这段代码使用hasOwnProperty方法判断传入对象是否修改过不克枚举属性，修改则赋值给第一个参数。
                if(source.hasOwnProperty(prop)) o[prop] = source[prop];//这段代码可写成下面格式，没验证  
                //if(source.hasOwnProperty(protoprops[j])) o[protoprops[j]] = source[protoprops[j]];//
            }
        }
        return o;//返回这个对象
    }
    var protoprops = ["toString","valueOf","constructor","hasOwnProperty","isPrototypeOf","prototypeIsEnumerable","toLocalString"];
}());//使用匿名函数，不会影响外部空间的变量。

//以下是个简单的类函数
function defineClass(
                    constructor,//用以设置实例属性的函数
                    methods,//添加实例方法
                    statics//添加类的属性和字段
                    )
{
    if(methods) extend(constructor.prototype,methods);//给extend()方法传入构造函数的原型对象，将需要添加的实例方法选为第二个参数
    if(statics) extend(constructor,methods);//构造函数的属性字段直接给其本身
    return constructor;//返回被赋值的构造函数
}

//实现range类的另一种方式
var SimpleRange = defineClass(function(f,t){this.f = f;this.t = t},{
    includes:function(x){return this.f<=x && x<= this.to;},
    toString:function(){return this.f+'到'+this.t;}    
},
//以下参数我并不知道作用和用法是什么，等看完后面部分回过头再看下
//以下参数回过头发现是我的参数传递错了，下面属于类本身的方法，不被实例所继承，可以使用函数调用方式
{
    upto:function(t){return new SimpleRange(0,t)}
});

//调用以上定义的SimpleRange类
var r = new SimpleRange(1,5);

//SimepleRange类本身的方法字段
var r = SimpleRange.upto(2);//这个本身的类方法返回一个该本身的构造函数，所以继续调用他的类方法
```
//例：表示负数的类 Complex.js
```
略...
```
命名写法：以大写字母命名的成员不能修改，以下划线命名的前缀类的外部是不可见的

## 类的扩充
JavaScript中基于原型的继承机制是动态的，对象从它的原型继承属性，如何对象创建之后，原型的属性发生变化，也会影响继承这个原型的所有实例对象。这意味着可以给原型对象添加新的方法来扩充类

例：多次调用一个函数输出3次“hello”
```
var n = 3;
n.times(function(n){console.log(n+'hello')});//调用下方给原型新添加的方法
//下面添加原型方法的必须在上面调用之前添加，否则会报错
Number.prototype.times = function(f,context){
//f参数是需要调用的函数，context感觉可有可无，去除这个context参数用this取代一样可行
    var n = Number(this);
    for(var i = 0;i<n;i++) f.call(context,i);//此处context只是指代了this，或者为null都可以,i为传入f函数的参数
}

//去除字符串前后空格trim()方法
String.prototype.trim = String.prototype.trim || function(){//判断是否有这个方法
    if(!this) return this;//排除空字符串
    return this.replace(/^\s+|\s+$/g,'');//使用正则匹配
}
```
不推荐给Object.prototype添加方法，因为在es5之前无法给这些属性设置为不可枚举的属性，会被for/in遍历到 es5中有着更好的Object.defineProperty()。但并不是所有的宿主环境都支持这个
在很多浏览器中可以给HTMLElement.prototype添加方法 ie中不支持

## 类和类型
JavaScript有7中的数据类型，null,undefined,布尔，数字，字符串，对象，函数，可以使用typeof来得出这些值类型，但是类的类型都属于'Object';

### instanceof运算符
instanceof左边是待检测类的对象，右边是定义类的构造函数
如：o继承自，c.prototype 则 o instanceof c 返回true,如果o继承的对象是继承另一个对象的，另一个对象也是继承c.prototype 结果依旧是true
构造函数只是类的公共标识，唯一标识是原型，instanceof实际检测的是继承关系

isPrototypeOf()方法可以检测对象的原型链上是否有某个特定的原型对象。
range.methods.isPrototypeOf(r);//range.methods是原型对象

以上两个方法的缺点：无法通过对象来获得类名，只能检测对象是否属于指定的类

### constructor属性
因为构造函数是类的公共标识，所以可以直接使用对象的constructor属性
```
function typeAndValue(x){
    if(x == null) return "";//null和undefined没有构造函数
    switch (x.constructor){
        case Number: return "Number:"+x;
        case String: return "String:"+x;
        //...等等原型类型
        case Date: return "Date:"+x;//处理内置类型
        case Complex: return "Complex:"+x;处理自定类型
    }
}
```
缺点:并非所有对象都包含constructor属性，有的原型被重新赋值后会丢失constructor属性，需要显示赋值
一个窗口的多个子页面，相同的而又独立的构造函数不是通过一个构造函数

### 构造函数的名称
通过构造函数的名称去作为类的标识符，而不是通过构造函数本身
下面例子使用typeof 处理原始值和函数，对于对象要么返回class属性值，要么返回构造函数名字
```
/**
 *以字符串形式返回o的类型
 *如果o是null返回'null'，o是NaN返回'nan'
 *如果typeof返回值不是‘object’,则返回这个值
 *如果o的类不是“Object”则返回这个值
 *如果o包含构造函，并且这个构造函数有名字，则返回这个名字
 *其他情况一律返回'Object'
 **/
 function type(o){
     var t,c,n;//type,class,name
     if(o == null) return 'null'
     //NaN自身和自身不相等
     if(o !== o) return 'nan';//NaN !== NaN
     //type可以识别原始类型的值和函数
     if((t = typeof o)&& !== 'object') return t; 
     //内置对象，返回对象类名
     if(c = classof(o)&& !== 'Object') return c;
     //如果构造函数的名字存在
     if(o.constructor && typeof o.constructor === 'function'&&(n = o.constructor.getName())) return n;
     //其他情况一律返回'Object'
     return 'Object';
 }

 function classof(o){//返回对象的类
    return Object.prototype.toString.call(o).slice(8,-1);
 }
 Function.prototype.getName = function(){//获取类名，即函数name
     if("name" in this) return this.name;
     return this.name = this.toString().match(/function\s*([^(]*)\(/)[1];
 }
```
这种做法依然不是所有对象都有constructor属性，如果是不带名字的函数定义表达式，也不会有函数名
getName()方法会返回空字符串
```
var A = function(){}//这个构造函数没名字
var B = function B(){}//这个构造函数有名字B
```
### 鸭式辩型
不注重类型，关注能干的事情
quacks()方法
略...

## JavaScript中的面向对象技术
```
//Set.js 值的任意集合
function Set(){//这是一个构造函数
    this.value = {};//新建一个集合用于存放传入的参数
    this.n = 0;//集合中值的个数
    this.add.apply(this,arguments);//把所有参数加入集合中,意思是当调用这个类时传入的参数已经加入到集合中了。
}
Set.prototype.add = function(){//给这个类添加一个参数加入到集合中的方法
    for(var i = 0;i<arguments.length;i++){//遍历所有传入参数
        var val = arguments[i];//保存每个参数
        var str = Set._v2s(val);//通过私有方法将传入的参数以一定规则转为字符串
        this.value[str] = val;//将转化后的字符串作为属性将值保存在集合
        this.n++;//没加入一个参数集合中值的个数自增
    }
    return this;//以便于链式调用
}

Set.prototype.remove = function(){//添加删除方法
    for(var i = 0;i<arguments.length;i++){//遍历传入的参数
        var str = Set._v2s(arguments[i]);//将传入参数保存
        if(this.value.hasOwnProperty(str)){//判断传入参数是否属于集合对象中的  hasOwnProperty()方法主要是判断属性是否属于对象的自有属性
            delete this.value[str];//找到的话直接删除
            this.n--;//集合值的个数自减
        }
    }
    return this;//返回这个对象，支持链式调用
}

Set.prototype.contains = function(val){//判断传入参数是否属于这个集合
    return this.value.hasOwnProperty(Set._v2s(val))
}
Set.prototype.size = function(){//集合大小
    return this.n;
}

Set.prototype.foreach = function(f,context){//遍历这个集合并且在指定上下文中对每个值调用f
    for(var s in this.value){
        if(this.value.hasOwnProperty){ //过滤继承属性
            f.call(context,this.value[s]);
    }
}
Set._v2s = function(val){//Set.js 私有的方法，实例不共享，用以将传入参数以唯一字符串连接，重复的参数会被过滤
    switch (val){
        case undefined: return 'u';
        case null: return 'n';
        case true: return 't';
        case false: return 'f';
        default: switch(typeof val){
            case 'number': return '#'+val;
            case 'string': return '"'+val;
                default:return '@'+objectid(val);
        }
    }
    
    function objectid(o){//此处如果传入参数是个对象的话，会默认给这个对象加个"|**objectid**|"属性
        var prop = "|**objectid**|";
        if(!o.hasOwnProperty(prop)){
            o[prop] = Set._v2s.next++;
        }
        return o[prop];
    }
}
Set._v2s.next = 100;//初始对象的id值
```
### 一个例子；枚举类型
创建一个新的枚举类型
```
//将传入对象作为原型生成新的对象
function inherit(p){
    if(p == null) throw TypeError();
    if(Object.create) return Object.create(p);
    var t = typeof p;
    if(t !== 'function' && t !== 'object') throw TypeError();
    function f(){}
    f.prototype = p;
    return new f();
}

//工厂函数，返回值是一个构造函数
function enumeration(namesToValues){
    
    //定义个构造函数，并且当做返回值（不能使用这个构造函数来创建新的实例，否则会抛出错误）
    //即返回的函数不能再次当做构造函数使用
    var enumeration = function(){throw "Can't Instantiate Enumeration";}

    //设置这个构造函数的原型对象，并添加几个方法
    var proto = enumeration.prototype = {
        constructor:enumeration,
        toString:function(){return this.name;},
        valueOf:function(){return this.value;},
        toJson:function(){return this.name;}
    }
    enumeration.values = [];//存放枚举对象的数组

    for(name in namesToValues){//遍历传入对象的属性，并创建新类型的实例
        var e = inherit(proto); //创建一个代表它的对象
        e.name = name; //给新实例一个名字
        e.value = namesToValues[name];给这个对象一个值
        enumeration[name] = e;//将遍历的属性设置为构造函数的属性
        enumeration.values.push(e);//将这个对象放到枚举对象数组中
    }
    //类方法，用来对类的实例进行迭代
    enumeration.foreach = function(f,c){
        for(var i = 0;i<this.values.length;i++){
            f.call(c,this.values[i]);
        }
    }
    return enumeration;//返回这个构造函数
}
```
例：使用枚举类型表示一个玩牌的类

```
//定义个表示“玩牌”的类
function Card(suit,rank){
    this.suit = suit;//每张牌都有花色
    this.rank = rank;//每张牌都有点数
}

//使用枚举类型定义花色和点数

//4种花色
Card.suit = enumeration({Clubs:1,Diamonds:2,Hearts:3,Spades:4});

//13张牌
Card.Rank = enumeration({
    Two:2,Three:3,Four:4,Five:5,Six:6,Seven:7,Eight:8,Nine:9,Ten:10,
    Jack:11,Queen:12,King:13,Ace:14
})

//描述牌面
Card.prototype.toString = function(){
    return this.rank.toString() + "of" + this.suit.toString();
};

//比较两张牌大小，只是按照点数比较，无关花色
Card.prototype.compareTo = function(that){
    if(this.rank < that.rank) return -1;
    if(this.rank > that.rank) return 1;
    return 0;
}

//按照点数排序，sort方法传入的方法
Card.orderByRank = function(a,b){return a.compareTo(b)};

//先按照花色比较，花色一样再按照点数比较
Card.orderBySuit = function(a,b){
    if(a.suit > b.suit) return 1;
    if(a.suit < b.suit) return -1;
    if(a.rank > b.rank) return 1;
    if(a.rank < b.rank) return -1;
    return 0;
}

//定义一个表示一副扑克牌的类
function Deck(){
    var cards = this.cards = [];//新建一个存放牌的数组

    //enumeration类里面定义了foreach方法，所以可以直接遍历到values，即初始化这个数组获得一副牌
    Card.suit.foreach(function(s){
        Card.Rank.foreach(function(r){
            cards.push(new Card(s,r));//4种花色加上13张牌混合一起52张
        })
    })
}

//定义洗牌的方法
Deck.prototype.shuffle = function(){
    var deck = this.cards,len = deck.length;//将一副扑克牌给到deck，len存放牌数组的长度
    for(var i = len-1;i>0;i--){//遍历数组中的每个元素
        var r = Math.floor(Math.random()*(i+1)),temp;//随机出一个元素
        //现将当前循环到的元素给临时变量temp，再将随机出来的给到当前循环到的，最后将临时变量给随机出来的元素
        temp = deck[i],deck[i] = deck[r],deck[r] = temp;
    }
    return this;//返回这个类，方便链式调用
}

//发牌的方法，返回数组
Deck.prototype.deal = function(n){
    if(this.cards.length < n) throw "Out of cards";
    return this.cards.splice(this.cards.length-n,n);//取出n张牌
}

//创建一副新的扑克牌，洗牌并发牌
var deck = (new Deck()).shuffle();
var hand = deck.deal(13).sort(Card.orderBySuit);
```

### 标准转换方法
```
function extend(o,p){//通过此方法可以将p对象的属性和方法复制到o
    for(prop in p){//遍历p对象的属性
        o[prop] = p[prop];//复制到o对象
    }
    return o;//返回o对象
}
```
可以通过以上方法去设置一个对象的某些属性，如“toString,toLocalString”等

### 比较方法
JavaScript的相等运算符比较对象时，比较的时引用而不是值。所以比较两个对象是否引用自同一个对象，可以使用相等运算符
如果定义一个类，希望比较类的实例，应该定义合适的方法来执行比较
略...

### 方法借用
JavaScript的方法无非是一些简单的函数，赋值给了对象的属性，可以通过对象的属性来调用它。
一个函数可以赋值给2个属性，再通过两个放法来调用。
多个类的方法，可以使用一个单独的函数
```
//方法借用的泛型实现 
var generic = {
    toString:function(){
        var s = '[';

        //函数的名字是非标准的，并不是所有环境中都有，因为需要进行判断
        if(this.constructor && this.constructor.name){
            s += this.constructor.name+': ';
        }

        var n = 0;
        for(var name in this){//遍历循环此对象中的属性

            //跳过继承属性
            if(!this.hasOwnProperty(name)) continue;
            var value = this[name];
            //跳过方法
            if(typeof value === 'function') continue;
            if(n++) s += ',';
            s += name + '=' + value;
        }
        return s+']';
    }
}

//ps:上面泛型实现修改后会在类的实例体现出来
```

### 私有状态
在构造函数内部定义一个函数这个函数返回私有状态

ps：使用闭包来封装状态，一定比不上等价类的运行速度，并且占用更多内存

### 构造函数的重载和工厂方法
初始化对象的方法

## 子类

### 定义子类
```
//定义一个函数来创建简单的子类
//需要传入的参数分别为"父类构造函数、新的子类构造函数、实例方法(复制到原型中)、类属性(复制到构造函数中)"
function defineSubClass(superClass,constructor,methods,statics){
    //使用inherit来生成新的继承自父类的构造函数
    constructor.prototype = inherit(superClass);
    //因为新的构造函数的原型对象被改写，所以需要将构造属性指向新创建的构造函数
    constructor.prototype.constructor = constructor;
    //判断是否传入了实例方法
    if(methods) extend(constructor.prototype,methods);
    //判断是否传入的实例属性
    if(statics) extend(constructor,statics)
    //返回这个新的类
    return constructor;
}

//通过父类的构造函数来创建子类
Function.prototype.extend = function(constructor,methods,statics){
    //此处this指向父类
    return defineSubClass(this,constructor,methods,statics);
}
```

### 构造函数和方法链