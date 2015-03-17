---
title: Learning jekyll 
layout: post
---

### 1. jekyll基本架构

使用`jekyll new`命令生成的初始基本架构如下：

    .
    ├── _config.yml
    ├── _drafts    #**_drafts为草稿文件夹**
    |   ├── begin-with-the-crazy-ideas.textile
    |   └── on-simplicity-in-technology.markdown
    ├── _includes   #**_includes为layout使用到的HTML文件**
    |   ├── footer.html
    |   └── header.html
    ├── _layouts   #**_layouts为布局文件夹，里面存放布局模板**
    |   ├── default.html
    |   └── post.html
    ├── _posts   #**最终发表文章的文件夹**
    |   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
    |   └── 2009-04-26-barcamp-boston-4-roundup.textile
    ├── _data
    |   └── members.yml
    ├── _site
    └── index.html 


### 2. jekyll _config.yml配置文件

_configy.yml文件是jekyll的全局配置文件，里面定义若干选项对，例如：

    title：Name of site
    email: Your email address

    还有一些默认的配置，如：
    source: .
    destination: ./_site
    limit_posts: 0
    highlighter: rouge
    permalink: date
    paginate_path: /page:num

    同样可以加一些自己喜欢的配置：
    author:
        name: XXX
        email: xxx@yyy.com
        imageLink: /path2image/img.jpg
        iterm-name-you-like: xxxxxx

    或者一些array：
    customLinks:
       - item:
          - text: 技术
            url: http://www.xxx.com
          - text xxx
            url: http://sss.com
          message: xxx
       - item:
          - text: 生活
            url: http://www.xxx.com
          - text xxx
            url: http://sss.com
          message: xxx

这些配置选项的关键词，在每个html文件中都会以`site.xxx.yyy`的形式出现，利用Liquid这种标签模板形式，如`{ { site.xxx.yyy } }`

### 3. jekyll YML Front Matter文件头
jekyll通过yml front matter解析主目录下的每个文件头，每个文件头为如下格式：

    ---
    layout：default
    title：hello world
    ---

layout、title为预定义的变量值。


jekyll下每个HTML页面（除了_layouts/default.html）、markdown文章都包含这个文件头。在每个页面HTML中使用`{{ page.xxx }}`来代表该关键字的值。

一般在_config.yml可以设置default关键字array，每个页面默认自动调用里面的配置，若独立页面有自己的文件头，则取代默认文件头对应的关键字。默认关键字配置参见[官网说明](http://jekyllrb.com/docs/configuration/)

### 4. 一些HTML & CSS知识
1). div、span等标签下 class="class-name"中的class-name对应css文件中相应的`.class-name`，如：
{%  highlight html %}
    <div class="class-name">
        ……
    </div>
{%  endhighlight %}



### n. jekyll Tips

**1).文章内嵌代码配色及行号：**
jekyll内部支持Pygments或Rouge语法高亮，如下

    { % highlight ruby linenos % }   #注意去掉{ 和%之间的空格
    def show
      @widget = Widget(params[:id])
      respond_to do |format|
        format.html # show.html.erb
        format.json { render json: @widget }
      end
    end
    { % endhighlight % }


效果如下：

{% highlight ruby linenos %}
	def show
	@widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}

**2).文章内指向某篇blog**

使用post_url标签，方法如下：

    { % post_url 2015-3-4-name-of-post % }  #注意去掉{ 和%之间的空格
    或
    "{ % post_url /DirToPost/2015-3-4-name-of-post % }"
    或
    "[Name of Link]({ % post_url 2015-3-4-name-of-post % })"
    效果为[这样]({% post_url 2015-3-6-搭建GithubBlog %})




