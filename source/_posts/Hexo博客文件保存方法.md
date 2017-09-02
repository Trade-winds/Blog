---
title: 使用Git管理Hexo博客文件
date: 2017-05-31 00:00:32
tags:
  - hexo
  - blog
categories:
  - Hexo
---

### 前言

关于如何使用Hexo搭建博客的方法网上有很多教程，例如 [如何搭建一个独立博客——简明Github Pages与Hexo教程](http://www.jianshu.com/p/05289a4bc8b2)。但是对于已经写了很多博客的Hexo，会碰到怎么保存和迁移的问题，毕竟很多情况下我们都会面临更换电脑、更换系统或者我们都多台电脑的问题。为了能够每一台电脑上都能写博客，可能大部分同学想到的方法就是讲整个文件拷贝过去，但其实还有更好的方法。对于我们的hexo博客文件，我们会发现在根目录下是有一个'.gitignore'文件的：  
<div align=center>![.gitignore](http://ok16gn2ql.bkt.clouddn.com/2017/05/31/01.png)</div>
这就意味着，使用Git来管理我们的hexo博客文件，是官方推荐的方法  

<!--more-->
### 使用Git管理Hexo

首先，我们要有已经搭建好的Hexo博客，如何搭建大家可以Google，网上一大堆教程，也可参考前言中的搭建教程
搭建好以后，在博客文件的根目录下执行Git操作，并将博客文件全部添加至Git仓库中：

``` bash
~ git init
~ git add .
~ git commit -m "init hexo"  //将博客文件提交git
```
接着将本地Git库提交到远程GitHub或码云等代码托管平台中,这样我们的博客文件就能脱离于电脑独立保存

``` bash
~ git remote add origin git@github.com:xxx/xxx.git
~ git push -u origin master
```

### 利用托管的博客文件重建Hexo

当需要重建Hexo博客时，将远程库中的文件克隆到本地

``` bash
~ git clone git@github.com:xxx/xxx.git
```
之后确定本地已经安装hexo,即执行安装hexo命令
``` bash
~ npm install -g hexo
```
进入clone至本地的hexo文件根目录中执行部署博客文件的命令，**注意：不要执行`hexo init`命令，该命令会在该目录下重新初始化hexo，这样我们的文件将会丢失**
之后执行
``` bash
~ hexo generate
~ hexo server
~ hexo deploy
```
等命令就可以在本地重新构建Hexo博客，接着我们之前的博客接着写作，并使用Git管理我们的Blog也非常方便
