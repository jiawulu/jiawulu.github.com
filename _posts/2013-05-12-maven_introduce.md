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

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=333jHH8w0&pInfo=A3YjLHQv&showBig=true&app_name=)

    pom.xml
![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=733jHH8w4&pInfo=A3YjLHQv&showBig=true&app_name=)

### 编写源码
工程创建好了之后我们就可以开始协同开发了，那我们在把源码放在哪里呢？ 

为什么我们需要关注源码的路径呢？ 因为我们需要对源码路径下的java和resource文件进行编译或者打包。

为了解决这个小问题，maven 默认设置了 `src/main/java` 和 `src/test/java` 为对应的源码文件路径，当然这个默认规则也是可以被改写的（重载）

另外我们还需要关注源码的 encoding 和 java 编译的版本

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=P33jHH8vw&pInfo=A3YjLHQv&showBig=true&app_name=)

当然文件源码路径也可以重载默认的配置

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=833jHH8wB&pInfo=A3YjLHQv&showBig=true&app_name=)

### 增加依赖
接下来我们为简单的详情系统增加spring和ibatis的依赖

#### 依赖坐标
怎样在maven庞大的仓库中找到对应的依赖呢？
通过groupid : artifactid : version 3个要素我们可以在maven 系统中定位到对应的依赖，这个也就是我们通常所说的坐标

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=G33jHH8vp&pInfo=A3YjLHQv&showBig=true&app_name=)

首先看一下完整的依赖树

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=633jHH8w5&pInfo=A3YjLHQv&showBig=true&app_name=)

#### 依赖范围
现实世界中的依赖作用各不一样，比如：

    1. junit 等单元测试类库理想情况下仅仅在测试环节有效，对于main 或者 打包后不应该有关联
    2. servlet api等应该只在编译的时候起作用，因为web服务器肯定会内置servlet api，打包进去也加载不了
    3. spring 我们希望在编译，测试，打包中都必须存在

所以为了满足我们这个需求，它提供了 编译，测试，provided ， runtime 4种scope，理解和使用这些scope是用好maven的关键之一

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=533jHH8w6&pInfo=A3YjLHQv&showBig=true&app_name=)

#### 依赖排除
依赖的间接依赖性可以很方便的解决好一系列的依赖性问题，但同时这也是把双刃剑。因为你不知道你的直接依赖会给你引入多少的其他依赖，
并且有些时候那些都会引起莫名奇妙的异常，比如 ClassNotFoundException。 所以我们必须时刻关注我们的依赖树。

我认为控制依赖树的冗余也是代码质量的一个方面，越多的依赖越容易引起问题

maven提供了dependency：tree 方便我们清楚的知道整个依赖的结构，通过分析我们可以做相关的优化。 那最常见的就是依赖的排除了

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=O33jHH8vx&pInfo=A3YjLHQv&showBig=true&app_name=)

#### 依赖仲裁
maven 有些时候显得比较茫然，需要我们的帮助
比如我们直接依赖 A 和 B, 但同时A 和 B 都间接依赖 C 这个间接依赖，那么如果我们不对C的版本做任何的控制的话，maven 会加载其中的某个
版本，从而不在我们的控制之内，可能造成某些异常~

解决这个方式的思路很简单就是明确支持这个工程中 C 的具体版本。

关于依赖仲裁有2个原则：

    1. 间接依赖重复了必须要仲裁
    2. 没有重复的间接依赖最好不要仲裁

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=N33jHH8vq&pInfo=A3YjLHQv&showBig=true&app_name=)


## maven进阶（模块化）
我们在追求代码的质量同时也在向模块化靠近。 为了不使用重复代码我们可以提取成公共方法，同样的道理为了跨工程共享这些方法我们可做的就
是共享我们的模块。

### 为什么要拆分
当系统发展到一定程度时候，业务代码比较多的时候，为了更加清晰合理的组织代码，我们一般把工程分成 dao,biz,web 3层， 通过拆分我们得到
的好处是：

    1. 理清依赖顺序，提高代码质量
    2. 有利于形成公有库，被其他工程复用

### 发布二方库
既然形成了公有库，就必须把它发布到maven服务器上，这样其他同事才可以拿到。那么maven是如何实现它的呢？
这就是maven服务器的作用了。

默认maven会带有一个官方服务器，在那里我们可以获取到各种开源的3方库，但是对于公司来说，它的业务代码是不适应放在官方当3方库来使用的，
所以我们必须还得在官方服务器前面搭建一个私服，用于发布和管理2方库。

既然涉及倒服务器的发布相关操作，一般都少不了授权。具体的配置我们看下setting.xml就一目了然了。

这样以后我们就可以通过mvn deploy 直接发布了

### 建议
对于发布出去的二方库，既然是给其他人用的,那么保证其质量是十分必要的。

我的建议如下：

    1. 好好考虑下库的粒度，不要因为对方只用了你的一个工具类而依赖了你整个biz层
    2. 有效的控制间接依赖，手工排除并不是一件轻松的事，可以考虑使用 option 来去除间接依赖
    3. 对于私有依赖需要传递下去，可以保证升级的顺利
    4. 测试，beta阶段尽量使用snapshot来保证代码的更新
    5. 稳定版本坚持使用release 保证稳定度
    6. 合理的使用parent，如果部署的时候不带上parent的话，这个依赖库也使用不了

## 插件
插件是maven的核心，maven 本身不具备任何功能，它只是提供了插件运行的平台， 而具体的实现都是靠一个个的插件完成的。

插件的插入点又和下面的概念有关。

### 了解生命周期
maven 定义了一整套的从构建到发布的完整生命周期。 正是基于这套完整的，可扩展的生命周期，我们可以在任意的阶段插入自定义的任务

### AutoConfig 
#### 为什么要重复造轮子
    1. 可以直接改变目标文件中的 placeholders 值
    2. 在替换的过程中及时的验证，提前发现错误
    3. 支持嵌套替换，满足复杂条件
    4. 统一化的配置管理中心，方便查询，审核

#### 简单使用
    1. 给工程加上autoconfig 插件
    3. 建立 auto-config.xml
    4. 建立模板文件
    2. 申请应用配置项

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=U33jHH8vz&pInfo=A3YjLHQv&showBig=true&app_name=)

