---
title: 原型和原型链
date: 2018-09-19 10:00:54
categories: JavaScript
---

* 原型：https://blog.csdn.net/u012468376/article/details/53121081 
* 原型链：https://blog.csdn.net/u012468376/article/details/53127929
* [Object和Function图示](https://pic2.zhimg.com/80/dcd9f21f6457d284950b767e6f7bdea3_720w.jpg?source=1940ef5c)

```js
Function.prototype.a = () => console.log(1);
Object.prototype.b = () => console.log(2);
function A() {};
var a = new A(); // 记住 new 返回的是对象
a.a(); // not a function
a.b(); // 2

let fun = function x() {}
fun.a() //1
fun.b() //2
```



