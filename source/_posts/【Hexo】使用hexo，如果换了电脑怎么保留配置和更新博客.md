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



### 异地同步博客内容

　　现在电脑已经很普及了，因为一般来说我们都是公司一台电脑，家里一台电脑，那么如何将两台电脑上博客的内容同步内，即两台电脑上都可以编辑更新博客？
要解决这个问题，首先我们要清楚我们博客文件的组成：

- node_modules
- public
- scaffolds
- source
- themes
- _config_yml
- db.json
- package.json
- .deploy_git

　　以上为利用hexo生成的博客全部内容，那么当我们执行hexo d时，正真被推送到github上的又有哪些内容呢？
　　我们可以看下github上的tengzhangchao.github.io项目，发现里面只有Public目录下的内容。也就是说，我们博客上呈现的内容，其实就是public下的文件内容。那么这个Pulic目录是怎么生成的呢？
　　一开始hexo init的时候是没有public目录的，而当我们运行hexo g命令时，public目录被生成了，换句话说hexo g命令就是用来生成博客文件的（会根据_config.yml，source目录文件以及themes目录下文件生成）。同样当我们运行hexo clean命令时，public目录被删除了。
　　好了，既然我们知道了决定博客显示内容的只有一个Public目录，而public目录又是可以动态生成的，那么其实我们只要在不同电脑上同步可以生成Public目录的文件即可。

以下文件以及目录是必须要同步的：

- source
- themes
- _config.yml
- db.json
- package.json
- .deploy_git

　　同步的方式有很多种，可以每次更新后都备份到一个地址。我采用github去备份，也就是新建一个项目用来存放以上文件，每次更新后推送到github上，用作备份同步。
　　同步完必须的文件后，怎么再其他电脑上也可以更新博客呢？
　　前提假设我们现在配置了一台新电脑，里面没有安装任何有关博客的东西，那么我们开始吧：

- 下载node.js并安装（官网下载安装），默认会安装npm。
- 下载安装git（官网下载安装）
- 下载安装hexo。方法：打开cmd 运行*npm install -g hexo*（要翻墙）
- 新建一个文件夹，如MyBlog
- 进入该文件夹内，右击运行git，输入：*hexo init*（生成hexo模板，可能要翻墙)

　　我们重复了一开始搭建博客的步骤，重新生成了一个新的模板，这个模板中包含了hexo生成的一些文件。

- git clone 我们备份的项目，生成一个文件夹，如：MyBlogData
- 将MyBlog里面的node_modules、scaffolds文件夹复制到MyBlogData里面。

　　做完这些，从表面上看，两台电脑上MyBlogData目录下的文件应该都是一样的了。那么我们运行hexo g
hexo d试试，如果会报错，则往下看。

- 这是因为.deploy_git没有同步，在MyBlogData目录内运行:*npm install hexo-deployer-git –save*后再次推送即可

　　总结流程：当我们每次更新MyBlog内容后，先利用hexo将public推送到github，然后再将其余必须同步的文件利用git推送到github。



## 创建Hexo分支

创建两个分支：master 与 Hexo,并将Hexo设置为默认分支（这个Hexo分支就是存放我们源文件的分支，我们只需要更新Hexo分支上的内容据就好，master上的分支hexo编译的时候会更新的）

## 删除文件夹内原有的.git缓存文件夹并编辑.gitignore文件

因为有些主题是从git上clone过来的，所以我们要先删除.git缓存文件，否则会和blog仓库冲突（.git默认是隐藏文件夹，需要先开启显示隐藏文件夹。**.git文件夹被删除后整个文件对应的git仓库状态也会被清空**)
.gitignore文件作用是声明不被git记录的文件，blog根目录下的.gitignore是hexo初始化带来的，可以先删除或者直接编辑，对hexo不会有影响。建议.gitignore内添加以下内容：

```
/.deploy_git
/public  
/_config.yml复制代码
```

> .deploy_git是hexo默认的.git配置文件夹，不需要同步

public内文件是根据source文件夹内容自动生成，不需要备份，不然每次改动内容太多
即使是私有仓库，除去在线服务商员工可以看到的风险外，还有云服务商被攻击造成泄漏等可能，所以不建议将配置文件传上去

## 初始化仓库

然后我们再初始化仓库，重新对我们的代码进行版本控制

```
git init
git remote add origin <server>复制代码
```

`<server>`是指在线仓库的地址。origin是本地分支,remote add操作会将本地仓库映射到云端

## 将博客源文件上传至Hexo分支

依次执行

```
git add .
git commit -m "..."
git push origin hexo复制代码
```

提交网站相关的文件；

## 对B电脑进行的操作

假设B电脑现在没有我们的源文件

```
git init
git remote add origin <server> #将本地文件和云端仓库映射起来。
git fetch --all
git reset --hard origin/master复制代码
```

## 日常改动

平时我们对源文件有修改的时候记得先pull一遍代码，再将代码push到Hexo分支，就和日常的使用git一样~

1. 依次执行git add .、git commit -m "..."、git push origin Hexo指令将改动推送到GitHub（此时当前分支应为Hexo）；
2. 然后才执行hexo g -d发布网站到master分支上。