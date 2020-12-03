---
title: 整理-JS异步编程，Promise
date: 2020-03-08 09:52:01
categories: Promise
---

* promise
* Generator
* async和await
* 宏任务，微任务，事件循环
* 手写promise，promise.all，promise.race

<!--more-->

## promise

首先想一下，怎么规定函数的执行顺序，通过回调的方式：

```js
function A(callback){
    console.log("I am A");
    callback();  //调用该函数
}

function B(){
   console.log("I am B");
}

A(B);//先执行A后执行B，当需要执行ABCDEFG时，就会造成回调地狱
```

回调地狱“也叫”回调金字塔“，我们平时写代码的时候 js如果异步 回调是不可避免的  例如 ajax不断的进行异步请求数据 回调方法里还要对数据进行处理，继续回调…形成回调地狱  这会使得我们的代码可读性变差，出现问题 不好调试 也会导致性能下降 

**Promise：同步代码解决异步编程**

- 是一个构造函数，用来传递异步操作消息，链式调用，避免层层嵌套的回调函数。
- promise接收两个函数参数，resolve和reject，分别表示异步操作执行成功后的回调和失败的回调
- promise在声明的时候就已经执行了
- 有三种状态：pending进行中、resolve已完成、rejected已失败,
- 这些状态只能由pending -> resolved, pending -> rejected,一旦promise实例发生改变，就不能在变了，任何时候都能得到这个结果
- promise对象的then方法会返回一个全新的promise对象
- 前面then方法中的回调函数的返回值会作为后面then方法回调的参数
- 如果回调中返回的是Promise，那后面的then方法的回调会等待它的结果
- promise.reslove()可以快速创建一个Promise对象

```js
console.log(1)
setTimeout(()=>console.log(5))
new Promise(function(resolve,reject){
    console.log(2) //立刻执行
    resolve()      //Promise.then是微任务
}).then(function(){
    console.log(3)
})
console.log(4)  //输出1,2,4,3,5

//resolve可以接收参数
new Promise(function(resolve,reject){
    resolve("2")
}).then(function(data){
    console.log(data)
})

//现在有work1 ~ work4四个方法,怎么实现顺序执行
function work1(){
    console.log("work1")
    return new Promise(function(res,rej){
        res()
    })
}
function work2(){
	console.log("work2")
    return new Promise(function(res,rej){
        res()
    })
}
function work3(){
    console.log("work3")
    return new Promise(function(res,rej){
        res()
    })
}
function work4(){
    console.log("work4")
    return new Promise(function(res,rej){
        res()
    })
}

new Promise(function(resolve,reject){
        resolve()
    }).then(()=>work1())    //.then里面放的是函数，会执行
      .then(()=>work2())
      .then(()=>work3())
      .then(()=>work4())
      .finally(()=>{
        console.log("work done!")
})

Promise.all([work1(),work2(),work3()]).then(()=>{
    console.log("work1,work2,work3")
})//所有操作全完成之后的操作

Promise.race([work1(),work2(),work3()]).then(()=>{
    console.log("有就行")
})//有一个成功就行


```

```js
// 拿点外卖为例，点外卖后可能会成功派送也可能会延迟，无论如何都会有个结果
funtion dianwaimai(){
    return new Promise((reslove,reject)=>{
        let result = cooking()
        if(result==="做好了") reslove("正在派送")
        else reject("还没做好")
    })
}
function cooking(){
    return Math.random() > 0.5 ? '菜烧好了' : '菜烧糊了'
}

//执行
dianwaimai().then(res=>console.log(res)).catch(res=>console.log(res))
```

## Generator

- Generator 的中文名称是生成器，
- 通过`function*`来定义的函数称之为“生成器函数”（generator function），它的特点是可以中断函数的执行，每次执行`yield`语句之后，函数即暂停执行，直到调用返回的生成器对象的`next()`函数它才会继续执行。
- 也就是说Generator 函数是一个状态机，封装了多个内部状态。执行 Generator 函数返回一个遍历器对象（一个指向内部状态的指针对象），调用遍历器对象的next方法，使得指针移向下一个状态。 

```js
function* say(){
    for(let i=0;i<10;i++){
        yield i
    }
}
let obj = say()//返回一个遍历器对象
obj.next()  //Object {value: 0, done: false}
obj.next()  //Object {value: 1, done: false}
obj.next()  //Object {value: 2, done: false}
...
obj.next()  //Object {value: 9 done: false}
obj.next()  //Object {value: undefined, done: true}

//计数
funtion* count(){
    let c=0
    while(true){
        yield c++
    }
}
var countNum = count()
countNum.next()

//next方法可以传值
funtion* count(){
    let c=0
    let status = false
    while(!status){
        status = yield c++ //还可以接收值，先接收值然后就暂停，赋值在后面
    }
}
var countNum = count()
countNum.next()
countNum.next()
countNum.next(true)//停止

```

## async和await

ES2017提供了`async`函数，使得异步操作变得更加方便。`async`函数就是`Generator`函数的语法糖。
 `async`函数就是将`Generator`函数的星号（`*`）替换成`async`，将`yield`替换成`await`，仅此而已。
 进一步说，`async`函数完全可以看作多个异步操作，包装成的一个`Promise`对象，而`await`命令就是内部`then`命令的语法糖。 

- async函数返回的就是一个Promise对象，所接收的值就是函数return的值

```js
async function f(){
    return 'hello world'
}
f().then((v)=>console.log(v))
```

- 在async函数内部可以使用await命令，表示等待一个异步函数的返回。
- 每遇到await关键字时，Promise都会停下，一直到运行结束。
- await后面跟着的是一个Promise对象，如果不是的话会调用Promise.resolve方法将其转为一个resolve的Promise对象
- Promise.resolve(x)相当于new Promise(resolve=>resolve(x))的简写
- async/await相比于Generator内置了执行器，可以自动执行，并且async返回的是Promise

```js
//首先定义一个方法
function fetchUser(){
    return new Promise((reslove,reject)=>{
        //... reslove()
        //... reject()
    })
}
```

## 宏任务，微任务，事件循环

- JS 任务分为同步任务和异步任务；
- 同步任务都在主线程上执行，形成一个执行栈；
- 主线程之外，事件触发线程管理着一个任务队列，只要异步任务有了结果，就在任务队列里面放置一个事件
- 一旦执行栈中所有同步任务执行完毕（JS 引擎空闲之后），就会去读取任务队列，将可运行的异步任务添加到可执行栈里面，开始执行。

> 任务有同步任务、异步任务，ES5中这么分足够了
>
> 由于ES6有promise，就任务又分为**宏任务（macro-task）**和**微任务（micro-task）** 

 **宏任务(macrotask)：**：

script(整体代码)、setTimeout、setInterval、UI 渲染、 I/O、postMessage、 MessageChannel、setImmediate(Node.js 环境)

**微任务(microtask)：**

Promise、 MutaionObserver、process.nextTick(Node.js环境

 **事件循环(Event Loop)**:   指主线程重复从任务队列中取任务、执行的过程 

- 选择最先进入队列的宏任务(通常是`script`整体代码)，如果有则执行
- 检查是否存在 Microtask，如果存在则不停的执行，直至清空 microtask 队列
- 更新render(每一次事件循环，浏览器都可能会去更新渲染)
- 重复以上步骤

```js
console.log('script start');
setTimeout(function() {
  console.log('setTimeout');
}, 0);
Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});
console.log('script end');

//script start
//script end
//promise1
//promise2
//setTimeout


async function async1(){
    console.log('async1 start')  //2
    await async2()               
    console.log('async1 end')    //6 放入了微队列  
}                                //    相当于async2.then(()=>{console.log('async1 end')})
async function async2(){
    console.log('async2')       //3
}
console.log('script start')     //1
setTimeout(function(){
    console.log('setTimeout')   //8
},0)
async1()
new Promise(function(resolve){
    console.log('promise1')     //4
    resolve()
}).then(function(){
    console.log('promise2')    //7   放入了微队列
})
console.log('script end')     //5

//await是一个让出线程的标志。await后面的表达式会先执行一遍，将await 后面的代码加入到microtask中，然后就会跳出整个async函数来执行后面的代码
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```

> 1.首先，事件循环从宏任务(macrotask)队列开始，这个时候，宏任务队列中，只有一个script(整体代码)任务；当遇到任务源(task source)时，则会先分发任务到对应的任务队列中去。

> 2.然后我们看到首先定义了两个async函数，接着往下看，然后遇到了 console 语句，直接输出 script start。输出之后，script 任务继续往下执行，遇到 setTimeout，其作为一个宏任务源，则会先将其任务分发到对应的队列中

> 3.script 任务继续往下执行，执行了async1()函数，前面讲过async函数中在await之前的代码是立即执行的，所以会立即输出async1 start。
>  遇到了await时，会将await后面的表达式执行一遍，所以就紧接着输出async2，然后将await后面的代码也就是console.log('async1 end')加入到microtask中的Promise队列中，接着跳出async1函数来执行后面的代码

> 4.script任务继续往下执行，遇到Promise实例。由于Promise中的函数是立即执行的，而后续的 .then 则会被分发到 microtask 的 Promise 队列中去。所以会先输出 promise1，然后执行 resolve，将 promise2 分配到对应队列

> 5.script任务继续往下执行，最后只有一句输出了 script end，至此，全局任务就执行完毕了。
>  根据上述，每次执行完一个宏任务之后，会去检查是否存在 Microtasks；如果有，则执行 Microtasks 直至清空 Microtask Queue。
>  因而在script任务执行完毕之后，开始查找清空微任务队列。此时，微任务中， Promise 队列有的两个任务async1 end和promise2，因此按先后顺序输出 async1 end，promise2。当所有的 Microtasks 执行完毕之后，表示第一轮的循环就结束了

> 6.第二轮循环依旧从宏任务队列开始。此时宏任务中只有一个 setTimeout，取出直接输出即可，至此整个流程结束

面试官问：

```js
setTimeout（()=>{
    console.log("1")
}, 0）;
new Promise（resolve => {
    resolve()
}）.then(() => {
	console.log("2")
})
console.log("3")
//为什么你说先运行宏任务，不直接运行setTimeout呢？
//因为后面如果有console.log("3")时，肯定会先执行3，setTimeout是放到宏任务队列里的。

Promise.resolve(1).then(2).then(Promise.resolve(3)).then(console.log) // 1
// then里面只要不是函数就不会传递，2是值，Promise.resolve(3)是Promise对象
// console.log 是函数
```

## Promise 方式的 AJAX

```js
function ajax (url) {
    return new Promise(function (reslove, reject) {
        var xhr = new XMLHttpRequest()
        xhr.open('GET', url) 
        xhr.reponeseType = 'json'
        xhr.onload = function () {
            if(this.status === 200) {
                reslove(this.response)
            } else {
                reject(new Errpr(this.statusText))
            }
        }
        xhr.send()
    })
}

ajax('/api/')
	.then(res => console.log(res))
	.catch(err => console.log(err))


//promise.reslove

Promise.resolve('foo')
    .then(function (value) {
 	   console.log(value)
	})
```

## Promise静态方法

* Promise.reslove
* Promise.reject

**这两种方法均会创建Promise对象**

```js
Promise.resolve('foo')
	.then( value => console.log(value) )
// foo

Promise.reject(new Error('rejected'))
	.catch(err => console.log(err))
// Error: rejected
//    at <anonymous>:1:16
```

## Promise并行执行

如果有多个请求接口的操作，如何判断全部执行完毕？`Promise.all`

```js
// 多个Promise对象组合成一个Promise对象
let promise = Promise.all([ //参数为数组
    // 请求1 必须为promise对象
    // 请求2
])

promise.then(values => console.log(values)) // 输出数组，保存每一个请求的结果
	   .catch(err => console.log(err))
```

## 手写Promise基础版

```js
const PENDING = 'pending' // 等待
const FULFILLED = 'fulfilled' // 成功
const REJECTED = 'rejected' //失败

class MyPromise {
  constructor (executor) {
    executor(this.resolve, this.reject)
  }
  // promise 状态
  status = PENDING
  // 成功之后的值
  value = undefined
  // 失败后的原因
  reason = undefined
  // 成功回调
  successCallback = undefined
  // 失败回调
  failCallback = undefined
  resolve = value => {
    // 如果状态不是等待 阻止程序向下执行
    if (this.status !== PENDING) return
    // 将状态更改为成功
    this.status = FULFILLED
    // 保存成功之后的值
    this.value = value
    // 判断成功回调是否存在 存在则调用
    this.successCallback && successCallback(this.value)
  }
  reject = reason => {
    // 如果状态不是等待 阻止程序向下执行
    if (this.status !== PENDING) return
    // 将状态更改为失败
    this.status = REJECTED
    // 保存失败后的原因
    this.reason = reason
    // 判断失败回调是否存在 存在则调用
    this.failCallback && failCallback(this.reason)
  }
  then (successCallback, failCallback) {
    // 判断状态
    if (this.status === FULFILLED) {
      successCallback()
    } else if (this.status === REJECTED) {
      failCallback()
    } else {
        // 等待
        // 将成功回调和失败回调存储起来
        this.successCallback = successCallback
        this.failCallback = failCallback
    }
  }
}

// 测试 
let promise = new MyPromise((resolve, reject) => {
    setTimeout(() => {
        resolve('成功')
    }, 2000)
    resolve('成功')
    // reject('失败')
})
promise.then(value => {
    console.log(value)
}, reason => {
    console.log(reason)
})
```

## promise.all

```js
		Promise.all = function(promises){
            return new Promise((resolve,reject)=>{
                let result = []
                let len = promises.length
                let index = 0
                if(len==0){
                    resolve(result)
                }
                for(let i=0;i<len;i++){
                    Promise.resolve(promise[i]).then((res)=>{
                        result[i] = res
                        index++
                        if(index==len){
                            resolve(result)
                            return
                        }
                    }).catch((err)=>{
                        reject(err)
                    })
                }
            })
        }
```

## promise.race

```js
		Promise.race = function(promises){
            return new Promise((resolve,reject)=>{
                let len = promises.length
                if(len==0) return
                for(let i=0;i<len;i++){
                    Promise.resolve(promises[i]).then(res=>{
                        resolve(res)
                        return
                    }).catch((err)=>{
                        reject(err)
                        return 
                    })
                }
            })
        }
```

## promise.resolve

```js
Promise.resolve = function (value) {
    if (value instaceif promise) return value
    return new Promise(resolve => resolve(value))
}

// 例子
Promise.resolve(100).then(value => console.log(value))
```

## 一道习题

```js
// 原代码
setTimeout(function () {
  var a = 'hello '
  setTimeout(function () {
    var b = 'lagou '
    setTimeout(function () {
      var c = 'I love you'
      console.log(a + b + c)
    }, 1000)
  }, 1000)
}, 1000)

// 改进：高阶函数 + promise链式调用
function f (str) {
  return function (param) {
    return new Promise((resolve,reject) => {
      setTimeout(() => resolve(param += str), 1000)
    })
  }
}

var p1 = f('hello ')
var p2 = f('lagou ')
var p3 = f('I love you')

p1('').then((res) => p2(res)).then((res) => p3(res)).then((res) => console.log(res))
p1('').then(p2).then(p3).then(console.log)

```

红灯3秒亮一次，绿灯1秒亮一次，黄灯2秒亮一次；如何让三个灯不断交替重复亮灯？（用Promise实现）三个亮灯函数已经存在：

```js
function red() {
    console.log('red')
}
function green() {
    console.log('green')
}
function yellow() {
    console.log('yellow')
}

function light(time, fn) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            fn()
            resolve()
        }, time)
    })
}

function step() {
    Promise.resolve()
    .then(() => light(3000, red))
    .then(() => light(2000, green))
    .then(() => light(1000, yellow))
    .then(() => step())
}

step()
```

* **只有返回promsie对象后才会链式调用**

```js
Promise.resolve().then(res => {
   console.log('1')
   new Promise((resolve, reject) => { 
        setTimeout(() => {
            console.log('2')
            resolve('newPromise')
        }, 2000);
    }).then(res =>{
        console.log('3')
        return "newPromise1"
    })
}).then(res=>{
    console.log('4', res)
})
// 1 4 undefined  2 3
```

```js
Promise.resolve().then(res => {
   console.log('1')
   return new Promise((resolve, reject) => { 
        setTimeout(() => {
            console.log('2')
            resolve('newPromise')
        }, 2000);
    }).then(res =>{
        console.log('3')
        return "newPromise1"
    })
}).then(res=>{
    console.log('4', res)
})
// 1 2  3 4 newPromise1
```