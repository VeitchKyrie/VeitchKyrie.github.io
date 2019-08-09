---
layout:     post
title:      遇到的问题---Argument list too long问题的解决方法
subtitle:   遇到的问题
date:       2019-08-01
author:     VK
catalog: true
tags:
    - linux
    - shell
---

# 遇到的问题---Argument list too long问题的解决方法

## 1.1 在linux系统中执行shell命令

    mv  /mnt/nfs/misquitofile/all_m1/*             /mnt/nfs/result
### 1.1.1 报错:

`Argument list too long`

### 1.1.2 原因:

操作的元素个数超过了系统内核规定的数量

##1.2 解决方式

使用find查找后 会逐一操作
使用命令如下：

    find /mnt/nfs/misquitofile/all_m1  -type f  -name '*' -print -exec mv -f {} /mnt/nfs/result/ \;
可能遇到的问题:
在linux下使用find命令时，报错：find: missing argument to `-exec’
解决方法

    -exec command {} \;  
    在{}和\之间必须要有空格，否则会报上面的错。
    \  和;之间不能有空格，否则同样报错：“find: missing argument to `-exec'”

还有其他几种解决方式列举如下:

### 1.2.1 方法1: 手动把命令行参数分成较小的部分

例1

`mv [a-l]* ../directory2 mv [m-z]* ../directory2`
这是4种方法里最简单的,但是远非理想的方法。你必须有办法平均分割文件，而且对于文件数目极多的情况，需要输入N遍命令。

### 1.2.2 方法2: 使用find命令

例2

`find directory −type f −name ′∗′ −exec mv directory2/.  `

方法2通过find命令筛选文件列表，把符合要求的文件传递给一系列命令。 优点是find命令有很强大的筛选功能，而且，也许是最重要的，这个方法只需要1行命令。 唯一的缺点是， 方法2需要遍历文件，因此耗时较多。

### 1.2.3 方法3: 建立函数

例3a
`function large_mv () { while read line1; do mv directory/$line1 ../directory2; done }; ls -1 directory/ | large_mv`

虽然写一个shell函数确实比较复杂，但这个方法比方法1或2更灵活，它依次处理每个文件，可以进行无数操作而只使用一个命令，如:

例3b

`function larger_mv () { while read line1; do md5sum directory/line1>> /md5sumsls−ldirectory/line1 >> ~/backup_list mv directory/$line1 ../directory2 ;done }； ls -1 directory/ | larger_mv`

例3b显示我们可以先轻松地得到文件的md5sum和备份文件列表然后移动文件

然而方法3也需要遍历每个文件，因此类似方法2,也比较耗时。根据经验，方法2要稍快一些，因此仅当需要复杂的操作时才使用方法3.

` function large_mv () { read line1 ;cp themes/$line1 . -r ; };ls -1 themes|large_mv`

### 1.2.4 方法4：重新编译Linux内核

最后一个方法需要2个字：谨慎，这个方法很高级，因此没有经验的linux用户最好不要尝试。此外，在永久使用前，务必在系统环境中全面测试。

方法4只需要手动增加内核中分配给命令行参数的页数。打开include/linux/binfmts.h文件，在文件起始附近位置有以下几行：

/*

MAX_ARG_PAGES defines the number of pages allocated for arguments
and envelope for the new program. 32 should suffice, this gives
a maximum env+arg of 128kB w/4KB pages! */ #define MAX_ARG_PAGES 32
为了增加分配格命令行参数的内存，只需要赋给MAX_ARG_PAGES一个更大的值，保存，重新编译，安装，重启，搞定

在我的系统中，我把MAX_ARG_PAGES的值增加到64,就解决了所有问题。在改变这个值后，我还没有遇到任何问题。这是可以理解的，当MAX_ARG_PAGES被改为64,最长的参数行仅占用256KB系统内存–对于现在的硬件标准不算什么。

方法4的优点很明显，现在你只要像通常一样运行命令。缺点也很明显,如果分配给命令行的内存大于可用的系统内存，可能导致对系统自身的拒绝服务攻击(DoS attack)，引起系统崩溃。尤其是对于多用户系统，即使增加很小的内存分配都会有很大影响，因为每个用户都被分配到额外内存。因此一定要充分测试来决定是否你的系统可以使用方法

