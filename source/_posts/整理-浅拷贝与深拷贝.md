---
title: 浅拷贝与深拷贝
date: 2018-09-27 18:37:54
categories: JavaScript
---

# 场景

除了基本类型跟null,对象之间的赋值，只是将地址指向同一个，而不是真正意义上的拷贝

将一个对象赋值给另外一个对象。

```
var a = [1,2,3];
var b = a;
b.push(4); // b中添加了一个4
alert(a); // a变成了[1,2,3,4]  
```

<!--more-->

自定义对象

```
var obj = {a:10};
var obj2 = obj;
obj2.a = 20; // obj2.a改变了，
alert(obj.a); // 20，obj的a跟着改变  
```

这就是由于对象类型直接赋值，只是将引用指向同一个地址，导致修改了obj会导致obj2也被修改

# 浅拷贝

所以，我们需要封装一个函数，来对对象进行拷贝，通过for in 循环获取基本类型，赋值每一个基本类型，才能真正意义上的复制一个对象

```js
var obj = {a:10};
function copy(obj){
    var newobj = {};
    for ( var attr in obj) {
        newobj[attr] = obj[attr];
    }
    return newobj;
}
var obj2 = copy(obj);
obj2.a = 20;
alert(obj.a); //10  
```

这样就解决了对象赋值的问题。

# 深拷贝

但是这里存在隐患，如果obj中，a的值不是10，而是一个对象，这样就会导致在for in中，将a这个对象的引用赋值为新对象，导致存在对象引用的问题。

```js
var obj = {a:{b:10}};
function copy(obj){
    var newobj = {};
    for ( var attr in obj) {
        newobj[attr] = obj[attr];
    }
    return newobj;
}
var obj2 = copy(obj);
obj2.a.b = 20;
alert(obj.a.b); //20  
```

因此，由于这个copy对象只是对第一层进行拷贝，无法拷贝深层的对象，这个copy为浅拷贝，我们需要通过递归，来拷贝深层的对象。将copy改造成递归即可

```js
var obj = {a:{b:10}};
function deepCopy(obj){
    if(typeof obj != 'object'){
        return obj;
    }
    var newobj = {};
    for ( var attr in obj) {
        newobj[attr] = deepCopy(obj[attr]);
    }
    return newobj;
}
var obj2 = deepCopy(obj);
obj2.a.b = 20;
alert(obj.a.b); //10  
```
原文地址：https://www.cnblogs.com/linhp/p/6085826.html

#### 场景

```js
var a = [1,2,3];
var b = a;
b.push(4); // b中添加了一个4
alert(a); // a变成了[1,2,3,4] 

var obj = {a:10};
var obj2 = obj;
obj2.a = 20; // obj2.a改变了，
alert(obj.a); // 20，obj的a跟着改变 
```
深拷贝和浅拷贝是针对复杂数据类型来说的，浅拷贝只拷贝一层，而深拷贝是层层拷贝。

#### 深拷贝

> 深拷贝复制变量值，对于非基本类型的变量，则递归至基本类型变量后，再复制。深拷贝后的对象与原来的对象是完全隔离的，互不影响，对一个对象的修改并不会影响另一个对象。

#### 浅拷贝

> 浅拷贝是会将对象的每个属性进行依次复制，但是当对象的属性值是引用类型时，实质复制的是其引用，当引用指向的值改变时也会跟着变化。

```js
 //Object.assign() 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象
let obj = {
    name:'a',
    hobbies:{
        like:'coding',
        hate:'reading'
    }
}     
let obj2 = Object.assign({},obj)
let obj3 = JSON.parse(JSON.stringify(obj))
obj.hobbies.like = 'reading'
obj.name='b'
console.log(obj)
console.log(obj2) //name:a,like:reading,说明没有实现深拷贝

//JSON.parse(JSON.stringify(obj))我们一般用来深拷贝，其过程说白了 就是利用JSON.stringify 将js对象序列化（JSON字符串），再使用JSON.parse来反序列化(还原)js对象
console.log(obj3) //实现了深拷贝，但是存在问题
```

> JSON.parse(JSON.stringify(obj)) 的问题：
>
> 1. 如果obj里面有时间对象，则JSON.stringify后再JSON.parse的结果，时间将只是字符串的形式。而不是时间对象；
> 2. 如果obj里有RegExp、Error对象，则序列化的结果将只得到空对象；
> 3. 如果obj里有函数，undefined，则序列化的结果会把函数或 undefined丢失；
> 4. 如果obj里有NaN、Infinity和-Infinity，则序列化的结果会变成null
> 5. JSON.stringify()只能序列化对象的可枚举的自有属性，例如 如果obj中的对象是有构造函数生成的， 则使用JSON.parse(JSON.stringify(obj))深拷贝后，会丢弃对象的constructor；
> 6. 如果对象中存在循环引用的情况也无法正确实现深拷贝；

### 递归方法实现深拷贝原理：

遍历对象、数组直到里边都是基本数据类型，然后再去复制，就是深度拷贝。

> 有种特殊情况需注意就是对象存在循环引用的情况，即对象的属性直接的引用了自身的情况，解决循环引用问题，我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。

```js
function deepClone(obj, hash = new WeakMap()) {
  if (obj === null) return obj; 
  // 如果是null或者undefined我就不进行拷贝操作
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);
  // 可能是对象或者普通的值  如果是函数的话是不需要深拷贝
  if (typeof obj !== "object") return obj;
  // 是对象的话就要进行深拷贝
  if (hash.get(obj)) return hash.get(obj);
  let cloneObj = new obj.constructor();
  // 找到的是所属类原型上的constructor,而原型上的 constructor指向的是当前类本身
  hash.set(obj, cloneObj);
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      // 实现一个递归拷贝
      cloneObj[key] = deepClone(obj[key], hash);
    }
  }
  return cloneObj;
}
let obj = { name: 1, address: { x: 100 } };
obj.o = obj; // 对象存在循环引用的情况
let d = deepClone(obj);
obj.address.x = 200;
console.log(d);
```

