---
title: TypeScript-学习笔记
date: 2019-08-01 15:33:52
tags: TypeScript
categories: TypeScript
---

## 接口 interface
作用: 为类型命名定义契约

如何使用
```
interface Info{
  name:string;
}

function userInfo(obj:Info){
  console.log(obj.name)
}

let myObj = {name:'zhangsan',age:20}
userInfo(myObj)
```
### 可选属性
```
interface Info{
  name?:string;
  age?:number;
}
function students(config:Info):{name:string;height:number;}{
  //逻辑
}
```

### 只读属性
一些对象的属性，只能在刚创建的时候修改他的值，可以直接在属性前面加入 readonly 来指定只读
```
  interface Info{
    readonly sex:number;//性别创建后不可修改
  }

//数组不可修改
let a:number[] = [1,2,3,4];
let ro:ReadonlyArray<number> = a;

```