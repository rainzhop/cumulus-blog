---
title: 我也来试试Jekyll + GitHub Pages
date: 2014-07-23 00:00:00
tags: blog
---

今天尝试在GitHub上使用Jekyll搭建Blog，正好就将过程记录下来作为一篇文章吧。

### Hello world

GitHub提供了一个[Hello world 教程](https://pages.github.com/)来介绍GitHub Pages的使用，作为菜鸟，我就先从这里开始。

首先在GitHub上建立一个名为`USERNAME.github.io`的库。然后将这个空项目clone到本地：

    $ git clone git@github.com:USERNAME/USERNAME.github.io.git

之后进入`USERNAME.github.io`目录新建一个页面，名为`index.html`， 内容为`hello world`：

    $ cd USERNAME.github.io
    $ echo "Hello world" > index.html

然后将项目push回GitHub：

    $ git add *
    $ git commit -m "Initial commit"
    $ git push origin master

稍等上几分钟，访问`http://USERNAME.github.io`就能看到刚才创建的页面了，但这只是一个单独的页面，为了让Blog的内容更为丰富，需要Jekyll的帮助。
<!--more-->

### 安装Jekyll

接下来就到了Jekyll了。Jekyll是一个静态网页生成器，通过它能够把用Markdown语法写成的文本转化为静态的网页，同时它还集成了一个开发用服务器，能够用来在本地预览页面。

针对Jekyll，GitHub pages同样提供了简单的教程[Using Jeklyll with Pages](https://help.github.com/articles/using-jekyll-with-pages)。

为了能够在本地预览所编辑的页面，我要在本地安装Jekyll，而Jekyll需要ruby语言，所以又要先安装ruby开发环境。

我是在Windows下安装的，所以首先在[http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/)分别下载rubyinstaller和DevKit并进行安装。

然后就可以通过gem安装Jekyll了：

    $ gem install github-pages

在运行上边命令的时候出现了连不上`rubygems.org`的问题，八成是天朝的网络吧，google了一下发现淘宝建了一个`rubygems.org`的国内镜像，于是指定它为安装源：

    $ gem sources --remove https://rubygems.org/
    $ gem sources -a http://ruby.taobao.org/

这回再来安装就ok了。安装之后来到刚才所建项目的所在目录`USERNAME.github.io`下，运行下面的命令来开启服务器：

    $ jekyll serve

开启后访问`http://localhost:4000`就能够看到刚才所创建的页面了，与之前访问`http://USERNAME.github.io`时看到的页面是一样的——一张可怜的`hello world`的白纸 0.0。这个时候在命令行中可以看到有行黄字`Configuration file: none`，这是因为在目录中缺少对站点的配置文件`_config.yml`，当然是因为刚才只在目录下创建了一个`index.html`呗。那么下面就使用Jekyll来构建自己的Blog！

### 开始创建Jekyll Blog

这里可以直接运行如下命令来创建一个站点的雏形：

    $ jekyll new site

是的，我偷懒了0.0。这条命令所创建的`site`文件夹中是一个具有基本要素的站点雏形，可以根据自己的需要对其中的内容进行修改来创建自己的Blog。

可是我压根不知道要修改些什么啊，于是又得了解Jekyll站点的基本目录结构：

    .
    ├── _config.yml
    ├── index.html
    ├── about.md
    ├── _layouts
    │   └── default.html
    ├── _includes           
    │   └── default.html
    ├── _posts
    │   ├── 2014-07-22-first-article.md
    │   └── 2014-07-22-second-article.md
    ├── _drafts
    │   ├── todo.md
    │   └── working.md
    ├── resources
    │   ├── main.js
    │   └── main.css
    └── _site


这个是比较典型的Jekyll目录结构，其中各个条目的作用如下：

- `_config.yml` 站点的配置文件
- `index.html` 首页
- `about.md` 「关于」页面
- `_layouts` 存放布局文件
- `_includes` 存放可以被布局或文章所包含的文件
- `_posts` 存放文章
- `_drafts` 存放草稿
- `resources` 存放诸如`.css`, `.js`之类的资源文件
- `_site` 存放由Jekyll生成的静态网页

在知道了这些信息之后就可以参照之前创建的站点雏形和Jekyll官网提供的[文档](http://jekyllrb.com/docs/home/)来构建自己的Blog了。改起来发现各种不确定，不过可以动手改一改然后在本地预览一下就知道改的是什么了。改了一会儿就头大了，于是就参照这些文件另外建了个超级简陋的站点，虽然外观很丑，但是贵在知道每个文件都是干嘛的呀。我好容易满足，于是就把站点push到GitHub上去了。

值得一提的是，GitHub上有着大量使用Jekyll构建的博客，所以也可以把别人的美观主题偷来用吧，啊哈哈哈。

（完）
