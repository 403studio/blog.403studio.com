---
title: 开发工具-SublimeLinter 3介绍
categories: 文档翻译
tags: Sublime SublimeLinter
source: http://www.sublimelinter.com/en/latest/
excerpt: SublimeLinter为Sublime Text 3的代码检查提供了一个框架插件。无论使用的哪种编程语言，SublimeLinter都能够帮助我们编写更友好，更少Bug的代码。SublimeLinter被设计成对用户便捷友好易用对Linter开发者简单易上手。如果正在使用Sublime Text 3，那么SublimeLinter是您必备的工具
---

* 目录
{:toc}

## SublimeLinter

[SublimeLinter 3][SublimeLinter]为Sublime Text 3代码检查提供了一个框架插件。无论使用的哪种编程语言，SublimeLinter都能够帮助我们编写更友好，更少Bug的代码。SublimeLinter被设计成对用户便捷友好易用对Linter开发者简单易上手。

SublimeLinter的文档分为两大章节：一个是对普通用户的另外一个是针对Linger开发者的。

[User Documentation](http://www.sublimelinter.com/en/latest/#user-documentation) | [Developer Documentation](http://www.sublimelinter.com/en/latest/#developer-documentation)

SublimeLinter的代码同时也部署在[GitHub](https://github.com/SublimeLinter/SublimeLinter3)

## 用户文档指南

### 关于SublimeLinter

SublimeLinter是一个Linter框架。真正做代码检查提供Linter功能的是而外其他的Sublime Text 3插件，这些插件都可以通过Package Control来进行安装。

### 什么是Linter

一个Linter就是一个简单程序它能够提供代码样式和错误的检查。有很多各种各种的Linter提供了各类语言的检查功能，例如Python的[flake8](http://flake8.readthedocs.org/en/latest/),JavaScript的[jshint](http://jshint.org/docs/),CSS的[csslint](https://github.com/stubbornella/csslint/wiki/About)。

SublimeLinter本身并不提供Linter功能，它为各类Linter插件提供了宿主功能它提供了一个框架基础，实际上Linter插件通常也不是真正的代码检查者，它为Sublime Text以及代码检查者Linter提供了一个连接运行方式。例如SublimeLinter-php就是通过php执行程序来检查代码，SublimeLinter-php只是为两者提供了一个连接通道。

每种语言并不是只能安装一个SublimeLinter，我们可以自由的安装各类合适的SublimeLinter
为某一类语言。

### 为什么需要使用SublimeLinter

编写代码无可避免的会出现各种错误，使用SublimeLinter最大的优势就是我们在编写代码的时候就能够为我们提示代码的语法错误并且高亮它。

SublimeLinter也能够帮助我们使用标准的编码风格

### 通过Package Control安装SublimeLinter

安装SublimeLinter最简单便捷的方式就是通过Package Control来安装。

1. 打开命令行界面（Windows下使用 ctrl+shift+p）
2. 输入`install`并选择**Package Control：Install Package**。Package Control在搜寻可用的插件包的时候可能会需要点时间。
3. 当可用的插件以列表的形式展现在命令行界面的时候，输入`linter`并选择**SublimeLinter**。注意：**github仓库名是使用的SublimeLinter3，但是插件的名称仍然使用的SublimeLinter**
4. 经过一小段时间后SublimeLinter将被安装到Sublime。根据个人的设置，可能会出现一些提示信息。
5. 安装完成后可以看到安装信息，阅读完安装信息后重新启动Sublime即可
 

[SublimeLinter]: http://www.sublimelinter.com/en/latest/
