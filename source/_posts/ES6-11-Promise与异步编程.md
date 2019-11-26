---
title: ES6-Promise与异步编程
date: 2019-10-29 19:51:39
tags: ES6
categories: ES6
---

## 异步编程的背景
1.JS是建立在单线程的引擎上，单线程意味着一次只能执行一段代码。java或者C++就是一次可以执行多段代码的语言，因此需要考虑到多段代码同时访问或者修改状态的问题JS不需要考虑到这样的问题

2.JS引擎同一时间只能执行一段代码，代码会被放置到作业队列中，当前代码执行结束后执行队列就会执行下一个队列。事件循环（Event Loop）是js引擎的一个内部处理线程。会以此从第一段执行到最后一段



### 事件模型
当用户点击一个按钮或者按下键盘，一个事件（event）例如---onclick---就会被触发。该事件可能会对交互做出响应，从而将一个新的作业添加到作业队列的尾部。
事件处理程序代码直到事件发生后才会被执行，此时它会拥有合适的上下文

例：
```
let btn = document.getElementById('my-btn')；
btn.onclick = function(event){
    console.log('my')
}
//当button被点击赋值给onclick的函数就会被添加到作业队列的尾部
```
事件可以很好的用于简单的交互，但是将多个分离的异步调用串联在一起就会很麻烦。

### 回调模式
异步的函数会先执行，但是执行到等待的过程会暂停，让代码继续执行其它的部分，当函数方法拿到回调后会把回调函数放到作业队列的尾部
当使用多个异步串联调用就会比较方便，但是会导致callback hell（回调地狱）
```
methods(err,res){
    if(err) throw err;
    methods2(err,res){
        if(err) throw err;
        methods3(err,res){
            if(err) thow err;
            methods4(err,res){
                //....
            }
        }
    }
}
```
在这种情况下就导致了回调地狱
并且如果同时并行2个异步操作，并且结束后通知。

## Promise
Promise的工作方式与setTimeout相似。Promise的执行器会立即执行。早于之后的所有代码
```
let promise = new Promise((resolve,reject)=>{
    console.log('Promise');//立即执行
    resolve()
})
console.log('hi');

//输出结果为
//Promise
//hi
```

```
let p1 = Promise.resolve('40');
let p2 = Promise.reject('43');

p1.then(res=>{
    console.log(res);//40
    return p2;
}).catch(err=>{
    console.log(err);//43
    return 10;
}).then(res=>{
    console.log(res);//10
})
```
PS：Promise的链式调用是串联的，如果上一个返回的是失败，则必须拿catch去接受然后继续return才能用then接收

## 响应多个Promise
ES6提供了2个监视Promise的方法

### Promise.all()方法
Promise.all()方法接受单个可迭代对象（如数组）作为参数，并且返回一个Promise。可迭代的对象的元素都是Promise，并且在它们都完成后所返回的Promise才会被返回

```
let p1 = Promise.resolve(1);
let p2 = Promise.resolve(2);
let p3 = Promise.resolve(3);
let p4 = Promise.reject(4);

Promise.all([p1,p2,p3]).then(res=>{
    console.log(res);/[1,2,3]
})

Promise.all([p1,p4,p2]).then(res=>{
    //此处代码用不执行，因为p4为已决的失败
    console.log(res)
}).catch(err=>{
    //此处仅仅返回4
    console.log(err);//[4]
})
```
PS:如果有个Promise失败了Promise.all()则直接进入catch 即相当于有一个失败，则就走失败


### Promise.race();方法
此方法和all()方法不同，只要中间有一个可迭代的Promis对象返回race方法就可以做出回应，不需要等待全部执行完成
如果在第二个Promise对象出现reject .then()方法依旧会执行第一次的返回，.catch()方法捕获到第二个reject的返回

## 继承Prpmise
Promise可以当作派生类的基类来使用
```
class myPromise extends Promise{
    success(resolve,reject){
        return this.then(resolve,reject);
    }
    faiil(reject){
        return this.catch(reject);
    }
}
```

## 异步任务运行
