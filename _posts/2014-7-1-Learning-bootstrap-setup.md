---
title: bootstrap初探
layout: post
category: blog
tag: bootstrap
---

### 引

Bootstrap是Twitter两名员工开发的最受欢迎的前端框架，给予HTML、CSS和JS，简洁明了，让我这样的前端新手也能快速上手。简单看一下网上的[教程](http://www.w3cschool.cc/bootstrap/bootstrap-tutorial.html),就能轻松修改一个模板或自己写出一个简单的前端页面。本博客网站就是给予jekyll和bootstrap搭建，有人说bootstrap过于庞大，当然也可以定制自己的小清新页面，但作为新手快速了解前端架构不失为一个不错的方案。

就本站来说jekyll+bootstrap可以写在一起，但不免抹煞bootstrap且看起来较为凌乱，因此把bootstrap独立出来，也给以后可能用到的地方作为经验参考。

本文会不定期更新bootstrap学习经验与成果，同时在本站实验前端效果，如果发现页面经常变化请淡定处理。

### 安装

So easy！[官网](http://getbootstrap.com/)下载，分为预编译版本和源码版本。因为bootstrap的css是一个叫less的动态样式工具编写的，可以动态生成css（番外：技术日新月异，本来本着构建博客的jekyll，半途出来bootstrap，看来以后也要摸索下less了），预编译版本是编译好的css文件，而源码版本还要使用Recess工具来编译less文件。

下载后的文件树如下：

![bootstrap-tree](/images/2015-3-15-Learning-bootstrap/bootstrap-tree.png)

**Hello World**

基本的HTML模板如下：

{% highlight html %}
<!DOCTYPE html>
<html>
   <head>
      <title>Bootstrap 模板</title>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <!-- 引入 Bootstrap -->
      <link href="http://apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet">

      <!-- HTML5 Shim 和 Respond.js 用于让 IE8 支持 HTML5元素和媒体查询 -->
      <!-- 注意： 如果通过 file://  引入 Respond.js 文件，则该文件无法起效果 -->
      <!--[if lt IE 9]>
         <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
         <script src="https://oss.maxcdn.com/libs/respond.js/1.3.0/respond.min.js"></script>
      <![endif]-->
   </head>
   <body>
      <h1>Hello, world!</h1>

      <!-- jQuery (Bootstrap 的 JavaScript 插件需要引入 jQuery) -->
      <script src="https://code.jquery.com/jquery.js"></script>
      <!-- 包括所有已编译的插件 -->
      <script src="js/bootstrap.min.js"></script>
   </body>
</html>

{% endhighlight %}

剩下的就是在此基础上添加html代码，利用css和js做出页面效果了。

这是标准的安装方法。至于本站嘛。。。还是在前人网站的基础上改的，仅供技术交流，嗯嗯。


往后就是学习过程了，排名不分先后、难易、美丑，学到哪写到哪，尤其是过程中遇到的坑。




