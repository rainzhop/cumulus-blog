---
title: 『笔记』DHT协议学习
date: 2016-05-23 00:00:00
tags: [bt, 协议, 笔记]
---

由于一些不可描述的原因，一些资源在部分下载工具上无法使用磁力链接进行下载，但通过这些资源的种子文件却可以。猜想其中原因，可能是因为这条磁力链接被下载工具或种子服务器索引了。这里希望能够通过学习BT的相关协议找寻一种方法，使得在不借助种子库的情况下能够将磁力链接转换为种子文件。

### 目前的思路

1. 通过[DHT协议](http://www.bittorrent.org/beps/bep_0005.html)找到拥有.torrent文件的peer；
2. 通过[BEP09](http://www.bittorrent.org/beps/bep_0009.html)从这些peer获取.torrent文件。

本文主要用来记录在学习DHT协议过程中所做的笔记。

<!--more-->

### 有关BT下载原理的总结

![bt-download-learning-summary](/assets/bt-download-learning-summary.png)

### DHT学习笔记

![dht-protocol-learn-notes](/assets/dht-protocol-learn-notes.png)
