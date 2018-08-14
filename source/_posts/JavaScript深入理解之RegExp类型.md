---
title: JavaScript深入理解之RegExp类型详解
date: 2018-03-22 20:21:22
tags:
 - 前端
 - JavaScript
 - RegExp
 - 正则表达式
categories:
 - 前端
 - JavaScript
---


![JavaScript深入理解之RegExp类型详解文章配图](http://p2p4htzmu.bkt.clouddn.com/peitu10-1.jpg)


## 写在前面

我们在验证输入或查找字符时经常需要用到正则表达式，正则表达式的强大毋庸置疑。在 JavaScript 中也提供了 RegExp 类型来作为相应的支持。下面是我对 RegExp 类型的看法和总结。

<!--more-->

## RegExp 对象的创建

ECMAScript 通过 RegExp 类型来支持正则表达式。一共有两种创建方式，一种是以字面量形式来定义的正则表达式，另一种是使用 RegExp 构造函数来创建正则表达式。下面我们来分别介绍一下。

### 字面量形式

使用下面类似 Perl 的语法，就可以创建一个正则表达式。

```js
var expression = / pattern / flags 
```

其中模式( pattern )部分可以是任何简单或复杂的正则表达式，可以包含字符类、限定符、分组、向前查找以及反向引用。每个正则表达式可以带有一个或多个标志，用以标明正则表达式的行为。正则表达式的匹配模式支持下列三个标志。

* `g`: 表示全局模式，即模式将应用于所有字符串，而非在发现第一个匹配项时就停止检索。
* `i`: 表示不区分大小写模式，即在确定匹配项时忽略模式与字符串的大小写。
* `m`: 表示多行匹配模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模式匹配的项。 

下面我们来看几个例子

```js

// 匹配字符串中所有"at"的实例
var pattern = /at/g;

// 匹配字符串中的第一个"at"，不区分大小写
var pattern2 = /at/i;


// 匹配字符串中所有的"at"，不区分大小写
var pattern3 = /at/gi;

```

注意，模式中使用到的元字符需要转义。正则表达式中的元字符包括: `( ) [ ] { } \ ^ $ | ? * + .`
因为这些元字符在正则表达式中有特殊的用途，所以如果需要匹配的字符串中含有这些字符，这必须对其转义，如下:

```js
// 匹配字符串中的第一个" [at] ",不区分大小写
var pattern = /\[at\]/i ;
```

### RegExp 构造函数

另一种创建正则表达式的方法是使用 RegExp 构造函数，它接收两个参数，一个是要匹配的字符串模式，另一个是可选的标志字符串。只要是可以用字面量定义的任何任何表达式，都可以用构造函数来定义。如下:

```js
// 匹配字符串中所有"at"的实例
var pattern = new RegExp("at","g");
```

这里需要注意，我们传递给构造函数的两个参数都是字符串的形式，所以这里会涉及到一个转义问题。因为是由字符串转化为正则表达式，所以所有的元字符都必须进行双重转义，如下面的例所示。

```js
// 字面量形式
/\[at\]/

// 构造函数中使用的双重定义的字符串
"/\\[at\\]/"

```

### 注意

这里对于这两种方法有一个区别需要注意，在 ES3 的规定中，使用正则表达式字面量始终会共享一个 RegExp 实例，而使用构造函数创建的每一个新 RegExp 实例都是一个新实例。如下

```js

// 在ES3 环境下

var re = null,
    i = 0;

// 使用字面量形式创建
for(i = 0;i < 0;i++){
    re = /cat/g;
    console.log(re.test("catastrophe"));
}

// 结果为 true false true false true false true false true false


// 使用构造函数创建
for(i = 0;i < 0;i++){
    re = new RegExp("cat","g");
    console.log(re.test("catastrophe"));
}

//结果为 true true true true true true true true true true

```
下面我们来分析以下上面两个例子。

第一个例子中因为正则表达式字面量共享一个实例，所以实例属性不会重置。在第一次调用 test() 方法找到了 "cat" 后返回 true ，但第二次调用是从索引为3的字符(上一次匹配的末尾)开始的，所以第二次就找不到它了，返回 false 。由于会测试到字符串末尾，所以下一次调用 test() 就又从头开始了，所以继续返回 true 和 false 的循环。

第二个例子使用构造函数在每次循环中创建正则表达式。因为每次迭代都会创建一个新的 RegExp 实例，所以每次调用都会返回 true 。

上面我们说了，这是在 ES3 下的表现。在 ES5 中对此作出了明确规定，使用字面量形式时也会像直接调用 RegExp 构造函数一样，每次创建新的 RegExp 实例。所以第一个例子在 ES5 下每次也都会返回 true 。因为现在 ES5 普及度已经很高了，所以上面的区别仅做一个了解，在实际应用中多数情况下不用考虑这个问题。

## RegExp 实例属性

RegExp 的每个实例都有以下属性，通过这些属性可以取得有关模式的各种信息。

* global ：布尔值，表示是否设置了 g 标志。
* ignoreCase ：布尔值，表示是否设置了 i 标志。
* multiline ： 布尔值，表示是否设置了 m 标志。
* lastIndex ：整数，表示开始搜索下一个匹配项的字符位置，从0算起。
* source ： 正则表达式的字符串表示，**按照字面量的形式而非传入构造函数中的字符串模式返回**。

```js
var pattern = new RegExp("\\[at\\]","g");

console.log(pattern.global); // true
console.log(pattern.ignoreCase); // false
console.log(pattern.multiline); // false
console.log(pattern.lastIndex); // 0 
console.log(pattern.source); // "\[at\]at"
```
通过这些属性来获得一个正则表达式的各方面信息，其实这在实际应用中没有多大的用处，因为这些信息基本都包含在了模式声明中，所以仅做了解就好。

## RegExp 实例方法

RegExp 实例一共有两个主要的方法 exec() 方法和 test() 方法，下面我们分别看一下它们的用法。

### exec() 方法

exec() 方法是专门为捕获组设计的。该方法接受一个参数，就是需要应用模式的字符串，然后返回包含第一个匹配项信息的数组，如果没有匹配项则返回 null。返回的数组虽然是 Arrey 的实例，但包含了两个额外的属性：index 和 input。index表示匹配项在字符串中的位置，而 input 则表示应用正则表达式的字符串。在数组中，第一项是与整个模式匹配的字符串，其他是与模式中匹配的字符串(如果模式中没有捕获组，则数组仅包含第一项)。
```js
var text = "mom and dad and baby";
var pattern = /mom( and dad( and baby)?)?/gi;

var matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches.input); // "mom and dad and baby"
console.log(matches[0]); // "mom and dad and baby"
console.log(matches[1]); // " dad and baby"
console.log(matches[2]); // " and baby"
```
对于 exec() 方法而言，无论模式中是否设置了全局标志，它每次都只会返回第一个匹配项。对于同一个字符串多次调用 exec() 方法，在不设置全局标志的情况下，将始终返回第一个匹配项的信息，在设置了全局标志的情况下，每次调用 exec() 方法否会在字符串中继续查找新匹配项。 

### test() 方法

test() 方法适用于只想知道目标字符串与某个模式是否匹配，但不需要知道文本内容的情况。该方法接受一个字符串参数，在模式与该参数匹配时返回 true ，不匹配时返回 false。

```js
var text = "000-00-0000";
var pattern =  /\d{3}-\d{2}-\d{4}/;

console.log(pattern.test(text)); // true
```

### 其他方法

RegExp 实例继承的 toLocalString() 和 toString() 方法都会返回正则表达式的字面量，而 valueOf 则返回正则表达式本身。

## RegExp 构造函数属性

RegExp 构造函数包含一些属性(这些属性在其他语言中被看为静态属性)。这些属性适用于作用域中的所有正则表达式，并且基于所执行的最近一次正则表达式操作而变化。这些属性分别有一个长属性名和一个短属性名( Opera 是例外，它不支持短属性名)。

| 长属性名     | 短属性名 | 说明                                                               |
| ------------ | -------- | ------------------------------------------------------------------ |
| input        | $_       | 最近一次要匹配的字符串。 Opera未实现此属性                         |
| lastMatch    | $&       | 最近一次的匹配项。 Opera未实现此属性                               |
| lastParen    | $+       | 最近一次的捕获组。 Opera未实现此属性                               |
| leftContext  | $`       | input 字符串中 lastMatch 之前的字符串                              |
| rightContext | $'       | input 字符串中 lastMatch 之后的字符串                              |
| multitline   | $*       | 布尔值，表示是否所有的表达式都使用多行模式。 IE和Opera未实现此属性 |

```js
var text = "this has been a short summer";
var pattern = /(.)hort/g;

if(pattern.test(text)){
    console.log(RegExp.input); // this has been a short summer
    console.log(RegExp.leftContext); // this has been a
    console.log(RegExp.rightContext); // summer
    console.log(RegExp.lastMatch); // short
    console.log(RegExp.lastPaern); // s 
    console.log(RegExp.multitline); // false 
}
```
和前面所说的一样，例子中使用的长属性名可以使用短属性名来代替。只不过，由于这些短属性名大都不是有效的 ECMAScript 字符，所以必须通过方括号语法来访问。

```js
console.log(RegExp["$*"]); // false
```

除了上面介绍到的几个属性外，还有多大9个用于存储捕获组的构造函数属性。访问这些属性的语法是RegExp.$1到RegExp.$9，分别用于存储第一到第九个匹配的捕获组。在调用 exec() 方法和 test() 方法时，这些属性会被自动填充。

## 模式的局限性

尽管 ECMAScript 中的正则表达式还是比较完备的，但还是缺少某些语言(特别是 Perl )所支持的高级正则表达式特性。下面列出了 ECMAScript 正则表达式不支持的特性。

* 匹配字符串开始和结尾的 \A 和 \Z 锚，但支持以插入符号`^`和美元符号`$`来匹配字符串的开始和结尾。
* 先后查找，但支持向前查找
* 原子组
* Unicode 支持(单个字符除外如 \uFFFF )
* 命名的捕获组，但支持编号的捕获组
* 单行和无间隔匹配模式
* 条件匹配
* 正则表达式注释

## 写在最后

关于 RegExp 类型的总结就到这里，其实一些复杂的属性在平常很少会用到，但这些东西还是要做一个了解，避免需要用到的时候没有头绪。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**