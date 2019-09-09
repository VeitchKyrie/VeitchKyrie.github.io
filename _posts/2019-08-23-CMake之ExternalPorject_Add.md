---
layout:     post
title:      CMake之ExternalPorject_Add
subtitle:   问题分析及解决
date:       2019-08-23
author:     VK
catalog: true
tags:
    - cmake
---

## CMake之ExternalPorject_Add

很多时候需要移植第三方的库，这时就可以使用ExternalPorject_Add关键字，简介方便快捷

```cmake

cmake_minimum_required(VERSION 2.6 FATAL_ERROR)

cmake_policy(VERSION 2.6)

# 1. Project Name

project(svp-issw-ServiceRegistry)

# 2. Platform Env (Include/Lib Path, C/CXX/LD FLAGS)

add_definitions("-fPIC -Wno-deprecated -O2") #-W -Wall

add_definitions("-DDEFAULT_SEND_TIMEOUT=10000")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -D_GLIBCXX_USE_NANOSLEEP")

# 3. Project Env (Include/Lib Path, C/CXX/LD FLAGS)
set(ServiceRegistryInstall ${PROJECT_SOURCE_DIR}/build/g5r2-release/ServiceRegistryInstallFiles)

# 4. Sub Projects
include(${CMAKE_ROOT}/Modules/ExternalProject.cmake) #需要包含这个模块才能使用这个方法
ExternalProject_Add(ServiceRegistry  #Project名字，自定义即可，不一定要和第三方包的文件夹同名
    SOURCE_DIR      ${PROJECT_SOURCE_DIR}/ServiceRegistry  #指定第三方库的相对路径目录为当前Project目录下的ServiceRegistry子目录
    #当需要在ExternalProject中指定CMAKE的环境变量时，如CMAKE_INSTALL_PREFIX等参数，需要使用 CMAKE_ARGS关键字 并使用-D开头
    CMAKE_ARGS      -DCMAKE_INSTALL_PREFIX=${ServiceRegistryInstall} #指定CMAKE的安装路径，因为第三方库默认都是将生成库和头文件安装到CMAKE_INSTALL_PREFIX目录下
    CMAKE_ARGS      -DPoco_ROOT_DIR=${PROJECT_SOURCE_DIR}/fakeroots #指定第三方库依赖的库文件路径，这里由于移植到的平台路径太长，我弄了一个软链接指定，因此命名为fakeroots以免混淆
    CMAKE_ARGS      -DOPENSSL_ROOT_DIR=${PROJECT_SOURCE_DIR}/fakeroots #同上
    CMAKE_ARGS      -DOPENSSL_LIBRARIES=ssl1 #指定依赖的库，由于本地会有ssl和ssl1两个版本的ssl库，但第三方库要求只要1.0版本以上，即ssl1，因此在这里重新指定，以免使用到默认环境变量中的ssl版本库
    CMAKE_ARGS      -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}  #在ExternalProject_Add外部声明的CMAKE_CXX_FLAGS不会被引入到ExternalProject的CMAKE编译中，因此需要使用CMAKE_ARGS参数在ExternalProject编译时引用
    )


#此时，只需要将CMAKE_INSTALL_PREFIX目录下的文件拷贝到指定目录就好了，因为正常来说，第三方库写好的cmake就会将它打算开放的库和头文件安装到CMAKE_INSTALL_PREFIX目录，当需要用到第三方project时，建议-L链接CMAKE_INSTALL_PREFIX目录下所有的库（因为第三方的库自身可能有依赖），而不需要自己用到某个库中的方法时再去install第三方源文件夹中去找所需对应头文件和库(这样太复杂)

# 5. Project Install 
install(DIRECTORY ${ServiceRegistryInstall}/include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/issw/ServiceRegistry)
install(DIRECTORY ${ServiceRegistryInstall}/lib/ DESTINATION ${CMAKE_INSTALL_LIBDIR}/issw/ServiceRegistry)

```

