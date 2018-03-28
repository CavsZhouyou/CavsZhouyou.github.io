---
title: JavaScript深入理解之Array类型详解
date: 2018-03-16 19:40:12
tags:
 - 前端
 - JavaScript
 - Array
categories:
 - 前端
 - JavaScript
---


![JavaScript深入理解之Array详解 配图](http://p2p4htzmu.bkt.clouddn.com/peitu8-1.jpg)


## 写在前面

Array类型可以说是ECMAScript中最常用的一种类型了。和其他语言中的Array类型相比，ECMAScript中的Array类型可以说是厉害多了，它能比它们多做很多事情，当然也同样存在更多使用上的隐患。以前也遇到了很多关于Array使用上的问题，一直没有时间整理总结，下面总结一下我对Array的理解。

<!--more-->

## 定义

>Array 对象用于在单个的变量中存储多个值。

**与其他数组的不同:**

* ECMAScript 数组中的每一项都可以保存不同类型的数据，也就是说你可以第一个位置保存一个数值，第二个位置保存一个字符串，第三个位置保存一个Boolean值。只要你有需要，这样的数组都是合法的。

    ```js
    var arr = [1 , "hello" , true];
    ```

* 数组的长度大小是可变的，也就是说你可以随意的向数组增添数据，如果大小不够时，数组会自动增长以容纳新的数据。

    ```js
    var arr = [1 , 2];
    console.log(arr.length); // 2

    arr.push(3);
    console.log(arr.length); // 3
    ```

## 创建方式

创建数组的方式一共有两种。

* 使用Array构造函数

    ```js
    var colors = new Array(); //注意这里的new操作符是可省的
    ```

* 使用数组字面量表示法。数组字面量由一对包含数组项的方括号表示，多个数组项之间用逗号隔开。这种方法不会调用Array的构造函数。

    ```js
    var names = []; // 创建一个空数组

    var colors = ["red","blue","white"] //创建一个包含3项的数组
    ```


## length属性

数组的项数保存在它的length属性中，这个属性不是只读的，也就是说我们可以通过修改这个属性来从数组末尾移除项或添加新的项。

```js
var colors = ["red","blue","white"];

console.log(colors[2]) // "white"

colors.length = 2; //通过减小数组的长度来移除末尾项

console.log(colors[2]); // undefined
```

利用length属性也可以很方便地在数组末尾添加新项，下面是我们平常使用最多的一种写法

```js
var colors = ["red","blue","white"];

colors[colors.length] = "black"; // 在数组，末尾添加新元素

console.log(colors[3]); // "black"
```

## 数组检测

自从ES3做出规定以后，如何判断某个对象是否为数组就成为了一个经典问题。一共有两种方法可以判断。

* 对于一个网页或者一个全局作用域而言，使用instanceof就可已解决问题。不过如果网页中存在多个框架，就意味着存在多个全局作用域,从而存在两个版本以上的Array的构造函数。如果我们从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中的原生创建的数组分别具有不同的构造函数，这种情况下就无法使用instanceof来判断了。
    
    ```js
    if(value instanceof Array){
        //对数组执行某些操作
    }
    ```

* 针对上面的情况，ES5新增克Array.isArray()方法。这个方法可以最终确定某个值到底是不是数组，而不管它是在哪个全局执行环境中创建的。
   
    ```js
    if(Array.isArray(value)){
        //对数组进行某些操作
    }
    ```

## 数组转换

在实际应用中，很多时候我们需要将数组转换为字符串的形式来表示，那么应该如何转换呢？将一个数组转化为字符串形式表示一共有三种方法。

* 使用toString()方法。调用数组的toString()方法会返回数组的每个值的字符串形式拼接而成的一个以逗号分隔的字符串。实际上，为了创建这个字符串，会调用数组的每一项的toString()方法。

    ```js
    var colors = ["red","blue","white"];

    console.log(colors.toString()); // red,blue,white

    ```

* 使用toLocalString()方法。调用这个方法得到的结果其实和调用toString()方法得到的结果多数情况下是相同的。不同的是,使用该方法创建字符串时，会调用数组的每一项的toLocalString()方法，而不再是toString()方法。
    
    ```js
    var colors = ["red","blue","white"];

    console.log(colors.toLocalString()); // red,blue,white

    ```
    
* 使用join()方法。在很多情况下，我们希望能够自己定义分隔符，这个时候使用join方法就很方便了。join方法接受一个参数，即用作分隔符的字符串，然后返回包含所有数组项的字符串。注意如果不传入参数，则默认使用逗号作为分隔符。

    ```js
    var colors = ["red","blue","white"];

    console.log(colors.join("||")); // red||blue||white

    ```
## 栈方法

我们知道栈是一种先进后出的数据结构，也就是最新添加的项最后被移除。而且栈中项的插入和移除只发生在一个位置——栈的顶部。ECMAScript为数组专门提供了push()和pop()方法，以此来实现类似栈的行为。

push()方法可以接收任意数量的参数，并把他们逐个添加到数组末尾，并返回修改后数组的长度。pop()方法则从数组末尾移除最后一项，减少数组的length值，然后返回移除的项。

```js
var colors = ["red","blue"];
var count = colors.push("green"); //入栈，推入一项

console.log(colors); // ["red","blue","green"]
console.log(count); // 3


var item = colors.pop(); // 弹栈，弹出最后一项

console.log(colors); // ["red","blue"]
console.log(item); // "green"

```

## 队列方法

栈数据结构的访问规则是先进后出，而队列数据结构的访问规则是先进先出。队列在列表的末端添加项，从列表的前端移除项。由于push()方法已经实现了向数组末端添加项的方法，因此模拟队列只需要一个从数组前端获取项的方法就行了。实现这一操作的数组方法是shift(),它能够移除数据组中的第一个项并返回该项，同时将数组长度减一。结合使用shift()和push()方法，就可以像使用队列一样使用数组。

```js
var colors = ["red","blue"];
var count = colors.push("green"); //入队列，推入一项

console.log(colors); // ["red","blue","green"]
console.log(count); // 3


var item = colors.shift(); // 出队列，移除数组第一项

console.log(colors); // ["blue","green"]
console.log(item); // "red"

```

ECMAScript还为数组提供了一个unshift()方法。这个方法的作用刚好和shift()方法相反，它能在数组前端添加任意个项并返回新数组的长度。因此使用unshift()和pop()方法，可以从相反的方向来模拟队列，即在数组的前端添加项，从数组末端移除项。

## 重排序方法

数值中有两种可以直接用来重排序的方法，一种是reverse()方法，另一种是sort()方法。

* reverse()方法可以反转数组项的顺序，注意该方法是直接在原始数组上进行操作的。

    ```js
    var numbers = [1,2,3,4,5,6,7,8,9];

    numbers.reverse();

    console.log(numbers); // [9,8,7,6,5,4,3,2,1]

    ```

* sort()方法，在默认情况下会按升序序列排列数组项——即最小的值位于最前面，最大的值排在最后面。不过这个升序的比较，比较的不是数值的大小，为了实现排序，sort()方法会调用每个数组项的toString()转型方法，然后比较得到的字符串，以确定该如何排序。即使数组中的每一项都是数值，sort()方法比较的也是字符串。

    ```js
    var numbers = [0,1,5,10,15];

    numbers.sort();

    console.log(numbers); // [0,1,10,15,5]
    ```

    从上面的例子中可以看出，虽然数值5小于10，但在进行字符串比较时，"10"则位于"5"的前面，于是数组的顺序就修改了。这种方法显然大多数情况下不会符合我们的需要，那么有什么其他办法吗？其实sort()方法可以接收一个比较函数作为参数，以便我们指定哪个值位于哪个值的前面。
    比较函数接收两个参数，如果第一个参数应该位于第二个参数则返回一个负数，如果两个参数相等则返回0，如果第一个参数应该位于第二个之后则返回一个正数。

    ```js
    var numbers = [0,1,5,10,15];

    numbers.sort(compare);

    console.log(numbers); // [15,10,5,1,0]

    function compare(value1,value2){
        retrun value2 - value1
    }
    ```

## 操作方法

ECMAScript为数组操作提供了很多方法，这些方法在我们实际应用中经常会用到，下面我们来一一介绍。

### concat()方法

conact()方法可以基于当前数组中的所有项和传入参数构建一个新的数组。这个方法会先创建当前数组的一个副本，然后将接收到的参数添加到这个参数的末尾，最后返回这个新的数组。需要注意的是:
* 在没有给concat()方法传递参数的情况下，它只是复制当前数组并返回脚本。
* 如果传递给concat()方法的是一个或多个数组，则该方法会将这些数组的每一项都添加到结果数组中。
* 如果传递的值不是数组这些值会被简单地添加到数组的末尾。
* 原始数组不受影响

```js
var colors = ["red","green","blue"];
var colors2 = colors.concat("yellow",["black","brown"]);

console.log(colors); // red,green,blue
console.log(coloers2); // red,green,blue,yellow,black,brown
```

### slice()方法

slice()方法它能够基于当前数组中的一个或多个项创建一个新数组，简单理解为可以用于截取数组中的一部分。slice()方法可以接受一或两个参数，即要返回项的起始和结束位置。需要注意的是：
* 在只有一个参数的情况下，slice()方法返回从该参数指定位置开始到当前数组末尾的所有项。
* 如果有两个参数，该方法返回起始位置和结束位置之间的项，**但不包括结束位置的项**。
* 如果slice()方法的参数中有一个负数，则用数组长度加上该数来确定相应的位置。
* 如果结束位置小于初始位置，则返回空数组
* 原始数组不受影响

```js
var colors = ["red","green","blue","yellow","purple"];
var colors2 = colors.slice(1);
var colors3 = colors.slice(1,4);
var colors4 = colors.slice(-2,-1); // 相当于调用slice(3,4)

console.log(colors2); // green,blue,yellow,purple
console.log(colors3); // green,blue,yellow
console.log(colors4); // yellow
```

### splice()方法

splice()方法可以说是最强大的数组方法了，它的用法有很多。splice()方法接受三个参数，起始位置，要删除的项数和要插入的任意数量的项，splice()方法始终会返回一个数组，该数组汇中包含从原始数组中删除的项，如果没有删除任何项，则返回一个空数组。使用这种方法的方式有如下三种:
* 删除: 可以删除任意数量的项，只需要指定两个参数：起始位置和要删除的项数。
  
    ```js
    var colors = ["red","green","blue","yellow"];
    var removed = colors.splice(0,2) //删除数组中的前两项

    console.log(colors); // blue,yellow
    console.log(removed) // red,green
    ```

* 插入: 可以向指定位置插入任意数量的项，需要提供三个参数，起始位置，0(要删除的项数)和要插入的项。

    ```js
    var colors = ["red","green","blue","yellow"];
    var removed = colors.splice(1,0,"white","black"); //从位置一开始插入两项

    console.log(colors); // red,white,black,green,blue,yellow
    console.log(removed) // 返回的是一个空数组 
    ```

* 替换: 可以向指定位置插入任意数量的项。插入的项数不必和删除的项数相等，需要三个参数，起始位置，要删除的项数和要插入的任意数量的项。

    ```js
    var colors = ["red","green","blue","yellow"];
    var removed = colors.splice(1,1,"white","black"); //删除位置一项，然后插入两项

    console.log(colors); // red,white,black,blue,yellow
    console.log(removed); // green
    ```

## 位置方法

ES5为数组实例添加了两个位置方法：indexOf()和lastIndexOf()。这两个方法都接收两个参数：要查找的项和表示查找起点位置的索引(可选)。其中，indexOf()方法从数组的开头(位置0)开始向后查找，lastIndexOf()方法则从数组的末尾开始向前查找。
这两个方法都是用于返回要查找的项在数组中第一次匹配到的位置，或者在没有找到的情况下返回-1。注意在比较第一个参数与数组中的每一项时，会使用全等操作符`===`，也就是说，要求查找的项必须严格相等。

```js
var numbers = [1,2,3,4,5,4,3,2,1];

console.log(numbers.indexOf(4)); // 3 

console.log(numbers.lastIndexOf(4)); // 5

console.log(nmbers.indexOf(4,4)); // 从位置4开始向后匹配

console.log(numbers.lastIndexOf(4,4)) // 从位置4开始向前匹配 
```

## 迭代方法

相信我们平时使用中，对数组操作最多的都是数组的遍历迭代。ES5中为数组定义了五个方法。每个方法都接收两个参数，要在数组没每一项上运行的函数和运行该函数的作用域对象——影响this的值(可选)。传入这些方法中的函数会接受三个参数：数组项的值，该项在数组中的位置和数组对象本身。根据使用方法的不同，这个函数执行后的返回值可能也可能不会影响方法的值。下面我们分别介绍这五个迭代方法，注意这些方法都不会修改原始数组包含的值。

### every()方法

every()方法，该方法对数组中的每一项都运行给定函数，如果该函数对每一项都返回true，则返回true。这种方法适用于判断数组中的项是否都满足某个条件。该方法的返回值为Boolean。
```js
var numbers = [1,2,3,4,5,6,7,8,9];

//判断数组项否都小于10
var everyResult = numbers.every(function(item , index , array){
    retrun item < 10;
});

console.log(everyResult); // true 证明数组项都小于10
```

### some()方法

some()方法，该方法对数组中的每一项运行给定函数，如果该函数汇中某一项返回true，则返回true。这种方法和every()方法使用很像，不过some()方法用于判断数组中是否含有满足某个条件的项。该方法的返回值为Boolean。
```js
var numbers = [1,2,3,4,5,6,7,8,9];

//判断数组中是否有小于10的项
var someResult = numbers.some(function(item , index , array){
    retrun item < 10;
});

console.log(someResult); // true 证明数组中含有小于10的项
```

### filter()方法

filter()方法，对数组中的每一项运行给定函数，返回该函数会返回true的项的数组。这种方法适用于数组的过滤，得到数组中满足特定条件的项。该方法会返回一个包含符合条件项的数组。

```js
var numbers = [1,2,3,4,5,6,7,8,9];

// 返回数组中大于5的项
var filterResult = numbers.filter(function(item , index , array){
    retrun item > 5;
});

console.log(filterResult); // [6,7,8,9]

```

### map()方法

map()方法，对数组的每一项运行给定函数，返回每次函数调用的结果的数组。这种方法使用在需要对数组中的元素整体进行一些操作的时候情况。该方法会返回一个对数组每项进行操作后的数组。
```js
var numbers = [1,2,3,4,5,6,7,8,9];

// 对数组中的每一项的乘2
var mapResult = numbers.map(function(item , index , array){
    retrun item * 2;
});

console.log(mapResult); // [2,4,6,8,10,12,14,16,18]

```

### forEach()方法

forEach()方法，对数组中的每一项运行给定函数。这种方法本质上与for循环迭代数组一样。这个方法没有返回值。

```js
var numbers = [1,2,3,4,5,6,7,8,9];

numbers.forEach(function(item , index , array){
    // 执行某些操作
});
```

## 归并方法

ES5中新增了两个归并数组的方法: reduce() 和 reduceRight()。这两个方法都会迭代数组的所有项，然后构建一个最终返回的值。其中，reduce()方法从数组的第一项开始，逐个遍历到最后。而reduceRight方法则从数组的最后一项开始，向前遍历到第一项。
这两个方法都接收两个参数：一个在每一项上调用的函数和作为归并基础的特殊值(可选)。传入reduce()和reduceRight()的函数接收4个参数：前一个值、当前值、项的索引和数组对象这个函数返回的任何值都会作为第一个参数自动传给下一项，也就是说第一个参数均为上一次迭代返回的结果。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项。第二个参数就是数组的第二项。使用reduce() 和 reduceRight()方法可以执行求数组中所有值之和的操作。
```js
var values = [1,2,3,4,5];
var sum = values.reduce(function(pre, cur, index, arrray){
    return pre + cur;
})

console.log(sum); //15

var sumRight = values.reduceRight(function(pre, cur, index, arrray){
    return pre + cur;
})

console.log(sumRight); //15

```

## 写在最后

不总结不知道，一种总结才发现Array涉及到的知识点和用法真的太多了。以前只会使用一些简单的循环来判断，没有发现其实ECMAScript已经为我们提供了很多方便的方法。这样总结了一下，感觉对Array对象的认识更深了一些，收获挺大的。希望我的学习总结可以给你带来帮助。本文章纯属个人的学习总结，如果文章中存在错误，也希望大家能够给我指出！