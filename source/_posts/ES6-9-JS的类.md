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