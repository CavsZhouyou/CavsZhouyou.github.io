---
title: JavaScript深入理解之闭包
date: 2018-08-07 19:29:49
tags:
 - 前端
 - JavaScript
 - 闭包
categories:
 - 前端
 - JavaScript
---

![JavaScript深入理解之闭包文章配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu22-1.jpg)

## 写在前面

闭包一直是 JavaScript 中一个非常重要的概念。以前一直很难理解这个概念，主要是对 JavaScript 中的执行原理不熟悉。通过对前面执行上下文和垃圾收集的总结，终于可以对这个概念好好进行分析了。下面是我的一些理解和总结。

<!-- more -->


## 定义 

闭包是指有权访问另一个函数作用域中的变量的函数。创建闭包的最常见的方式，就是在一个函数内部创建另一个函数。

我们举一个例子

```js
function outer(){
    var a = 1;

    function inner(){
        console.log(a);
    }

    inner(); // 1
}

outer(); 
```

在 inner 函数中，我们可以通过作用域链访问到 a 变量，因此这就可以算是构成了一个闭包，因为 a 变量是其他函数作用域中的变量。

当然这和我们平时遇到的一些闭包问题可能不太一样，不过这些问题的实质其实都是在当前函数作用域中访问另一个函数作用域中的变量。

## 实例分析

下面我们来看一个经典的闭包的例子

```js
function createFunctions(){
    var result = new Array();

    for(var i=0; i < 10; i++){
        result[i] = function(){
            console.log(i);
        }
    }
     return result;
}

var result = createFunctions();

result[0](); // 9 
result[1](); // 9 
result[2](); // 9 
result[3](); // 9 
result[4](); // 9 
result[5](); // 9 
```

我们创建了一个函数用来创建一个函数数组，希望数组中的每个函数都打印自己的索引值，即位置0的函数打印0，位置1的函数打印1。但实际上最终每个函数打印的都是10，这是为什么呢？下面我们从程序执行开始分析。

### 全局代码执行

首先在执行全局代码之前， JavaScript 引擎会首先对全局代码进行解析，创建全局执行上下文 globalContext 。

1. 首先会创建全局执行上下文的第一个属性全局变量对象 globalVO 。

    ```js
    // 省略全局对象其他属性

    globalContext.globalVO = {
        createFunctions: reference to function createFunctions,
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
   createFunctions.[[Scope]] = globalContext.[[Scope]];
   ```

全局执行上下文创建好后，进入到全局执行代码的执行阶段，首先将全局执行上下文压入执行上下文栈中，然后按顺序依次执行代码。

 1. 在执行代码前判断得到全局执行上下文中的 this 指向 `globalVO`;
   
 2. 执行代码 `createFunctions();`进入createFunctions()函数执行阶段。

### createFunctions() 函数执行阶段

在执行 createFunctions() 函数前，Javascript 引擎会先对 createFunctions 函数代码进行解析，创建 createFunctions 函数的执行上下文 createFunctionsContext。

1. 创建 createFunctions 函数执行上下文的活动对象 createFunctionsAO。

    ```js
    createFunctionsContext.createFunctionsAO = {
        result: undefined,
        i: undefined,
    };
    ```

2. 复制 createFunctions 函数的 `[[Scope]]` 属性，为函数执行上下文的作用域链赋值，然后将函数执行上下文的活动对象压入作用域链顶端。
    
    ```js
    createFunctionsContext.[[Scope]] = [
        createFunctionsContext.createFunctionsAO,
        globalContext.globalVO
    ];
    ```

createFunctions 函数执行上下文创建后，进入函数代码的执行阶段，将 createFunctionsContext 压入执行上下文栈中，按顺序依次执行代码。

1. 在执行代码前，根据函数调用方式，判断得到 createFunctionsContext 的 this 指向为 `globalVO`。

2. 执行第一行代码 `var result = new Array();` ，为 createFunctionsAO 的 result 属性赋值。

   ```js
    createFunctionsContext.createFunctionsAO = {
        result: reference to Array result,
        i: undefined,
    };
   ```
3. 然后执行 for 循环语句代码，执行第一次循环，i赋值为0，然后为每一个数组项创建一个函数对象。
   ```js
    createFunctionsContext.createFunctionsAO = {
        result: reference to Array result,
        i: 0,
    };
   ```
4. 循环代码执行完后，此时的 createFunctionsAO 为
   ```js
    createFunctionsContext.createFunctionsAO = {
        result: reference to Array result,
        i: 9,
    };
   ```

5.  然后执行代码`return result;`，返回函数数组。

6.  createFunctions 函数的执行上下文弹栈，控制权交回全局执行上下文，接着执行代码 `result[0]();` ，进入 result[0] 函数的执行阶段。

### result[0]函数执行阶段

在执行 result[0]() 函数前，Javascript 引擎会先对 result[0] 函数代码进行解析，创建 result[0] 函数的执行上下文 result0Context。

1. 创建 result0 函数执行上下文的活动对象 result0AO。

    ```js
    result0Context.result0AO = {
    };
    ```

2. 复制 result0 函数的 `[[Scope]]` 属性，为函数执行上下文的作用域链赋值，然后将函数执行上下文的活动对象压入作用域链顶端。
    
    ```js
    result0Context.[[Scope]] = [
        result0Context.result0AO,
        createFunctionsContext.createFunctionsAO,
        globalContext.globalVO
    ];
    ```

result0 函数执行上下文创建后，进入函数代码的执行阶段，将 result0Context 压入执行上下文栈中，按顺序依次执行代码。

1. 在执行代码前，根据函数调用方式，判断得到 result0Context 的 this 指向为 `globalVO`。

2. **重点来了，执行代码 `console.log(i);` ，首先 Javascript 引擎会搜寻当前函数变量对象，当前函数的变量对象找不到 i 值时，会根据作用域链搜寻其他函数作用域中的变量，因此我们在 createFunctionsAO 中找到了 i 变量，此时的 i 变量保存的是 i 叠加后结果，因此函数打印的结果为 9。这里我们还需注意的一点是一般来说，在 createFunctions 函数执行完后 createFunctionsAO 就应该销毁了，但是由于我们在 result0Context 的作用域链中保留了对它的引用，因此在垃圾收集的时候，判断可以通过引用找到该对象，因此它就不会被清除掉，而是继续保留在内存中，让我们访问**。

3. 代码执行完成后，继续执行后面 result[1]() 函数执行代码。


### 解决办法

那么我们如果想让这个函数符合我们的预期的话，我们应该怎么办呢？我们可以做一下这样的修改

```js
function createFunctions(){
    var result = new Array();

    for(var i=0; i < 10; i++){
        result[i] = (function(num){
            return function(){
                console.log(num);
            }
        })(i);
    }
     return result;
}

var result = createFunctions();

result[0](); // 0 
result[1](); // 1 
result[2](); // 2 
result[3](); // 3 
result[4](); // 4 
result[5](); // 5 
```

相信通过上面的分析，大家应该能够明白这样写原因，这里就不再分析了。


## 写在最后

闭包这个概念其实涉及到的知识点很过，如果执行上下文不熟悉，对垃圾收集机制不熟悉，理解起来其实是很模棱两可的。通过这样一个整体的分析，对闭包也有了一个更好的理解。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**