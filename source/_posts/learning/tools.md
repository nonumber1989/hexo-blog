---
title: 工具笔记
date: 2016-04-27 17:21:55
tags: [coding]
categories: learning
---

# 常用命令或者工具的小笔记

> 有时候你会发现一些常用的小命令虽然简单却不是那么容易就拼写出来，说白了就是用的少
  好记性不如烂笔头嘛，记下来呗。
 

## Maven
  1. skip test 的命令:只是在后面加上参数** -Dmaven.test.skip=true **
  
## Git
  1. git 只checkout仓库中的子文件夹。 需要用到git的sparse checkout
     **[sparse checkout](http://stackoverflow.com/questions/600079/is-there-any-way-to-clone-a-git-repositorys-sub-directory-only)**
     需要在本地设置git config，在pull之前确定需要pull的文件夹
  2. git display all remote branch
         git ls-remote --heads <remote-name>
  3. How to create orphan branches
        ```
        git checkout --orphan config/services
        git rm -rf .
        git add <your files>
        git commit -m 'Initial commit'
        git push origin HEAD:refs/heads/config/services
        ```
     [Source](http://stackoverflow.com/questions/1384325/in-git-is-there-a-simple-way-of-introducing-an-unrelated-branch-to-a-repository#answer-4288660)

