---
title: 通过JavaScript实现七牛云图片上传
date: 2018-02-06 19:54:47
tags:
- 七牛云
- JavaScript
categories: 
- 开发者手册
- 七牛云
---

![七牛云图片上传配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu2-1.jpg)

## 写在前面

最近在做一个图片分享网站类型的小 demo，我的想法是将用户上传的图片保存到七牛云上，然后将获取的链接存入数据库中。虽然以前写博客一直都是用七牛云来存储配图，但对于如何通过七牛云 JS 的 SDK 来完成上传操作还真是不太了解。在看了官网的教程后，有一些地方不是很明确，查了一些资料，所以总结一下前端如何通过 js 上传图片到七牛云。

<!-- more -->

## 官方文档

* [七牛云 Qiniu-JavaScript-SDK 官方文档](https://github.com/qiniu/js-sdk/#usage "七牛云Qiniu-JavaScript-SDK官方文档")

## 概述

> Qiniu-JavaScript-SDK （下文简称为 JS-SDK）适用于 IE8+、Chrome、Firefox、Safari 等浏览器，基于七牛云存储官方 API 构建，其中上传功能基于 Plupload 插件封装。开发者基于 JS-SDK 可以方便的从浏览器端上传文件至七牛云存储，并对上传成功后的图片进行丰富的数据处理操作。

## 安装

因为七牛云的 JS-SDK 是基于 Plupload 插件封装的，所以我们需要在引入七牛云的 js 依赖文件前，引入 Plupload 的相关 js 依赖文件。我这里介绍使用 NPM 安装的方法。

* Plupload 下载

    ```npm
    npm install Plupload --save-dev
    ```

* qiniu-js 下载
    ```npm
    npm install qiniu-js --save-dev
    ```

## 使用

在使用前我们需要在页面里引入相应的 js 依赖文件。

* 在页面中引入 plupload，plupload.full.min.js（生产环境）或 引入 plupload.dev.js 和 moxie.js（开发调试）。这些 js 文件可在 node_modules 文件夹下的 plupload 的文件夹中找到。
* 在页面中引入 qiniu.min.js（生产环境）或 qiniu.js（开发调试）这些 js 文件可在 node_modules 文件夹下的 qiniu-js 的文件夹中找到。

例如

```js
//import js 具体路径可能会不同
require("../../js/qiniu/plupload/plupload.dev.js");
require("../../js/qiniu/plupload/moxie.js");
require("../../js/qiniu/qiniu.js");
```

然后需要在页面中初始化 uploader，如下:

```js
var uploader = Qiniu.uploader({
    disable_statistics_report: false, // 禁止自动发送上传统计信息到七牛，默认允许发送
    runtimes: "html5,flash,html4", // 上传模式,依次退化
    browse_button: "pickfiles", // 上传选择的点选按钮，**必需**
    // 在初始化时，uptoken, uptoken_url, uptoken_func 三个参数中必须有一个被设置
    // 切如果提供了多个，其优先级为 uptoken > uptoken_url > uptoken_func
    // 其中 uptoken 是直接提供上传凭证，uptoken_url 是提供了获取上传凭证的地址，如果需要定制获取 uptoken 的过程则可以设置 uptoken_func
    uptoken: "<Your upload token>", // uptoken 是上传凭证，由其他程序生成
    // uptoken_url: "/uptoken", // Ajax 请求 uptoken 的 Url，**强烈建议设置**（服务端提供）
    // uptoken_func: function(file){    // 在需要获取 uptoken 时，该方法会被调用
    //    // do something
    //    return uptoken;
    // },
    get_new_uptoken: false, // 设置上传文件的时候是否每次都重新获取新的 uptoken
    // downtoken_url: '/downtoken',
    // Ajax请求downToken的Url，私有空间时使用,JS-SDK 将向该地址POST文件的key和domain,服务端返回的JSON必须包含`url`字段，`url`值为该文件的下载地址
    unique_names: false, // 默认 false，key 为文件名。若开启该选项，JS-SDK 会为每个文件自动生成key（文件名）
    save_key: false, // 默认 false。若在服务端生成 uptoken 的上传策略中指定了 `save_key`，则开启，SDK在前端将不对key进行任何处理
    domain: "<Your upload token>", // bucket 域名（就是你在七牛云上创建的存储空间的测试域名），下载资源时用到，如：'http://xxx.bkt.clouddn.com/' **必需**
    container: "container", // 上传区域 DOM ID，默认是 browser_button 的父元素，
    max_file_size: "10mb", // 最大文件体积限制
    flash_swf_url: "../plupload/Moxie.swf", //引入 flash,相对路径，这个也可以在 plupload 的文件夹中找到
    max_retries: 3, // 上传失败最大重试次数
    dragdrop: true, // 开启可拖曳上传
    drop_element: "photo-container", // 拖曳上传区域元素的 ID，拖曳文件或文件夹后可触发上传
    chunk_size: "4mb", // 分块上传时，每块的体积
    auto_start: true, // 选择文件后自动上传，若关闭需要自己绑定事件触发上传,
    //x_vars : {
    //    自定义变量，参考http://developer.qiniu.com/docs/v6/api/overview/up/response/vars.html
    //    'time' : function(up,file) {
    //        var time = (new Date()).getTime();
    // do something with 'time'
    //        return time;
    //    },
    //    'size' : function(up,file) {
    //        var size = file.size;
    // do something with 'size'
    //        return size;
    //    }
    //},
    init: {
        FilesAdded: function(up, files) {
            plupload.each(files, function(file) {
                // 文件添加进队列后,处理相关的事情
            });
        },
        BeforeUpload: function(up, file) {
            // 每个文件上传前,处理相关的事情
            console.log("上传开始");
        },
        UploadProgress: function(up, file) {
            // 每个文件上传时,处理相关的事情
            console.log("上传中");
        },
        FileUploaded: function(up, file, info) {
            // 每个文件上传成功后,处理相关的事情
            // 其中 info.response 是文件上传成功后，服务端返回的json，形式如
            // {
            //    "hash": "Fh8xVqod2MQ1mocfI4S4KpRL6D98",
            //    "key": "gogopher.jpg"
            //  }
            // 参考http://developer.qiniu.com/docs/v6/api/overview/up/response/simple-response.html

            var domain = up.getOption("domain");
            var res = JSON.parse(info.response);
            var sourceLink = domain + res.key; //获取上传成功后的文件的Url;
        },
        Error: function(up, err, errTip) {
            base.hideLoading();
            //上传出错时,处理相关的事情
            if (file.code == "-600") {
                base.showAlertDialog("上传图片的大小不能超过10mb！");
            } else if (file.code == "-601") {
                base.showAlertDialog("上传图片的格式有误！");
            } else {
                base.showAlertDialog(err);
            }
        },
        UploadComplete: function() {
            //队列文件处理完毕后,处理相关的事情
        },
        Key: function(up, file) {
            // 若想在前端对每个文件的key进行个性化处理，可以配置该函数
            // 我使用的是图片名加上当前时间来定义key
            // 该配置必须要在 unique_names: false , save_key: false 时才生效
            // do something with key here
            var timestamp = Date.parse(new Date());
            var key = file.name + "/" + timestamp;
            return key;
        }
    }

    // domain 为七牛空间（bucket)对应的域名，选择某个空间后，可通过"空间设置->基本设置->域名设置"查看获取

    // uploader 为一个 plupload 对象，继承了所有 plupload 的方法，参考http://plupload.com/docs
});
```

以上 uploader 的配置适用于单文件上传的情况。相信小伙伴们看完都有一个疑惑，什么是 uptoken 呢？ uptoken 是 我们图片上传的上传凭证，它会根据你的 Access Key 和 Secret Key 还有你上传图片到的存储空间 bucket 来生成(最基本的三项)。那么怎么生成 uptoken 呢？这当然不是由我们前端自己来生成的，这个时候就需要我们后端的小伙伴来帮帮忙了。具体可以参考我的另一篇博客,通过 java 生成七牛云 uptoken。

## 写在最后

关于七牛云上传组件的用法还有很多，如对上传的图片生成水印等，大家可以好好参考一下官方文档。我总结的只是一些最简单的用法，目前我的小 demo 只需要用到这一些，以后有需要再继续拓展 :)

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**
