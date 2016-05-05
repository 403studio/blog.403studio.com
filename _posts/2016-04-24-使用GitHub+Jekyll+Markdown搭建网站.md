---
title: 使用GitHub + Jekyll + Markdown搭建网站
categories: 学习资料
tags: GitHub Jekyll Markdown
---

## 简介

本站的[博客](http://blog.403studio.com)和[文档](http://docs.403studio.com)使用的Jekyll + Markdown工具，之所以采用这两个工具是因为它们让站长真正的关注内容（博客）本身，让站长从网站界面，网站生成中解放出来。其中`博客`是托管在自己搭建的jekyll服务上，`文档`则是托管在GitHub上。

随着GitHub的崛起，越来越多的开源软件工具等都托管在GitHub上。与之相伴随的是static website(静态网站)，[Jekyll](https://jekyllrb.com/)，[Markdown](http://baike.baidu.com/link?url=u_0EtmkBL96fxcJQG8htHn1Ayee-8ajeaYblq_nUj4qdAGP5xMkx_7Sb0yWIJXTfZ9LVBISkEw7fKHRcMKpQOq)的一起跟着流行起来。

GitHub为你的项目提供了一整套环境，无论是代码托管，项目文档说明，项目Wiki，自动部署等都可以在GitHub上一站式解决。代码托管和GitHub Pages为开源项目提供了非常好的环境。

## GitHub Pages

[GitHub Pages](https://pages.github.com/)为你的项目提供了一个非常好得到文档展示环境，他能够自动为你生成文档网站，而你需要做的就是编写文档。具体的案例有[nodejs文档](https://nodejs.org/en/docs/)；[React 文档](http://facebook.github.io/react/docs/getting-started.html)等他们都是直接由GitHub Pages托管生成的。

GitHub内置了Jekyll环境，开发者可以通过使用非常便捷的Markdown语法编写文档，其他的工作则交给GitHub自动完成，具体可以参考官方网站[GitHub Pages](https://pages.github.com/)。



## Jekyll

[Jekyll](https://jekyllrb.com/)能够把普通的文本转换为静态的网站和博客，让你真正需要做的事就是关注内容本身。对于轻博客或者项目文档来说这种静态网站生成工具是非常不错的选择。Jekyll + disqus是非常不错的搭档组合，disqus为网站添加评论功能。

### Jekyll安装

在官方文档上面有安装介绍

1. Ruby安装，`yum install ruby`
2. RubyGems安装，`yum install rubygems`
3. NodeJS安装，`yum install nodejs yum install npm`
4. 使用RubyGems安装Jekyll，`gem install jekyll`，在CentOS系统中如果报错`mkmf.rb can't find header files for ruby at /usr/lib/ruby/ruby.h
`可以试试安装ruby-devel来解决问题
5. 启动，进入相应的目录并启动Jekyll服务 `jekyll serve --detach`
6. 停止，`kill -9 PID`

**注意：**本人在CentOS上根据官方文档使用`gem install jekyll`的时候，提示报错***ruby版本较低，需要升级ruby的版本***，这个是由于使用yum的ruby版本导致的，yum上的软件版本很多时候低于官方的发布版本。可以先卸载已安装的ruby`yum remove ruby`然后直接去ruby上下载源码包安装即可。

## Markdown
Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。它具有非常简单的语法，简单到只有有限的几个常用标记标签。国内非常著名的时候Markdown的案例是[简书](http://www.jianshu.com/)，简书就支持Markdown语法撰写文章。

## 相关资源

[GitHub Pages,关于GitHub Pages的官方说明介绍](https://pages.github.com/)

[在GitHub Pages上使用Jekyll](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)

[Jekyll官方网站](https://jekyllrb.com/)

[Jekyll中文档,bootcss上的Jekyll官方翻译文档](http://jekyll.bootcss.com/)

[搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)