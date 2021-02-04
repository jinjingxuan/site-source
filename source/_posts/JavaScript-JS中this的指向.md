---
title: Javascript中this的指向
date: 2018-10-31 10:00:54
categories: JavaScript
---

* 以函数的形式调用时，**this**指向的是**window**

* 以方法的形式调用时，this就是调用方法的那个对象

* 当以构造函数的形式调用时，this就是新创建的那个对象

* 使用call()和apply()调用时，this就是call()和apply()第一个参数的对象

* 使用bind()()调用时，this就是bind()第一个参数的对象

* 箭头函数的 this 始终指向函数定义时的 this，而非执行时。

# this的指向

1. 普通函数指向函数的调用者

> 有个简便的方法就是看函数前面有没有点,如果有点,那么就指向点前面的那个值;
> 也就是说this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象 

2. 箭头函数指向函数所在的所用域:

> 注意理解作用域,只有函数的{}构成作用域,对象的{}以及 if(){}都不构成作用域;

## 1.普通函数与箭头函数this指向的区别

```js
const obj = {
    name: 'objName',
    say() {
        console.log(this.name);
    },
    read: () => {
        console.log(this.name);
    }
}
obj.say(); // objName,函数被obj对象调用，所以this指向obj
obj.read(); // undefined，此时函数的作用域为全局环境，window.name未定义
```

## 2.普通函数与作为对象方法函数this指向的区别 

```js
window.val = 1;
var obj = {
    val: 2,
    dbl: function() {
        this.val *= 2;
        val *= 2;
        console.log(val);
        console.log(this.val);
    }
}
//作为对象方法
obj.dbl(); // 2 4,this指的是obj，所以this.val值为4，val相当于window.val值为2

//普通函数
var func = obj.dbl;
func(); // 8 8，func()没有任何前缀，this指的是window.func();所以此时this值得是window，值均为8
```

## 3.如何解决函数体内的函数绑定到全局对象的问题 

我们希望在 moveTo 方法内定义两个函数，分别将 x，y 坐标进行平移。结果可能出乎大家意料，不仅 point 对象没有移动，反而多出两个全局变量 x，y。 

```js
		var point = { 
			x : 0, 
			y : 0, 
			moveTo : function(x, y) { 

			    var moveX = function(x) { 
			    	this.x = x;
			    }; 

			    var moveY = function(y) { 
			   		this.y = y;
			    }; 
			 
                //moveX(x)函数前面没有对象，默认为window.moveX(x),所以this指向window
			   moveX(x); 
			   moveY(y); 
			} 
		}; 

		point.moveTo(1, 1); 
		console.log(point.x); //==>0 
		console.log(point.y); //==>0 
		console.log(x); //==>1 
		console.log(y); //==>1
```

这属于 JavaScript 的设计缺陷，正确的设计方式是内部函数的 this 应该绑定到其外层函数对应的对象上，为了规避这一设计缺陷，聪明的 JavaScript 程序员想出了变量替代的方法，约定俗成，该变量一般被命名为 that。 

```js
        var point = { 
            x : 0, 
            y : 0, 
            moveTo : function(x, y) { 
                //调用move函数To的对象为point,所以这里的that和this指向point
                var that = this; 

                var moveX = function(x) { 
                    that.x = x; 
                }; 

                var moveY = function(y) { 
                    that.y = y; 
                } 
                moveX(x); 
                moveY(y); 
           } 
        }; 
        point.moveTo(1, 1); 
        point.x; //==>1 
        point.y; //==>1
```

## 构造函数中this的指向

JavaScript 支持面向对象式编程，与主流的面向对象式编程语言不同，JavaScript 并没有类（class）的概念，而是使用基于原型（prototype）的继承方式。相应的，JavaScript 中的构造函数也很特殊，如果不使用 new 调用，则和普通函数一样。作为又一项约定俗成的准则，构造函数以大写字母开头，提醒调用者使用正确的方式调用。如果调用正确，this 绑定到新创建的对象上。 

```js
function Point(x, y){ 
	this.x = x; 
	this.y = y; 
}

var p = new Point(1,2);
console.log(p.x);//==>1,this指向对象p
```

## 5. apply或call调用

在 JavaScript 中函数也是对象，对象则有方法，apply 和 call 就是函数对象的方法。这两个方法异常强大，他们允许切换函数执行的上下文环境（context），即 this 绑定的对象。很多 JavaScript 中的技巧以及类库都用到了该方法。 

```js
function Point(x, y){ 
   this.x = x; 
   this.y = y; 
   this.moveTo = function(x, y){ 
       this.x = x; 
       this.y = y; 
   } 
} 
 
var p1 = new Point(0, 0); 
var p2 = {x: 0, y: 0}; 
p1.moveTo(1, 1); 
p1.moveTo.apply(p2, [10, 10]);
```

在上面的例子中，我们使用构造函数生成了一个对象 p1，该对象同时具有 moveTo 方法；使用对象字面量创建了另一个对象 p2，我们看到使用 apply 可以将 p1 的方法应用到 p2 上，这时候 this 也被绑定到对象 p2 上。另一个方法 call 也具备同样功能，不同的是最后的参数不是作为一个数组统一传入，而是分开传入的。 



部分转载自https://www.ibm.com/developerworks/cn/web/1207_wangqf_jsthis/index.html，

https://juejin.im/post/5a0d9ff4f265da432e5b91da