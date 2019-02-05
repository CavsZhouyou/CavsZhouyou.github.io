---
title: JavaScript深入理解之对象创建
date: 2018-08-04 11:00:36
tags:
 - 前端
 - JavaScript
 - 对象创建
 - 构造函数
 - new
categories:
 - 前端
 - JavaScript
---


![JavaScript深入理解之对象创建文章配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu19-1.jpg)


## 写在前面

在 JavaScript 中虽然 Object 构造函数或对象字面量都可以用来创建单个对象，但是这些方法都有一个明显的缺点：使用同一个接口创建很多对象，会产生大量的重复代码。为了解决这些问题，人们提出了很多对象创建的解决办法，下面是我对 JavaScript 对象创建的一些理解和总结。

<!-- more -->

## 工厂模式

工厂模式是软件工程领域一种广为人知的设计模式，这种模式抽离了创建对象的具体过程。考虑到在 ECMAScript 中无法创建类，开发人员发明以一种函数，用函数来封装以特定接口创建对象的细节。如下面的例子所示

```js
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };
    return o;
}

var person1 = createPerson("james"，9，"student");

var person2 = createPerson("kobe"，9，"student");
```

优点：解决了创建多个相似对象时，代码的复用问题

缺点：使用工厂模式创建的对象，没有解决对象识别的问题（就是怎样知道一个对象的类型是什么）

## 构造函数模式

前面我们提到过，ECMAScript 中的构造函数可用来创建特定类型的对象。像 Object 和 Array 这样的原生构造函数，在运行时会自动出现在执行环境中。此外我们也可以创建自定义的构造函数，从而定义对象类型的属性和方法。我们使用构造函数的方法可以将前面的例子重写如下

```js
function createPerson(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    };
    return o;
}

var person1 = new createPerson("james"，9，"student");

var person2 = new createPerson("kobe"，9，"student");
```

当我们使用构造函数实例化一个对象的时候，对象中会包含一个 \_\_proto\_\_ 属性指向构造函数原型对象，而原型对象中则包含一个 constructor 属性指向构造函数。因此在实例对象中我们可以通过原型链来访问到 constructor 属性，从而判断对象的类型。

优点：解决了工厂模式中对象类型无法识别的问题，并且创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型。

缺点：我们知道 ECMAScript 中的函数是对象，在使用构造函数创建对象时，每个方法都会在实例对象中重新创建一遍。拿上面的例子举例，这意味着每创建一个对象，我们就会创建一个 sayName 函数的实例，但它们其实做的都是同样的工作，因此这样便会造成内存的浪费。


## 原型模式

我们知道，我们创建的每一个函数都有一个 prototype 属性，这个属性指向函数的原型对象，这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。我们通过使用原型对象可以让所有的对象实例共享它所包含的属性和方法，因此这样也解决了代码的复用问题。如下面所示：

```js
function Person(){

}

Person.prototype.name = "james";
Person.prototype.age = 9;
Person.prototype.job = "student";
Person.prototype.sayName = function(){
    alert(this.name);
}

var person1 = new Person();
person1.sayName(); // "james"

var person2 = new Person();
person2.sayName(); // "james"


console.log(person1.sayName === person2.sayName) // true
```

与构造函数模式不同的是，原型对象上的属性和方法是有所有实例所共享的。也就是说，上面 person1 和 person2 访问的都是同一组属性和同一个 sayName() 函数。

优点：解决了构造函数模式中多次创建相同函数对象的问题，所有的实例可以共享同一组属性和函数。

缺点：
1. 首先第一个问题是原型模式省略了构造函数模式传递初始化参数的过程，所有的实例在默认情况下都会取得默认的属性值，会在一定程度上造成不方便。

2. 因为所有的实例都是共享一组属性，对于包含基本值的属性来说没有问题，但是对于包含引用类型的值来说（例如数组对象），所有的实例都是对同一个引用类型进行操作，那么属性的操作就不是独立的，最后导致读写的混乱。我们创建的实例一般都是要有属于自己的全部属性的，因此单独使用原型模式的情况是很少存在的。


## 组合使用构造函数模式和原型模式

创建自定义类型的最常见方式，就是组合使用构造函数模式和原型模式。构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。使用这种模式的好处就是，每个实例都会拥有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。而且这中混成模式还支持向构造函数传递参数，可以说是及两种模式之长。

下面我们通过这种方法来重写一下上面的例子

```js
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
}

Person.prototype = {
    constructor: Person,
    sayName: function(){
        alert(this.name);
    }
}

var person1 = new createPerson("james"，9，"student");

var person2 = new createPerson("kobe"，9，"student");

console.log(person1.name); // "james"
console.log(person2.name); // "kobe"
console.log(person1.sayName === person2.sayName); // true
```

优点：采用了构造函数模式和原型模式的优点，这种混成模式是目前使用最广泛，认同度最高的一种创建自定类型的方法。

缺点：由于使用了两种模式，因此对于代码的封装性来说不是很好。


## 动态原型模式

由于上面混成模式存在封装性的问题，动态原型模式是解决这个问题的一个方案。这个方法把所有信息都封装到了构造函数中，而在构造函数中通过判断只初始化一次原型。下面我们来看一下例子

```js

function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;

    if(typeof this.sayName !== "function" ){

        Person.prototype.sayName: function(){
            alert(this.name);
        } 
    } 
}

var person1 = new createPerson("james"，9，"student");

person1.sayName(); // "james"
```

注意在 if 语句中检查的可以是初始化后应该存在的任何属性或方法，不必要检查每一个方法和属性，只需要检查一个就行。

优点：解决了混成模式中封装性的问题


## 寄生构造函数模式

如果在前面几种模式不适用的情况下，可以使用寄生构造函数模式。这种模式的基本思想是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后返回新创建的对象。如下面的例子所示：

```js
function Person(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };
    return o;
}

var person1 = new Person("james"，9，"student");
```

通过上面的例子我们可以发现，其实这个模式和工厂模式基本上是一摸一样的，只不过我们是采用 new 操作符最后来创建对象。

注意在构造函数不返回值的情况下，默认会返回新创建的对象，而通过在构造函数的末尾添加一个 return 语句，可以重写调用构造函数时返回的值。

优点：我对这个模式的理解是，它主要是基于一个已有的类型，在实例化时对实例化的对象进行扩展。这样既不用修改原来的构造函数，也达到了扩展对象的目的。

缺点：和工厂模式一样的问题，不能依赖 instanceof 操作符来确定对象的类型。

## 稳妥构造函数模式

Douglas Crockford 发明了 JavaScript 中的稳妥对象这个概念。所谓稳妥对象，指的就是，没有公共属性，而且其方法也不使用 this 的对象。稳妥对象最适合在一些安全的环境中（这些环境中会禁止使用 this 和 new），或者在防止数据被其他应用程序改动时使用。

稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：一是新创建的对象的实例方法不引用 this ；二是不使用 new 操作符调用构造函数。因此我们可以将前面的例子改写如下：

```js
function Person(name, age, job){

    //创建要返回的对象
    var o = new Object();

    //可以在这里定义私有变量和函数

    //添加方法
    o.sayName = function(){
        console.log(this.name);
    }

    //返回对象
    return o;
} 

var person1 =  Person("james"，9，"student");

person1.sayName(); // "james"
```

优点：以上面为例，除了 sayName 方法外，没有别的方法可以访问数据成员，这就是稳妥构造函数提供的安全性。

缺点：和寄生构造函数一样，没有办法使用 instanceof 操作符来判断对象的类型

## 写在最后

简单总结了一下对象的创建方式，感觉收获很多，理解了每一种模式的优缺点后，以后才能更好的去应用。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**