---
layout:     post
title:      遇到的问题---could not read symbols: Archive has no index; run ranlib to add one
subtitle:   问题分析及解决
date:       2019-12-05
author:     VK
catalog: true
tags:
    - linux
---

### 遇到的问题---could not read symbols: Archive has no index; run ranlib to add one

原因是一开始编译的点o文件用了32位编译器，然后重新编译的时候用了64位编译器，所以连接的时候出错，只需要make clean之后 重新make即可