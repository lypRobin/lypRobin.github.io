---
layout: post
title: Atom编辑器下Markdown中使用latex
category: blog
tag: jekyll
---

在jekyll中一般使用markdown作为文本格式，其中不免遇到需要输入数学公式，这时latex就要大显身手。而在Atom编辑器中，需要一些插件才能可以预览Markdown文本以及编辑好数学公式的文本。

### 1. 在Atom中加入预览Markdown插件

在ubuntu环境下，Atom菜单栏`Edit->preference`调出setting，进入到`install`来安装`Markdown-preview-plus`这个插件，同时在`package`中差找到`Markdown-preview`插件并Disable掉，到此插件安装完毕。

* 开始/结束预览: `ctrl+shift+m`
* 在预览状态，开始/结束预览数学公式: `ctrl+shift+x`

在Atom中默认使用MathJax作为数学公式编辑插件，支持多数latex语法。

### 2. 让jekyll能够解析带有latex的markdown文本

在个人博客项目根目录下，在`_layout/default.html`文件中适当位置加入

```
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
```

### DONE！
