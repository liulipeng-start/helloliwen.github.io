---
title: 使用hexo，如果换了电脑怎么保留配置和更新博客
categories: hexo
abbrlink: 870ed150
date: 2019-06-03 15:54:53
tags:
keywords:
description:
---

思路还是使用git的分支来管理，@直上云霄的回答清晰明了，但是需要使用两个文件夹，而且两个文件夹会出现以后新建博客同步问题，除非使用新文件夹进行编辑、发布博客。而@CrazyMilk的回答步骤清楚，但是不够清晰。所以合并@直上云霄 和 @[CrazyMilk](https://www.zhihu.com/people/CrazyMilk) 的回答，使用目前已有的hexo博客目录来管理。

<!--more-->

## 一.备份

1.在github上新建一个hexo分支

2.在仓库的设置里面，设置默认分支为hexo

3.在其他目录，打开git bash，克隆hexo仓库到本地，拿到隐藏的.git文件夹

```
git clone -b hexo <github的https或者ssh的URL>
```

4.复制这个隐藏的.git文件夹到你使用hexo博客的目录下，同时删除博客主题，比如next下面的.git文件夹(因为git不能嵌套上传)，然后你之前克隆hexo仓库的文件夹就可以删除了。

5.上传

```
git add .
git commit –m "你的备注"
git push origin hexo
```

## 二、编辑博客

在新建博客之后

1.先备份到hexo分支

```
git add .
git commit –m "你的备注"
git push origin hexo
```

2.再进行部署

```
hexo cl && hexo g && hexo d
```

## 三、更换电脑

1.安装git

2.设置git全局邮箱和用户名

3.配置SSH Key

4.克隆github分支hexo到本地文件夹

5.安装nodejs 

6.安装hexo(不需要初始化)

参见@直上云霄的回答

来源：[使用hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)

