---
title: Markdown—文档解读
date: 2018-01-17 14:04:45
tags: Markdown
categories: Markdown
---

![Markdown语法学习配图](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1.jpg)

## 写在前面

记得第一次接触 Markdown 应该是在 github 上，当时看每一个仓库里的 README 文档都是 md 格式的，文档内容的排版让我眼前一亮。诶这是什么东西，好奇的我赶紧百度了一下，看完官方文档，感觉自己推开了新世界的大门，有没有很方便！看完文档后，感觉文档一些东西介绍的有一点晦涩，不容易理解。所以我就想分享一下我对 Markdown 的一些认识。

<!-- more -->

## 一、认识 Markdown

### 什么是 Markdown

> Markdown 是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。

* [创始人 John Gruber 的 Markdown 语法说明](https://daringfireball.net/projects/markdown/syntax "创始人 John Gruber 的 Markdown 语法说明")
* [Markdown 中文版语法说明](https://www.appinn.com/markdown/#link "Markdown 中文版语法说明")

Markdown 其实是一种文档编辑的标记语言，它的目标是实现「易读易写」，成为一种适用于网络的书写语言。那怎样才能算易读易写呢？我们在书写文档时，有时候最头疼的就是文章的排版问题，这个时候使用 Markdown 就很方便了。试想一下在写文档时，用键盘一气呵成敲好内容，而且这个时候文章已经排版好了，大声告诉我，是不是很方便。现在很多网站都支持 Markdown 形式的文档书写，比如简书、掘金、github......Markdown 在很大程度上能提高你的码字效率和体验，如果你想快速编写格式丰富并且美观的文章，Markdown 是你的不二选择，怎么有一种打广告的感觉......

### 兼容 HTML

相信大家对超文本标记语言 HTML 都不陌生，那么 Markdown 和 HTML 有什么关系呢？简单来说就是，HTML 是一种发布的格式，而 Markdown 是一种书写的格式。什么意思呢，就是你在浏览器里边看到的所有东西都是由 HTML 标记组成的。那么 Markdown 想要显示对应的内容该怎么办呢，答案就是将其解析为 HTML 的形式来显示内容。

举一个例子，在 Markdown 语法中，有序列表使用数字接着一个英文句点来表示:

```
1.  Bird
2.  McHale
3.  Parish
```

上面的列表所产生的 HTML 标记为：

```html
<ol>
<li>Bird</li>
<li>McHale</li>
<li>Parish</li>
</ol>
```

上面的例子就是 Markdown 到 HTML 的转换。这个时候可能有的同学就会提问了，那如果我在 Markdown 里边插入 HTML 标签，这时候该怎么显示呢？这就是我想告诉大家的 Markdown 的一个特性**兼容 HTML**。Markdown 的格式语法只涵盖纯文本可以涵盖的范围。不在 Markdown 涵盖范围之内的标签，都可以直接在文档里面用 HTML 撰写。不需要额外标注这是 HTML 或是 Markdown 只要直接加标签就可以了。要制约的只有一些 HTML 区块元素――比如 `<div>`、`<table>`、`<pre>`、`<p>` 等标签，必须在前后加上空行与其它内容区隔开，还要求它们的开始标签与结尾标签不能用制表符或空格来缩进。Markdown 的生成器足够智能，不会在 HTML 区块标签外加上不必要的 `<p>` 标签。也就是说你如果在 Markdown 文档里添加了一个`<table>`标签，最后解析完成后，它仍然是一个`<table>`标签，不会解析错误，当然你如果想以代码段的形式呈现就是另外的情况了。

### 特殊字符自动转换

在 HTML 文件中，有两个字符需要特殊处理： `<` 和 `&` 。 `<` 符号用于起始标签，`&` 符号则用于标记 HTML 实体，如果你只是想要显示这些字符的原型，你必须要使用实体的形式，像是 `&lt;` 和 `&amp;`但你如果使用 Markdown，你就无需担心这些问题，它让你可以自然地书写字符，如果需要转换则全由它来处理。

### Markdown 编辑器

工欲善其事必先利其器，那么我们该用什么来编辑 Markdown 文档了？当然你用文本编辑器也是可以的.....现在支持 Markdown 语法编辑的有很多，每个人的习惯不同可能选择不同，只要自己用着顺手就行，这里我推荐几个比较热门的编辑器。

* 在线编辑器 Dillinger

    [Dillinger](https://dillinger.io/ "Dillinger") 简洁却不失强大，同样支持支持 md, styled HTML, pdf 文件导出。 支持 dropbox, onedrive，google drive, github 存储。

    ![Dillinger编辑器](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-1.png "Dillinger编辑器")

* Windows 平台编辑器 MarkdownPad

    [MarkdownPad](http://markdownpad.com/ "MarkdownPad")一款非常强大的 win 系统桌面端编辑器，支持实时预览，PDF 导出及导出格式自定义，公式编辑代码，桌面端使用最为广泛的 markown 编辑工具之一，推荐适用 pro 版，有破解版。

    ![MarkdownPad](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-2.png "MarkdownPad")

* OS X 平台编辑器 Mou

    [Mou](http://25.io/mou/ "Mou") 是 Mac 下杰出的 Markdown 编辑器，提供语法高亮、在线预览、同步滚动、全屏模式，支持自定保存、自动匹配，允许自定义主题，支持 CSS，HTML 和 PDF 导出等。

    ![Mou](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-3.png "Mou")

* 多平台编辑器 vscode

    我现在一般使用 vscode 进行编辑，只需要安装[Markdown Preview Enhanced](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced "Markdown Preview Enhanced")插件，因为现在码代码基本都用 vscode，所以我用起来还是挺顺手的，强行安利一波。

    ![Markdown Preview Enhanced](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-4.png "Markdown Preview Enhanced")

下面再给大家提供一些 Markdown 编辑器参考网址，总能找到适合你的一款。

* [10 款流行的 Markdown 编辑器，总有一款适合你](https://www.csdn.net/article/2014-05-05/2819623 "10款流行的Markdown编辑器，总有一款适合你")
* [Markdown 编辑器一览,总有一款适合你](https://www.jianshu.com/p/6ea395a6a35b "Markdown编辑器一览,总有一款适合你")
* [Markdown 编辑器之比较](https://www.jianshu.com/p/dcffb6f60fe2 "Markdown编辑器之比较")
* [好用的 Markdown 编辑器一览](http://www.williamlong.info/archives/4319.html "好用的Markdown编辑器一览")

## 二、Markdown 语法介绍

### 段落

一个 Markdown 段落是由一个或多个连续的文本行组成，它的前后要有一个以上的空行。空行的定义即是在显示上看起来像是空的，便会被视为空行。比如，若某一行只包含空格和制表符，那么该行不会显示任何东西，则该行也会被视为空行。

![段落](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-5-1.png "段落")

这里还需要注意的一点是，我们在写文章时，经常会习惯使用空格或制表符来**缩进段落**，但在 Markdown 语法里边普通段落是不能用空格或制表符来表示缩进的，即便加了也不会显示缩进。这是因为 Markdown 作为一种轻量级的文本编辑标记语言一开始只是为英文设计的，所以省去了缩进式的写法使书写更为简洁。那么如果我们一定要使用缩进呢？这里有四种方法可以实现。

* 在段落开头插入半角空格: `&ensp;`或 `&#8194;`
* 在段落开头插入全角空格: `&emsp;`或 `&#8195;`
* 在段落开头插入不换行空格: `&nbsp;`或 `&#160;`
* 如果是网页的话，最好使用样式表,将下段代码插入相应 css 即可。
    ```css
    p {
        text-indent: 2em; /*首行缩进*/
    }
    ```

![有缩进的段落](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-6-1.png "有缩进的段落")

### 换行

> 「由一个或多个连续的文本行组成」这句话其实暗示了 Markdown 允许段落内的强迫换行（插入换行符）

在 Markdown 语法中，除了段落自动换行以外，我们可以通过插入换行符来使段落强制换行。这里需要注意的一点是 Markdown 语法中换行符的插入和其他大部分的 text-to-HTML 格式不一样。一般是点击回车即可换行，但在 Markdown 语法中必须**在文字末尾输入 2 个及以上的空格，再点击回车才可实现换行**。不过在一些编辑器中可以设置为回车换行，我使用的 Markdown Preview Enhanced 插件里默认的换行方式就是使用回车换行，当然你如果不习惯也可以更改设置。

### 空行

刚开始使用 Markdown 语法时，我发现无论两个段落之间相距了多少行，在不做其他处理的情况下，最终显示时两个段落间的距离都只有一个空行。后来了解到 markdown 会把多余空行省略，这是它的成功之处之一，文章结构不靠空行来区分。但有时候我们需要添加空行来调整段落间的距离时，这个时候该怎么办呢？其实原理和实现缩进是一样的，这里有五种方法可以实现。

* 使用半角空格来代表空行: `&ensp;`或 `&#8194;`
* 使用全角空格来代表空行: `&emsp;`或 `&#8195;`
* 使用换行空格来代表空行: `&nbsp;`或 `&#160;`
* 直接插入换行符: `<br/>`
* 调整 markdown 的 css，改变 `p` 的 `margin-bottom` 属性，也可以实现段落间的距离的调整，不过这种方式是全局性的，可能不会每种情况都适用。

![空行展示](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-7.png "空行展示")

### 标题

Markdown 支持两种标题的语法，**类 Setext 形式** 和**类 Atx 形式**。

* 类 Setext 形式是用底线的形式，利用 `=` （最高阶标题）和 `-` （第二阶标题），任何数量的 `=` 和 `-` 都可以有效果。如下图:

    ![类 Setext 形式](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-8.png "类 Setext 形式")

* 类 Atx 形式则是在行首插入 1 到 6 个 `#` ，对应到标题 1 到 6 阶，这一种形式使用得相对多一些。这里需要注意一点就是在`#`和标题间最好隔一个空格，不然可能在有的编辑器下显示有问题，如下图:

    ![类 Atx 形式](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-9.png "类 Atx 形式")

### 区块引用 Blockquotes

Markdown 标记区块引用是使用类似 email 中用 `>` 的引用方式。一共有两种方式，一种是在引用段落的每一行最前面加上`>`，还有一种方式是只在引用段落的第一行最前面加上`>`，这两种方式都能标记区块引用。

![区块引用](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-10.png "区块引用")

* 区块引用里还可以可以嵌套（例如：引用内的引用），只要根据层次加上不同数量的 >
* 引用的区块内也可以使用其他的 Markdown 语法，包括标题、列表、代码区块等

![区块引用扩展](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-11.png "区块引用扩展")

### 列表

Markdown 支持**有序列表**和**无序列表**。

* 无序列表使用`*`、`+`或是`-`作为列表标记:

    ![无序列表](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-12.png "无序列表")

* 有序列表则使用数字接着一个英文句点,这里需要注意的一点是你在列表标记上使用的数字并不会影响最后输出的 HTML 结果，就是说

    不管你写成这样

    ```
    1.  这是有序列表
    1.  这是有序列表
    1.  这是有序列表
    ```

    还是这样

    ```
       3.  这是有序列表
       1.  这是有序列表
       8.  这是有序列表
    ```

    最后的输出结果左边的列表标记都是根据第一行的标记数字往后顺序递增，不会顺序混乱，如图:

    ![有序列表扩展](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-13.png "有序列表扩展")

    这里还需注意的是两个独立的有序列表间，如果中间没有其他的标记元素作为分隔，那么这两个有序列表则被视为一个有序列表来进行标记的顺序递增。

    ![有序列表扩展](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-14.png "有序列表扩展")

    还需要注意几点的是

    * 列表的项目标记通常是放在最左边，但是其实也可以缩进，最多 3 个空格，项目标记后面则一定要接着至少一个空格或制表符。
    * 列表项目可以包含多个段落，每个项目下的段落都必须缩进 4 个空格或是 1 个制表符
    * 项目列表很可能会不小心产生，比方说如果在行首出现数字-句点-空白的情况，则会产生项目列表。

        ```
        1986. What a great season.
        ```

        可以通过在句点前面加上反斜杠来避免

        ```
        1986\. What a great season.
        ```

### 代码块

作为一个程序猿，接触最多的自然是代码啦，那么在 Markdown 语法中该如何表示呢？简单说，插入代码可能的情况有以下两种:

* 只需要标记一小段行内代码，这种情况需要使用反引号 ` 把需要标记的代码给包起来。注意，如果你要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段。

    ![标记行内代码](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-15.png "标记行内代码")

* 需要标记一大段代码(即已经排版好的代码区块)，这里有两种方式来标记。

    * 一种方式是通过简单的缩进 4 个空格或是 1 个制表符来标记。

        ```
        这是一个普通段落：

            这是一个代码区块。
        ```

    * 另一种 Fenced Code Block 方式是通过在第一行和最后一行添加三个反引号，中间的行为代码的形式来进行标记。在第一行的反引号加上代码类型，有助于代码在某些编辑器下的高亮显示。
        ````
            ```
            <代码块>
            ```  
        ````

![代码块](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-16-1.png "代码块")

### 分割线

华丽的分割线该如何来显示呢？我们只需在一行内输入三个以上的`*`或`-`或`_`来建立分割线，并且这一行中不能够包含其他东西。恩..你如果想要在符号间加几个空格也是可以的，这样也可以建立分割线。

![华丽的分割线](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-17.png "华丽的分割线")

### 链接

在写文章时，我们经常会插入一些链接。在 Markdown 语法中一共支持两种形式的链接语法: **行内式**和**参考式**两种形式。

* 参考式的形式，采用一个方括号来标记链接文字，方括号后紧跟一个圆括号标记链接地址，如果还想要加上链接的 title 文字，只需要在链接地址后面隔一个空格，用双引号把 title 文字包起来即可。

    ```
    [链接文字](链接地址 "title")
    ```

* 参考式的形式，采用一个方括号来标记链接文字，方括号后紧跟一个方括号来填入辨识链接地址的标记，然后你可以在文件的任意处将该标记的内容标记出来。我认为这种形式一方面是比较好读，增加链接而不让文章的阅读感觉被打断，另一方面也适用于在文章的不同位置需要使用相同的链接地址这种情况，这时只需要定义一次，然后就可以全局引用。

    ```
     [链接文字][链接标识]

     [链接标识]: 链接地址 "title"   (可在文件任何地方定义，title 内容，可以用单引号、双引号或是括弧包裹)
    ```

    需要注意:

    * 链接标识可以由字母、数字、空白和标点符号组成，但是并不区分大小写。
    * 链接标识可以为空，此时链接变为隐式链接，这种情况下，链接标识会视为等同于链接文字，链接内容通过链接文字来定义。

        ```
        [链接文字][]

        [链接文字]: 链接地址 "title"
        ```

![链接](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/petu1-18.png "链接")

还有一种链接方式是自动链接，Markdown 支持以比较简短的自动链接形式来处理网址和电子邮件信箱，只要是用尖括号包起来， Markdown 就会自动把它转成链接。一般网址的链接文字就和链接地址一样。

```
<https://www.baidu.com/?tn=90294326_hao_pg>
```

### 强调

在 Markdown 语法中，使用`*`和`_`来作为标记强调字词的符号。

* 被一个`*`或`_`包围的字词，最后显示的是斜体。
* 被两个`*`或`_`包围的字词，最后显示的是加粗体。

![强调](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu1-19.png "强调")

### 图片

在 Markdown 语法中，图片的引入方法和链接的方法很相似，也有两种形式，**行内式**和**参考式**。唯一不同的是，图片映入时需要在第一个方括号前添上一个感叹号`!`,因为基本类似，这里就不继续详说了，详细语法可参考链接。

## 参考文献

* [Markdown 中文版语法说明](https://www.appinn.com/markdown/#link "Markdown 中文版语法说明")
* [Markdown 编辑器之比较](https://www.jianshu.com/p/dcffb6f60fe2 "Markdown编辑器之比较")
* [好用的 Markdown 编辑器一览](http://www.williamlong.info/archives/4319.html "好用的Markdown编辑器一览")
* [怎样引导新手使用 Markdown？](https://www.zhihu.com/question/20409634 "怎样引导新手使用 Markdown？")
* [Markdown——入门指南](https://www.jianshu.com/p/1e402922ee32 "Markdown——入门指南")

## 写在最后

总结了一下对 Markdown 的认识后，感觉自己对 Markdown 的理解又有了一些新的收获，注意到了一些以前忽视的东西。写一写总结感觉对自己帮助真的好大 :) ,希望我的总结也能够给你了解学习 Markdown 提供一点帮助。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**
