---
title: 基于 WSL 和 Hyper 的 Windows 终端优化
date: 2018-11-22 07:54:11
tags:
 - WSL
 - Hyper
 - 终端
categories:
 - 开发者手册
 - Windows 终端
---


![基于 WSL 和 Hyper 的 Windows 终端美化 文章配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu26-1.jpg)


## 写在前面

Windows 下的终端环境一直让人吐槽，虽然现在的 powershell 已经很强大了，但是它的界面还是让人不敢恭维。前段时间一直在使用 Linux 作为工作环境，感叹 zsh 的强大与方便，最近就一直想着如何能在 Windows 下打造一个支持 Linux Bash 的美观高效的终端环境。下面是我这段时间踩坑后，对基于 WSL 和 Hyper 的 Windows 终端优化的一些总结。


<!-- more -->

## Windows Subsystem for Linux

### 简介

Windows Subsystem for Linux（WSL）是一个可以直接执行 Linux 二进制可执行文件（ELF）的兼容层。和 Hyper-v虚拟化不同，WSL 是底层把 Linux 系统调用翻译成 Native Windows API 。例如安装 Ubuntu 子系统，就基本可以当作一个 Ubuntu 系统来用，例如使用 Linux Bash，apt-get 安装软件，编译和运行 Linux 程序，文件系统交互等。

### 开启Ubuntu子系统

1. 开启开发者模式

   打开 开始->设置->更新和安全->开发者选项，勾选开发人员模式

   ![文章配图2](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu26-2.png)

2. 开启 windows 子系统功能

   打开 控制面板->程序->启用或关闭 Windows 功能

   ![文章配图3](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu26-3.png)

3. 重启电脑，等待更新。

4. 在 Microsoft Store 中搜索 Ubuntu ，并安装（其他的 Linux 系统也可以）

   ![文章配图4](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu26-4.png)

   注意：如果遇到 Microsoft Store 中出现网络错误连接的问题，并且你确定你的网络连接没有问题的话，那么很有可能是 VPN 的问题，关掉 VPN 再试一下就行。

   安装完后，就可以直接打开 Ubuntu 了，默认是一个终端，如下：

   ![文章配图5](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu26-5.png)

   注意：Ubuntu 默认把磁盘挂载到 /mnt 目录下，可以直接 cd /mnt/c 进入 C 盘，进而操作文件。

5. 更改科大源

  Ubuntu 默认的源服务器在国外，速度慢，需要打开下面文件 `/etc/apt/sources.list`，在首行添加如下内容：

  ```bash
  deb https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
  deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
  deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
  deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
  deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
  deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
  deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
  deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
  deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
  deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
  ```
  保存后更新

  ```bash
  apt-get update
  apt-get upgrade
  ```



## Hyper

### 简介

Hyper Terminal 是一个基于 JS/HTML/CSS 的终端 (Terminal) 指南，类似于 macOS 自带的 terminal ，只不过具有更高的可塑性以及更美观的外观。

![文章配图6](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu26-6.png)

### 安装

访问[官方网站](https://hyper.is/)，选择适合的版本下载。

### 设置自动以 WSL 启动

在左上角 edit->preferences 打开配置文件，找到对应行并更改成如下内容：

```cmd
updateChannel: 'canary',
shell: 'C:\\Windows\\System32\\cmd.exe',
shellArgs: ['--login', '-i', '/c wsl'],
```

### 主题选择

Hyper 中有很多主题可以供我们选择，官网中已经提供了安装方法，我们在这里就不再介绍了。现在 Hyper 还比较小众，社区资源还不是很丰富，但现有的特性已经很让人激动了！




## Zsh

Zsh 是一个功能强大的交互式 shell 脚本命令解释器。它不仅支持 bash ，还提供一些强大和高效的功能，例如更好的自动补全和纠错。

### 安装

```bash
 sudo apt-get install zsh
```

检查当前的 shell 有哪些

```bash
cat /etc/shells
```

检查当前的 shell

```bash
echo $SHELL
```

设置默认 shell 为 zsh，在 ~/.bashrc 中加入

```bash
if [ -t 1 ]; then
exec zsh
fi
```

然后执行

```bash
source ~/.bashrc
```


### 安装 oh-my-zsh

oh my zsh 用来管理 zsh 的配置

```bash
# 安装 Oh My Zsh
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
# 以上命令可能不好使，可使用如下两条命令
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh
bash ./install.sh
```

### 插件安装

1.  [fasd](https://github.com/clvv/fasd)

  快速访问文件或目录

  安装：

  ```bash
  sudo apt install fasd
  ```

  用法：

  ```bash
  alias f='fasd -f'          # 文件
  alias d='fasd -d'        # 目录
  alias a='fasd -a'        # 任意
  alias s='fasd -si'       # 显示并选择

  alias sd='fasd -sid'        # 选择目录
  alias sf='fasd -sif'          # 选择文件
  alias z='fasd_cd -d'       # 跳转至目录
  alias zz='fasd_cd -d -i'  # 选择并跳转至目录
  ```

2. [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

    命令行命令键入时的历史命令建议插件

    按照官方文档提示，直接执行如下命令安装：

    ```bash
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
    ```

3. [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)
   
  命令行语法高亮插件

  按照官方文档提示，直接执行如下命令安装：

  ```bash
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
  ```

4. 最后记得在~/.zshrc文件中配置插件：

  ```bash
  plugins=(
    git fasd zsh-autosuggestions zsh-syntax-highlighting
  )
  ```

### 主题修改

on-my-zsh 中提供了很多主题，大家可以在下面这个网站预览

[on-my-zsh 主题](https://github.com/robbyrussell/oh-my-zsh/wiki/External-themes)

oh_my_zsh的主题和设置一般都在主目录下

```bash
# 进入主题目录
cd ~/.oh-my-zsh/themes

#查看当前使用的主题
echo $ZSH_THEME
```

主题修改需要对 ~/.zshrc 文件进行相应的修改

```bash
ZSH_THEME="你喜欢的主题"
```

但是有可能你对这个主题的 title 不太满意，比如多余的主机名或者太长的路径名之类的，这个时候我们最好的做法是把我们想要修改的主题文件做一个复制，然后在这个文件里边修改，最后再将.zshrc 文件中的主题修改为我们复制的这个主题。

## 成果截图

下面就是我经过优化后的终端环境，不得不说，Hyper 再配上 zsh 的使用真的是丝滑般的用户体验！

![文章配图2](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu26-7.png)

## 参考文档


[Windows10开启Ubuntu子系统简易步骤](https://zhuanlan.zhihu.com/p/34133795)
[WSL + Ubuntu + ZSH + Hyper终端](https://suiyuanjian.com/225.html)
[Ubuntu 下 Oh My Zsh 的最佳实践「安装及配置」](https://juejin.im/post/5b216263f265da6e44326959)
[oh-my-zsh终端用户名设置](https://blog.csdn.net/jichunw/article/details/80088995)

## 写在最后

通过这一番优化，基于 WSL 和 Hyper 的 Windows 终端已经很大程度上的能够满足我的需求了。这篇文章只是一个简单的总结，就拿 WSL 来说，这已经是 Windows 上一个很成熟的技术了，它能够发挥的作用还有很多。通过这个过程，感觉自己收获到了很多东西，意义还是蛮大的。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**