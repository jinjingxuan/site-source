---
title: 灵活的JavaScript
date: 2019-1-17 16:00:54
categories: 设计模式
---

## 几个验证表单的函数

```js
function checkName(){
    //验证姓名
}
function checkEmail(){
    //验证邮箱
}
function checkPassword(){
    //验证密码
}
```

<!--more-->

## 用对象收编变量

```js
var checkObject = {
    checkName:function(){
        //验证姓名
    },
    checkEmail:function(){
         //验证邮箱
    },
    checkPassword:function(){
        //验证密码
    }
}
```

## 对象的另一种形式

```js
var CheckObject = function(){};
CheckObject.checkName = function(){
    //验证姓名
}
CheckObject.checkEmail = function(){
    //验证邮箱
}
CheckObject.checkPassword = function(){
    //验证密码
}
```

但是这种对象不能复制，我们可以这么写,每次调用这个函数的时候都返回了一个新对象，每个人在使用的就不互相影响了。

## 真假对象

```js
var CheckObject = function(){
    return{
      	 checkName:function(){
        //验证姓名
        },
        checkEmail:function(){
             //验证邮箱
        },
        checkPassword:function(){
            //验证密码
        }  
    }
}

var a = CheckObject();
a.checkEmail();
```

但是这种方式不是一个真正意义上类的创建，并且创建的a和对象CheckObject没有任何关系，我们还需改造一下

## 类也可以

```js
var CheckObject = function(){
    
    	this.checkName = function(){
        	//验证姓名
        },
        this.checkEmail = function(){
             //验证邮箱
        },
        this.checkPassword: = unction(){
            //验证密码
        }
	
}

var a = new CheckObject();
a.checkEmail();
```

每个新创建的对象都会有自己的一套方法，这样是很奢侈的,改用原型设置方法

```js
var CheckObject = function(){};
CheckObject.prototype.checkName = function(){
    //验证姓名
}
CheckObject.prototype.checEmail = function(){
    //验证邮箱
}
CheckObject.prototype.checkPassword = function(){
    //验证密码
}

var a = new CheckObject();
a.checkName();
a.checkEmail();
a.checkPassword();
```

我们调用了三次方法，我们可以实现链式调用

```js
var CheckObject = function(){};
CheckObject.prototype.checkName = function(){
    //验证姓名
    return this;
}
CheckObject.prototype.checEmail = function(){
    //验证邮箱
    return this;
}
CheckObject.prototype.checkPassword = function(){
    //验证密码
    return this;
}

var a = new CheckObject();
a.checkName().checkEmail().checkPassword();
```

