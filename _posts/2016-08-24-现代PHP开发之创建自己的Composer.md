---
title: ModernPHP开发之创建自己的Composer
categories: 学习资料
tags: PHP Composer
layout: project
project: ModernPHP
permalink: /:categories/ModernPHP/:title
excerpt: Composer是 现代新型PHP开发的重要组成部分，推动了PHP的大力发展。把组件的思想在PHPer中进行普及，很多小型的项目开发甚至可以从框架开发转移到组件开发。我们只需要定义好依赖的组件一个小型的自定义框架就可以很快的搭建好。
---

* 目录
{:toc}

## Composer

[Composer][Composer]是PHP的一个依赖管理工具，可以认为是PEAR的继承者。Composer借鉴了Node的npm和ruby的bundler，为开发者提供了一个依赖声明和管理工具。在Composer之前PHP官方维护并使用了PEAR，但在很多PHPer心中PEAR不是一个很熟悉的工具。随着PHP的更新以及开源事业的发展，PHP世界需要这么一个被广大PHPer介绍的类似于“包管理”的工具，Composer在这个时候诞生崛起。当然准确来说Composer并不是类似于yum的包管理工具，它是一个依赖管理工具。

关于Composer的一些详细情况可以通过官网进行了解查看，如果读者是一名PHPer还没有了解过这个强大的工具，那么笔者强烈推荐你去了解和使用它同时也试着写一个自己的组件了解Composer的整个流程。

### 创建自己组件
 

[Composer]: https://getcomposer.org/
