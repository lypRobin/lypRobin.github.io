---
layout: post
title: bootstrap静态图片
category: blog
tag: bootstrap
---

bootstrap中使用静态图片遇到的一些坑，算是前端新手交的学费吧。

### 使用图片

最开始我想在导航栏以下、“标题栏”的位置加入图片，在图片上插入文字信息，效果类似于这样：


<img src="/images/2014-7-3-Learning-bootstrap-image/title-image.png" width="600"></img>

首先一个坑是注意css中`body`类的`padding-top`，这个决定了页面内容距离屏幕顶端起始位置，一开始没注意，导致图片或内容总是往下一点。

第一个方法是直接页面插图片，因为我的图片比较大总是超出屏幕范围，设置width、height比较固定，不好适应屏幕大小。


目前是用bootstrap 超大屏幕组件——Jumbotron，在一个`.container`class中加入`jumbotron`就可以了。

{% highlight html %}
<div class="container">
   <div class="jumbotron">
      <h1>Hello！</h1>
      <p>这是一个超大屏幕（Jumbotron）的实例。</p>
      <p><a class="btn btn-primary btn-lg" role="button">
         Know more</a>
      </p>
   </div>
</div>
{% endhighlight %}

效果如下：

![jumbotron](/images/2014-7-3-Learning-bootstrap-image/jumbotron.png)

在css中设置`background-image:url(xxx/yy.jpg)`,注意url使用的*相对路径*（相对css文件），这里也徘徊许久。

先用这这个方法，有更好的以后更新。




