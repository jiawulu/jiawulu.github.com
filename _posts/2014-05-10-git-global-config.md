---
layout: post
title: "git global config"
description: ""
category: "tools"
tags: [git,shell,tools]
---
{% include JB/setup %}

## git 的全局配置

### alias

    git config --global alias.co checkout
    git config --global alias.ci commit
    git config --global alias.br branch
    git config --global alias.st status


### 验证

    ➜  ~  git config -l
    alias.co=checkout
    alias.ci=commit
    alias.br=branch
    alias.st=status
    user.name=wuzhong
    user.email=wuzhong@taobao.com
    push.default=simple
    core.autocrlf=input
    core.excludesfile=~/.gitignore

### ~/.gitconfig

## ignore

    git config --global core.excludesfile '~/.gitignore'


### demo

    ➜  ~  more .gitignore
    *.iml
    .idea
    out



