---
layout: post
title: 搭建Github Blog （环境安装）
category: blog
tags: [github, jekyll, bootstrap]
---


作为一个技术控，总有着利用技术改变生活的情怀，做成一件小事，满足下自己的成就感，如果碰巧能够改变一部分人的生活那简直喜出望外了。自大学起混迹技术界，看了CSDN、cnblog等无数blog技术文章，受益匪浅，想着有一天建立自己的博客，github又生来就是属于程序员的应许之地，在github搭建自己的blog系统，一来可以和其他项目一同进行管理，二来可以灵活管理自己的博客系统，在过程中学习一些如markdown、jekyll、js、css（这篇文章就是markdown的处女作呀）等新的东西。

### 1. 准备

**github**

账号申请、建立仓库等github基础操作就不说了，网上文章详解很多，这里只介绍些自己遇到问题的地方，发现这些问题在一些通用文章中没有提到，留给自己做一个记录，同时如果有需要的人看到了也算一点贡献吧。

由于在Windows和Linux环境中均有需要，这里也分别在这两个环境中安装对应工具，等工具搭建好了，剩下就是操作方式的问题了。


**markdown**

好处不多说了，这篇文章就是处女作，在写作的同时也在学习体会。从别人的博客看到一个[markdown实时在线编辑网站](https://stackedit.io/editor)，在这也推荐下。首页就是编辑与预览界面的对比效果，我用它来当做markdown的教程来用，现在一些记不清的功能去上面查查，用多了也就熟悉了。

至于编辑器各有所爱，我还是用自己大爱的sublime吧, sublime text 2下安装Markdown可使用[MarkdownEditing插件](https://github.com/SublimeText-Markdown/MarkdownEditing)。

**jekyll**

自动生成静态网页的工具，当然还有Hexo、Octopress(基于jekell)等，看了其他人的文章感觉用这个的多些，先用它学习吧，用熟了再和其他的做对比。

**安装jekyll**

> **系统环境:**

> - 64bit Ubunt 14.04 LTS

> **安装过程需要**: ( 官方安装过程参考[http://jekyllrb.com/docs/installation/](http://jekyllrb.com/docs/installation/) )

> - [Ruby](https://www.ruby-lang.org/en/downloads/)
> - [RubyGem](https://rubygems.org/pages/download)
> - [Node.js](https://nodejs.org/)


**会报错**的安装过程是这样的：

> - $ sudo apt-get install ruby
> - $ gem install jekyll

安装后ruby版本为

> ruby 1.9.3p484 (2013-11-22 revision 43786) [x86_64-linux]


会报如下的错误：

> Building native extensions.  This could take a while...
ERROR:  Error installing jekyll:
        ERROR: Failed to build gem native extension.
  .......

后来安装gem:

> $sudo apt-get install gem

也没有解决

在stackoverflow上查到一个[解决方法](http://stackoverflow.com/questions/22460117/error-error-installing-jekyll-error-failed-to-build-gem-native-extension)，安装步骤修改如下：

> - $ sudo apt-get install ruby ruby-dev make
> - $ sudo gem install jekyll --no-rdoc --no-ri
> - $ sudo apt-get install nodejs 

OK!

**Windows下**jekyll的[安装方法](http://jekyllrb.com/docs/windows/#installation)

所有安装完成之后，输入如下命令完成基本创建：

> $ jekyll new myblog
> $ tree myblog

结构如下图：

![myblog_tree](/images/2014-6-7-build-GithubBlog-setup/myblog_tree.png)

启动jekyll本地服务器：

> $ jekyll serve

可在本地页面查看文字效果，浏览器地址*localhost:4000*

至此，一个blog雏形已经搭建完毕了，剩下的就是丰满她的羽翼啦！

### 2 发布

在github上建立名为username.github.io的repositry，注意名字一定是这种结构，这里先不考虑用自己的域名作解析。

这里可以直接使用jekyll new创建的基础框架发布，也可以使用jekyll-bootstrap这样的高级框架，有人说jekyll-bootstrap过于庞大，不利于自己定制，为了学习我也用的jekyll基础框架,慢慢填上自己的东西，当然也可以fork别人的[博客代码](https://github.com/jekyll/jekyll/wiki/Sites)。

在本地建立github博客仓库并发布

> #### #建立本地仓库目录

> $ mkdir username.lypRobin.io

> $ cd username.lypRobin.io

> #### #初始化

> $ git init

> #### #新建博客

> $ jekyll new .

> $ git add .

> $ git remote ser-url origin htts://github.com/username/username.github.io.git

> $ git commit -m "build a new blog site"

> $ git push origin master

![blog-sample](/images/2014-6-7-build-GithubBlog-setup/blog-sample.png "blog-sample")

在username.github.io就能看到一个基础的博客了。至此在github搭建blog完成，后篇开始学习jekyll装饰博客、写博文的细节。




####  *----------The ENd------------*







