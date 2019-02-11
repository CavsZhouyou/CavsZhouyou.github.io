---
title: JavaScript深入理解之this详解
date: 2018-03-29 09:24:29
tags:
 - 前端
 - JavaScript
 - 执行上下文
 - this
categories:
 - 前端
 - JavaScript
---

![JavaScript深入理解之this详解 文章配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu14-1.jpg)

## 写在前面
在文章[《JavaScript深入理解之执行上下文》](http://cavszhouyou.top/JavaScript%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3%E4%B9%8B%E6%89%A7%E8%A1%8C%E4%B8%8A%E4%B8%8B%E6%96%87.html#more)中我们谈到了执行上下文一共有三个属性：变量对象、作用域链、this指针。本篇文章我们将介绍执行上下文最后一个重要的属性 —— this。this指向一直是一个老生常谈的问题，下面是我的一些理解和总结。

<!--more-->


## 定义

> this 指向最后一次调用这个方法的对象

记住上面这句话，对于 this 的用法我们其实已经懂了一半了。在实际应用中，一共有四种调用方式：方法调用模式、函数调用模式、构造器调用模式和 apply 、 call 、 bind 调用模式。下面我们来分别看一下在这几种不同模式下 this 的指向。

## 方法调用模式

当一个函数被保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用时， this 被绑定到该对象。

那么我们怎么来判断一个函数是否是被作为方法来调用呢？ 如果调用表达式包含一个提取属性的动作（即包含一个 `.` 点或 `[subscript]` 下标表达式），那么它就是被当做一个方法来调用。

下面我们来看一个例子：

```js
var myObjct = {
    value: 1;
    increment: function(){
        this.value++;
    }
}

myObjct.increment(); // myObject 调用 increment() 方法，this 指向 myObject 对象 

console.log(mvObject.value); // 2

```

方法可以使用 this 访问自己所属的对象，所以它能从对象中取值或对对象进行修改。 this 到对象的绑定发生在函数调用的时候，而不是函数定义的时候。

## 函数调用模式

当一个函数并非一个对象的属性时，那么它就是被当做一个函数来调用的。如下面这个例子：

```js
var value = 1;

increment(); // 作为函数调用， this 绑定到全局对象

console.log(value); // 2

function increment(){
    this.value++;
}
```

以此模式来调用函数时，this被绑定到全局对象。

这其实是语言设计上的一个错误。倘若语言设计正确，那么当内部函数被调用时，this 应该仍然绑定到外部函数的 this 变量（其他大部分语言都是这样实现的）。

这个设计错误的结果就是方法不能利用内部函数来帮助它工作，因为内部函数的 this 被绑定了错误的值，所以不能共享该方法对对象的访问权。

不过我们可以通过定义一个变量并给它赋值为 this 来解决，常用的如 that。

## 构造器调用模式

如果在一个函数前面带上 new 调用，那么背地里将会创建一个连接到该函数的 prototype 成员的新对象，同时 this 也会被绑定到那个新对象上。

如下面例子所示

```js
var peo = new person("xiao ming"); // this 绑定到 peo 对象

console.log(peo.name); // "xiao ming"

function person(name){
    this.name = name;
}
```

一个函数，如果创建的目的就是希望结合 new 前缀来调用，那么它就被称为构造器函数。按照约定，它们保存在以大写格式命名的变量里。

## apply 、 call 和 bind 调用模式

这三个方法用途都是在特定的作用域中调用函数，简单说就是用于指定函数调用时 this 指向的对象。下面我们分别来讲讲它们用法的不同。

### apply 调用

apply() 方法接收两个参数：一个是 this 绑定的对象，一个是参数数组。其中第二个参数可以是 Array 的实例，也可以是 arguments 对象。

如下面的例子所示：

```js
var person = {
    name: ""
};

setName.apply(person,["xiao","ming"]);

console.log(person.name); // "xiao ming" 

function setName(firstName,lastName){
    this.name = firstName + lastName;
}
```

### call 调用

call() 方法和 apply() 方法的作用相同，它们的区别仅在于接收参数的方式不同。对于 call() 方法而言，第一个参数是 this 值没有变化，变化的其余参数都直接传递给函数。换句话说，在使用 call() 方法时，传递给函数的参数 必须逐个列举出来。

如下面的例子所示：

```js
var person = {
    name: ""
};

setName.call(person,"xiao","ming");

console.log(person.name); // "xiao ming" 

function setName(firstName,lastName){
    this.name = firstName + lastName;
}
```

### bind 调用 

ES5中新定义了一个方法：bind()。这个方法会创建一个函数的实例，其 this 值会被绑定到传给 bind() 函数的值。

如下面的例子所示：

```js
var color = "red";
var o = {
        color: "blue"
    };

sayColor(); // "red"

var objectSayColor = sayColor.bind(o); // 创建新的函数实例，并将其 this 值绑定为 o 对象

objectSayColor();// "blue"

function sayColor(){
    console.log(this.color);
}

```

我们看到了，使用 bind() 函数创建的实例，即使在全局作用域中调用这个函数，this 绑定的依然是 o 对象。

不过我们需要注意一点，如果将 bind() 函数创建的实例作为构造器函数使用时，它的 this 值会绑定为新创建的对象，而不再是 bind() 时绑定的对象了。


## 还有一点疑问

其实了解了上面几种调用方式后，我们对 this 应该说已经足够了解了。那么让我们来看一下下面这道题：

```js
var value = 1;

var foo = {
  value: 2,
  bar: function () {
    return this.value;
  }
}

//示例1
console.log(foo.bar()); // 2
//示例2
console.log((foo.bar)()); // 2
//示例3
console.log((foo.bar = foo.bar)()); // 1
//示例4
console.log((false || foo.bar)()); // 1
//示例5
console.log((foo.bar, foo.bar)()); // 1
```

看完后是不是有一种头大的感觉，怎么，怎么感觉没一个对得上......

要想解决这些疑惑，或许我们应该从 this 的规范入手，下面推荐一篇文章[《JavaScript深入之从ECMAScript规范解读this 》](https://github.com/mqyqingfeng/Blog/issues/7)。作者给我们提供了一个很好的思路去从规范的角度理解 this 。

## 写在最后

好了，关于 this 的总结暂时先到这里了。对于一般的 this 指向问题，我们了解这四种调用模式其实就已经足够了，而且通过调用方式的角度是最适合我们理解的。当然了我在文章的末尾提出的那道问题，涉及到的就不是仅仅使用调用模式就能够理解的，我们需要从规范的角度去理解，我是认为这种方式应该是最有效的，但很不容易理解，后边有时间我会再从规范角度去总结一下 this 的指向问题。


**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**