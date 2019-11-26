---
title: ES6-9-JS的类
date: 2019-10-17 17:24:51
tags: ES6
categories: ES6
---

ES1-ES5都没有类的声明，ES6中才引入

## ES5中的仿类解构
```
function PersonType(name){
    this.name = name
}

PersonType.prototype.sayName = function(){
    console.log(this.name)
}
```


## ES6基本类的声明
```
class PersonType{ //相当于上方ES5的放生类
    //constructor相当于ES5中的构造器
    constructor(name){
        this.name = name;
    }

    //相当于ES5定义在原型上的方法
    sayName(){
        console.log(this.name);
    }
}

```
为何要使用类的语法
1. 类的声明就相当于let声明 不会被提升。
2. 类声明中的所有代码运行在严格模式中，并且无法退出此模式
3. 类所有的方法都是不可枚举的，而使用es5定义的必须使用Object.defineProperty();去修改
4. 类的所有内部方法都没有 Constructor 因为不能使用new 来进行调用
5. 调用类，即调用构造函数时候必须使用new 否则会抛出错误，es5中直接调用，就相当于调用普通函数
6. 试图在类的内部重写类名会抛错，不能在内部修改 但是在外部可以修改


## 类表达式
类的声明跟函数声明差不都，都分为声明式和表达式
```
class PersonClass {

}

let PersonClass = class {

}
//以上2种方式没有太大区别
```

## 作为一级公民的类
一级公民：在编程中能被当做值来使用的就是一级公民，意味着能够作为参数传递给函数，能作为函数返回值，能用来给变量赋值
JS的函数就是一级公民。
ES6种的类也是一级公民
```
function createObject(classDef){
    return new classDef();
}

let obj = createObject(class{
    sayHi(){
        console.log('hi')
    }
})
obj.sayHi();//hi
```
类的另一种用途是立即调用的构造器
```
let person = new class {
    constructor(name){
        this.name = name;
    }
    sayName(){
        console.log(this.name)
    }
}('liu');
person.sayName();//liu
```

## 访问器属性
自有属性需要在类的构造器中创建即需要定义在constructor中,但是访问器属性还可以定义在原型上

```
class CustomHtml {
    constructor(element){
        this.element = element;
    }
    get html(){
        return this.element.innerHtml;
    }
    set html(value)){
        this.element.innerHtml = value;
    }
}
```

## 需计算的成员名
类方法与类的访问器属性也能使用跟对象字面量一样的访问器属性
```
let methodsName = 'sayName';
class PersonClass {
    constructor(name){
        this.name = name;
    }
    [methodsName](){
        console.log(this.name);
    }
    
    get [methodsName](){
        //....
    }
}
```

## 生成器方法

## 静态成员
类的静态成员是直接在构造器中定义的方法
ES5中
```
function PersonType(name){ //定义构造函数
    this.name = name;
}
PersonType.prototype.sayName(){//定义实例上的方法
    console.log(this.name);
}

PersonType.create = function(name){//定义类的静态方法
    return new PresonType(name);
}

var person = PersonType.create('nico');

```

ES6中定义静态方法
```
class PersonType{
    constructor(name){
        this.name = name;
    }
    
    static create(name){
        return new PersonType(name);
    }
    sayName(){
        console.log(this.name);
    }
}
var person = PersonType.create('nico');
```

PS:类的静态方法在ES6中 前面多个 static 关键字  并且类的静态方法不能通过实例访问，只能通过类自身来直接访问

## 使用派生类进行继承
```
class RectRange{
    constructor(width,height){
        this.width = width;
        this.height = height;
    }
    getArea(){
        return this.width*this.height;
    }
}

class Squre extends RectRange{
    constructor(length){
        
        //此处super指向RectRange类 如果不适用super，super会被自动调用，并且提供所有的参数
        super(length,length);
    }
}
```

```
//默认情况
class Squre extends RectRang{

}

//默认情况super方法会自动调用，因此显示指定传给基类的参数比较好
class Squre extends RectRang{
    constructor(...arg){
        super(...arg);
    }
}
```

PS:
1. 只能在派生类中使用super(),若没有在派生类即extends关键字没有的类中使用会抛错
2. 在构造器中必须访问this之前调用super()
3. 避免调用super()的唯一方法 ，是从类构造器中返回一个对象

### 屏蔽类方法
派生类中的方法总是会屏蔽基类的同名方法。

### 继承静态成员
如果基类包含静态成员，那么这些静态类在派生类中也是可以使用的。
例:
```
class RectRange{
    static create(length,width){
        return new RectRange(length,width)
    }
}

class Squre extends RectRange{
    constructor(length){
        super(length,length)
    }
}

//RectRange 中的静态成员在Squre中调用和基类中一样的
```
### 从表达式中派生类
ES6中派生类最强大的能力或许就是能够从表达式中派生类。只要这个表达式能够返回一个具有Constructor属性已经原型的函数，就可以对其使用extends

### 继承内置对象
