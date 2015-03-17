---
layout: default
title: bootstrap初探
---
### 0. 引

Bootstrap是Twitter两名员工开发的最受欢迎的前端框架，给予HTML、CSS和JS，简洁明了，让我这样的前端新手也能快速上手。简单看一下网上的[教程](http://www.w3cschool.cc/bootstrap/bootstrap-tutorial.html),就能轻松修改一个模板或自己写出一个简单的前端页面。本博客网站就是给予jekyll和bootstrap搭建，有人说bootstrap过于庞大，当然也可以定制自己的小清新页面，但作为新手快速了解前端架构不失为一个不错的方案。

就本站来说jekyll+bootstrap可以写在一起，但不免抹煞bootstrap且看起来较为凌乱，因此把bootstrap独立出来，也给以后可能用到的地方作为经验参考。

本文会不定期更新bootstrap学习经验与成果，同时在本站实验前端效果，如果发现页面经常变化请淡定处理。

### 1. 安装

So easy！[官网](http://getbootstrap.com/)下载，分为预编译版本和源码版本。因为bootstrap的css是一个叫less的动态样式工具编写的，可以动态生成css（番外：技术日新月异，本来本着构建博客的jekyll，半途出来bootstrap，看来以后也要摸索下less了），预编译版本是编译好的css文件，而源码版本还要使用Recess工具来编译less文件。



这是标准的安装方法。至于本站嘛。。。还是在前人网站的基础上改的，仅供技术交流，嗯嗯。


往下就是学习过程了，排名不分先后、难易、美丑，学到哪写到哪。

### 2. 栅格系统

bootstrap的栅格系统作为布局页面的基本方法，Bootstrap内置了一套响应式、移动设备优先的流式栅格系统，随着屏幕设备或视口（viewport）尺寸的增加，系统会自动分为最多12列。它包含了易于使用的预定义classe，还有强大的mixin用于生成更具语义的布局。

栅格布局如下

！[bootstrap grid](/images/2015-3-15-Learning-bootstrap/bootstrap-grid.jpg)

在<div>的class选项中前缀分别为col-xs、col-sm、col-md、col-lg，后面跟以数字，例如`col-mid-12`、`com-sm-8`等，分别为














