---
title: 实现hexo博客与Github库同步更新
date: 2019-10-15 11:14:32
categories:
- hexo博客搭建
tags:
- hexo
---

![山水](http://qiniusave.codeyu.cn/the%20first.jpg)

<!-- less -->

---

## 概述：

​		本篇文章不讲解`hexo`博客初次搭建的基础内容，而是着重讲授如何将本地的博客源文件同步到`Github`库中去，这样做的优点有如下几条：

1.当本地博客源文件不小心丢失后，可以从远端`Github`库上重新下载博客源文件，相当于一个备份作用。

2.可以在不同的电脑上下载自己的博客源文件并对博客内容进行更新管理。

## 第一步：根目录操作阶段

1.在自己的`Github`上新建一个`mthgh123.github.io`的库，用于存储本地源文件和网页静态文件。

2.新建完上述库之后，在自己本地的任意一个文件夹里新建一个`README.md`文件，里面可写上任意内容，之后通过下方图片所示步骤将该文件上传至名称为`mthgh123.github.io`的库中。

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B01.jpg)

3.刷新`mthgh123.github.io`库的页面，然后新建另外一个名为`hexo`的分支，并将`hexo`分支设定为默认分支，目的就是使得以后通过git bash直接将本地源文件上传到此`hexo`分支中去。

4.我们将刚刚的`mthgh123.github.io`库下载到你想要建立博客的目录下（我的是`F:\blogger`），命令如下：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B02.jpg)

下载结果如下：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B03.jpg)

将`README.md`文件可以删除掉，因为后续也没太大用处。关键是`.git`文件的处理，因为后续在该文件夹下执行`hexo init`时要求该文件夹下不能有任何的文件，为避免冲突，我们先将`mthgh123.github.io`文件夹下已经存在的.git文件（和`README.md`文件）剪切到其他地方，然后在`mthgh123.github.io`文件夹下执行`npm install hexo`和`hexo init`操作，操作完成后，然后再把最开始剪切到其他地方的.git文件再剪切回来，从而又实现`mthgh123.github.io`文件夹下的文件与`github`上的名为`mthgh123.github.io`库的关联。在此之后，把部署插件也安装上，方便后续将博客部署到网站上。如下：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B04.jpg)

`Windows`和`linux`系统会提示相应的如上方所示的警告，不管它。

## 第二部分：下载子模块内容到本地：

1.将fork到自己的库中的名称为`hexo-theme-next`的主题库（注意：这个库是从别人的Github库那里fork来的，它是一个主题文件库，如比较经典的next主题）作为子模块下载到***博客根目录下***的themes/next目录下。操作如下：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B05.jpg)

上述的`warning : LF will be replaced be CRLF in .gitmodules`不用管，小问题。如果文件没有下载下来，则需要在`F:\blogger\mthgh123.github.io\themes\next`目录下使用`git submodule update`等操作。

2.接下来修改自己喜欢的主题配置即可。常规主题自行设置，其中包括设置：网页头像，网站头像，背景图片（云彩图真的不错，尤其是上方是蓝天，下方是云朵的那种），文章折叠，文章分类、关于、标签等，文章阴影设置，文章结束标志等。

## 小插曲：简要介绍几个配置

1.文章折叠：只需要在主题配置文件中搜索auto_excerpt并将enable设置为true即可。

2.给每一篇博客配置一个美图封面，如下方所示：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B06.jpg)

注意：需要首先下载了一个插件，叫做：`hexo-less`

在git bash命令行中输入：`npm install --save hexo-less`，该插件在的网址为：[hexo-less](https://github.com/fuchen/hexo-less)

## 第三部分：提交子模块内容及根目录内容：

操作如下所示：

分别是在`themes/next`文件夹下执行`git add --a`、`git commit -s -m "   "`、

`git push origin master`三个操作用以将子模块中的文件同步到自己fork来的主题库中去。

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B07.jpg)

1.先进入到子模块的目录下：`F:\blogger\mthgh123.github.io\themes\next`然后执行上述操作即可。因为子模块当初是从我的`github`中的`hexo-theme-next`库中下载下来的，所以二者是关联的关系，因此执行完`git push origin master`之后，修改后的配置以及文件又重新同步更新到我的`github`中的`hexo-theme-next`库中去。

2.提交博客的根目录下的文件，因为最开始根目录就已经和`github`上的`mthgh123.github.io`库相关联了，因此同步更新的文件是到这个库中去了。注意：操作是退回到根目录中执行的，即`F:\blogger\mthgh123.github.io`如下：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B08.jpg)

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B09.jpg)

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B010.jpg)

注意：在发布内容时提交到的分支是`hexo`（因为我们用来存储源文件的`branch`是`hexo`啊），而不是`master`。然后到`github`上的`mthgh123.github.io`库中刷新：如预期所示

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B011.jpg)

这样，本地源文件就被成功上传至`mthgh123.github.io`库的`hexo`分支了。而`master`分支是空的，只有最初新建时的一个`README.md`文件：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B012.jpg)

这是正常的，该分支是用于存储静态网页的文件，等会`hexo g、hexo d`之后就会自动同步到该分支中。

## 第四部分：将博客部署到网站上

在根目录下执行`hexo clean`、`hexo g`、`hexo d`操作即可。

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B013.jpg)

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B014.jpg)

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B015.jpg)

注意：在上面提示很多警告，我搜了相关博客了解了一下，有说法说是因为可能是别人的库使用`linux`系统上开发的，存在一些换行符等之类的差异，但整体影响不大。所以当你在windows系统上使用的时候，它就会提示说存在错误，不过影响不大，可以通过`git config –global core.autocrfl true` 这条命令来进行设置解决上方问题。

再次刷新`mthgh123.github.io`库中的`master`分支：

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B016%20.jpg)

对吧，静态网页的相关文件已经更新到`mthgh123.github.io`库中的`master`分支了。

一切正常。

同时，在网站上的刷新结果如下所示，也一切正常。

![](http://qiniusave.codeyu.cn/%E5%AE%9E%E7%8E%B0hexo%E5%8D%9A%E5%AE%A2%E4%B8%8EGithub%E5%BA%93%E5%90%8C%E6%AD%A5%E6%9B%B4%E6%96%B017.jpg)