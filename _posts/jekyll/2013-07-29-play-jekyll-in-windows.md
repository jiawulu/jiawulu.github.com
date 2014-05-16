---
layout: post
title: "play jekyll in windows"
description: "如何在windows下安装运行jekyll"
category: "tools"
tags: [tools]
---
{% include JB/setup %}

# why jekyll

jekyll 是近来比较流行的一个静态页面生成工具，同时它又能和`markdown`有非常好的配合。 使用这2者之后：

1. 写作者只需要关注自己的文章，稍微再加些markdown的点缀
2. jekyll则提供文本之外的所有环境，包括样式，小widget，评论等，学习使用的过程也是了解开源世界的过程。

# install

在类unix系统下安装jekyll非常方便，短短几个命令就行：

    apt get install ruby
    gem install jekyll

但是这些在windows下没这么方便，经过早上1个多小时的实践总结如下：

## 安装ruby

直接去 ruby installer 下载 [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/),版本最好是1.9.3的.

最后安装之后也应该把ruby的path加入到 windows 的环境变量中去，这样 ruby 和 gem 2个命令行工具就准备好了

## 安装devkit

下载最新的DevKit(我下载的是[DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe](https://github.com/downloads/oneclick/rubyinstaller/DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe))并双击运行解压到C:\DevKit。然后打开终端cmd，输入下列命令进行安装：

    cd C:\DevKit
    ruby dk.rb init
    ruby dk.rb install

## 安装 jekyll

完成上面的准备就可以安装Jekyll了,因为Jekyll是用Ruby编写的,最好的安装方式是通过RubyGems(gem):

    gem install jekyll

并使用命令jekyll --version检验是否安装成功

一般而言，还可以安装Rdiscount，这个用来解析Markdown标记的包，使用如下命令：

    gem install rdiscount

# jekyll project搭建

## 初始化
jekyll本身的作用只是静态页面的生成，如何要做出漂亮的个人wiki完全要看开发者的水平了，但是有个好消息就是有比较多的模板可以选择，其中一个就是[https://github.com/plusjade/jekyll-bootstrap](https://github.com/plusjade/jekyll-bootstrap), 直接集成了bootstrap，cool~

其他细粒度的配置还要自己参考教程

## 添加文章

    rake post title="TODO"

这样默认就在`_post` 目录下生成了一个  md 文件，我们只需要去编辑它就可以了

## 生成静态站点

    jekyll build

这样就生成了一个静态站点，目录位于`_site`
# 注意事项

## 编码问题
windows的默认编码是gbk，而一般我们的文本都是用UTF-8编码的，所以不进行一些处理的话难免有点问题，一个有效的解决方法如下：

    export LC_ALL=zh_CN.UTF-8
    export LANG=zh_CN.UTF-8
    jekyll build

## post写法注意事项
    ---
    layout: post
    title: "install jekyll in windows"
    description: "如何在windows下安装运行jekyll"
    category: "tools"
    tags: [tools]
    ---
    {% include JB/setup %}

每篇post都对应这么一段，特别注意 **tags**那里， 【】 中的tag 切不可加**引号** ，可以用 `,` 分隔  

>如 `tags: [a,b,c]`


