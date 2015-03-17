---
title: bootstrap 栅格系统
layout: post
---


关于栅格系统详情请看[http://www.w3cschool.cc/bootstrap/bootstrap-grid-system.html](http://www.w3cschool.cc/bootstrap/bootstrap-grid-system.html)，想简略的往下看。

bootstrap的栅格系统作为布局页面的基本方法，Bootstrap内置了一套响应式、移动设备优先的流式栅格系统，随着屏幕设备或视口（viewport）尺寸的增加，系统会自动分为最多12列。它包含了易于使用的预定义class，还有强大的mixin用于生成更具语义的布局。

栅格布局如下

![bootstrap grid](/images/2015-3-15-Learning-bootstrap/bootstrap-grid.png)

在`<div>`的class选项中前缀分别为col-xs、col-sm、col-md、col-lg，后面跟以数字，例如`col-mid-12`、`com-sm-8`等，分别为12格和8格。

栅格系统是这样工作的，同时也是需要注意的：

>- 行必须放置在 .container class 内，以便获得适当的对齐（alignment）和内边距（padding）
>- 使用行来创建列的水平组
>- 内容应该放置在列内，且唯有列可以是行的直接子元素
>- 预定义的网格类，比如 .row 和 .col-xs-4，可用于快速创建网格布局。LESS 混合类可用于更多语义布局
>- 列通过内边距（padding）来创建列内容之间的间隙。该内边距是通过 .rows 上的外边距（margin）取负，表示第一列和最后一列的行偏移。
>- 网格系统是通过指定您想要横跨的十二个可用的列来创建的。例如，要创建三个相等的列，则使用三个 .col-xs-4

大致的结构为：

{% highlight html %}
	<div class="container"> 
		......
		<div class="row">
			<div class="col-mid-8">
				<p>first 8 col</p>
			</div>
			<div class="col-mid-4">
				<p>second 4 col</p>
			</div>
		</div>
	</div>

{% endhighlight %}

网格系统是以左对齐的，如果在一行中只使用了`col-md-8`或`col-md-6`这样，那么页面就只占用最左边的8个或6个grid，这时可以使用列偏移class——`col-md-offset-*`，可以从左边偏移*个grid。

当然还有列嵌套了，只不过**不能**把被嵌套的列重新分为12个grid了，有几个grid就是几个。



















