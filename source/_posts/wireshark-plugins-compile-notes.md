---
title: Wireshark插件编译记录
date: 2015-02-01 00:00:00
tags: 技术
---

> 最近想学习下Wireshark插件开发，但在插件的编译上遇到了一些问题，归根结底是不熟悉如何使用Makefile进行编译所致。网上关于Wireshark插件开发的文章对插件编译的介绍都不太清楚，这里就将插件的编译过程记录下来，以备后用。

### 下载源码

    git clone https://code.wireshark.org/review/wireshark

### 编译安装Wireshark:

在Wireshark的源码根目录下执行下列命令即可完成编译安装：

    $ ./autogen.sh
    $ ./configure
    $ make
    $ make install

<!--more-->

### 插件编译

在`plugins`下新建一个目录`yourproto`来存放要添加的协议解析插件的源码，如`packet-yourproto.c`。

此外，还要从`plugins`目录内的其他插件的目录中拷贝以下文件到新目录下:

    Makefile.am
    Makefile.common
    Makefile.nmake
    moduleinfo.h
    moduleinfo.nmake
    plugin.rc.in

之后，需要根据新插件的名字来修改`Makefile.am`和`Makefile.common`中的相关部分，修改`moduleinfo.h`和`moduleinfo.nmake`中的版本号。

此时插件目录已经准备完毕。

接着，需要对其他一些文件进行修改：

##### `configure`

* 在`ac_config_files`中按照字母顺序加入新插件的内容`plugins/yourproto/Makefile`
* 在`# Handling of arguments.`后的循环中按照字母顺序加入新插件的内容`"plugins/yourproto/Makefile") CONFIG_FILES="$CONFIG_FILES plugins/yourproto/Makefile" ;;`

##### `configure.ac`

* 在`AC_OUTPUT`字段中按照字母顺序加入新插件的内容`plugins/yourproto/Makefile`。

##### `plugins/Makefile.am`

* 在`SUBDIRS`中按照字母顺序加入刚才新建的插件目录名字`yourproto`。

以上准备完毕后，到Wireshark源码根目录下执行命令:

    $ ./autogen.sh
    $ ./configure

然后到`plugins`目录下执行命令：

    $ make

此时在`plugins/yourproto/.libs`目录下就会生成新插件的动态库`yourproto.so`。

最后将刚生成的动态库拷贝到`/usr/local/lib/wireshark/plugins/[版本号]`目录下，就可以在Wireshark中使用了。

也可以在插件代码目录下直接执行`make install`安装动态库。

（完）
