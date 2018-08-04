---
title: JavaScript深入理解之执行上下文示例分析
date: 2018-04-23 17:34:39
tags:
 - 前端
 - JavaScript
 - 执行上下文
 - 执行环境
categories:
 - 前端
 - JavaScript
---

![JavaScript深入理解之执行上下文示例分析 文章配图](http://p2p4htzmu.bkt.clouddn.com/peitu15-1.jpg)

## 写在前面

前面已经对执行上下文的创建及其三个属性做了总结，本篇文章通过一个示例分析来将前面的总结的知识点做一个串联与回顾。不了解执行上下文的同学可以先看我前面的几篇对执行上下文的总结。

<!--more-->

## 讲解示例

我们先来看一看下边这段代码

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
执行这段代码，我们很容易判断得到如下结果：

```js
var in outerFunc
var in innerFunc
global var
```
下面我们来分析一下这段代码的执行过程。

## 执行分析

### 全局代码执行

首先在执行全局代码之前， JavaScript 引擎会首先对全局代码进行解析，创建全局执行上下文 globalContext 。

1. 首先会创建全局执行上下文的第一个属性全局变量对象 globalVO 。

    ```js
    // 省略全局对象其他属性

    globalContext.globalVO = {
        a: undefined,
        foo: reference to function foo,
        outerFunc: reference to function outerFunc
    };
    ```

2. 然后将 globalVO 压入全局上下文作用域链的顶端。

   ```js
   globalContext.[[Scope]] = [ 
       globalContext.globalVO 
   ];
   ```

3. 然后将全局上下文的作用域链赋值给 globalVO 中所有的函数的 `[[Scope]]` 属性。

   ```js
   foo.[[Scope]] = globalContext.[[Scope]];

   outerFunc.[[Scope]] = globalContext.[[Scope]];
   ```

全局执行上下文创建好后，进入到全局执行代码的执行阶段，首先将全局执行上下文压入执行上下文栈中，然后按顺序依次执行代码。

 1. 在执行代码前判断得到全局执行上下文中的 this 指向 `globalVO`;
 
 2. 执行第一行代码 `var a = "global var";` ,为 globalVO 中的 a 属性赋值为 `"global var"`;
 
    ```js
     globalContext.globalVO = {
        a: "global var",
        foo: reference to function foo,
        outerFunc: reference to function outerFunc
    };
    ```
 3. 执行代码 `outerFunc();`，进入outerFunc()函数执行阶段。

### outerFunc() 函数执行阶段

在执行 outerFunc() 函数前，Javascript 引擎会先对 outerFunc 函数代码进行解析，创建 outerFunc 函数的执行上下文 outerFuncContext。

1. 创建 outerFunc 函数执行上下文的活动对象 outerFuncAO。

    ```js
    outerFuncContext.outerFuncAO = {
        b: undefined,
        innerFunc: reference to function innerFunc
    };
    ```

2. 复制 outerFunc 函数的 `[[Scope]]` 属性，为函数执行上下文的作用域链赋值，然后将函数执行上下文的活动对象压入作用域链顶端。
    
    ```js
    outerFuncContext.[[Scope]] = [
        outerFuncContext.outerFuncAO,
        globalContext.globalVO
    ];
    ```
3. 然后将作用域链赋值给 outerFuncAO 中所有函数的 `[[Scope]]` 属性。

    ```js
    innerFunc.[[Scope]] = outerFuncContext.[[Scope]];
    ```

outerFunc 函数执行上下文创建后，进入函数代码的执行阶段，将 outerFuncContext 压入执行上下文栈中，按顺序依次执行代码。

1. 在执行代码前，根据函数调用方式，判断得到 outerFuncContext 的 this 指向为 `globalVO`。

2. 执行第一行代码 `var b = "var in outerFunc";` ，为 outerFuncAO 的 b 属性赋值。

   ```js
    outerFuncContext.outerFuncAO = {
        b: "var in outerFunc",
        innerFunc: reference to function innerFunc
    };
   ```
3. 然后执行代码 `console.log(b);` ，打印 b 的属性值。

4. 然后执行代码 `innerFunc();` ，进入 innerFunc 函数的执行阶段。

### innerFunc() 函数执行阶段

在执行 innerFunc() 函数前，Javascript 引擎会先对 innerFunc 函数代码进行解析，创建 innerFunc 函数的执行上下文 innerFuncContext。

1. 创建 innerFunc 函数执行上下文的活动对象 innerFuncAO。

    ```js
    innerFuncContext.innerFuncAO = {
        c: undefined
    };
    ```

2. 复制 innerFunc 函数的 `[[Scope]]` 属性，为函数执行上下文的作用域链赋值，然后将函数执行上下文的活动对象压入作用域链顶端。
    
    ```js
    innerFuncContext.[[Scope]] = [
        innerFuncContext.innerFuncAO,
        outerFuncContext.outerFuncAO,
        globalContext.globalVO
    ];
    ```

innerFunc 函数执行上下文创建后，进入函数代码的执行阶段，将 innerFuncContext 压入执行上下文栈中，按顺序依次执行代码。

1. 在执行代码前，根据函数调用方式，判断得到 innerFuncContext 的 this 指向为 `globalVO`。

2. 执行第一行代码 `var c = "var in innerFunc";` ，为 innerFuncAO 的 c 属性赋值。

   ```js
    innerFuncContext.innerFuncAO = {
        c: "var in innerFunc"
    };
   ```

3. 然后执行代码 `console.log(c);` ，打印 c 的属性值。

4. 然后执行代码 `foo();` ，进入 foo 函数的执行阶段，注意这里我们是通过作用域链找到的 foo 函数的引用。

### foo() 函数执行阶段

在执行 foo() 函数前，Javascript 引擎会先对 foo 函数代码进行解析，创建 foo 函数的执行上下文 fooContext。

1. 创建 foo 函数执行上下文的活动对象 fooAO。

    ```js
    fooContext.fooAO = {
    };
    ```

2. 复制 foo 函数的 `[[Scope]]` 属性，为函数执行上下文的作用域链赋值，然后将函数执行上下文的活动对象压入作用域链顶端。
    
    ```js
    fooContext.[[Scope]] = [
        fooContext.fooAO,
        globalContext.globalVO
    ];
    ```

foo 函数执行上下文创建后，进入函数代码的执行阶段，将 fooContext 压入执行上下文栈中，按顺序依次执行代码。

1. 在执行代码前，根据函数调用方式，判断得到 fooContext 的 this 指向为 `globalVO`。

2. 然后执行代码 `console.log(a);` ，根据作用域链找到 a 的属性值，然后打印它。

### 执行结束阶段

我们先来看一下此时的执行上下文栈

![配图11-2](http://p2p4htzmu.bkt.clouddn.com/peitu11-2.png)

函数执行完成后是一个执行上下文弹栈的过程。

1. 在 foo() 函数代码执行完成后，执行上下文栈将 fooContext 弹栈，在没有其他引用的情况下，fooContext中的数据都将被销毁，然后将控制权交还给 innerFuncContext。

2. 继续执行 innerFunc 函数，因为没有其余可执行代码，所以 innerFunc() 函数执行完成，执行上下文栈将innerFuncContext 弹栈，在没有其他引用的情况下，innerFuncContext中的数据都将被销毁，然后将控制权交还给 outerFuncContext。

3. 继续执行 outerFunc 函数，因为没有其余可执行代码。所以 outerFunc() 函数执行完成，执行上下文栈将 outerFuncContext 弹栈，在没有其他引用的情况下，outerFuncContext中的数据都将被销毁，然后将控制权交还给 globalContext。

4. 继续执行全局代码，在应用退出时，globalContext 弹栈，整段代码执行结束。


## 写在最后

这个例子的分析基本结束了，通过这个示例分析，我对执行上下文中的三个属性间的联系有了更深刻的认识，对执行上下文也有了更深的理解，这样的分析对我们理解闭包等知识点有很大的帮助。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**