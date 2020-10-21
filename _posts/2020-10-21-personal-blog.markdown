---
layout: post
title:  "Your first personal blog!"
date:   2020-10-20 20:05:40 +0800
categories: jekyll
---
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

```bash
jekyll new helloworld
cd helloworld
jekyll serve
```

### github.io

1. #### Create a repository

在GitHub中创建一个名为username.github.io的新仓库，其中username是你在GitHub上的用户名

![image-20201020190937302](C:\Users\wmh\AppData\Roaming\Typora\typora-user-images\image-20201020190937302.png)

2. #### Clone the repository

转到要存储项目的文件夹，然后克隆这个仓库：

{% highlight bash %}
git clone https://github.com/username/username.github.io
{% endhighlight %}

3. Hello world

{% highlight bash %}
cd username.github.io
echo 'Hello, world' > index.html
{% endhighlight %}

### Personal Blog

1. Run the first blog

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
ERROR:  Error installing eventmachine:
    ERROR: Failed to build gem native extension.
current directory: /var/lib/gems/2.5.0/gems/eventmachine-1.2.7/ext
/usr/bin/ruby2.5 -I /usr/local/lib/site_ruby/2.5.0 -r ./siteconf20201020-5764-1v6qrrl.rb extconf.rb    
mkmf.rb can't find header files for ruby at /usr/lib/ruby/include/ruby.h
        extconf failed, exit code 1
Gem files will remain installed in /var/lib/gems/2.5.0/gems/eventmachine-1.2.7 for inspection.     
Results logged to /var/lib/gems/2.5.0/extensions/x86_64-linux/2.5.0/eventmachine-1.2.7/gem_make.out 
username:/jekyll-theme-monos-master$ sudo apt install ruby-dev
{% endhighlight %}

2. markdown编译器

我的第一篇博客如下图所示：

![image-20201021092055720](C:\Users\wmh\AppData\Roaming\Typora\typora-user-images\image-20201021092055720.png)

感觉样式被解析的十分奇怪，这是因为jekyll的markdown语法和github，或简书上的markdown语法很不一样，比如代码高亮部分只能用以下形式:

`{``%` highlight `%``}`
your code....
`{``%` endhighlight `%``}`

当我们查看配置文件时，发现`Jekyll`使用的`markdown`解析器是默认使用的`kramdown`，我们可以将其修改为我们需要的markdown解析器，jekyll支持三种markdown解析器：kramdown,redcarpet,rdiscount，同时我们可以通过在**_config.yml**文件中修改`markdown: kramdown`和`kramdown:[]`修改markdown解析器的默认设置。

Github Pages在今年宣布开始使用Jekyll 3.0, 导致的变化有：

* 将只支持kramdown作为Markdown引擎
* 将只支持Rouge作为Markdown代码语法高亮
* 大幅提升构建速度
* 不再支持Permalinks和Textile

所以之前的配置方法现在不再适用，我们需要对Jekyll的相应配置重新进行修改。





