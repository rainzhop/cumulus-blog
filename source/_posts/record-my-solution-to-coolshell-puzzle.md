---
title: 记录我在CoolShell Puzzle的闯关过程
date: 2014-08-04 00:00:00
tags: 技术
---


> 闯关地址在这里：[CoolShell Puzzle](http://fun.coolshell.cn/)

> 注意：这不是提示，而是直接给出了各关答案！

---

#### 0 - Fuck your brain

`first.html`

这一关使用到了奇葩语言[brainfuck](https://zh.wikipedia.org/zh/Brainfuck)，谜面是一段用brainfuck写成的代码，自己写一个或网上找一个解释器对这段程序运行就能得到答案。
<!--more-->

---

#### 1 - Multiply

`welcome.html`

两个数的乘法，从谜面数列推算下一个数为`18 × 108 = 1944`，另一个数则是「生命、宇宙以及任何事情的终极答案」，答案非`42`莫属（[原因](https://zh.wikipedia.org/wiki/42)）。所以答案就是`1944 × 42 = 81648`。

---

#### 2 - Keyboard

`81648.html`

谜面给出的是Dravok键盘布局和一句话，将这句话在QWERTY键盘上按照Dravok布局敲出来就会得到一段C代码：

```
main() {
    printf(&unix["\021%six\012\0"],(unix)["have"]+"fun"-0x60);
}

```

在Linux系统中编译运行则得到答案。

---

#### 3 - QR Code

`unix.html`

一个二维码图片，扫来一看发现是字符的映射关系，按照这个关系翻译下边的乱码则得到：

> Where there is a shell, there is a way. I expect you use the shell command to solve this problem, now, please try using the rot13 of "shell" to enter next level.

按照指示来做就能得到答案。

```
$ rot13
shell
```

---

#### 4 - cat

`furyy.html`

总结左侧回文的规律可以发现，都是五位的回文，且中间一位为小写字母，第一第二位分别是一个数字一个大写字母，最后组成单词的是回文的中间一位。按照这个规律查找网页源码注释里的字符则可以得出答案。

---

#### 5 - variables

`variables.html`

点击图片可以发现链接到了一个只有数字的页面。一步步追踪下去就会到达谜底页面。

---

#### 6 - tree

`tree.html`

通过中序遍历和后序遍历恢复出二叉树，求出最深路径即`zWp8LGn01wxJ7`，接着将下方的密码写入mess.txt后运行命令就可得到答案：

```
$ openssl enc -aes-128-cbc -a -d -pass pass:zWp8LGn01wxJ7 -in mess.txt
```

---

#### 7 - N Queens

`nqueens.html`

首先算出九皇后问题所有的解，然后依照谜面上的公式穷举，可得当`$code = 953172864`时公式成立。

---

#### 8 - Excel Column

`953172864.html`

26进制计算。

---

#### 9 - Fraternal Organisation

`DUYO.html`

两张图片：猪圈（pigpen）、共济会（Freemason）。Google后找到[猪圈密码](https://zh.wikipedia.org/wiki/%E8%B1%AC%E5%9C%88%E5%AF%86%E7%A2%BC)。将谜面的符号解密后即得到答案。

---

#### Congratulations!

`helloworld.html`

---
最后，感谢[@左耳朵耗子](http://weibo.com/haoel)准备的游戏。

（完）
