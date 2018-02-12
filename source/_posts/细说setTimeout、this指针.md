---
title: 细说setTimeout
date: 2018-02-07 10:57:16
tags: JS
---
当我最开始接触setTimeout的时候，没觉得这个函数有什么，后来在码代码、面试的过程才慢慢踩中一些坑，然后再去网上找了很多文章来看，发现setTimeout里面的水很挺深。借这篇文章，我希望能够比较全面的展现setTimeout常见的疑点，同时也帮助自己梳理思路。  

### setTimeout是什么
setTimeout(Func, time) 是指定一个时长（单位是毫秒），等待这一段时长过后调用Func函数。
setTimeout返回一个标识该定时器的id值，使用clearTimeout传入id值就可以提前清除该定时器。

### setTimeout属于谁
我们在调用setTimeout常常是直接使用setTimeout(...),但其实setTimeout是某一个对象的方法。该对象就是全局对象。

而全局对象跟JS的运行环境有关。在浏览器中，全局对象是window对象。在nodeJs中，全局对象是global对象。

所以，在浏览器环境中，我们直接调用setTimeout是省略了window对象，其实在后台是window.setTimeout.

那么这里有一个问题：能不能使用非全局对象调用setTimeout方法呢？请看下面一段代码
```javascript
    var obj = {};
    obj.setTimeout = setTimeout;
    obj.setTimeout(function(){
      console.log("111")
    },100);
```
你觉得这段代码能正常运行吗？

事实是在node环境中可以正常运行，但在chrome浏览器中会报错
> Uncaught TypeError: Illegal invocation at test.html:51

可以看出浏览器下setTimeout只能由window对象调用

### setTimeout的常见调用方式
setTimeout(func||code, delay)接收两个参数，第一个参数是延迟执行的函数名，或者一段代码，第二个参数是延迟的毫秒数

给几个示例
```javascript
  setTimeout('console.log("hello world")', 1000)

  setTimeout(function(){
    console.log('hello world')
  }, 1000)

  var a = function () {
    console.log('hello world')
  }
  setTimeout(a, 1000)
```
既然setTimeout第一个参数既可以是函数名，又可以是字符串，那么setTimeout(func,10)与setTimeout('func()',10)这两种调用方式有何区别呢。
我们来看一段代码
```javascript
var b = function () {
  console.log('outer')
}
function container () {
  var b = function () {
    console.log('inner')
  }
  setTimeout(b,1000)
  setTimeout('b()',5000)
}
container()
```
请问上面这段代码控制台会输出什么呢？

答案是先输出inner，再输出outer。

由此，我们可以看出第一个参数带引号和不带引号的区别在于其回调函数执行的作用域不同。

当是字符串时，引擎会调用eval()执行，其环境是全局，所以不能访问函数作用域内的局部变量。

当是函数时，则执行该函数，可以访问到该函数作用域链上的变量。

因此，建议setTimeout第一个参数使用函数比较安全。

有时候会希望往setTimeout的回调函数里面传参，可以像下面这样写
```javascript
  var a = 3
  var b = 2
  var sum = function (first, second) {
    console.log(first + second)
  }
  setTimeout('sum(a,b)',3000);
  setTimeout(sum,6000,a,b);
  setTimeout(function(){
    sum(a,b)
  },9000)
```

### setTimeout与this指针

在JS中，this一直指向调用当前函数代码的对象。在全局作用域中，this指向全局对象.

在setTimeout的回调函数中，其this指针始终指向window对象。我们跑一段程序看看效果
```javascript
  var a = 'outer'
  var obj = {
    a: 'inner',
    getA: function () {
      console.log(this.a)
      setTimeout(function () {
        console.log(this.a)
      },1000)
    }
  }
  obj.getA() // 先打印inner，再打印outer
```
如果我们需要setTimeout回调函数的this指向正确的对象时，通常可以采用三种办法。

1.将this赋给另一个变量
```javascript
  var a = 'outer'
  var obj = {
    a: 'inner',
    getA: function () {
      var that = this
      setTimeout(function () {
        console.log(that.a)
      },1000)
    }
  }
  obj.getA() // inner
```
这种方法是通过闭包去访问保存了this值的局部变量that

2.bind()
```javascript
  var a = 'outer'
  var obj = {
    a: 'inner',
    getA: function () {
      setTimeout(function () {
        console.log(this.a)
      }.bind(this),1000)
    }
  }
  obj.getA() // inner
```
bind()可以将函数绑定在某个对象上

3.ES6的箭头函数
```javascript
  var a = 'outer'
  var obj = {
    a: 'inner',
    getA: function () {
      setTimeout(() => {
        console.log(this.a)
      },1000)
    }
  }
  obj.getA() // inner
```
箭头函数中的this指针总是指向函数定义时的对象。

### setTimeout与异步/event loop
通常情况下，我们使用setTimeout都是希望去异步的在未来某个时刻执行某段代码。
那么setTimeout是真的异步吗？setTimeout会如我们所愿的非常精确的去执行这些动作吗？要回答以上问题，我们需要先理解JS在浏览器下的执行模式。

首先，众所周知，JS是单线程执行的，异步明显和单线程不能共存。为了实现异步，浏览器采用了事件循环机制和任务队列。

任务队列里面保存了JS未来需要执行的任务。
事件循环是指浏览器会不停的从任务队列中取任务去执行，只有在当前任务执行完后才会继续从任务队列中取任务并执行。JS的执行就是这种循环模式。

而setTimeout并不是精确的在未来某个时间执行回调函数，而是在一段时间后将代码放到任务队列末尾。如果时间设置为0，则表示立即插入，而不是立即执行。
为了展示这种机制，我们看一段代码
```javascript
  setTimeout(function () {
    console.log('first')
  },0)
  console.log('second')
  // 输出second first
```
因此，在一些情况下，setTimeout回调函数执行的时间并不精确等于我们所设定的时间，而是会大于该时间。