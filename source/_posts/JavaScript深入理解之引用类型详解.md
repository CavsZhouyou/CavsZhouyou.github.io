---
title: JavaScript深入理解之引用类型详解
date: 2018-08-02 18:41:49
tags:
 - 前端
 - JavaScript
 - 引用类型
 - 原型链
 - 构造函数
 - new
categories:
 - 前端
 - JavaScript
---

![JavaScript深入理解之引用类型详解文章配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu17-1.jpg)

## 写在前面

相信提到 JavaScript 中的引用类型大家都不陌生，但是对于什么是引用类型这个问题，我一直充满疑惑，对这个概念的理解不是很清晰。引用类型与类的概念有什么不同？引用类型的实现机制到底是什么？下面是我对引用类型的一些理解和总结。

<!-- more -->

## 引用类型定义

引用类型是一种数据结构，用于将数据和功能组织在一起。它也常被称为类，但这种称呼并不妥当。尽管 ECMAScript从技术上讲是一门面向对象的语言，但它不具备传统的面向对象语言所支持的类和接口等基本结构。引用类型有时候也被称为对象定义，因为它们描述的是一类对象所具有的属性和方法。

**简单来理解，引用类型在逻辑上等价于其他面向对象语言中的类的概念。**

## 预定义引用类型

JavaScript 提供了一些预定义的引用类型，用于创建相应引用类型的对象。如 Object 类型 、 Array 类型 、 Date 类型、 RegExp 类型、 Function 类型、 Boolean 基本包装类型、 String 基本包装类型、 Number 基本包装类型等。  

## 引用类型的结构

在上面我们了解到引用类型是一种数据结构，那这种数据结构是如何定义的呢？前面我们知道了引用类型在逻辑上其实等价于其他面向对象语言中的类的概念，那么它的作用与类肯定也是相似的。

在 JavaScript 语言中对象是某个特定引用类型的实例。新对象是使用 new 操作符后跟一个 **构造函数** 来创建的。注意构造函数与一般的函数没有区别，只不过该函数是出于创建新对象的目的而定义的。

```js
var laker = new Object();
```
因此我们可以看出，**引用类型的定义其实就是其对应的构造函数的定义**。

```js
// 在控制台测试一下看看

console.log(typeof Array) // function

console.log(typeof Date) // function

```

我们知道无论在什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个 prototype 属性，这个属性指向函数的原型对象，原型对象可以使所有对象实例共享它所包含的属性和方法。

同样的，**引用类型对应的构造函数也拥有原型对象**，在这个原型对象中保存了该引用类型预定义的方法，如 Array 类型中的 concat 、 every方法等。

关于原型这一块的内容会在以后详细讲解。


## 引用类型的值

引用类型的值是引用类型的一个实例，也就是 JavaScript 中常说的对象。每一个对象都是其对应引用类型的实例，对象是使用 new 操作符后跟一个 **构造函数** 来创建的。

我们知道构造函数和普通函数其实没有区别，只是因为由 new 操作符来调用才被称为构造函数，那么使用 new 操作符来创建一个对象的过程到底是怎样的呢？下面让我们来看一下具体的步骤：

1. 首先创建一个新的对象
2. 然后将构造函数的作用域赋给新对象（因此 this 就指向了这个新对象）
3. 执行构造函数的代码（为这个新对象添加属性）
4. 返回新对象

当调用构造函数创建一个新实例后，该实例的内部将包含一个指针，指向构造函数的原型对象。因此该实例可以通过原型链访问到原型对象中的方法及属性。


## 预定义引用类型间的关系

在所有预定义引用类型中，Object 是特殊的一个数据类型。因为它是最基础的类型，其他的所有类型都都从 Object 继承了基本的行为。这种继承的方式是以原型链的方式实现的。

```js
// 在控制台测试一下看看

console.log(Array.prototype.__proto__ === Object.prototype)

console.log(Date.prototype.__proto__ === Object.prototype) 

```
在后面原型链的总结中我还会详细解释一部分，在这里我们只需要记住，Object 是最基础的类型，其他的所有类型都从 Object 继承了基本的行为。

## 写在最后

对于引用类型的理解每个人可能都有不同，主要是 JavaScript 的实现机制和一般的面向对象语言有所不同，所以理解起来有些困难。深入理解引用类型的概念对理解 JavaScript 的面向对象实现机制很有帮助。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**