---
title: JavaScript深入理解之继承
date: 2018-08-04 20:13:51
tags:
 - 前端
 - JavaScript
 - 继承
categories:
 - 前端
 - JavaScript
---

![JavaScript深入理解之继承](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu20-1.jpg)


## 写在前面

继承是面向对象语言中最重要的一个概念。许多面向对象语言都支持两种继承方式：接口继承和实现继承。接口继承只继承方法和签名，而实现继承则继承实际的方法。由于在 JavaScript 中函数没有签名，因此无法实现接口继承，只支持实现继承。

<!-- more -->

## 原型链

在 ECMAScript 中描述了原型链的概念，并将原型链作为实现继承的主要方法。其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。这一部分已经在前面总结过了，就不再多说了。

缺点：

1. 包含引用类型的原型属性会被所有实例属性共享，容易造成属性的修改混乱。

2. 在创建子类型的实例时，不能向超类型的构造函数中传递参数。

基于以上问题，在实践中很少会单独使用原型链。

## 借用构造函数

借用构造函数的思想主要是在子类型的构造函数中调用超类型构造函数。如下面的例子所示

```js
function SuperType(){
    this.colors = ["red", "blue", "green"];
}

function SubType(){
    //继承了 SuperType

    SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors);  //"red,blue,green,black"

var instance2 = new SubType();
console.log(instance2.colors);  //"red,blue,green"

```

优点：可以在子类型构造函数中向超类型构造函数添加参数

缺点：和构造函数模式一样的问题，所有的方法都在构造函数中定义，因此就无法做到函数的复用。而且超类型的原型中定义的方法，对于子类型而言也是不可见的。

基于以上问题，借用构造函数的技术也是很少单独使用的。


## 组合继承

组合继承指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。这种方法的主要思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。如下面的例子所示：

```js
function SuperType(name){
    this.name = name
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function(){
    console.log(this.name);
}

function SubType(name, age){
    
    //继承属性
    SuperType.call(this,name);

    this.age = age;
}

//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    console.log(this.age);
}

var instance1 = new SubType("james",9);
instance1.colors.push("black");
console.log(instance1.colors);  //"red,blue,green,black"
instance1.sayName(); // "james"
instance1.sayAge(); // 9

var instance2 = new SubType("kobe",10);
console.log(instance2.colors);  //"red,blue,green"
instance2.sayName(); // "kobe"
instance2.sayAge(); // 10

```

优点：组合继承避免了原型链和借用构造函数的缺陷，融合了它们的优点，成为 JavaScript 中最常用的继承模式。而且，instanceof 和 isPropertyOf() 也能够用于识别基于组合继承创建的对象。

缺点：调用了两次超类的构造函数，导致基类的原型对象中增添了不必要的超类的实例对象中的所有属性。

## 原型式继承

原型式继承的主要思路是可以基于已有的对象创建新的对象，同时还不必因此创建自定义类型。如下面的例子所示

```js
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}
```
简单来说这个函数的作用就是，传入一个对象，返回一个原型对象为该对象的新对象。

ECMAScript 5中新增了 Object.create() 方法规范了原型式继承。这个方法接收两个参数，一个是将被用作新对象原型的对象，一个是为新对象定义额外属性的对象（可选）。

注意第二个参数的格式与 Object.defineProperties() 方法的第二个参数格式相同。以这种方式指定的任何属性都会覆盖原型对象上的同名属性。在第二个参数为空的情况下，该方法与 object() 方法的行为相同。

优点：可以实现基于一个对象的简单继承，不必创建构造函数

缺点：与原型链中提到的缺点相同，一个是传参的问题，一个是属性共享的问题。

## 寄生式继承

寄生式继承的思路是，创建一个仅用于封装继承过程的函数，该函数在内部以某种方式增强对象，最后返回这个对象。如下面的例子所示

```js
function createAnother(original){
    
    var clone = object(original); //通过调用函数创建一个新对象
    
    clone.sayHi = function(){  // 某种方式增强这个对象
        console.log("hi");
    }

    return clone;  // 返回这个对象
}

var person = {
    name: "james"
}

var anotherPerson = createAnother(person);

anotherPerson.sayHi(); // "hi"
```

优点： 在主要考虑对象而不是自定义类型和构造函数的情况下，实现简单的继承。

缺点：使用该继承方式，在为对象添加函数的时候，没有办法做到函数的复用。


## 寄生式组合继承

前面我们提到过了组合继承的缺点，由于调用了两次超类的构造函数，导致基类的原型对象中增添了不必要的超类的实例对象中的所有属性。

寄生式组合继承就是用来解决这个问题，它与组合继承不同的地方主要是，在继承原型时，我们继承的不是超类的实例对象，而是原型对象是超类原型对象的一个实例对象，这样就解决了基类的原型对象中增添了不必要的超类的实例对象中的所有属性d的问题。

我们可以封装继承原型时函数如下：

```js
function inheritPrototype(subType, superType){

    var prototype = object(superType.prototype); // 创建原型对象是超类原型对象的一个实例对象
  
    prototype.constructor = subType; // 弥补因为重写原型而失去的默认的 constructor 属性。
 
    subType.prototype = prototype; // 实现原型继承
}
```

优点：效率高，避免了在 SubType.prototype 上创建不必要的属性。与此同时还能保持原型链不变，开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

## 写在最后

对于继承相关的知识就总结到这里，感觉总结一遍对每种继承方式的理解就更深刻了一些，加油！

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**