---
title: Vue历险记之可输入下拉框的实现
date: 2018-06-12 22:24:53
tags:
 - 前端
 - JavaScript
 - vue
 - select下拉框
categories:
 - 前端
 - JavaScript
 - Vue历险记
---


![Vue历险记之可输入下拉框的实现 文章配图](http://p2p4htzmu.bkt.clouddn.com/peitu16-1.jpg)

## 写在前面

最近在做项目时，遇到了一个奇怪的需求，简单来说就是在下拉框选择后，可以对选中项的文本进行修改，选项文本和用户的输入之间属于双向绑定的关系。以前没有遇到过这种需求....在尝试了几种方法后，总算把这个问题解决了，下面简单谈一下我的方法。

<!-- more -->

## Demo地址

[可输入拉框demo地址](http://cavszhouyou.top/Demo-Display/inputSelectBoxDemoDisplay.html)


## 大致思路

我刚开始的想法是能不能直接在每个 option 里边嵌套一个 input 标签,然后把每个 input 的 value 值与 option 的文本值进行绑定，实践后这种方法行不通。

我现在的方法是使用一个 input 标签来动态获取 select 框当前选项的文本值，然后当 input 输入时，动态修改对应 option 的文本值，以此来实现双向绑定的功能。

因为我们使用的分别是 input 和 select ，因此我们需要对这两个标签进行一些样式的修改以此来让它们看着就像是一个可输入的 select 框。主要是调整 input 的位置，使其覆盖住 select 框中原本文本的位置，然后设置 input 隐藏掉右边的边框。因为它们的数据是双向绑定的，因此这样看起来就是一个可输入的下拉框。

## 具体实现

```html
<!DOCTYPE html>
<!--[if lt IE 7]>      <html class="no-js lt-ie9 lt-ie8 lt-ie7"> <![endif]-->
<!--[if IE 7]>         <html class="no-js lt-ie9 lt-ie8"> <![endif]-->
<!--[if IE 8]>         <html class="no-js lt-ie9"> <![endif]-->
<!--[if gt IE 8]><!-->
<html class="no-js">
<!--<![endif]-->

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>可输入下拉框demo展示</title>
    <meta name="description" content="可输入下拉框demo展示">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="">

    <style type="text/css">
        #container {
            position: absolute;
            top: 200px;
            left: 0;
            right: 0;
            margin: 0 auto;
            width: 600px;
            height: 300px;
            text-align: center;
        }

        .select-box {
            width: 300px;
            height: 50px;
            padding-left: 20px;
            font-size: 18px;
        }

        .input-box {
            position: absolute;
            margin-left: -300px;
            box-sizing: border-box;
            border: 1px solid #9e9e9e;
            border-right: none;
            width: 280px;
            height: 50px;
            padding-left: 23px;
            font-size: 18px;
        }
    </style>
</head>


<body>
    <!--[if lt IE 7]>
            <p class="browsehappy">You are using an <strong>outdated</strong> browser. Please <a href="#">upgrade your browser</a> to improve your experience.</p>
        <![endif]-->


    <div id="container">
        <h3>可输入下拉框</h3>
        <select class="select-box" v-model="selectValue">
            <option v-for="(item, index) in options" :key="index" :value="item.value">{{item.text}}</option>
        </select>
        <input type="text" class="input-box" ref="inputBox" :value="inputValue" @input="changeValue()">
    </div>



    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <script>
        var container = new Vue({
            el: '#container',
            data: {
                selectValue: 1000,
                options: [{
                    value: 1000,
                    text: "骑士总冠军"
                }, {
                    value: 2000,
                    text: "湖人总冠军"
                }, {
                    value: 3000,
                    text: "火箭总冠军"
                }, {
                    value: 4000,
                    text: "马刺总冠军"
                }]
            },
            computed: {

                // 获取select框中所选文本值
                inputValue: function () {
                    const self = this;
                    var value;

                    //遍历找到对应文本值
                    this.options.forEach(function (item, index) {
                        if (item.value === self.selectValue) {
                            value = item.text;
                        }
                    });

                    return value;
                }
            },
            methods: {

                // input输入修改options中的对应数值
                changeValue: function () {
                    const self = this;
                    var text = this.$refs.inputBox.value;

                    //遍历修改对应文本值
                    this.options.forEach(function (item, index) {
                        if (item.value === self.selectValue) {
                            item.text = text;
                        }
                    });

                }
            }
        })
    </script>
</body>

</html>
```


## 写在最后

这是我暂时想到的解决办法，以前没有遇到过这样的问题，实现的过程还是挺有趣的，以后有时间再看看能不能再完善一些。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**