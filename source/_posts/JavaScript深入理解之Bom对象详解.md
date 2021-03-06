---
title: JavaScript深入理解之Bom对象详解
date: 2018-08-13 23:05:36
tags:
 - 前端
 - JavaScript
 - Bom对象
categories:
 - 前端
 - JavaScript
---

![JavaScript深入理解之Bom对象详解文章配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu24-1.jpg)

## 写在前面

当我们在 Web 中使用 JavaScript 的时候，BOM 对象——浏览器对象模型起着至关重要的作用。BOM 提供了很多对象，用于访问浏览器的功能。熟悉了解 BOM 对象对我们 WEB 开发有着重要的帮助，下面是我的一些理解和总结。

<!-- more -->

## BOM 对象定义

BOM是指浏览器对象模型，它是对一系列在浏览器环境中使用对象的统称，这些对象提供了访问浏览器的功能。

在BOM对象中，window对象是最顶层对象，在浏览器环境中它是一个Global全局对象，其它对象（如：DOM对象）对是这个对象的属性（子对象）。BOM对象是与内容无关，主要用于管理浏览器窗口及窗口之间的通讯。下面是BOM对象的组成结构：

![配图24-2](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu24-2.png)

## window 对象

window 对象表示一个浏览器窗口或者一个 frame 框架，它处于对象层次的最顶端。在浏览器中 window 对象有双重角色。它既是通过 JavaScript 访问浏览器窗口的一个接口，提供处理浏览器窗口的方法和属性。又是 ECMAScript 中规定的 Global 对象，因此在全局作用域中声明的变量和函数都会成为 window 对象的属性和方法。

举个例子

```js
var age = 29;

console.log(window.age); // 29
```

简单来说，Global 对象只是作为 window 对象的一部分来实现的，window 对象相对于 Global 对象扩展了处理浏览器窗口的方法和属性。

## DOM（document）相关对象

DOM 可以认为是 BOM 的一个子集，DOM 中文档操作相关对象，如：Node、Document、Element 等 DOM 节点类型对象，都是做为window对象的子属性出现的。

document 是 window 对象的子属性，它是一个 Document 对象实例，表示当前窗口中文档对象。通过该对象，可以对文档和文档中元素、节点等进行操作。

## frames 对象

frames 对象是一个集合，表示当前页面中使用的子框架。如果页面中使用了框架，将产生一个框架集合 frames ，在集合中可以用数字下标（从0开始）或名字索引框架。集全中的每一个对象，包含了框架的页面布局信息，以及每一个框架所对应的 window 对象。

## navigator 对象

navigator 是指浏览器对象，该对象提供了当前正在使用的浏览器的信息。navigator 对象中的属性是只读的，在 W3C 在HTML5 标准中，对该对象进行了规范。由于浏览器的同，该对象的具体值可能有所区别。

## history对象

history 对象来保存浏览器历史记录信息，也就是用户访问的页面。浏览器的前进与后退功能本质上就是 history 的操作。history 对象记录了用户浏览过的页面，通过该对象提供的 API 可以实现与浏览器前进/后退类似的导航功能。

## location对象

location是一个静态对象，该对象是对当前窗口URL地址的解析。该对象提供了可以访问URL中不同部分的信息属性，通过location对象也可以实现页面或锚点跳转等功能。

## screen对象

screen对象中包含了用户显示器屏幕相关信息。通过该对象，可以访问用户显示器屏幕宽、高、色深等信息。

## 写在最后

最近感觉总结一些东西的时候，没有抓住重点，很多时候时间都浪费在一些没有必要的知识点上。对于 BOM 的了解其实没必要太深入，其实主要是知道这个东西是什么，太细节的东西深究起来也没太大的必要。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！** 