---
layout:     post
title:      遇到问题--- Linux  MQTT 修改端口后拒绝访问问题
subtitle:   问题分析及解决
date:       2019-08-24
author:     VK
catalog: true
tags:
    - mqtt
    - linux
---

# 遇到问题---Linux  MQTT 修改端口后拒绝访问问题

## 1.1 问题分析：

端口已成功配置，但建立连接时总是拒绝访问，推测是新的端口没有在防火墙中开放。

## 1.2 解决方法：

### 1.2.1 防火墙工具的安装

新版本上的系统，firewalld服务替代了iptables服务，新的防火墙管理命令firewall-cmd与图形化工具firewall-config。

```SHELL
$ apt-get install firewalld 
```

### 1.2.2 命令及使用方法

（1）查看对外开放的端口状态

- 查询已开放的端口: netstat -anp
- 查询指定端口是否已开: firewall-cmd --query-port=666/tcp
- 提示 yes，表示开启；no表示未开启。



（2）查看防火墙状态

- 查看防火墙状态 systemctl status firewalld
- 开启防火墙 systemctl start firewalld  
- 关闭防火墙 systemctl stop firewalld
- 开启防火墙 service firewalld start 

> 若遇到无法开启
>
> 先用：systemctl unmask firewalld.service 
> 然后：systemctl start firewalld.service



（3）对外开发端口
查看想开的端口是否已开：

```shell
$ firewall-cmd --query-port=6379/tcp
```



（4）添加指定需要开放的端口：

```shell
$ firewall-cmd --add-port=123/tcp --permanent
```

重载入添加的端口：

```shell
$ firewall-cmd --reload
```

查询指定端口是否开启成功：

```shell
$ firewall-cmd --query-port=123/tcp
```



（5）移除指定端口：

```shell
$ firewall-cmd --permanent --remove-port=123/tcp
```

（6）安装iptables-services ：

```shell
$ apt-get install iptables-services 
```

进入下面目录进行修改：

```shell
$ /etc/sysconfig/iptables
```

