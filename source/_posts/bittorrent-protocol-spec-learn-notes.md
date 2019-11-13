---
title: 『笔记』BitTorrent协议学习
date: 2016-05-14 00:00:00
tags: [bt, 协议, 笔记]
---

心血来潮，想学习下BitTorrent协议，做个学习笔记~

BitTorrent协议的维基百科释义：
> **BitTorrent协议**（简称**BT**，俗称**比特洪流**、**BT下载**）是用在[对等网络](https://zh.wikipedia.org/wiki/%E5%AF%B9%E7%AD%89%E7%BD%91%E7%BB%9C)中[文件分享](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E5%88%86%E4%BA%AB)的[网络协议](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E5%8D%8F%E8%AE%AE)[程序](https://zh.wikipedia.org/wiki/%E7%A8%8B%E5%BA%8F)。和[点对点](https://zh.wikipedia.org/wiki/%E9%BB%9E%E5%B0%8D%E9%BB%9E)（point-to-point）的协议[程序](https://zh.wikipedia.org/wiki/%E7%A8%8B%E5%BA%8F)不同，它是用户群对用户群（peer-to-peer），而且用户越多，下载同一文件的人越多，且下载后，继续维持上传的状态，就可以“分享”，成为其用户端节点下载的[种子文件](https://zh.wikipedia.org/wiki/%E7%A7%8D%E5%AD%90%E6%96%87%E4%BB%B6)（.torrent），下载该档案的速度越快。

<!--more-->
> **历史**

> 该技术由美国的程序员[布莱姆·科亨](https://zh.wikipedia.org/wiki/%E5%B8%83%E8%8E%B1%E5%A7%86%C2%B7%E7%A7%91%E4%BA%A8)于2001年4月时发布，并于2001年7月2日时首次正式应用。

> **原理简述**

> 普通的[HTTP](https://zh.wikipedia.org/wiki/HTTP)／[FTP](https://zh.wikipedia.org/wiki/FTP)下载使用[TCP/IP协议](https://zh.wikipedia.org/wiki/TCP/IP%E5%8D%94%E8%AD%B0)，BitTorrent协议是架构于TCP/IP协议之上的一个[P2P](https://zh.wikipedia.org/wiki/P2P)文件传输[协议](https://zh.wikipedia.org/wiki/%E5%8D%8F%E8%AE%AE)，处于TCP/IP结构的[应用层](https://zh.wikipedia.org/wiki/%E5%BA%94%E7%94%A8%E5%B1%82)。BitTorrent协议本身也包含了很多具体的内容协议和扩展协议，并在不断扩充中。

> 根据BitTorrent协议，文件发布者会根据要发布的文件生成提供一个.torrent文件，即[种子文件](https://zh.wikipedia.org/wiki/%E7%A7%8D%E5%AD%90%E6%96%87%E4%BB%B6)，也简称为“种子”。

> 种子文件本质上是[文本文件](https://zh.wikipedia.org/wiki/%E6%96%87%E6%9C%AC%E6%96%87%E4%BB%B6)，包含Tracker信息和文件信息两部分。Tracker信息主要是BT下载中需要用到的Tracker服务器的地址和针对Tracker服务器的设置，文件信息是根据对目标文件的计算生成的，计算结果根据BitTorrent协议内的[Bencode](https://zh.wikipedia.org/wiki/Bencode)规则进行编码。它的主要原理是需要把提供下载的文件虚拟分成大小相等的块，块大小必须为2k的整数次方（由于是虚拟分块，硬盘上并不产生各个块文件），并把每个块的索引信息和[Hash](https://zh.wikipedia.org/wiki/Hash)验证码写入种子文件中；所以，种子文件就是被下载文件的“索引”。

> 下载者要下载文件内容，需要先得到相应的种子文件，然后使用BT客户端软件进行下载。

> 下载时，BT客户端首先解析种子文件得到Tracker地址，然后连接Tracker服务器。Tracker服务器回应下载者的请求，提供下载者其他下载者（包括发布者）的IP。下载者再连接其他下载者，根据种子文件，两者分别告知对方自己已经有的块，然后交换对方所没有的数据。此时不需要其他服务器参与，分散了单个线路上的数据流量，因此减轻了服务器负担。

> 下载者每得到一个块，需要算出下载块的Hash验证码与种子文件中的对比，如果一样则说明块正确，不一样则需要重新下载这个块。这种规定是为了解决下载内容准确性的问题。

> 一般的HTTP/FTP下载，发布文件仅在某个或某几个服务器，下载的人太多，服务器的[带宽](https://zh.wikipedia.org/wiki/%E5%B8%A6%E5%AE%BD)很易不胜负荷，变得很慢。而BitTorrent协议下载的特点是，下载的人越多，提供的带宽也越多，下载速度就越快。同时，拥有完整文件的用户也会越来越多，使文件的“寿命”不断延长。

> 为了解决某些用户“下完就跑”的现象，在非官方BitTorrent协议中还存在一种慢慢开放下载内容的[超级种子](https://zh.wikipedia.org/wiki/%E8%B6%85%E7%B4%9A%E7%A8%AE%E5%AD%90)的[算法](https://zh.wikipedia.org/wiki/%E7%AE%97%E6%B3%95)。

以下内容出自BEP03（[The BitTorrent Protocol Specification](http://www.bittorrent.org/beps/bep_0003.html)）

## bencode编码

.torrent文件是经过bencode编码的文本文件。bencode编码规则如下：

* 字符串：`<字符串长度>+冒号+<字符串>`，例：`"abcd"`编码为`4:abcd`
* 整数：`i+<整数>+e`，例：`13`编码为`i13e`，`-4`编码为`i-4e`
* 列表：`l+<列表内容>+e`，例：`["abcd", 13]`编码为`l4:abcdi13ee`
* 字典：`d+<键值对>+e`，例：`{"dog": "bark", 13: '"Judas"}`编码为`d3:dog4:barki13e5:Judase`

## 元信息文件

元信息文件（metainfo文件）就是.torrent文件，是经过bencode编码的字典。这个字典包含如下两个key：

* `announce`：Tracker服务器URL
* `info`：这个key又映射到另一个字典

### info字典

info字典包含如下key：

* `name`：映射到一个字符串，表示资源名字。资源文件仅有一个时，该字符串就是资源文件的名字，当资源文件有多个时，该字符串是文件夹的名字。
* `pieces length`：映射到一个整数，表示每个文件块的大小。资源文件会被分割成多个固定大小的分块，除了最后一块可能因截断较小外，其他文件分块的大小事一样的。`piece length`几乎总是2的n次幂，通常为2<sup>18</sup>=256KB（）。
* `pieces`：映射到一个字符串，该字符串长度为20的倍数。没20个字节表示一个piece的SHA1哈希值。
* `length`或`files`：当出现`length`时则表示资源仅有一个文件，value为该文件大小。当出现`files`时则表示不仅一个文件，value为一个集合了资源文件的列表，列表中每个元素表示一个资源文件，形式是字典，该字典包含两个key：
 * `length`：表示文件大小。
 * `path`：又是一个列表，其中元素是一个个字符串，表示了一层层文件夹名字，最后一项是资源文件名。

## 向Tracker服务器查询peer信息

向Tracker服务器发出的GET请求应包含如下key：

* `info_hash`：20字节长的字符串，为.torrent文件中info字典部分的sha1哈希值，用来唯一标识种子文件进而标识资源。
* `peer_id`：20个字节长的字符串，用来标识下载器。每个下载器在开启一个新的下载时都会随机生成它自己的id。
* `ip`：可选。指示本peer所在IP。
* `port`：指示本peer所监听的端口。默认从6881开始选择可用端口，若尝试到6889仍失败则放弃监听。
* `uploaded`：上传量。
* `downloaed`：下载量。
* `left`：尚需下载量。
* `event`：可选。通知本peer的下载状态，`stared`/`completed`/`stopped`/`empty`。

Tracker服务器返回的响应是经过bencode编码的字典，若GET请求失败，则仅含key：

* `failure reason`：映射到一个字符串，用自然语言说明为啥失败。

若成功，则含key如下：

* `interval`：value是个整数，告诉peer等多久再发起请求。但若是因事件触发的请求则不受此值约束。
* `peers`：映射到一个元素为字典的列表。列表中的元素代表了一堆peer，每个peer有一个peer id，ip和port。这些是拥有所查询资源的peer。

这是通过HTTP协议向Tracker进行查询的协议，此外还可以通过UDP进行查询，详见[UDP Tracker Protocol for BitTorrent](http://www.bittorrent.org/beps/bep_0015.html)。

## peer协议

BT的peer协议可工作在TCP或 [uTP](http://www.bittorrent.org/beps/bep_0029.html)上。

peer连接是对称的。数据和消息传输都是双向的且采用相同形式。

peer协议通过元信息文件中的信息索引文件分块，当peer下载完一个文件分块下载时会对该文件分块做哈希校验，并通知其他peer它已经完成该分块的下载。

连接两端均有一个2bit的状态位，分别表示是否choked和是否有兴趣。choked表示在unchoking发生前不会有数据被发送。

当一端有兴趣且另一端没有choke时，数据得以传输。兴趣位的维护必须时刻保持更新，只要下载器没有要请求的东西就应当表示没有兴趣，即使它本来就被choke了。

peer协议从握手消息开始，后边跟着没完没了的消息流，消息的格式是`<长度>+<字符串>`。握手消息从内容为数字19+字符串"BitTorrent protocol"开始。这之后的所有整数都将被编码成4字节大端。

在协议名称之后跟着8个保留字节。[BEP 10](http://www.bittorrent.org/beps/bep_0010.html)中提到了如何利用这些保留字节扩展协议。

紧接着是20个字节，是元信息文件中info部分的SHA1哈希值，内容和发给Tracker服务器的GET请求中的info_hash一样。如果连接两端发送的这个值不一致，则应断开这个连接。

上边那个哈希值之后是20字节的peer id，这个peer id就是在Tracker服务器查询到的peer id。如果收到这个id的peer的id不是这个id（拗口啊o(╯□╰)o），则连接应被断开。

到此握手消息就完毕了，接着就是一串串消息流。0长度的消息用来keepalive，每两分钟一次。

## peer协议消息

本节介绍keepalive消息之外的peer消息。

所有消息使用第一个字节标识消息类型，值和类型的对应关系如下：

| 值 | 类型 |
| --- | --- |
| 0 | choke |
| 1 | unchoke |
| 2 | interested |
| 3 | not interested |
| 4 | have |
| 5 | bitfield |
| 6 | request |
| 7 | piece |
| 8 | cancel |

其中前四个消息类型不带有消息载荷。

bitfield消息用于告知对方自己对资源的拥有情况。

have消息的载荷是一个数，用来告知对方自己刚刚完成下载的分块索引值。

request消息包含index+begin+length。请求文件分块。

cancel消息包含index+begin+length。

piece消息包含index+begin+piece。piece消息与request消息存在关联。

下载器通常乱序下载文件分块。
