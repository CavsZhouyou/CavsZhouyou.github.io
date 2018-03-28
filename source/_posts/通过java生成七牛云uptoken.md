---
title: 通过Java生成七牛云uptoken
date: 2018-02-07 12:04:56
tags:
 - 后端
 - 七牛云
categories: 七牛云
---

![通过 java 生成七牛云 UpToken 文章配图](http://p2p4htzmu.bkt.clouddn.com/peitu3-1.jpg)

## 写在前面

最近在写一个图片分享网站类型的小 demo，使用七牛云图片上传需要服务端生成上传凭证 uptoken。本来对后端了解的不是很多，看了官网的说明，写了一个最基本的生成方法，所以总结一下生成 uptoken 的具体步骤。我这里介绍的是使用七牛云 Java SDK 的生成方法。

<!-- more -->

## 简介

> 此 SDK 适用于 Java 7 及以上版本。使用此 SDK 构建您的网络应用程序，能让您以非常便捷地方式将数据安全地存储 到七牛云上。无论您的网络应用是一个网站程序，还是包括从云端（服务端程序）到终端（手持设备应用）的架构服务或应 用，通过七牛云及其 SDK，都能让您应用程序的终端用户高速上传和下载，同时也让您的服务端更加轻盈。
> Java SDK 属于七牛服务端 SDK 之一，主要有如下功能：
>
> * 提供生成客户端上传所需的上传凭证的功能
> * 提供文件从服务端直接上传七牛的功能
> * 提供对七牛空间中文件进行管理的功能
> * 提供对七牛空间中文件进行处理的功能
> * 提供七牛 CDN 相关的刷新，预取，日志功能

## 官方文档

* [七牛云 java-sdk 官方文档](https://developer.qiniu.com/kodo/sdk/1239/java)

## 安装

我们需要安装七牛云的 java-sdk，我这里介绍的是 Maven 的安装方法

```xml
<dependency>
  <groupId>com.qiniu</groupId>
  <artifactId>qiniu-java-sdk</artifactId>
  <version>[7.2.0, 7.2.99]</version>
</dependency>
```

这里的 version 指定了一个版本范围，每次更新 pom.xml 的时候会尝试去下载 7.2.x 版本中的最新版本，我们也可以手动指定一个固定的版本。

## 使用

引入 java-sdk 以后，我们需要创建一个 Auth 类，这个类的代码如下

```java
package com.qiniu.util;

import com.qiniu.http.Client;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.security.GeneralSecurityException;

public final class Auth {

    /**
     * 上传策略
     * 参考文档：<a href="https://developer.qiniu.com/kodo/manual/put-policy">上传策略</a>
     */
    private static final String[] policyFields = new String[]{
            "callbackUrl",
            "callbackBody",
            "callbackHost",
            "callbackBodyType",
            "callbackFetchKey",

            "returnUrl",
            "returnBody",

            "endUser",
            "saveKey",
            "insertOnly",
            "isPrefixalScope",

            "detectMime",
            "mimeLimit",
            "fsizeLimit",
            "fsizeMin",

            "persistentOps",
            "persistentNotifyUrl",
            "persistentPipeline",

            "deleteAfterDays",
            "fileType",
    };
    private static final String[] deprecatedPolicyFields = new String[]{
            "asyncOps",
    };
    public final String accessKey;
    private final SecretKeySpec secretKey;

    private Auth(String accessKey, SecretKeySpec secretKeySpec) {
        this.accessKey = accessKey;
        this.secretKey = secretKeySpec;
    }

    public static Auth create(String accessKey, String secretKey) {
        if (StringUtils.isNullOrEmpty(accessKey) || StringUtils.isNullOrEmpty(secretKey)) {
            throw new IllegalArgumentException("empty key");
        }
        byte[] sk = StringUtils.utf8Bytes(secretKey);
        SecretKeySpec secretKeySpec = new SecretKeySpec(sk, "HmacSHA1");
        return new Auth(accessKey, secretKeySpec);
    }

    private static void copyPolicy(final StringMap policy, StringMap originPolicy, final boolean strict) {
        if (originPolicy == null) {
            return;
        }
        originPolicy.forEach(new StringMap.Consumer() {
            @Override
            public void accept(String key, Object value) {
                if (StringUtils.inStringArray(key, deprecatedPolicyFields)) {
                    throw new IllegalArgumentException(key + " is deprecated!");
                }
                if (!strict || StringUtils.inStringArray(key, policyFields)) {
                    policy.put(key, value);
                }
            }
        });
    }

    private Mac createMac() {
        Mac mac;
        try {
            mac = javax.crypto.Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
        } catch (GeneralSecurityException e) {
            e.printStackTrace();
            throw new IllegalArgumentException(e);
        }
        return mac;
    }

    public String sign(byte[] data) {
        Mac mac = createMac();
        String encodedSign = UrlSafeBase64.encodeToString(mac.doFinal(data));
        return this.accessKey + ":" + encodedSign;
    }

    public String sign(String data) {
        return sign(StringUtils.utf8Bytes(data));
    }

    public String signWithData(byte[] data) {
        String s = UrlSafeBase64.encodeToString(data);
        return sign(StringUtils.utf8Bytes(s)) + ":" + s;
    }

    public String signWithData(String data) {
        return signWithData(StringUtils.utf8Bytes(data));
    }

    /**
     * 生成HTTP请求签名字符串
     *
     * @param urlString
     * @param body
     * @param contentType
     * @return
     */
    public String signRequest(String urlString, byte[] body, String contentType) {
        URI uri = URI.create(urlString);
        String path = uri.getRawPath();
        String query = uri.getRawQuery();

        Mac mac = createMac();

        mac.update(StringUtils.utf8Bytes(path));

        if (query != null && query.length() != 0) {
            mac.update((byte) ('?'));
            mac.update(StringUtils.utf8Bytes(query));
        }
        mac.update((byte) '\n');
        if (body != null && Client.FormMime.equalsIgnoreCase(contentType)) {
            mac.update(body);
        }

        String digest = UrlSafeBase64.encodeToString(mac.doFinal());

        return this.accessKey + ":" + digest;
    }

    /**
     * 验证回调签名是否正确
     *
     * @param originAuthorization 待验证签名字符串，以 "QBox "作为起始字符
     * @param url                 回调地址
     * @param body                回调请求体。原始请求体，不要解析后再封装成新的请求体--可能导致签名不一致。
     * @param contentType         回调ContentType
     * @return
     */
    public boolean isValidCallback(String originAuthorization, String url, byte[] body, String contentType) {
        String authorization = "QBox " + signRequest(url, body, contentType);
        return authorization.equals(originAuthorization);
    }

    /**
     * 下载签名
     *
     * @param baseUrl 待签名文件url，如 http://img.domain.com/u/3.jpg 、
     *                http://img.domain.com/u/3.jpg?imageView2/1/w/120
     * @return
     */
    public String privateDownloadUrl(String baseUrl) {
        return privateDownloadUrl(baseUrl, 3600);
    }

    /**
     * 下载签名
     *
     * @param baseUrl 待签名文件url，如 http://img.domain.com/u/3.jpg 、
     *                http://img.domain.com/u/3.jpg?imageView2/1/w/120
     * @param expires 有效时长，单位秒。默认3600s
     * @return
     */
    public String privateDownloadUrl(String baseUrl, long expires) {
        long deadline = System.currentTimeMillis() / 1000 + expires;
        return privateDownloadUrlWithDeadline(baseUrl, deadline);
    }

    public String privateDownloadUrlWithDeadline(String baseUrl, long deadline) {
        StringBuilder b = new StringBuilder();
        b.append(baseUrl);
        int pos = baseUrl.indexOf("?");
        if (pos > 0) {
            b.append("&e=");
        } else {
            b.append("?e=");
        }
        b.append(deadline);
        String token = sign(StringUtils.utf8Bytes(b.toString()));
        b.append("&token=");
        b.append(token);
        return b.toString();
    }

    /**
     * scope = bucket
     * 一般情况下可通过此方法获取token
     *
     * @param bucket 空间名
     * @return 生成的上传token
     */
    public String uploadToken(String bucket) {
        return uploadToken(bucket, null, 3600, null, true);
    }

    /**
     * scope = bucket:key
     * 同名文件覆盖操作、只能上传指定key的文件可以可通过此方法获取token
     *
     * @param bucket 空间名
     * @param key    key，可为 null
     * @return 生成的上传token
     */
    public String uploadToken(String bucket, String key) {
        return uploadToken(bucket, key, 3600, null, true);
    }

    /**
     * 生成上传token
     *
     * @param bucket  空间名
     * @param key     key，可为 null
     * @param expires 有效时长，单位秒
     * @param policy  上传策略的其它参数，如 new StringMap().put("endUser", "uid").putNotEmpty("returnBody", "")。
     *                scope通过 bucket、key间接设置，deadline 通过 expires 间接设置
     * @return 生成的上传token
     */
    public String uploadToken(String bucket, String key, long expires, StringMap policy) {
        return uploadToken(bucket, key, expires, policy, true);
    }

    /**
     * 生成上传token
     *
     * @param bucket  空间名
     * @param key     key，可为 null
     * @param expires 有效时长，单位秒。默认3600s
     * @param policy  上传策略的其它参数，如 new StringMap().put("endUser", "uid").putNotEmpty("returnBody", "")。
     *                scope通过 bucket、key间接设置，deadline 通过 expires 间接设置
     * @param strict  是否去除非限定的策略字段，默认true
     * @return 生成的上传token
     */
    public String uploadToken(String bucket, String key, long expires, StringMap policy, boolean strict) {
        long deadline = System.currentTimeMillis() / 1000 + expires;
        return uploadTokenWithDeadline(bucket, key, deadline, policy, strict);
    }

    public String uploadTokenWithDeadline(String bucket, String key, long deadline, StringMap policy, boolean strict) {
        // TODO   UpHosts Global
        String scope = bucket;
        if (key != null) {
            scope = bucket + ":" + key;
        }
        StringMap x = new StringMap();
        copyPolicy(x, policy, strict);
        x.put("scope", scope);
        x.put("deadline", deadline);

        String s = Json.encode(x);
        return signWithData(StringUtils.utf8Bytes(s));
    }

    public String uploadTokenWithPolicy(Object obj) {
        String s = Json.encode(obj);
        return signWithData(StringUtils.utf8Bytes(s));
    }

    public StringMap authorization(String url, byte[] body, String contentType) {
        String authorization = "QBox " + signRequest(url, body, contentType);
        return new StringMap().put("Authorization", authorization);
    }

    public StringMap authorization(String url) {
        return authorization(url, null, null);
    }

    /**
     * 生成HTTP请求签名字符串
     *
     * @param urlString
     * @param body
     * @param contentType
     * @return
     */
    public String signRequestV2(String urlString, String method, byte[] body, String contentType) {
        URI uri = URI.create(urlString);

        Mac mac = createMac();
        StringBuilder sb = new StringBuilder();

        sb.append(String.format("%s %s", method, uri.getPath()));
        if (uri.getQuery() != null) {
            sb.append(String.format("?%s", uri.getQuery()));
        }

        sb.append(String.format("\nHost: %s", uri.getHost()));
        if (uri.getPort() > 0) {
            sb.append(String.format(":%d", uri.getPort()));
        }

        if (contentType != null) {
            sb.append(String.format("\nContent-Type: %s", contentType));
        }

        // body
        sb.append("\n\n");
        if (body != null && body.length > 0 && !StringUtils.isNullOrEmpty(contentType)) {
            if (contentType.equals(Client.FormMime)
                    || contentType.equals(Client.JsonMime)) {
                sb.append(new String(body));
            }
        }

        System.out.println(sb.toString());
        mac.update(StringUtils.utf8Bytes(sb.toString()));

        String digest = UrlSafeBase64.encodeToString(mac.doFinal());

        return this.accessKey + ":" + digest;
    }

    public StringMap authorizationV2(String url, String method, byte[] body, String contentType) {
        String authorization = "Qiniu " + signRequestV2(url, method, body, contentType);
        return new StringMap().put("Authorization", authorization);
    }

    public StringMap authorizationV2(String url) {
        return authorizationV2(url, "GET", null, null);
    }
}
```

如果出现报错，只要导入相应的包即可，Auth 代码的 github 地址为

* [Auth 代码 github 地址](https://github.com/qiniu/java-sdk/edit/master/src/main/java/com/qiniu/util/Auth.java)

创建 Auth 类后，下面我们就来看一下如何生成一个最简单的上传凭证。最简单的上传凭证只需要 AccessKey，SecretKey 和 Bucket 就可以。

```java
String accessKey = "access key";//七牛云账号对应的公匙
String secretKey = "secret key";//七牛云账号对应的司匙
String bucket = "bucket name";//上传图片到的存储空间

Auth auth = Auth.create(accessKey, secretKey);
String upToken = auth.uploadToken(bucket);//获取uptoken
```

## 接口示例

我在这里展示一下我的 demo 中获取 uptoken 接口的示例，大家可以参考一下

```java
package com.photoshareweb.controller;

import com.photoshareweb.common.Auth;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;


@Controller
@RequestMapping(value = "/PhotoShareWeb/share/auth")
public class AuthController {


    //通过七牛云上传凭证
    @RequestMapping(value = "/getUpToken",method = RequestMethod.GET)
    public @ResponseBody String getUpToken(){
        String accessKey = "access key";//七牛云账号对应的公匙
        String secretKey = "secret key";//七牛云账号对应的司匙
        String bucket = "bucket name";//上传图片到的存储空间
        Auth auth = Auth.create(accessKey, secretKey);
        String upToken = auth.uploadToken(bucket);
        return upToken;
    }

}
```

## 写在最后

好啦，这样我们就可以生成一个最基本的 uptoken 了，当然只是最基本的，更多的用法大家可以参考官方文档。
