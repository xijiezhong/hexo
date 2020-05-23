---
title: JS中的赋值、浅拷贝与深拷贝
date: 2020-05-23 15:17:48
tags:
---

# 赋值、浅拷贝与深拷贝

拷贝是写代码中经常使用的方法。浅拷贝与深拷贝是指拷贝的两种情况。本文将深入探究下JS的赋值、浅拷贝与深拷贝。

### 数据类型

在探究深拷贝与浅拷贝之前，我们先梳理一下JS的数据类型。在JavaScript中，数据类型有两大类。一类是基本数据类型，一类是引用数据类型。

基本数据类型有五种：number、string、boolean、null、undefined。基本数据类型存放在栈中。存放在栈中的数据具有数据大小确定，内存空间大小可以分配、直接按值存放的特点。所以存放在栈中的数据可以直接访问。在JavaScript中，基本的数据类型值是不可更改的。可能这一点不符合我们平常的使用认知。我们在修改字符串或其他基本数据类型变量时，实际上是返回了一个新的值，而不是【改变】原始值。

而对于引用数据类型，是存放在堆内存中。引用数据类型的变量并不是存放的实际值，而是一个存放在栈内存的指针，该指针指向堆内存中的某个地址。每个数据所占的空间大小不一致，需要根据情况进行特定的分配。与基本数据类型不同，引用类型的值是可以改变的。

### 赋值：传值与传址

有了对JavaScript数据类型的一定了解后，我们接下来看看不同类型的数据类型在赋值时的区别。

对于基本数据类型来说，当我们进行赋值操作（=）时，实际上是在内存中新开一段栈内存，然后再将值赋值到新的栈中。

举个例子

```
let a = 1
let b = a
a = 5
console.log(a) // 5
console.log(b) // 1
```

对于上述语句，b变量虽然是a变量的复制，但与a变量是独立互不影响的变量。

而对于引用数据类型来说，在进行赋值操作时，实际上是把变量的地址传给了另一个变量，所以称为传址。传址之后，两个变量就指向同一个地址，两者的操作是互有影响的。

例

```
let a = { age: 12 }
let b = a

a.age = 13
console.log(a.age) // 13
console.log(b.age) // 13

b.age = 14
console.log(a.age) // 14
console.log(b.age) // 14
```

### 浅拷贝与深拷贝

只有当拷贝引用数据类型时，拷贝才存在浅拷贝与深拷贝之分。

#### 浅拷贝

浅拷贝就是指创建一个新对象，该对象拥有原始对象第一层属性的精确拷贝。即：如果原始对象的属性是基本类型数据，则拷贝的就是基本数据类型的值；如果原始对象的属性是引用类型，则拷贝的是内存地址。当原始对象的引用类型属性发生改变时，拷贝对象的对应属性值也会发生变化。这里需要强调一下，浅拷贝与赋值是有所区别的，赋值时与原数据指向同一对象，而浅拷贝则指向了不同对象。

让我们来实现一个浅拷贝方法

```
function shallowCopy(src) {
  const dist = {};
  for (let prop in src) {
    if (src.hasOwnProperty(prop)) {
      dist[prop] = src[prop]
    }
  }
  return dist;
}
```

#### 深拷贝

浅拷贝是对原始对象第一层属性的精确拷贝，而深拷贝则是对原始对象所有层级属性的递归精确拷贝。

### 深拷贝的实现

#### JSON

```
function deepCopy (src) {
    let temp = JSON.stringify(src)
    let dist = JSON.parse(temp)
    return dist
}
```

#### 递归实现

```
function deepCopy (src) {
    if (typeof src !== 'object') {
       return src
    }
    if (src == null) {
       return null
    }
    let dist = Array.isArray(src) ? [] : {};
    if (src && typeof src === "object") {
      for (let prop in src) {
        if (src.hasOwnProperty(prop)) {
          // 如果子属性为引用数据类型，递归复制
          if (src[prop] && typeof src[prop] === "object") {
            dist[prop] = deepCopy(src[prop])
          } else {
            // 如果是基本数据类型，只是简单的复制
            dist[prop] = src[prop]
          }
        }
      }
    }
    return dist
}
```

