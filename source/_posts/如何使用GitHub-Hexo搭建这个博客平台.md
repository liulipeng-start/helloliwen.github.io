---
title: 如何使用GitHub+Hexo搭建这个博客平台
categories: hexo
abbrlink: 62ffb8b5
date: 2019-06-03 20:47:42
tags:
keywords:
description:
---

### 一、为什么要写博客

​		**当自己主动学习，主动思考其效率和对个人的提升无疑是高于被动接受的。然而使你提高最大的是主动说出自己认知，把自己的知识和理解传达给他人，这种方式是对你提升无疑是最显著的。**

<!--more-->

引用《码农翻身》里的一段话：

<font color="red">对自己狠一点，开始写作吧。</font>

​		自己心里觉得对一个技术点已经掌握了，但是当我试图给别人讲述的时候，发现并不能轻松自如、深入浅出地讲出来，这就说明了一个问题：自以为掌握了，其实并没有真正掌握。

​		为什么要有这门技术、这门技术解决了什么问题，然后才是这门技术是怎么使用的。

​		整理资料和思考的过程是很珍贵的，只有这样才能把信息变成自身的知识。不写出来，很容易放弃深度思考。

​		我们已经进入了一个碎片化的时代，我们的大脑已经养成了碎片化的习惯，一天不看碎片化的信息就觉得不舒服，这样下去会慢慢的丧失深度思考的能力。写作会逼着你去思考，梳理知识体系，防止自己被碎片化所填满。

​		最后，能够坚持的人才更有可能成功！

​		[为什么你要写博客？——陈素封](https://zhuanlan.zhihu.com/p/19743861?columnSlug=cnfeat)

### 二、为什么使用GitHub+Hexo

​		我很喜欢掘金的风格，里面也有很多大牛，但是因为没有文章分类，只好作罢。CSDN是很多人的入门博客，但是不屏蔽广告完全没法看。简书也不错，但是个人感觉总少点技术氛围。其他博客平台没有使用过，不作评论。至于为什么选择GitHub+Hexo，可能出于成就感吧，而且给我的感觉完全像一张白纸可以在上面任意发挥，而且文章有分类、没有广告、风格很简约，喜欢，所以选择这个平台。

​		至于其他平台的评论，参看这篇文章：[程序员可以选择哪些平台写技术博客？](<https://mp.weixin.qq.com/s?src=11×tamp=1559567191&ver=1646&signature=M8olE56Lf0LYxFyo5e5mClnfNlP0dHG04YgH-pTO1AIe3r5Ao7RSccXBVIn1PAATEhuniqA-Nr2cI*XCQEdrQ6SzjtrtFBmZv8G7vmOs8lOr9Ip4gG9gJLqapMh2SXk6&new=1>)，如果提示无法访问，使用[搜狗微信](https://weixin.sogou.com/)平台搜索文章。

### 三、准备环境

#### 1.关于Hexo、Node.js

**Hexo的工作机制**

​	   由于github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动

态内容，假如每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，所以hexo所做的就是

将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面

提交到github。

​	    Hexo基于Node.js，将/source文件夹下的资源(文章、图片、模板)，按照预定的配置文件，转换成静态页面

放置到/public目录下。如果需要预览或者部署，hexo会把public作为web目录处理。具体的细节可以通过实践接

下来的步骤，来逐渐明晓。

**Hexo 特点**

1. 支持Markdown: 支持Markdown意味着你可以把经历从排版中解放出来
2. 轻量: 无需拥有后台及数据库，专心写好你的文章
3. 一键部署: 可以通过Git或者ftp来将生成的静态页面部署到服务器或者主机空间中
4. 插件丰富: 丰富的插件可以满足你的各种需求

**Node.js**
        Node.js是一个基于Chrome V8引擎的JavaScript运行环境，为我们的Hexo提供js脚本的运行环境。而npm则

是一个JavaScript的包管理工具。主流的很多语言都会有自己的包管理器，Hexo官网教程中使用的是npm。

**优势：**

1. 全是静态文件，访问速度快；
2. 免费方便，不用花一分钱就可以搭建一个自由的个人博客，不需要服务器不需要后台；
3. 可以随意绑定自己的域名，不仔细看的话根本看不出来你的网站是基于github的；
4. 数据绝对安全，基于github的版本管理，想恢复到哪个历史版本都行；
5. 博客内容可以轻松打包、转移、发布到其它平台；
6. 等等。

**博客搭建过程**

一个博客的搭建过程分为三步：

- 编写：包含内容的书写与格式的配置
- 构建：从编写的原始内容生成可发布的最终内容
- 发布：让待发布的内容对读者可见

依托于博客平台（如博客园、新浪博客等）发布内容的用户只需要关注编写部分，但要搭建一个独立的个人博客则

以上三方面都需要关心。幸运的是，现在有大量的工具帮助我们简化这个过程：丰富的 Markup 语言简化了编

写；强大的静态站点生成器简化了构建；友好的托管平台简化了发布。

这个博客的诞生也得益于这些工具：

- 编写：使用 Markdown，内置大量层级、列表、超链接、代码等的简便语法支持
- 构建：使用 Hexo，几条命令完成生成、预览、发布步骤
- 发布：使用 GitHub Pages 进行托管，方便又免费

#### 2.准备工作

（1）有一个Github账户

（2）安装Node.js

[Node.js官网](https://nodejs.org/zh-cn/download/)下载LTS版本，并安装，配置环境变量，在命令行中通过查看版本。

（3）安装Git

[Git官网](https://git-scm.com/downloads)下载，安装，配置环境变量，使用git --version查看是否安装成功。


（4）注意事项

​		很多命令既可以用Windows的cmd来完成，也可以使用git bash来完成，但是部分命令会有一些问题，为避

免不必要的问题，建议全部使用git bash来执行；

​		hexo不同版本差别比较大，网上很多文章的配置信息都是基于2.x的，所以注意不要被误导；

​		hexo有2种`_config.yml`文件，一个是根目录下的全局的`_config.yml`，一个是各个`theme`下的。

#### 3.设置使用ssh提交到Github Pages

**设置用户名和邮箱**

初次安装git需要配置用户名和邮箱，否则git会提示：please tell me who you are.

你需要运行命令来配置你的用户名和邮箱：

```shell
$ git config --global user.name "yourName"
$ git config --global user.email "your_email@example.com"
```

注意：引号内请输入你自己设置的名字，和你自己的邮箱。此用户名和邮箱是git提交代码时用来显示你身份和联

系方式的，并不是github用户名和邮箱。

**git使用ssh密钥**

git支持https和git两种传输协议。相应的，github分享链接时会有两种协议可选：git格式和https格式。

使用https协议，每次pull, push都会提示要输入密码。

使用git协议，然后使用ssh密钥，这样免去每次都输密码的麻烦。

（1）生成密钥对

​		大多数 Git 服务器都会选择使用 SSH 公钥来进行授权，SSH 公钥默认储存在账户的主目录下的 ~/.ssh 目录。

首先需要检查你电脑是否已经有 SSH key 

​		运行 git Bash 客户端，输入如下命令，看一下有没有id_rsa和id_rsa.pub(或者是id_dsa和id_dsa.pub之类成

对的文件)，有 .pub 后缀的文件就是公钥，另一个文件则是密钥。假如没有这些文件，甚至连 .ssh 目录都没有，

可以用 ssh-keygen 来创建。

```shell
$ cd ~/.ssh
$ ls
```

（2）创建一个 SSH key，直接按Enter就行。然后，会提示你输入密码，如下(建议输一个，安全一点，当然不输

也行，应该不会有人闲的无聊冒充你去修改你的代码)：

```shell
$ ssh-keygen -t rsa -C "your_email@example.com"
```

**添加公钥到你的远程仓库（github）**

（1）查看你生成的公钥：

```shell
$ cat ~/.ssh/id_rsa.pub
```

（2）登陆你的github帐户。点击你的头像，然后 `Settings -> 左栏点击 SSH and GPG keys -> 点击 New `

`SSH key`

（3）然后你复制上面的公钥内容，粘贴进“Key”文本域内。 title域，自己随便起个名字。

（4）验证

```shell
$ ssh -T git@github.com
```

如果看到

```shell
Hi helloliwen! You've successfully authenticated, but GitHub does not provide shell access.
```

恭喜你，你的设置已经成功了。

**修改git的remote url**

在hexo目录下，使用命令 git remote -v 查看你当前的 remote url

```shell
$ git remote -v
origin https://github.com/someaccount/someproject.git (fetch)
origin https://github.com/someaccount/someproject.git (push)
```

如果是以上的结果那么说明此项目是使用https协议进行访问的（如果地址是git开头则表示是git协议）

你可以登陆你的github，就像本文开头的图例，你在上面可以看到你的ssh协议相应的url，类似：

 ![img](https://images2015.cnblogs.com/blog/1160195/201705/1160195-20170512120555144-795931549.png)

复制此ssh链接，然后使用命令 git remote set-url 来调整你的url。

```
git remote set-url origin git@github.com:someaccount/someproject.git
```

然后你可以再用命令 git remote -v 查看一下，url是否已经变成了ssh地址。

然后你就可以愉快的使用git fetch, git pull , git push，再也不用输入烦人的密码了。

#### 4.创建仓库

新建一个名为`你的用户名.github.io`的仓库，比如说，如果你的github用户名是test，那么你就新建`test.github.io`的仓库（必须是你的用户名，其它名称无效），将来你的网站访问地址就是 http://test.github.io 了，是不是很方便？

由此可见，每一个github账户最多只能创建一个这样可以直接使用域名访问的仓库。

几个注意的地方：

1. 注册的邮箱一定要验证，否则不会成功；
2. 仓库名字必须是：`username.github.io`，其中`username`是你的用户名；
3. 仓库创建成功不会立即生效，需要过一段时间，大概10-30分钟，或者更久，我的等了半个小时才生效；

创建成功后，默认会在你这个仓库里生成一些示例页面，以后你的网站所有代码都是放在这个仓库里啦。

![](https://images2017.cnblogs.com/blog/1250458/201710/1250458-20171017153553099-864426472.png)

### 四、Hexo博客安装使用

#### 1.安装hexo

（1）首先需要知道两个配置文件，需要特别注意的地方是，冒号后面必须有一个空格，否则可能会出问题。

- Hexo配置文件，这里面是全局配置：**hexo/_config.yml**
- 主题配置：**hexo/themes/next/_config.yml**

（2）安装hexo

~~~shell
npm install hexo-cli -g  
~~~

（3）选择一个目录作为hexo站点目录，然后进入目录，执行以下命令初始化：

~~~shell
hexo init
~~~

hexo会自动下载一些文件到这个目录，包括node_modules，目录结构如下图：

![](http://image.liuxianan.com/201608/20160818_115922_773_1148.png)

（4）生成并启动服务

~~~shell
hexo g #生成 或 hexo generate  
hexo s #启动本地服务器 或者 hexo server，这一步之后就可以通过http://localhost:4000查看了
~~~

#### 2.安装主题

（1）安装主题NexT（当然还有很多其他主题，可以去选择自己喜欢的主题：[官方主题](https://hexo.io/themes/)，本人选择NexT）

~~~shell
git clone https://github.com/iissnan/hexo-theme-next themes/next
~~~

（2）启用主题

打开Hexo配置文件：hexo/_config.yml，修改

~~~shell
theme: next
~~~

（3）设置语言

hexo/_config.yml

~~~shell
language: zh-Hans
~~~

#### 3.创建博客及常用命令

~~~java
hexo new "文章名" #新建文章
hexo new page "页面名" #新建页面   
~~~

那么`hexo new 'postName'`命令和`hexo new page 'postName'`有什么区别呢？

```
hexo new page "my-second-blog"
```

生成如下：

![img](http://image.liuxianan.com/201608/20160823_184852_854_6502.png)

最终部署时生成：`hexo\public\my-second-blog\index.html`，但是它不会作为文章出现在博文目录。

常用命令及缩写

~~~shell
hexo cl = hexo clean	# 清除缓存
hexo n = hexo new		# 新建文章
hexo g = hexo generate	# 生成静态页面至public目录
hexo g && gulp  		# 会根据 gulpfile.js 中的配置，对 public 目录中的静态资源文件进行压缩。
hexo s = hexo server	# 启动服务
hexo d = hexo deploy	# 部署到GitHub
hexo h = hexo help  	# 查看帮助
hexo v = hexo version  	#查看Hexo的版本
~~~

组合命令：

~~~shell
hexo cl && hexo g && gulp && hexo d
hexo s -g #生成并本地预览
hexo d -g #生成并上传
~~~



#### 4.部署

上传之前：在上传代码到github之前，一定要记得先把你以前所有代码下载下来（虽然github有版本管理，但备份

一下总是好的），因为从hexo提交代码时会把你以前的所有代码都删掉。

首先安装扩展文件

~~~shell
npm install hexo-deployer-git --save   
~~~

其次需要配置Hexo配置文件：hexo/_config.yml

~~~xml
#正确写法
deploy:
    type: git
    repo: git@github.com:cczeng/cczeng.github.io.git  #这里的网址填你自己的，中间有空格要注意
    branch: master   

#错误写法
deploy:
  type: github
  repository: https://github.com/liuxianan/liuxianan.github.io.git
  branch: master
~~~

接下来就是使用Hexo部署到我们的Github仓库上

~~~shell
hexo cl && hexo g && hexo d
~~~

接下来等待几分钟就可以打开浏览器，输入：https://username.github.io就可以看到博客了。

#### 5.常见问题

（1）如何让博文列表不显示全部内容：在合适的位置加上`<!--more-->`即可

（2）电脑重装了系统/多台电脑写博客？那就赶紧戳这里：[使用hexo，如果换了电脑怎么更新博客？](<https://www.zhihu.com/question/21193762>)

（3）想要给网站添加图片？方式一：使用图床。国内的有：**微博图床**、**阿里云OSS**、**腾讯云COS**、**七牛云**。具体可以查看这里：[嗯，图片就交给它了](<https://sspai.com/post/40499>)。方式二：在根目录 **source** 下建立一个文件夹，加入取名叫upload-images，把图片放在这个文件夹下，然后在博客中连接就是：/upload-images/图片名，执行hexo g的时候此文件夹自动生成到public中。

### 五、Hexo配置

#### 1.设置头像及网站的icon

首先把图片放在**hexo/source/images**目录下，并修改配置文件**hexo/themes/next/_config.yml**

~~~shell
avatar: /images/avatar.jpg
favicon: /images/favicon32.ico
~~~

#### 2.添加标签页面

新建页面：

  ```
  hexo new page tags
  ```

设置页面（编辑 **source/tags/index.md**）：

  ```
  ---
  type: "tags"
  comments: false
  ---
  ```

修改菜单（编辑 **themes/next/_config.yml**）：

  ```
  menu: 
  	tags: /tags
  ```



参考：

[使用hexo+github搭建免费个人博客详细教程](<https://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html>)

[我是如何利用Github Pages搭建起我的博客，细数一路的坑](https://www.cnblogs.com/jackyroc/p/7681938.html)

[git使用ssh密钥](http://liujinkai.com/2015/09/18/git-use-ssh/?utm_source=tuicool&utm_medium=referral)

[github设置添加SSH](https://www.cnblogs.com/ayseeing/p/3572582.html)

[Markdown——入门指南](<https://www.jianshu.com/p/1e402922ee32/>)

[Markdown语法汇总](<https://www.jianshu.com/p/45faddb1526d>)