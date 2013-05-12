---
layout: post
title: "maven_introduce"
description: ""
category: "tec"
tags: [maven,课程]
---
{% include JB/setup %}

## maven要解决的问题

在我们学习第一个java程序helloworld的时候，我们只需要jdk 和 文本编辑器就可以了，通过javac 编译源码，java 运行class，简单直接，完全没有
工程（project）的概念。

然而在现实世界里，我们更多的在完成一个个工程。比如详情系统

原始的系统很简单，大致结构是：
> DAO 层负责从数据库获取数据
>
> WEB 层负责把DAO 层获取到的数据展示出来

为了实现这样一个系统，我们需要组织好我们的代码，加入相关的依赖，最终打包成标准的 war 。

在maven 之前：

> 我们可以用ide创建project，在对应的目录下copy进相关依赖jar，在相应的目录下编写源码，通过ide打包。done

虽然这样我们也能顺利完成工作，但是也存在以下的几点问题：

1. 工程结构和ide相关，我们有可能使用不同的ide（eclipse,idea,netbeans等），所以可能存在一些合作上的问题
2. 依赖太难维护，需人工的去下载依赖并和源码一起传播
3. 生产环境下不能直接通过源码进行测试打包

***maven***的快速流行就是因为它很好的解决了以上问题：

1. maven 提供了标准的项目结构，规范及生命周期
2. 强大的依赖体系方便我们快速的找到依赖及间接依赖
3. 强大的插件体系可以完成项目的优化和定制

## maven的简单使用

maven是一套完整的解决方案，它包含 maven 服务器 Nexus ， maven 客户端2进制包 ，以及构建在客户端api上的ide 插件体系，如m2eclipse.
对于我们初学者来说，掌握maven 客户端是第一步。

接下来我们一起初步认识下maven

### 创建工程
创建maven 工程的方式有多种：
1. 通过ide生成
2. 通过命令行: mvn　archetype:create　-DarchetypeArtifactId=maven-archetype-webapp
3. 手工生成，创建文件夹及修改pom文件

以上3种方式都一样，对于我们来说有有最重要的4个概念：
1. groupId
2. artifactId 
3. version
4. package

### 编写源码
工程创建好了之后我们就可以开始协同开发了，那我们在把源码放在哪里呢？ 

为什么我们需要关注源码的路径呢？ 因为我们需要对源码路径下的java和resource文件进行编译或者打包。

为了解决这个小问题，maven 默认设置了 `src/main/java` 和 `src/test/java` 为对应的源码文件路径，当然这个默认规则也是可以被改写的（重载）

另外我们还需要关注源码的 encoding 和 java 编译的版本

### 增加依赖
接下来我们为简单的详情系统增加spring和ibatis的依赖

#### 依赖坐标
怎样在maven庞大的仓库中找到对应的依赖呢？
通过groupid : artifactid : version 3个要素我们可以在maven 系统中定位到对应的依赖，这个也就是我们通常所说的坐标

#### 依赖范围
maven 中的依赖根据现实场景不同会有不同的表现形式，比如编译，测试，打包


#### 依赖排除

#### 依赖仲裁

### 打包发布



## maven进阶（模块化）

## 定制化
