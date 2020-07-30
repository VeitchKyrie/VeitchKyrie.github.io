---
layout:     post
title:      遇到问题---Git使用出现Automatic merge failed
subtitle:   
date:       2019-10-06
author:     VK
catalog: true
tags:
    - Git
---



# Git使用出现Automatic merge failed

# 遇到问题

今天使用git pull时候遇到Automatic merge failed; fix conflicts and then commit the result。

### 产生原因

首先这个问题产生的原因是因为你git  pull 的时候会分为两步,第一步先从远程服务器上拉下代码，第二步进行merge,但是merge时候失败了就会产生上述问题。

### 解决方法:

1. 丢弃本地提交，强制回到线上最新版本
   git fetch --all
   git reset --hard origin 你需要下拉的分支(默认master)
   git fetch
2. 保存本地提交
   git  reset --abort
   git reset --merge
   git commit -am '提交信息'
   git pull

### 类似问题

You have not concluded your merge (MERGE_HEAD exists).
Please, commit your changes before you can merge.
也可以使用此方法解决.