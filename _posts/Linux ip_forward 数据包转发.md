# Linux ip_forward 数据包转发

出于安全考虑，Linux系统默认是禁止数据包转发的。所谓转发即当主机拥有多于一块的网卡时，其中一块收到数据包，根据数据包的目的ip地址将数据包发往本机另一块网卡，该网卡根据路由表继续发送数据包。这通常是路由器所要实现的功能。

要让Linux系统具有路由转发功能，需要配置一个Linux的内核参数`net.ipv4.ip_forward`。这个参数指定了Linux系统当前对路由转发功能的支持情况；其值为0时表示禁止进行IP转发；如果是1,则说明IP转发功能已经打开。

要配置Linux内核中的`net.ipv4.ip_forward`参数有多种配置方式可供选择，下面分别介绍。

## 临时生效的配置方式

临时生效的配置方式，在系统重启，或对系统的网络服务进行重启后都会失效。这种方式可用于临时测试、或做实验时使用。

### 使用 sysctl 指令配置

sysctl 命令的 -w 参数可以实时修改Linux的内核参数，并生效。所以使用如下命令可以开发Linux的路由转发功能。

```
sysctl -w net.ipv4.ip_forward=1

```

有关 sysctl 指令的更详细介绍，请参见Linux的系统man手册（`man sysctl`），或其他有关sysctl指令详细介绍的文章。

### 修改内核参数的映射文件：/proc/sys/net/ipv4/ip_forward

内核参数在Linux文件系统中的映射出的文件：/proc/sys/net/ipv4/ip_forward中记录了Linux系统当前对路由转发功能的支持情况。文件中的值为0,说明禁止进行IP转发；如果是1,则说明IP转发功能已经打开。可使用vi编辑器修改文件的内容，也可以使用如下指令修改文件内容：

```shell
echo 1 > /proc/sys/net/ipv4/ip_forward
```

## 永久生效的配置方式

永久生效的配置方式，在系统重启、或对系统的网络服务进行重启后还会一直保持生效状态。这种方式可用于生产环境的部署搭建。

### 修改/etc/sysctl.conf 配置文件

在sysctl.conf配置文件中有一项名为`net.ipv4.ip_forward`的配置项，用于配置Linux内核中的`net.ipv4.ip_forward`参数。其值为0,说明禁止进行IP转发；如果是1,则说明IP转发功能已经打开。

需要注意的是，修改sysctl.conf文件后需要执行指令`sysctl -p` 后新的配置才会生效。

有关 sysctl 指令和sysctl.conf配置文件的更详细介绍，请参见Linux的系统man手册（`man sysctl`和`man sysctl.conf`），或其他有关sysctl指令和sysctl.conf配置文件的文章。

### 修改/etc/sysconfig/network配置文件

在文件最后添加一行：`FORWARD_IPV4=YES`

需要注意的是，修改/etc/sysconfig/network配置文件后需要重启网络服务(`service netwrok restart`)才能使新的配置生效。