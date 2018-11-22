---
title: 阿里云ECS基于Linux系统部署javaweb项目
date: 2018-02-11 13:42:03
tags: 阿里云
categories: 阿里云
---

![阿里云ECS基于Linux系统部署javaweb项目文章配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-1.jpg)

## 写在前面

最近发现阿里云上推出了一个云翼计划，大学生购买 ECS 实例最低只要 9.9 元/月(不是打广告),真的是太实惠了有没有。算一算我还有两年才毕业，这要是不申请一个来试试都不好意思说自己读过大学...本来对服务器这一块不是很了解，正好借此机会学习学习。纯基础教学，带你一步一步了解如何使用 ECS 部署你的 javaweb 项目。

<!-- more -->

## 云服务器 ECS 购买

因为我们需要部署一个 javaweb 项目，所以我们需要在服务器配置相应的 JAVA 环境。当然我们购买最基本的 ECS 实例然后再安装对应的 mysql，tomcat 这些也是可以的。这里我们有一个一步到位的方法，就是选择已经配置好的镜像。大家可以通过在阿里云的云市场里边搜索找到 JAVA 环境（CentOS7.4 Nginx Tomcat8 JDK）这一个镜像，然后点击购买(这一个镜像是免费的)。下面是地址:

* [JAVA 环境（CentOS7.4 Nginx Tomcat8 JDK）镜像](https://market.aliyun.com/products/53400005/cmjj016483.html?spm=a2c4g.11186623.2.8.6yiYw6)

如图:

![配图5-2](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-2.png)

点击立即购买，然后进入 ECS 实例的购买配置页面，如果没有特殊需求，按照默认的配置购买即可，大学生的话可以享受 9.9 元/月的优惠，好像买一年还会送域名的代金券。

![配图5-3](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-3.png)

## 配置安全组

购买了服务器后我们可以登陆阿里云的管理控制台查看我们购买的 ECS 示例，然后选择安全组，如图:

![配图5-6](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-6.png)

配置安全组可以让我们访问以及使用 mysql 远程连接数据库等，点击配置规则，如图:

![配图5-7](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-7.png)

点击快速创建规则，先创建第一个规则，一定要和我的一致，如图:

![配图5-8](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-8.png)

点击添加安全组规则，创建第二个规则，如图:

![配图5-9](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-9.png)

点击添加安全组规则，创建第三个规则，如图:

![配图5-10](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-10.png)

我们还要添加一个出方向的规则，如图:

![配图5-11](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-11.png)

配置好后我们就可以通过我们的公网 ip 访问我们的服务器了，访问公网 ip 出现如下界面说明我们的安全组配置成功了，如图:

![配图5-12](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-12.png)

## 参数优化

我们可以通过 Xshell 5 来远程连接到我们的服务器，如图:

![配图5-13](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-13.png)

点击确定后需要输入密码，就是在购买实例时设置的密码。

由于镜像制作是下 1 核 1G 内存配置完成，Nginx、MySQL、JVM 参数还是该配置的参数，如果不是这个配置，我们需要执行下面优化脚本(建议都执行一下)

```
cd /root/oneinstack  //必须进入oneinstack目录下执行
./optimize.sh      //请勿bash ./optimize.sh（或者sh）这样执行
```

需要注意:

* 可能需要等待 1 分钟，请勿中断
* 此脚本会根据系统配置，自动优化 Nginx、MySQL、JVM 等参数

优化成功，如下图:

![配图5-14](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-14.png)

## MySQL 数据库管理

由于我们需要部署 javaweb 项目，肯定会使用到数据库。直接使用 cmd 界面管理肯定不方便，所以我们需要使用如 Navicat Premium 这样的工具来远程连接数据库进行操作。不过直接远程连接是不行的，因为我们没有开放权限。

我们先来看一下数据库默认 root 的密码

```
cd /root/oneinstack
grep dbrootpwd options.conf  //显示数据库root密码
```

如下图:

![配图5-15](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-15.png)

默认密码太复杂了，我们想要重置密码怎么办呢？可以这样做

```
cd /root/oneinstack
./reset_db_root_password.sh
```

如下图:

![配图5-16](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-16.png)

好了，进入正题，如何获取权限远程连接 mysql

首先登陆，然后打开 mysql 数据库

```
mysql -u root -h localhost -p
use mysql                      //打开mysql数据库
```

然后将 host 设置为%表示任何 ip 都能连接 mysql，当然您也可以将 host 指定为某个 ip

```
update user set host='%' where user='root' and host='localhost';
```

最后刷新权限表，使配置生效

```
flush privileges;
```

如果您想关闭远程连接，恢复 mysql 的默认设置（只能本地连接），只需重复上述步骤，将`host='%'`改为`host='localhost'`就行了。

使用 Navicat Premium 来远程连接到数据库，如图:

![配图5-17](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-17.png)

## 虚拟主机

一般情况下我们不会使用公网 ip 来访问网站，这个时候我们需要为我们的服务器绑定域名，绑定域名后网站需要备案后才能访问，备案的过程可能会有点长，两到三周左右。你如果不想使用域名绑定，那么可以跳过这一小节，不使用域名也是可以的。当我们的网站备案好后，我们需要通过我们绑定的域名在服务器上创建虚拟主机以此来部署我们的项目文件。下面我们来看一下如何创建和删除虚拟主机。

添加虚拟主机

```
cd /root/oneinstack
./vhost.sh
```

如图

![配图5-21](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-21.png)

删除虚拟主机

```
cd  /root/oneinstack
./vhost.sh del
```
![配图5-22](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-22.png)


## 上传项目文件到服务器

上传项目文件到服务器上，我们需要用到 WinSCP,连接配置和其他远程连接需要输入的基本相同，这里就不列举了。我们需要把如下项目文件上传到服务器目录下。如果你创建了虚拟主机，那么你需要上传的服务器目录为`/data/wwwroot/你的域名/`。如果你未绑定域名，你可以上传到默认的服务器目录下`/data/wwwroot/default/`，如图:

![配图5-18](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-18.png)

这里需要注意的是我们需要把 index.html 默认网站首页放在根目录下，要不然会出现403无法访问的错误。

是不是这样就能访问了呢？我们还需要做最后几步工作。

默认 Tomcat 是以一般 www 用户运行，所以我们要将网站代码权限改为 www，执行命令：

```
chown -R www.www /data/wwwroot
```

如图:

![配图5-19](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-19.png)

最后，我们重启一下 Tomcat 就行了。

```
service tomcat restart
```

## 访问主页

下面我们访问一下我们网站的主页试试，这里要注意网站的根目录是 default 目录，所以我们需要注意一下我们访问的页面的路径。这是我的图片分享网站的首页，大家可以参考一下:

![配图5-20](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu5-20.png)

## 写在最后

好了，终于将我们的 javaweb 部署到服务器上了。虽然弄了很久.....但还是很高兴，感觉通过部署服务器学到了很多东西，对网站的创建和运行有了一定的了解。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**