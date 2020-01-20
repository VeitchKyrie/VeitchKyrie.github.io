---
layout:     post
title:      Ubuntu实现远程登陆之telnet——安装telnet服务端
subtitle:   远程登录telnet
date:       2019-10-26
author:     VK
catalog: true
tags:
    - telnet
---

# Ubuntu实现远程登陆之telnet——安装telnet服务端

telnet是一种网络通信协议，我们可以使用它登录远程服务器，虽然telnet有安全问题这一硬伤，但正因如此，它十分简洁，非常容易地在资源受限设备上（如嵌入式环境）运行这套协议。

Ubuntu安装后默认只有telnet客户端，即只能在Ubuntu内去连接其他telnet服务器，本文将详细介绍在Ubuntu下安装部署telnet服务端，以便实现其他客户端使用telnet协议远程登录Ubuntu服务器。

环境
`Ubuntu Desktop amd64 18.04 LTS（Vmware Workstation 14 Pro 14.1.1 build-7528167）`

`telnetd 0.17-41`

`xinetd 1:2.3.15.3-1`

安装
1. 使用apt工具下载并安装`telnetd`

```SHELL
sudo apt install telnetd
```




2. 使用apt工具下载并安装`xinetd`

```SHELL
sudo apt install xinetd
```




3. 完成

```SHELL
sudo service xinetd status
```


可以看到日志显示“added service telnet [file=/etc/inetd.conf] [line=23]”，说明我们的telnet服务已经正常运行起来了。



连接
telnetd安装成功后会自动运行，我们可以尝试使用secure CRT等工具连接这台Ubuntu服务器。

首先打开Secure CRT，新建连接，“Protocol”选择为Telnet，“Hostname”输入Ubuntu服务器的IP地址，“Port”使用默认的22，“Firewall”保持为”None“，点击“Connect”按钮启动连接。



输入用户名和密码，登录成功！





版权声明：本文为CSDN博主「xkwy100」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/xkwy100/article/details/80328646