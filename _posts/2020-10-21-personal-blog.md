---
layout: post
title:  "Your first blog"
date:   2020-10-21 10:21:40 +0800
typora-root-url: ..
categories: jekyll
---

* content
{:toc}

# First Post——使用github.io和jekyll搭建个人博客

由于最近可能会阅读一些论文，因此决定搭建一个个人博客用以记录论文阅读过程中可能会产生的笔记，而CSDN的博客我个人不太喜欢，又由于自己的囊中羞涩，最后决定使用github.io结合jekyll搭建一个博客。

## 配置环境

### jekyll

1. install

因为win10内嵌了linux子系统，而使用linux安装jekyll个人觉得要比windows系统相对来说更为容易，因此决定在该linux子系统内配置环境。

{% highlight bash %}

sudo apt install ruby jekyll

{% endhighlight %}

2. new project

{% highlight bash %}

jekyll new helloworld

cd helloworld

jekyll serve

{% endhighlight %}

### github.io

#### Create a repository

在GitHub中创建一个名为username.github.io的新仓库，其中username是你在GitHub上的用户名

![image-20201020190937302](/img/2020-10-21-personal-blog/image-20201020190937302.png)

#### Clone the repository

转到要存储项目的文件夹，然后克隆这个仓库：

{% highlight bash %}

git clone https://github.com/username/username.github.io

{% endhighlight %}

#### Hello world

{% highlight bash %}

cd username.github.io

echo 'Hello, world' > index.html

{% endhighlight %}

### Personal Blog

#### Run the first blog

目前有很多的jekyll主题和模板，在这里提供一个网站[jekyll themes](http://jekyllthemes.org/)和一个教程：[说明文档](https://github.com/Huxpro/huxpro.github.io/blob/master/_doc/README.zh.md)

我个人选择了[monos](http://jekyllthemes.org/themes/monos/)这个主题，下载该主题后解压到你的项目文件夹下，运行之后会出现一些依赖不存在的情况，只需要按照它所提示的名称和版本安装即可

{% highlight bash %}

username:/jekyll-theme-monos-master$ cd jekyll-theme-monos-master

username:/jekyll-theme-monos-master$ jekyll serve

Traceback (most recent call last:

  10: from /usr/bin/jekyll:7:in `<main>'

  9: from /usr/lib/ruby/vendor_ruby/jekyll/plugin_manager.rb:33:in `require_from_bundler'

  8: from /usr/local/lib/site_ruby/2.5.0/bundler.rb:149:in `setup'

  7: from /usr/local/lib/site_ruby/2.5.0/bundler/runtime.rb:20:in `setup'

  6: from /usr/local/lib/site_ruby/2.5.0/bundler/runtime.rb:101:in `block in definition_method'

  5: from /usr/local/lib/site_ruby/2.5.0/bundler/definition.rb:226:in `requested_specs'

  4: from /usr/local/lib/site_ruby/2.5.0/bundler/definition.rb:237:in `specs_for'

  3: from /usr/local/lib/site_ruby/2.5.0/bundler/definition.rb:170:in `specs'

  2: from /usr/local/lib/site_ruby/2.5.0/bundler/spec_set.rb:80:in `materialize'

  1: from /usr/local/lib/site_ruby/2.5.0/bundler/spec_set.rb:80:in `map!'

/usr/local/lib/site_ruby/2.5.0/bundler/spec_set.rb:86:in `block in materialize': Could not find public_suffix-4.0.3 in any of the sources (Bundler::GemNotFound)

username:/jekyll-theme-monos-master$ sudo gem install bundler:1.17.2

{% endhighlight %}

安装过程中可能会出现如下的错误，安装ruby-dev

{% highlight bash %}

username:/jekyll-theme-monos-master$ sudo gem install eventmachine -v 1.2.7

Building native extensions. This could take a while...               

ERROR: Error installing eventmachine:

  ERROR: Failed to build gem native extension.

current directory: /var/lib/gems/2.5.0/gems/eventmachine-1.2.7/ext

/usr/bin/ruby2.5 -I /usr/local/lib/site_ruby/2.5.0 -r ./siteconf20201020-5764-1v6qrrl.rb extconf.rb  

mkmf.rb can't find header files for ruby at /usr/lib/ruby/include/ruby.h

​    extconf failed, exit code 1

Gem files will remain installed in /var/lib/gems/2.5.0/gems/eventmachine-1.2.7 for inspection.   

Results logged to /var/lib/gems/2.5.0/extensions/x86_64-linux/2.5.0/eventmachine-1.2.7/gem_make.out 

username:/jekyll-theme-monos-master$ sudo apt install ruby-dev

{% endhighlight %}

#### markdown编译器

我的第一篇博客如下图所示：

![image-20201021092055720](/img/2020-10-21-personal-blog/image-20201021092055720.png)

感觉样式被解析的十分奇怪，这是因为jekyll的markdown语法和github，或简书上的markdown语法很不一样，比如代码高亮部分只能用以下形式:

![image-20201021104402448](/img/2020-10-21-personal-blog/image-20201021104402448.png)

当我们查看配置文件时，发现`Jekyll`使用的`markdown`解析器是默认使用的`kramdown`，我们可以将其修改为我们需要的markdown解析器，jekyll支持三种markdown解析器：kramdown,redcarpet,rdiscount，同时我们可以通过在***\*_config.yml\****文件中修改`markdown: kramdown`和`kramdown:[]`修改markdown解析器的默认设置。

Github Pages在今年宣布开始使用Jekyll 3.0, 导致的变化有：

* 将只支持kramdown作为Markdown引擎

* 将只支持Rouge作为Markdown代码语法高亮

* 大幅提升构建速度

* 不再支持Permalinks和Textile

所以之前的配置方法现在不再适用，我们需要对Jekyll的相应配置重新进行修改。



#### 配置github

在你的github仓库中修改settings设置，

![image-20201021101011843](/img/2020-10-21-personal-blog/image-20201021101011843.png)

当出现提示“Your site is ready to be published at http://2bebetter.github.io”时，配置成功。

#### 插入图片

我使用的markdown编辑器是typora，插入图片时非常方便，但是不能正确在github上显示，修改typora和配置文件如下：

* typora 

偏好设置->图像->图像插入时，选择"复制到指定位置"并将地址设为 `../img/${filename}`

![image-20201021103739381](/img/2020-10-21-personal-blog/image-20201021103739381.png)

* jekyll

在每一篇post的开头配置行中加入`typora-root-url: ..`

这样插入图片的时候，就会自动在_post平级的img文件下面生成一个与post同名的文件夹并放入名称类似`../img/in-post/post_name/picture_name.png`的图片,通过`/img/post_name/picture_name.png`引用图片的时候typora会去找`../img/post_name/picture_name.png` 能够预览图片，在github pages时`typora-root-url: ..` 无效，找的还是`/img/post_name/picture_name.png`，能够正确显示图片。

#### 目录

plugin：[jossets/jekyll-toc](https://github.com/jossets/jekyll-toc)

这种方法可以兼容github-page：

*A GitHub Pages compatible Table of Contents generator without a plugin or JavaScript :octocat: https://pure-liquid.allejo.org/*

使用方法：

* 下载最新的[toc.html](https://github.com/jossets/jekyll-toc/blob/master/_includes/toc.html)
* 将该文件拖进_includes文件夹下
* 在模板布局页面中`{\{ content \}}`前加入：`\{\% include toc.html html=content \%\}`

#### 数学公式

使用latex编写数学公式是十分方便的，但是markdown本身并不支持latex公式，因此需要一些拓展，或者使用Typora编辑器自带的latex公式编辑。

虽然jekyll并不支持latex，但是可以通过工具来实现，是一个开源的基于 Ajax 的数学公式显示的解决方案，结合多种先进的Web技术，支持主流的浏览器。需要把下面的JS调用代码以及配置插入到你Jekyll博客的header文件中：

{% highlight HTML %}
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
        tex2jax: {
        skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
        inlineMath: [['$','$']]
        }
    });
</script>
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
{% endhighlight %}