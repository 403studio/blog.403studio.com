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

## 创建自己组件

Composer使用[packagist](https://packagist.org/)来管理组件信息，使用Github作为组件代码仓库。所以创建自己的组件比较简单，使用composer.json来配置组件信息，使用Github来管理代码和自动更新，而且packagist本身也可以使用Github的账号进行登陆。

在[packagist](https://packagist.org/)上使用Github账号登陆后，导航栏有一个“submit”点击进去后填写Github地址就可以自动获取相关信息了。composer.json的具体相关信息可以访问[https://getcomposer.org/doc/04-schema.md](https://getcomposer.org/doc/04-schema.md)查看，这里需要特别注意的是*autoload*的设置。

### autoload配置

autoload在psr中有psr-0和psr-4，根据官方建议psr-0是不建议使用的。我自己因为没看文档就折腾一开始使用的psr-0，及composer.json的autoload如下：

```
"autoload": {
    "psr-0": {
        "Message403": "src"
    }
}
```
 
 而我的代码组织结构是PROJECT_ROOT/src，我本意是希望Message403这个命名空间指定的目录为src，假如使用：

```
use Message403\Message;
use Message403\MessageHandler\JuHeSMSHandler;
```

希望查找文件应该是PROJECT_ROOT/src/Message.php和PROJECT_ROOT/src/MessageHandler/JuHeSMSHandler.php我直接发布以后，使用composer安装自己的组件，发现用composer提供的加载方式始终无法找到类文件，于是查找问题。后来发现**根据我的配置命名空间Message403指的是/src/Message403**这个位置。后来查看官方文档也发现了psr-4的说明

> Note that as opposed to the older PSR-0 style, the prefix (Foo\\) is not present in the file path.

psr-0会把命名空间的名字添加到文件路径里面。

所以在做配置时最好先看清楚文档，而且psr-0并不推荐使用。我的composer.json修改后变成：

```
"autoload": {
    "psr-4": {
        "Message403\\": "src"
    }
  }
```

### 自动部署

Github为Packagist提供了webservice，进入Github项目的setting找到webservices可以很方便的实现自动更新部署。

### 手动添加命名空间

在创建组件的时候我们可能需要实时的编写一些test脚本，这个时候我们需要把我们的命名空间以composer统一的方式来进行处理

```
require_once  '/vendor/autoload.php';

$loader = new \Composer\Autoload\ClassLoader();
$loader->addPsr4('Message403\\', __DIR__.'/src');
$loader->register();
```

ClassLoader实现了很多包括psr-0自动加载，psr-4自动加载，classmap，includefile这些都是composer的autoload文档里面提到的。手动添加自动加载机制，我们遵循使用psr-4标准然后调用这个类即可完成工作。

[Composer]: https://getcomposer.org/
