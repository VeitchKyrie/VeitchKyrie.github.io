---
layout:     post
title:      遇到问题---‘apt-add-repository：找不到命令错误’
subtitle:   问题分析及解决
date:       2019-08-25
author:     VK
catalog: true
tags:
    - linux
---

# 遇到的问题---apt-add-repository：找不到命令错误。

在linux中有时候需要使用ppa源来安装一些软件，默认并不支持ppa添加命令add-apt-repository，要解决此问题可以参考以下内容。

Ubuntu安装过程提示

```shell
$ sudo apt-add-repository ppa:mosquitto-dev/mosquitto-ppa
$ apt-add-repository：找不到命令错误。
```

修复方法如下：

```shell
apt-get install software-properties-common
```

重新执行命令即可。