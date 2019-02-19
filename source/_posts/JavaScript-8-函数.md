---
title: JavaScript-8.函数
date: 2019-01-08 16:05:13
tags: JavaScript
categories: JavaScript
---
函数：是一段Javascript代码，定义一次后可以被多次调用。
函数调用时拥有一个值—-本次调用的上下文,即this的值

## this的指向和闭包
如果函数挂载在一个对象上，作为对象的一个属性，就称这个函数为对象的方法
当通过这个对象调用这个函数时,这个对象就是此次调用的上下文,也就是this指向这个对象

用于初始化一个新创建的对象的函数称为构造函数（constructor） 

闭包：javascript函数可以嵌套在其他函数中，这样就可以访问它们被定义时所处的作用域中的任何变量，这就构成了闭包。

## 函数的定义
#### 函数语句式定义
变量声明被提前，函数名是必须的

#### 函数表达式定义
函数名可选
如果一个函数定义表达式包含名称,函数的局部作用域将包含一个绑定到函数对象的名称.
实际上函数名称将成为函数内部的一个局部变量
函数表达式通常适用只用到一次的场景。
无法在定义前调用

return语句会导致函数停止运行，并返回它的表达式给这个函数的调用者,没有return 或者没有相关表达式则返回undefined给调用者
没有返回值的函数，有时候称为过程

## 函数调用
    1.作为函数
    2.作为方法
    3.作为构造函数
    4.通过他们的call()和apply()方法间接调用

#### 作为函数调用
普通的函数调用基本是一个函数对象加上左圆括号加上参数加上右圆括号组成的像"getAllList(x,y)"

函数调用的返回值：如果因为解释器到达结尾，返回undefined。如果因为解释器执行到return 则返回return后的语句。如果return语句没有值返回undefined

在es3和非严格模式的es5中以函数调用的函数this指向全局对象，严格模式this是undefined
例：判断当前是否是严格模式
```
var static = (function(){return !this;}());
//严格模式下this的返回值是undefined所以函数返回true
```

#### 作为方法调用
方法调用的参数和返回值的处理完全等同于函数调用。他们之间的区别就是调用的上下文不同即“this”的指向不同
属性访问表达式由两个部分组成“对象名称+属性名称” 这个对象成了调用的上下文，即“this”指向这个对象
例：
```
var a = {
    x:1,
    y:2,
    c:function(){
        this.z = this.x+this.y;
    }
}
a.c();//先执行c()方法
a.z;//输入3
ps:方括号的方式也可以访问 a["c"]()
```
任何函数只要当做方法调用都会传入一个隐式的实参，这个实参是一个对象，方法调用的母体就是这个对象

方法链：当方法的返回值是一个对象的时候，这个对象还可以调用它自己的方法，这种调用序列通称为链后者级联。
当方法不需要返回值的时候，最好直接返回“this”因为当前放回调用他的对象就可以继续在后面调用其他方法，这就实现了“链式调用”（此处的方法调用不同意构造函数的链式调用）

##### 使用this的注意事项
1.this并不是一个变量，也不是一个属性名它是关键字，javascript不允许给关键字赋值
2.this没有作用域的限制，嵌套函数不会从调用他的函数中继承this，如果嵌套函数作为方法调用，this指向调用的对象。如果嵌套函数作为函数调用，这个函数在非严格模式下指向全局对象，严格模式返回undefined。
嵌套函数中的函数调用需访问外部函数的this值需要提前将this保存在一个变量，这个变量和函数同在一个作用域内。
例：
```
var o = {
    a:1,
    b:2,
    c:function(){
        var self = this;//将this保存在self变量中
        console.log(this);//对象o中的方法c()。this指向调用他的对象o
        console.log(this === o);//返回true

        function test(){ //嵌套函数
            console.log(self === o);//返回true self是外部函数的this值
            console.log(this === o);//返回false,this指向全局对象，或者undefined
        }
    }
}
o.c(); //调用o中的c方法
```
#### 构造函数调用
构造函数：如果函数或者方法调用之前带有关键字 new,它就构成了构造函数。
如果构造函数调用含有实参，先计算实参再传入函数内
如果没有参数可以省略后面的圆括号
```
var o = new Object();
var o = new Object;
//以上代码是等价的
```
构造函数调用创建的新的空对象，这个对象继承自构造函数的prototype属性 。构造函数试图初始化这个新创建的对象，并将这个对象作为调用的上下文，即“this”指向这个新创建的对象
```
var o = new Object;
o.m = function(){
    console.log(this === o);
}
new o.m();//输出是false; this指向这个新创建的对象
```
PS:构造函数通常没有不使用return关键字，他们通常初始化新对象，执行完毕时显示返回这个对象
即：
```
function O(){};
var a = new O();
//以上代码创建个O函数，使用构造函数初始化a这个新的对象。new O()这个返回的就是O函数本身;
```
如果构造函数使用了return返回了一个对象，那么调用表达式的值就是返回的这个对象。
如果使用了return但没有指定返回对象，则忽略返回值，使用这个新对象作为调用结果

#### 间接调用
JavaScript中函数也是对象，所以函数也可以跟对象一样有自己的方法。
其中call()和apply()方法可以间接的调用函数（即通过这2个方法对象可以调用其他函数，不是这个对象的方法也是可以调用的）
这个2个方法可以显示的指定，调用所需的this值
区别：call()方法传递的参数实参列表，即使一个个的。apply()方法传递的参数数组，即参数放到一个数组中传递。


## 函数的形参和实参
实参：调用函数时候所传递的参数
形参：创建函数是所需的参数

### 可选形参
1.当调用函数时候传入的实参比创建函数的形参少的时候，按顺序剩下的形参都将为undefined
2.给省略未传的参数赋个默认值
```
function a(o,a){
    //o参数是一个必传的对象参数
    //a参数是个非必传的数组
    a = a || []
    return a;
}
```

### 可边长的实参列表：实参对象 arguments（这是一个类数组对象，不是真正的数组）
标识符arguments指向实参对象的引用，这是一个类数组对象。arguments的length属性就表示传入的参数个数。
例：运用实参对象找到传入参数的最大值
```
function getMax(/*...*/){
    var max = Number.NEGATIVE_INFINITY;//表示JavaScript所能表示比最小负数还小的值
    for(var i = 0;i<arguments.length;i++){//遍历传入的参数
        if(arguments[i]>max) max = arguments[i];
    }
    return max;//返回最大值
}
//如果没有传入的话这个函数将返回“-Infinity”表示负无穷
//这个函数也叫不定实参函数，因为可以接受任意个参数
```
在非严格模式下，可以通过arguments的类数组对象去修改传入实参的的值
ECMAScript 5中移除了arguments这个特性

#### callee和caller属性
实参对象除了数组元素，还定义了callee和caller属性。ES5严格模式中对这2个属性的读写都会报类型错误.
callee属性指代当前正在执行的函数。
caller是非标准的，大多数浏览器实现了这个属性，指代调用当前正在执行函数的函数，通过caller属性可以访问调用栈
例：递归函数
```
function a(x){//a函数传入参数为x
    if(a == 1)return 1;//如果x是1，则直接返回1;
    return x*arguments.callee(x-1);//arguments的callee属性指代正在执行的函数即a()
}
```
### 将对象属性用做实参
无需知道函数形参的顺序，当函数有很多个形参时候，将参数放到一个对象中去传递无需记得位置
例：复制一个数组到另个数组中
```
//普通写法
function arraycopy(a,b,start,end){//从a数组复制到b数组,start和end是开始结束位置
    //...逻辑代码
}

//不需要记得顺序写法
function easycopy(args){//args是传入的对象参数
    arraycopy(
        args.a,
        args.b,
        args.start || 0,
        args.end || 0
    )
}
//调用easycopy()
var a = [1,2,3,4,5];var b = [];//从a数组复制到b
easycopy({a:a,b:b});//省略开始和结束位置，因为有默认值
```
### 实参类型
...省略

## 作为值的函数
...省略
## 作为命名空间的函数
```
(function(){}());//匿名函数内部定义的变量不会影响到外部
```
## 闭包
闭包：函数对象可以通过作用域链关联起来，函数内部的变量都可以保存在函数作用域内，这种特性成为闭包。
#### PS:函数定义时候的作用域链，在执行时候依然有效
```
var a = 0;//定义全局变量
function o(){//定义o函数
    var a = 1;//定义局部变量
    function f(){定义一个函数并返回这个局部变量
        return a;
    }
    return f;//这个函数返回上面的函数
}
o()();//输出1，因为函数作用域链是函数创建的时候产生的，f()定义在o的作用域内部，a一定是局部变量，并被作用域链一直保存下来，所以输出的依然是o中的a;
```
防止共享变量，因为会共享其他闭包
```
function a(){//定义函数
    var b = [];//定义数组存储函数
    for(var i = 0;i<10;i++){//循环10次创建10个闭包函数
        b[i] = function(){return i};
    }
    //变量i在循环结束时候已经是10了
    return b;
}
var b = a();
b[5]();//因为b[5]这个函数访问a()里面的变量i全部是10 所以b[5]()返回10
```
注意：this是JavaScript的关键字不是变量，所有在嵌套函数内部需要调用外部的this值需要将this保存至一个变量中，如var self = this;
arguments也是一样的道理 var outarguments = arguments;

## 函数属性、方法和构造函数
JavaScript中函数是值，对其使用typeof运算符返回‘function’，函数也是特殊的对象，拥有属性和方法。
### length属性
arguments.length表示函数所传入的实参个数
函数本身的length属性表示函数其他传入的实参数量。
例：当传入的实参和定义的形参不等的时候抛出错误
```
function check(arg){
    var a = arg.length;//定义的实参数量
    var b = arg.callee.length;
    //arguments.callee表示正在执行的函数arguments.callee.length表示传入的实参个数
    if(a!==b){
        throw Error('传入参数不对等')
    }
}
function test(x,y,z){
    check(arguments);//传入arguments到check函数中
}
test(1,2);//报错。传入参数不够三个
```
### prototype属性
函数也是对象，所以函数也有原型属性，每一个函数也都包含不同的原型对象。
将函数用作构造函数的时候，新创建的对象会从原型对象上继承属性。

### call( )方法和apply( )方法
通俗理解法：我要过一条河，你家有一个梯子能够搭在河上过去，现在我不想自己去造梯子太麻烦了，所有我找你家借这个梯子，但我不认识你家call()和apply()认识，我就找他俩去问你借。

书面话：call()和apply()方法第一个参数是调用函数的母对象，就跟对象的方法一样第一个参数是对象。有那么点绕
例：
```
var a = {"0":"a","1":"b","2":"c",length:3}
Array.prototype.join.call(a,'+');
//a对象并没有join方法，所以需要一座桥梁来让a可以使用join方法,也就是用数组方法去调用对象a
```
例：想要以o对象的方法来调用函数f(),即现在f()方法相当于绑定在o上了
```
f.call(o)
f.apply(o)
//以上方法和下方的等价
o.m = f;//f方法存储为o对象的方法
o.m();//执行这个o对象的新方法
delete o.m;//执行完再删除，就跟借完梯子送回去一个道理
```     

## bind( )方法
作用：将函数绑定至某个对象。当函数f()上调用bind()方法并传入个对象o作为参数，这个方法将返回一个新的函数,这个新的函数将会当做绑定对象的方法来使用
例：
```
function f(y){return this.x + y;}//新建一个函数
var o = {x:1};//新建一个对象
var a= f.bind(o);//将函数f绑定至o这个对象
a(2);//这个函数相当于现在是o对象的一个方法  
```

## 总结call()apply()和bind()方法
都是将一个方法绑定到对象上，this指向绑定的这个对象
都是方法在前，绑定的对象在后
bind方法会生成一个新的函数，可以直接函数调用，传递的参数和老的函数一样，需要啥传递啥

例：兼容的bind()方法实现
```
function bind(f,o){//传入的2个参数分别为绑定的方法和对象
    if(f.bind) return f.bind(o);//如果存在这个方法直接使用
    else return function(){//不存在bind方法返回一个函数
        //这个函数使用间接调用实现将方法绑定到对象,传入arguments因为下方调用这个方法时候会传入参数进去，arguments直接指向这个函数对象,将用传入的参数一并给到新创建函数
        return f.apply(o,arguments);
    }
}
var n = bind(f,o);//bind()方法返回的是新的函数，所以下方还需要调用
n(1);//调用这个新的函数，并传入参数
```
函数柯里化:即将多参数的函数变成单参数的函数
例：柯里化函数
```
var sum = function(x,y){return x+y};//这是计算和的函数
var succ = sum.bind(null,10);将函数this指向null，并将第一个参数赋值为10
succ(1);//返回11,这时候新函数只需要1个参数了

//另一种
var sum = function(y,z){return this.x+y+z};//依旧计算和函数
var succ = sum.bind({x:1},1);//将sum方法绑定到对象上，这时候sum中的this指向这个对象并依旧给个参数将第一个赋值为1
sucd(2);//y参数被赋值为1，this指向{x:1}这个对象，所以返回4
```
ES3版本的Function.bind()方法
```
if(!Function.prototype.bind){//判断浏览器是否支持这个方法
    //没有的话给函数原型添加这个方法
    Function.prototype.bind = function(){
        //这个this指向Function.prototype即其他调用bind方法的函数指向保存起来，传入的参数也保存取来
        var self = this,boundArgs = arguments;
        //es5的bind()方法返回的是新函数,因此添加的方法依旧返回一个新的函数
        return function(){
            //将除了第一个传入参数,剩下的参数都保存给args；i变量只是为了遍历方便
            var args = [],i;
            //这个循环为了拿到调用这个方法时候传入的参数
            for(i = 1;i<boundArgs.length;i++) args.push(boundArgs[i]);
            //这个循环为了拿到返回对象被调用时的参数，即当前这个函数的参数
            for(i = 0;i<arguments.length;i++) args.push(arguments[i]);
            //以上两个循环相当于es5bind方法中第一次传递参数，和第二次传递参数并不会被赋值

            //self指向调用函数，o表示调用对象，args表示用户给的参数
            return self.apply(o,args);
        }
    }
}

function f(x,y){return x+y;}
//这个a保存的是上面Function.prototype里面返回的函数，参数是上面第一个for循环得来的
var a = f.bind(null,1);
//调用这个新函数传入的参数是第二个for循环得来的
a(1);
```
模拟的bind()和真实的bind()方法区别
1.真正bind()方法返回的是一个函数对象，这个函数对象的length属性是绑定函数的形参减去绑定实参的个数(length不能小于0)
2.真正bind()方法可以顺带用作构造函数，只是this指向不能改变了,原始的函数就会以构造函数方式来调用。
...等等(这里后期可以回过头再看一次，两者之间区别未做深入了解)

### toString()方法
和对象一样，函数也有toString()方法，大多数函数返回函数的完整源码，内置函数返回"[native code]"

### Function()构造函数
函数除了使用定义语句还是函数直接量还有使用Function()构造函数来定义
例：
```
var f = new Function("x","y","return x+y;")

var f = function(x,y){return x+y;};
以上2种方式几乎等价
```
PS:以Function()构造函数创建的函数总是最顶层函数，嵌套在另一个函数中将不会访问另个函数中的作用域（很少用到）

### 可调用的对象
所有函数都是可调用的，但所有可调用的对象不一定是函数
Window.alert();Document.getElementById()  IE8之前的版本
RegExp对象,这个是可以调用的对象。不能过分依赖

## 函数式编程 ☆☆☆☆☆
JavaScript并非函数式编程语言，但可以像操作对象那样来操作函数
### 使用函数处理数组

···此章节很重要略过，看的有点麻木了！！！