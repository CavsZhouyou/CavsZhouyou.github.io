---
title: JavaScript深入理解之执行上下文
date: 2018-03-24 19:34:43
tags:
 - 前端
 - JavaScript
 - 执行上下文
 - 执行环境
categories:
 - 前端
 - JavaScript
---


![JavaScript深入理解之执行上下文 文章配图](http://p2p4htzmu.bkt.clouddn.com/peitu11-1.jpg)

## 写在前面

JavaScript 中的执行上下文（ Execution Context ）相信大家都不陌生，也就是我们常说的执行环境。想要弄懂 JavaScript 代码的执行过程，执行上下文是其中最重要的一个知识点。理解执行上下文有助于我们理解函数作用域链、变量对象和 this 指向等。这两天查阅了很多资料，下面是我对执行上下文的一个理解和总结。

<!--more-->

## 代码解析

众所周知 JavaScript 是一种描述型的脚本语言，不同于 C# 或者 java ，它不需要编译成中间语言，而是由 JS 引擎动态的解析和执行。那么 JavaScript 引擎是如何解析和执行一段 JavaScript 代码的呢？让我们先来看一个小例子。

```js

test(); // this is a function!

function test(){
    console.log("this is a function!");
}
```

从上面我们可以看出，test 函数调用在它的声明之前，按道理应该调用失败才对！而事实是 test 函数能够被正常调用，原因是 test 函数的声明被提升了，使得 test 函数在整个代码执行期间都可以访问到。那声明是什么时候提升的呢？就是在 JavaScript 引擎对代码解析的时候。

因此我们一定要记住，JavaScript 引擎在执行一段可执行代码之前，会先进行准备工作，也就是对这段代码进行解析（也可以称为预处理）。这个阶段会根据**可执行代码**创建相应的**执行上下文（ Execution Context ）**，也就是做声明提升等工作（后边会详细讲解）。然后在代码解析完成后才开始代码的执行。

### 注意

这里我们需要注意的一点是， JavaScript 引擎解析执行代码的过程是一个边执行边解析的过程，解析发生在执行一段可执行代码之前。举个例子，当执行到一个函数的时候，就会先对这个函数进行解析，然后再执行这个函数。

## 可执行代码

上面我们提到了，在代码解析阶段会根据不同的可执行代码创建相应的执行上下文，那么可执行代码有哪些呢？其实可以分为下面三种：

* 全局执行代码，在执行所有代码前，解析创建全局执行上下文。
* 函数执行代码，执行函数前，解析创建函数执行上下文。
* eval执行代码，运行于当前执行上下文中。

## 执行上下文的组成

执行上下文定义了变量或函数有权访问的其他数据，决定了它们各自的行为。每一个执行上下文都由以下三个属性组成。

* 变量对象（Variable object，VO）

* 作用域链(Scope chain)
 
* this

## 执行上下文栈

执行每一段可执行代码时都会对应创建一个执行上下文，那么我们是如何来管理这些执行上下文的呢？JavaScript 引擎创建了执行上下文栈（Execution context stack，ECS）来管理执行上下文。 

下面我们通过一个例子来看一下执行上下文压栈是如何工作的。

```js
var a = "global var";

function foo(){
    console.log(a);
}

function outerFunc(){
    var b = "var in outerFunc";
    console.log(b);
    
    function innerFunc(){
        var c = "var in innerFunc";
        console.log(c);
        foo();
    }
    
    innerFunc();
}

outerFunc();
```

代码首先进入Global Execution Context，然后依次进入outerFunc，innerFunc和foo的执行上下文，执行上下文栈就可以表示为：

![配图11-2](http://p2p4htzmu.bkt.clouddn.com/peitu11-2.png)

JavaScript 开始要解释执行代码的时候，最先遇到的就是全局代码，所以 JavaScript 引擎会先解析创建全局执行上下文，然后将全局执行上下文压栈。然后当执行流进入一个函数时，会先解析创建函数的执行上下文，然后将它的执行上下文压栈。而在函数执行之后，会将其执行上下文弹栈，弹栈后执行上下文中所有的数据都会被销毁，然后把控制权返回给之前的执行上下文。注意，全局执行上下文会一直留在栈底，直到整个应用结束。

## 写在最后

本篇文章只是对执行上下文做一个初步的介绍，为后面讲解执行上下文的三个属性作一个铺垫。先了解创建执行上下文这个过程，然后再去分析过程里边的细节。本片文章纯属于个人的学习总结，如果文章中存在错误或观点不当的地方，也希望大家指出！