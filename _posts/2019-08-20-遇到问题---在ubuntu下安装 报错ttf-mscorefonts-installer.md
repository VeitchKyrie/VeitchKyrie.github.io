---
layout:     post
title:      遇到问题---在ubuntu 下安装 报错ttf-mscorefonts-installer 
subtitle:   问题分析及解决
date:       2019-08-20
author:     VK
catalog: true
tags:
    - linux
---



# 遇到问题---在ubuntu 下安装 报错ttf-mscorefonts-installer 

在ubuntu 下，ttf-mscorefonts-installer 提供微软的TrueType 核心字库的安装向导。如果你已经安装了一个可选包”ubuntu-restricted-extras”，则这个向导会自动安装，很多用户遇到这个问题：终端出现EULA(nd User License Agreement)，提示用户选择，但用户却没法选择，点击OK按钮没任何反应。



## 1 解决方法

### 1.1 安装软件包

 

如果你想安装微软的TrueType 核心字库，则你既可以安装可选的"ubuntu-restricted-extras"包，这个包能给你提供很好的字体，音频，视频，解码，flash， java等等，同时你也可以只安装"ttf-mscorefonts'installer” 向导，安装方法如下：

 

```
sudo apt-get installubuntu-restricted-extras
```

或

```
sudo apt-get installttf-mscorefonts-installer
```

如果你在输入如上的命令后终端提示找不一些下载资源，则可执行下列命令：

```
sudo apt-get update
```



### 1.2 接受 EULA

 

在安装这个软件包之间，在最顶层，给你呈现出来一个类似屏幕解图一样的窗口（注意”<OK>” 在缺省下是没选择的）。

不幸的是，你不能够利用鼠标来处理这个对话框。因此你需要做的通过鼠标点击对话框的任何一个地方确保终端是焦点，接下来按<Tab> 键选中”<OK>”按钮，回车：



接下来又给你呈现出一个新的窗口：

选择“<YES>” 再按回车

接下来应该能安装成功，你不要要做任何事情。

 

### 1.3 故障分析

在安装上述软件时，你可能不能使用pacakage manager , ubuntu Softeware Center, Update Manager 等等，因为 dpkg 被锁住了。解决的办法是：

 

1.      首先重启电脑或虚拟机，确保“dpkg” 没有被锁住。

2.      打开终端，运行如下这个命令，它能初始化所有还没有配置好的包，包括但不仅仅限定于"ttf-mscorefonts-installer":

     sudo dpkg --configure -a

      3. 现在你可以按照上述的方式重新安装，即可成功。
      ​

