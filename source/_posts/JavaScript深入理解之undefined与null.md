---
title: JavaScript深入理解之undefined与null
date: 2018-03-10 10:59:26
tags: 
 - 前端
 - JavaScript
 - 数据类型
 - undefined
 - null
categories: 
 - 前端
 - JavaScript
---


![配图7-1](http://p2p4htzmu.bkt.clouddn.com/peitu7-1.jpg)


## 写在前面

JavaScript中有两个特殊的值，undefined与null。平常在写项目时，遇到需要判断一个值是否为空的时候，我总会想到undefined与null。既然都是代表空值(以前我就是这么认为的)，那么它们有没有区别呢？反正我是一直傻傻分不清楚，看了又忘了。最近查阅了一些资料，才发现自己以前真的是误解它们了，知错就改，下面总结一下它们的用法和区别。

<!--more-->

## 基本数据类型

在介绍undefined与null之前，我们先来了解一下ECMAScript中的数据类型。在ECMAScript中有五种简单数据类型(也称为基本数据类型): Undefined、Null、Boolean、Number 和 String 。还有一种复杂数据类型——Object。

没错，首字母大写的Undefined与Null其实都属于ECMAScript中的基本数据类型。这两个数据类型是五种数据类型中最特殊的两个类型，因为它们都只有唯一的一个值，分别是undefined与null，就是我们今天要介绍的两个主角。

## Undefined 类型

### 定义

上面我们说过了，Undefined类型只有一个值，就是特殊的undefined，在两种情况下我们会得到undefined：

1. 声明了一个变量，但未对其初始化时，这个变量的值就是undefined。

   ```js
   var data;
   console.log(data === undefined); //true
   ``` 

   那么我么是否可以显式地把一个变量初始化为undefined呢，答案是可以的。

   ```js
   var data = undefined;
   console.log(data === undefined); //true

   var value = 1;
   console.log(data); //1

   value = undefined;
   console.log(data === undefined); // true
   ``` 

   一般而言，我们不存在需要显式地把一个变量设置为undefined值的情况，因为对于未经初始化的值默认就会取得undefined值，而已经初始化的值再将其赋值为undefined来表示空值是没有意义且不可取的。况且字面值undefined的主要目的以用于比较，来区分空对象指针(后面我们会介绍到这指的就是null)与未经初始化的变量的情况。

2. 对未定义的变量执行typeof操作符也会返回undefined

   ```js
   //data变量未定义
   var value;

   console.log(typeof data); // "undefined"
   console.log(typeof value); // "undefined"
   ``` 
   
   这里我们没有使用`===`来判断，因为对于尚未声明过的变量，我们只能执行一项操作，即使用typeof操作符检测其数据类型，使用其他的操作都会报错。
 
   ```js
   //data变量未定义

   console.log(data === undefined); //报错
   ``` 
   
   结果表明对未初始化和未声明的变量执行typeof操作符都返回了undefined值，这个结果有其逻辑上的合理性。因为虽然这两种变量从技术角度看有本质区别，但实际上无论对哪种变量也不可能执行真正的操作。

还有其他几种情况也会返回undefined，比如一个函数如果没有使用return语句指定返回值，就会返回一个undefined值，或者调用函数时没有传参数值，参数同样也会被初始化为undefined值。这些都是属于上面两种情况在代码中的体现，这里就不单独解释了。 

   
### 全局属性 window.undefined

从上面的例子我们可以看出，无论我们是否初始化过变量，都可以给变量赋值为undefined。其实这里用于赋值的undefined不是一个值，它是一个属性名，undefined是全局对象的一个属性，也就是说，它是全局作用域的一个变量，即`window.undefined`，而`window.undefined`这个属性的值才是前面所说的原始值undefined。`data = undefined;`这就相当于把一个变量`window.undefined`的值赋值给另一个变量`data`，这个值就是原始值undefined。其实在JavaScript代码中,我们看到的undefined大多数情况指的都是`window.undefined`(本篇文章中多数情况下也是，原始值undefined除外)，原始值undefined多数情况下只存在于文档或规范中,不存在于JavaScript代码中(具体可以理解为代码中参与判断、比较或赋值的都是`window.undefined`，而在控制台中输出，或函数中返回的则是原始值undefined)。

```js
console.log(window.undefined); //原始值undefined
``` 
   
注意，在ES3之前其实是没有原始值undefined这个值的，第三版引入这个值，其实是为了正式区分空对象指针(后面我们会介绍到这指的就是null)与未经初始化的变量。在ES3中,`window.undefined`就是一个普通的属性，我们完全可以把它的值改为任何真值。但从ES5之后,`window.undefined`成了一个不可写,不可配置的数据属性,它的值永远是undefined。

### 局部属性 undefined

大家可能注意到了，上面我提到的是在大多数情况下undefined指的都是`window.undefined`，那还有什么其他情况吗？其实在ECMAScript中，undefined不是一个保留字，这意味着什么呢？也就是说我们可以将undefined作为一个局部变量来使用，就像局部作用域中任何其他普通变量一样，没有任何特殊性，我们可以对其赋予任何类型的值。

```js
(function() {
    var undefined = 'not is undefined';
    console.log(undefined); //"not is undefined"
    console.log(typeof undefined) // "string"
})()
``` 

我们可以看到undefined的值和类型都已经改变，这样的做法是非常不友好的，这样会使我们的代码难以维护和排错。

### undefined 判断

如何判断一个变量是否为undefined，这里有两种方法。

1. 使用严格相等符`===`或不相等操作符`!==`来决定一个变量是否拥有值，这里不使用标准相等操作符`==`,是因为标准相等符还会会检查变量是不是为null，但是严格相等操作符不会检查。null不等同于undefined，这点我们会在后面讲到。

2. 使用typeof操作符，这种方式我们在上面已经使用过了，对未定义的变量检测时只能使用这种方式，要不然会出现报错。
 
### void 0 

上面我们提到过了，undefined作为局部变量使用是可以被重写的，那么如果我们使用下面这种判断方式，是有风险的。
```js
if(data === undefined){
    //do something
}
``` 

那么我们怎样做才能确保万无一失呢?让我们先来了解一下void运算符,官方文档是这样解释的:

>The void operator evaluates the given expression and then returns undefined.
>
>void 运算符 对给定的表达式进行求值，然后返回 undefined

什么意思呢？就是使用void对后面的表达式求值，无论结果是多少，都会返回原始值undefined。因此我们可以用`void 0`来代替undefined进行判断，因为`void 0`始终返回的都是原始值undefined。

```js
   var data;
   console.log(data === void 0); //true
``` 

## Null类型

### 定义

Null类型是第二个只有一个值的数据类型，这个特殊的值就是null。值 null 是一个字面量，它不像undefined 是全局对象的一个属性。从逻辑角度来看，null值表示一个空对象指针，指示变量未指向任何对象。把 null 作为尚未创建的对象，也许更好理解。在 APIs 中，null 常在返回类型是对象，但没关联值的地方使用，就像下面一个例子。

```js
   //document.getElementById() 可以返回对拥有指定 ID 的第一个对象的引用

   var $container = document.getElementById("container"); // 注意:container是不存在的

   console.log($container); // null
``` 

### typeof null

当我们使用typeof操作符检测null值，我们理所应当地认为应该返"Null"类型呀，但是事实返回的类型却是"object"。
```js
   var data = null;
   console.log(typeof data); // "object"
``` 

是不是很奇怪？其实我们可以从两方面来理解这个结果

* 一方面从逻辑角度来看，null值表示一个空对象指针，它代表的其实就是一个空对象，所以使用typeof操作符检测时返回"object"也是可以理解的。

* 另一方面，其实在JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是 0。由于 null 代表的是空指针（大多数平台下值为 0x00），因此，null的类型标签也成为了 0，typeof null就错误的返回了"object"。在ES6中，当时曾经有提案为历史平凡, 将type null的值纠正为null, 但最后提案被拒了,所以还是保持"object"类型。

### null 判断

null的判断可以使用严格相等符`===`或不相等操作符`!==`判断，不使用标准相等符的原因是因为undefined会影响判断结果。和undefined不一样，不能使用typeof来判断一个值是否为null，原因上边已经讲了，使用typeof来检测null会返回"object",这样的话我们是没办法判断的。
```js
   if(data === null){
       console.log("data中没有保存对象引用！");
   }
``` 

### null 使用

那么我们在什么情况下需要将变量赋值为null呢？这里我想到的有两种情况。

* 如果定义的变量在将来用于保存对象，那么最好将该变量初始化为null，而不是其他值。换句话说，只要意在保存对象的变量还没有真正保存对象，就应该明确地让该变量保存null值，这样有助于进一步区分null和undefined。

* 当一个数据不再需要使用时，我们最好通过将其值设置为null来释放其引用，这个做法叫做解除引用。不过解除一个值的引用并不意味着自动回收改值所占用的内存。解除引用的真正作用是让值脱离执行环境，以便垃圾收集器在下次运行时将其回收。解除引用还有助于消除有可能出现的循环引用的情况。这一做法适用于大多数全局变量和全局对象的属性，局部变量会在它们离开执行环境时(函数执行完时)自动被解除引用。

## undefined 与 null

实际上undefined值是派生自null值的，因此ECMA-262规定对它们的相等性测试要返回true:

```js
   console.log(null == undefined); //true
``` 

因为使用的是标准相等符`==`,这个操作符出于目的会转换其操作数为相同类型后再做比较，如果我们使用严格相等符比较，我们会发现它们是不相等的，因为严格相等符不会进行类型转换，然而undefined与null属于不同的类型，所以不相等。

```js
   console.log(null === undefined); //false
``` 

尽管null和undefined有这样的关系，但上面我们已经提到过了，它们的用途完全不同，我们在平常使用时一定要学会区分。

## 参考文献

《JavaScript高级程序设计(第三版)》
 [JavaScript中undefined和null的区别](http://www.css88.com/archives/6236)
 [JavaScript 参考文档 null](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)
 [JavaScript 参考文档 undefined](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)
 [JavaScript 参考文档 typeof](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof)
 [(void 0) 与 undefined 之间的小九九](https://juejin.im/post/591153ceac502e450281e22f)


## 写在最后

花了一些时间来总结undefined与null的用法和区别，以前一直没有注意，经过这次总结才发现它们所代表的的意义和用法完全不同。可能在平常使用的时候我们不需要考虑这么多问题，但通过重新看一些东西，总结一些东西，通过这样的方式对我来说收获挺大的。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**