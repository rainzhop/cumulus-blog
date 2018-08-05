---
title: 在CentOS6.6上使用uWSGI部署Flask应用
tags: 技术
date: 2017-03-15 21:57:40
---


前段时间和成儿聊起了微信公众号，突然又想试一试了。

之前是使用SAE作为第三方服务器连接过微信，但如今国内这些云应用平台都收费了[嘤嘤嘤]。那么，就好（cong）好（tou）复（xue）习（qi），搭一个Web服务器吧，顺便也能把租来科学上网的VPS再充分利用一下，所以这篇文章是记录一下在VPS上部署Flask的过程。

## 准备工作

我VPS上装的CentOS6.6是最简系统，因此还需要安装不少东西。

### 安装python-devel

因为安装其他python类库可能需要进行编译，所以需要安装python开发包。

```
$ yum -y install python-devel
```
<!--more-->

### 安装pip

安装Python包管理工具pip。

```
$ yum -y install python-pip
```

在调用yum命令时还出现了一个插曲，提示说`thread.error: can't start new thread`，这是由于系统内存太小，导致fastestmirror插件无法开启线程。解决方法是将该插件的默认线程数改小，只需要将`/etc/yum/pluginconf.d/fastestmirror.conf`中的`maxthreads`进行修改即可，我这里默认是15，改成5后再执行yum就不再报错了。我的VPS内存是64MB，仅供参考。

### 安装virtualenv

接着安装virtualenv，因为以后可能要部署不止一个python项目，使用virtualenv可以为不同项目提供相互隔离的包依赖环境。

```
$ yum -y install virtualenv
```

### 安装uWSGI

uWSGI是一个Web服务器，实现了WSGI协议，HTTP协议。一方面其使用HTTP协议接收和处理请求，一方面其通过WSGI与Web应用进行通信。

```
$ pip install uwsgi
```

## 操练起来

这样，所有需要安装的部件都已经准备完毕，下面通过一个实际的例子介绍Flask应用的部署过程。

### 准备好Web项目

我们建立一个最简的Flask应用。

首先在项目目录下使用virtualenv创建一个Python虚拟环境。

```
$ virtualenv venv
$ source venv/bin/activate
```

此处由于要使用Flask，因此还要在虚拟环境中安装Flask。

```
(venv) $ pip install flask
```

接着我们来准备一个简单但完整的Flask Web程序，将其命名为`hello.py`。

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return '<h1>Hello World!</h1>'

if __name__ == "__main__":
    app.run(debug=True)
```

### 配置uWSGI

接下来需要建立uWSGI的配置文件。路径无所谓，这里将其命名为uwsgi.ini，内容如下。

```
[uwsgi]
http = 0.0.0.0:5000
chdir = /path/to/webapp/
wsgi-file = hello.py
callable = app
processes = 1
threads = 2
stats = 0.0.0.0:9191
```

其中需要将`chdir`配置为上边Web项目的目录，`wsgi-file`配置为上边Flask Web程序的程序文件。

### Give it a go.

此时，通过`uwsgi uwsgi.ini -H /path/to/venv`即可启动uWSGI。

其中，`-H`选项用来设置环境变量`PYTHONHOME`。此处将其指定为Python虚拟环境，开启uWSGI时将会在该目录下搜索Python模块。

OK，大功告成，现在可以通过浏览器访问`http://remotehost:5000`来打开上边准备的Web项目了。

### 让uWSGI自启动

修改`/etc/rc.local`文件，在其中加入如下一行

```
uwsgi /path/to/***/uwsgi.ini -H /path/to/webapp/venv &
```

这样以后每次服务器重启后，Web服务器就自动开启啦。
