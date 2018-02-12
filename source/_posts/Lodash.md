---
title: Lodash
date: 2018-02-12 10:51:44
tags:
---
>"自从使用了Lodash，妈妈再也不用担心我的生产力了"

这句改编广告词只是个玩笑，但是博主在这里墙裂推荐大家使用lodash，这个库真是居家旅行、敲码做项目、JS码农必备之良库。

闲话不多话，让我们掌声有请Lodash。
![大佬](Lodash/dalao.jpg)

在日常coding的过程中，总有一些操作是我们会重复使用的，比如说循环遍历数组的每个元素。编程思想启迪我们，*'凡人皆有一死...'*，不对，是*'凡重复皆需抽象固定'*。在江湖上，很多码农称这个过程为造轮子。

在JS中，循环遍历数组这个操作的轮子已经提供了现成的forEach/map。但是还有很多较为复杂的操作JS并没有提供接口。Lodash就是一个提供了很多非常实用模块功能的工具库。

在开始API式的介绍之前，让我们先来看看Lodash的强大方便之处。

某场景："数组A元素全部为number，求数组A的元素之和"

读者朋友们可以先思考下自己会怎么写。下面我们看使用lodash怎么写
```javascript
_.sum(array)
```

再来一个场景：嵌套数组[[1,2],[3,4]]去掉嵌套，可以这样写：
```javascript
_.flatten(array)
```
可以看出lodash是多么的强大与方便。

Lodash由很多模块组成：Array,Collection,Date,Function,Lang,Math,Number,Object,Seq,String,Util,Properties,Methods

每一个模块都封装了很多相关的实用工具函数。具体函数请查阅[api](https://lodash.com/docs/4.17.5)

举一个例子，访问对象对象的属性值：
```javascript
return a.b
```
使用这种访问方式时，如果a对象没有b属性就会报错。而lodash提出了更可科学的访问接口
```javascript
_.get(object, path, [defaultValue])
```
在对象没有所访问的属性时，会返回指定的默认值。

类似的实用又安全的函数在lodash还有很多，非常值得掌握。

### 链式调用

lodash提供chain部件，支持优雅的链式调用，这一接口用过的人都说好。

lodash的链式调用分为两种：显式调用和隐式调用

显式调用
```javascript
//数组中的偶数+1再平方，之后求和
const _ = require('lodash')
const a = [1, 2, 3, 4, 5]
const b = _.chain(a)
    .filter(n => n % 2 === 0)
    .map(n => (n + 1) * (n + 1))
    .sum()
    .value()
```

隐式调用
```javascript
//数组中的偶数+1再平方，之后求和
const _ = require('lodash')
const a = [1, 2, 3, 4, 5]
const b = _(a)
    .filter(n => n % 2 === 0)
    .map(n => (n + 1) * (n + 1))
    .sum()
```
可以看出lodash的链式调用非常简洁直观，且解耦了各个过程，方便后期维护和修改。

### lodash/fp

fp就是函数式编程(Functional Programming),这里就不详细的介绍fp了，感兴趣的读者可以搜相关的文章来学习。

lodash/fp是lodash的一个模块，支持fp方式的函数调用。

具体怎么用我们还是来看一个例子

需求如下：
```javascript
const persons = [{name:'Judy'},{name:'Cherry'},{name:'Jake'}]
//返回由persons数组成员的name属性组成的数组
```
大家可以先想一下传统的写法是怎么样

下面我们来看使用Lodash/fp怎么写
```javascript
const {prop} = require('lodash/fp')
const persons = [{name:'Judy'},{name:'Cherry'},{name:'Jake'}]
const names = persons.map(prop('name'))
console.log(names) // [ 'Judy', 'Cherry', 'Jake' ]
```
可以看出，这种写法是非常简明易懂的。

写这篇文章的目的就是介绍一下lodash这个非常好用的库，能够切实提高我们的代码效率，关于lodash的深入学习还是需要大家亲身去实践。