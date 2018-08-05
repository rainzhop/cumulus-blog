---
title: 初学Git，记录一下常用命令
date: 2014-08-09 00:00:00
tags: 技术
---

>最近参照[308tube](http://308tube.com/youtube/)的[Git入门教程](http://www.308tube.com/youtube/git/index.html)学习了一下Git的使用，下面是学习时记录的一些常用命令。

* `git init <project>` 初始化一个项目
* `git add <file or .>` add指定文件或所有文件到暂存区
* `git commit -m "message"` 将暂存区提交到本地库
* `git status` 查看文件状态
* `git log` 查看commit历史
* `git diff` 查看工作区与本地库的文件差异
* `git diff --cached` 查看暂存区与本地库的文件差异
<!--more-->
* `ssh -keygen -t rsa -C "emailaddr"` 生成RSA公／私钥对
* `ssh -T git@github.com` 测试是否能够通过ssh连接到GitHub
* `git remote add origin git@github.com:username/projectname.git` 为本地库对应个名为origin的远程库
* `git push origin master` 将本地库的主分支上传到远程库
* `git branch` 查看库中都有哪些分支并显示所在分支
* `git branch branchname` 新建一个名为`branchname`的分支
* `git checkout branchname` 转到名为`branchname`的分支t
* `git clone ssh-link` 将远程库clone到本地
* `git push remotename branchname` 将本地库的某分支上传到远程库
* `git merge branchname` 将名为`branchname`的分支合并到当前分支，如果存在冲突则需要手动合并
* `git pull remotename branchname` 将远程库的某分支同步到本地
* `git pull` == `git fetch` + `git merge`
* `git fetch remotename` 从远程库下载
* `git pull --rebase` == `git fetch` + `git rebase`
* `git tag -a annotation -m "msg"` 增加标签，并添加注释（annotation）
* `git tag` 显示所有标签
* `git show annotation` 显示标签的注释


>默认情况下, `git push`不会上传标签到远程库

* `git push remotename annotation` 上传名为`annotation`的标签到与远程库
* `git push remotename --tag` 上传所有标签到远程库
* `git checkout commithash` 切换到某次commit，返回后处于一个未命名的分支，此时可以通过`git checkout branchname`返回原来分支
* `git checkout annotation` 切换到名为`annotation`的标签
* `git reset --hard commithash` 回滚到某次commit，所有在这次commit之后的改动都将被永久擦除
* `git revert commithash [commithash]` 撤销某次commit，不会删除其它commit，而是在所有commit之后新建了一个撤销这次commit的commit


一些快捷命令:

* `git log --oneline` 以每条commit显示为一行的方式查看commit log
* `git commit -a -m "msg"` 如果没有新增文件，可以通过该命令跳过自动进行`add`
* `git status -s` 以简明的形式查看文件状态

（完）
