---
layout: post
title: "grunt_introduce"
description: "grunt basic use"
category: "tools"
tags: [grunt,h5]
---
{% include JB/setup %}

##grunt简介
grunt类似于maven或者ant，是一个基于配置的任务执行器。 grunt本身不具备任何的实际功能，它只负责解析配置文件和执行各个插件任务。 所有具体的功能都是插件完成的。在这一点上和maven非常类似

##grunt安装
参考[官方文档](http://gruntjs.com/getting-started)

    npm install -g grunt-cli

> Note that installing grunt-cli does not install the grunt task runner! The job of the grunt CLI is simple: run the version of grunt which has been installed next to a Gruntfile

grunt-cli 不是真正的 grunt , 它负责运行 配置 中对应的具体 grunt 。为什么要这么做，我的理解是：
1. grunt-cli作为通用的grunt执行者在一台电脑上只有一个版本，并且提供了统一的grunt命令方便用户使用
2. 具体的grunt是和我们的代码工程相关的，有可能我们要对应多个不同的grunt版本，这样我们就可以统一的通过grunt命令来执行具体的grunt了

##Gruntfile.js
gruntfile就是具体的要执行的任务配置，下面看一下简单的配置：
### 插件配置
        less: {
            dev: {
                files: {
                    "<%= 'dist/detail_' + pkg.version + '.css' %>" : "less/detail.less"
                }
            }
        }
less 对应的是插件less的名称，dev 标示的是一个group，这个应该是可选的，具体要看插件的配置说明。 这样以后我们就可以通过 ``grunt less:dev`` 执行了。

### 装载插件
仅仅加上配置是不够的，我们还必须给插件挂上

    grunt.loadNpmTasks('grunt-contrib-less');

这样在grunt执行的时候就能把less插件加载起来，保证可以对上面的配置进行解析，接下来我们再了解下插件怎么安装的

### 插件安装
一般来说我们对应的工程都会有一个package.json , 这里负责写一些开发者信息，版本及依赖配置，类似于maven的pom文件。所以首先我们需要在package.json中加入依赖的插件

    "devDependencies": {
        "grunt": "0.4.x",
        "grunt-contrib-less" : "~0.5.1",
    },

这样插件的信息配置好了，less需要大于 0.5.1 的版本，接下来我们就在package.json对应的目录下执行 ``npm install`` 就能把具体的依赖（插件）安装到本地了，具体路径在对应目录的 ``node_modules``目录下，grunt （nodejs） 也就会在该目录下找到依赖

### 注册任务
其实通过上边的步骤我们已经可以通过grunt执行less任务了，但是需要执行 ``grunt less``, 这只是一个插件，如果我们希望一个命令能执行多个插件的话那就需要用如下方式了：

    grunt.registerTask("default", [ "atc","concat:base","concat:detail","concat:desc","concat:rate", "less:dev"]);

这样以后我们直接在目录下执行grunt就可以执行多个任务了

## 最后
grunt的强大在于它有丰富的插件体系，你可以使用现有的各类插件，也可以自己动手写一个发布到npmjs上，都很简单。通过grunt可以大大的提供我们的开发效率，让我们更加专注在代码上。另外发现和寻找一些插件也是grunt使用过程中的乐趣

##Gruntfile.js demo
    module.exports = function (grunt) {
        "use strict";
        var _ = grunt.util._,
            path = require("path"),
            name = "item_detail",
            dist = "dist";

        // Project configuration.
        grunt.config.init({
            pkg: grunt.file.readJSON("package.json"),
            version: "<%= pkg.version %>",
            versionSuffix: "<%= pkg.version %>",
            headHash: "",
            headShortHash: "",

            less: {
                dev: {
                    files: {
                        "<%= 'dist/detail_' + pkg.version + '.css' %>" : "less/detail.less"
                    }
                }
            },

            atc: {
                detail : {
                    src : ['js/detail/template'],
                    namespace : 'taobao.plugins.item.detail.template',
                    cloneHelpers : false
                }
            },

            concat: {
                base : {
                    src : [
                        '../../../base/utils/namespace.js',
                        './js/utils/view.js'
                    ],
                    dest: path.join(dist, 'base') + "_<%= versionSuffix %>.js"
                },
                detail: {
                    src: [
                        './js/detail/template/$helpers.js',
                        './js/detail/template/detail.js',
                        './js/detail/main.js'
                    ],
                    dest: path.join(dist, 'detail') + "_<%= versionSuffix %>.js"
                },
                desc: {
                    src: [
                        './js/desc/view/paramView.js',
                        './js/desc/view/imagetView.js',
                        './js/desc/main.js'
                    ],
                    dest: path.join(dist, 'desc') + "_<%= versionSuffix %>.js"
                },
                rate: {
                    src: [
                        './js/rate/view/commentView.js',
                        './js/rate/main.js'
                    ],
                    dest: path.join(dist, 'rate') + "_<%= versionSuffix %>.js"
                }
            },

            jshint: {
                files: ['gruntfile.js', 'dist/*.js'],
                options: {
                    // options here to override JSHint defaults
                    globals: {
                        namespace: true,
                        $: true,
                        console: true,
                        document: true
                    }
                }
            },

            watch: {
                files: ['**/*.js', 'less/*.less','!dist/*'],
                tasks: ['default']
            }
        });

        // grunt plugins
        grunt.loadNpmTasks('grunt-contrib-jshint');
        grunt.loadNpmTasks("grunt-atc");
        grunt.loadNpmTasks("grunt-contrib-concat");
        grunt.loadNpmTasks('grunt-contrib-watch');
        grunt.loadNpmTasks('grunt-contrib-less');

    //    grunt.registerTask( "concat", [ "concat:js" ] );
        // Default grunt
        grunt.registerTask("default", [ "atc","concat:base","concat:detail","concat:desc","concat:rate", "less:dev"]);

    };




