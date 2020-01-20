---
layout:     post
title:      CMake使用Makefile中的pkg-config
subtitle:   GDB调试配置
date:       2019-09-18
author:     VK
catalog: true
tags:
    - CMake
---

## CMake使用Makefile中的pkg-config

#### 一、Makefile中的pkg-config

##### 简介及使用：

在编写Makefile的过程中，一般对于库的依赖可以通过在执行configure的时候通过--with-**=/**/**/**来进行指定，然后在configure脚本中进行处理生成相应的变量存储该路径，然后放入到Makefile中，在执行命令的时候附在gcc命令中即可。

同时我们可以采用另外一种方法来获取库文件的信息，例如下面要介绍的pkg-config，这样就能够迅速获得完整的依赖的库的信息，而不需要我们自己去通过给Makefile中的命令加参数的方式去写。

##### 1. pkg-config的作用

1. 检查库的版本号。如果所需要的库的版本不满足要求，它会打印出错误信息，避免链接错误版本的库文件。
2. 获得编译预处理参数，如宏定义，头文件的位置。
3. 获得链接参数，如库及依赖的其它库的位置，文件名及其它一些连接参数。
4. 自动加入所依赖的其它库的设置。

事实上，为了让pkg-config可以得到这些信息，要求库的提供者，提供一个.pc文件，而这个文件一般在编译成功之后会生成，在prefix/lib/pkgconfig/目录下，例如我们编译了mongoc这个库，得到的pkgconfig如下所示：

[![mongoc.png](http://assets.tianmaying.com/md-image/f635986eb7b79b1616e9384bced8fd80.png)](http://assets.tianmaying.com/md-image/f635986eb7b79b1616e9384bced8fd80.png)

打开libmongoc-1.0.pc则可看到主要记录了头文件、库文件位置的信息，都很简单，但是非常完整

[![mongocpkgconfig.png](http://assets.tianmaying.com/md-image/353838fa5a6de144864dd9a32453e4aa.png)](http://assets.tianmaying.com/md-image/353838fa5a6de144864dd9a32453e4aa.png)

例如我们通过命令

```shell
pkg-config --cflags --libs libmongoc-1.0
```

即可获取到跟libmongoc相关的信息

```shell
-I/home/xixy10/waf/mongoc/include/libmongoc-1.0  -L/home/xixy10/waf/mongoc/lib -L/usr/local/lib -lsasl2 -lssl -lcrypto -lrt -lmongoc-1.0 -lbson-1.0
```

##### 2 .pc文件路径

这个文件一般放在 /usr/lib/pkgconfig/ 或者 /usr/local/lib/pkgconfig/ 里，当然也可以放在其它任何地方，如像 X11 相关的pc文件是放在 /usr/X11R6/lib/pkgconfig 下的。为了让pkgconfig可以找到你的pc文件，你要把pc文件所在的路径，设置在环境变量 PKG_CONFIG_PATH 里。

在ubuntu 14.04中，是放在了/usr/local/lib/pkgconfig和/usr/lib/pkgconfig/中的，我把自己的库文件编译完之后都把*.pc放在了/usr/local/lib/pkgconfig中，这样直接用pkg-config来获取信息以及相应的依赖信息。这样无论将库文件make install到哪里都可以，无需自己记这些内容。但是一般还是建议库文件管理比较清楚才好。

##### 简单的直接使用示例：

```shell
gcc -o test test.c $(pkg-config --libs --cflags libpng)
```



#### 二、CMake中的PkgConfig

项目中需要使用到gstreamer-1.0，但由于gstreamer-1.0是C编译的，官方使用的是Makefile文件编写的编译脚本。而项目中只使用CMake，其实用两个方法：

1.将Makefile的内容转换为CMake中的内容

2.在CMake中直接调用Makefile（待学习）

本项目中用到的是方法1：

以下是网上在Cmake中使用pkgconfig的方法

>GStreamer implement standard pkg-config metadata. CMake provides the
>PkgConfig package to let you interface with that. Here's an exemple:
>
>find_package(PkgConfig)
>
>pkg_check_modules(GST REQUIRED gstreamer-1.0>=1.4
>​                               gstreamer-sdp-1.0>=1.4
>​                               gstreamer-video-1.0>=1.4
>​                               gstreamer-app-1.0>=1.4)
>
>Notice that GStreamer is a collection of libraries. The list will
>depend on your application needs. From there you'll have to update few
>things. Globally, link_directories with ${GST_LIBRARY_DIRS} (only
>needed if you use GStreamer from non-system installation). And per
>target:
> - target_include_directories with ${GST_INCLUDE_DIRS}
> - target_compile_options with ${GST_CFLAGS}
> - target_link_libraries with ${GST_LIBRARIES}
>
>You'll also have to choose between private and public exposure
>depending if you create an app or library and if you expose GStreamer
>through your library or not.



#### 三、实际案例：

原始Makefile：

```shell
#
# Simple Makefile for cross compilation
#
# Author : Pierre-Yves MORDRET
#
CFLAGS	   =`pkg-config --cflags  gstreamer-1.0` 
LDFLAGS	   =`pkg-config --libs gstreamer-1.0`

CFLAGS += -g -O0 -D_REENTRANT
LDFLGS += -lpthread

EXE:= rvc_handler
install_dir = .

all:  $(EXE)

clean:
	rm -f *.o $(EXE)

%.o: %.c
	@echo ""
	@echo "Compiling $<"
	$(CC) -Wall -c $(CFLAGS) $< -o $@

$(EXE):	$(EXE).o
	@echo ""
	@echo "Linking $<"
	$(CC) $(LDFLAGS) $< -o $@  #需要注意的事，LDFLAGS放在目标$@之前，实际上会报undefined reference的错误（因为需要生成目标后才能链接），因此正确的指令是：$(CC) $< -o $@ $(LDFLAGS)

install:
	install -d $(install_dir)
	@set -e; for i in $(EXE) ; do install $$i $(install_dir)/$$i ; done ;
```

转换到CMake中：

```shell
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

project(rvc_handler)

find_package(PkgConfig)

pkg_check_modules(GST REQUIRED gstreamer-1.0>=1.4)#因为gstreamer-1.0需要依赖其libs/的多个库文件，因此必须使用pkg的方式，找到libs的路径去target_link_libraries

set(EXEC_NAME rvc_handler)

set(SRC_FILE
    rvc_handler.c
)

add_executable(${EXEC_NAME} ${SRC_FILE})

include_directories(${GST_INCLUDE_DIRS})
link_directories(${GST_LIBRARY_DIRS})

target_link_libraries(${EXEC_NAME}  ${GST_LIBRARIES} pthread)


#install
install(TARGETS ${EXEC_NAME}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
```







