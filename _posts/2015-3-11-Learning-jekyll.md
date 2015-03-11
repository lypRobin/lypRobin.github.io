---
title: Learning jekyll (持续更新)
layout: post
---

### *更新记录*

> 2015-3-11  Create blog

### 1 jekyll基本架构

使用`jekyll new`命令生成的初始基本架构如下：






_posts下文章包含数据头 http://jekyllrb.com/docs/frontmatter/


文章内嵌代码配色及行号：

{% highlight ruby linenos%}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}



