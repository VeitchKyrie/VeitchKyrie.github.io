---
layout:     post
title:      Ubuntu 下libfreenect依赖环境的安装
subtitle:   环境依赖
date:       2019-08-18
author:     VK
catalog: true
tags:
    - lib
---

# Ubuntu 下libfreenect依赖环境的安装

OpenGL 是一套由SGI公司发展出来的绘图函式库，它是一组 C 语言的函式，用于 2D 与 3D 图形应用程式的开发上。OpenGL 让程式开发人员不需要考虑到各种显示卡底层运作是否相同的问题，硬体由 OpenGL 核心去沟通，因此只要显示卡支援 OpenGL，那么程式就不需要重新再移植，而程式开发人员也不需要重新学习一组函式库来移植程式。

## 1 安装


首先不可或缺的就是编译器与基本的函式库，如果系统没有安装的话，依照下面的方式安装：

```shell
$ sudo apt-get install build-essential
```

### 1.1 安装OpenGL Library

```shell
$ sudo apt-get install libgl1-mesa-dev
```

### 1.2 安装OpenGL Utilities

```shell
$ sudo apt-get install libglu1-mesa-dev
```

       OpenGL Utilities 是一组建构于 OpenGL Library 之上的工具组，提供许多很方便的函式，使 OpenGL 更强大且更容易使用。

### 1.3 安装OpenGL Utility Toolkit

```shell
$ sudo apt-get install libglut-dev
```

       OpenGL Utility Toolkit 是建立在 OpenGL Utilities 上面的工具箱，除了强化了 OpenGL Utilities 的不足之外，也增加了 OpenGL 对于视窗介面支援。
       注意：在这一步的时候，可能会出现以下情况，shell提示：
Reading package lists... Done
Building dependency tree
Reading state information... Done
E: Unable to locate package libglut-dev
​将上述sudo apt-get install libglut-dev命令改成 sudo apt-get install freeglut3-dev即可。

或者执行如下命令[libfreenect](https://github.com/OpenKinect/libfreenect)

![libfreenect](img/libfreenect.jpg)

## 测试

示例test.c源码：

```c
include <GL/glut.h>

void init(void)
{
    glClearColor(0.0, 0.0, 0.0, 0.0);
    glMatrixMode(GL_PROJECTION);
    glOrtho(-5, 5, -5, 5, 5, 15);
    glMatrixMode(GL_MODELVIEW);
    gluLookAt(0, 0, 10, 0, 0, 0, 0, 1, 0);

    return;
}

void display(void)
{
    glClear(GL_COLOR_BUFFER_BIT);
    glColor3f(1.0, 0, 0);
    glutWireTeapot(3);
    glFlush();

    return;
}

int main(int argc, char *argv[])

{
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_RGB | GLUT_SINGLE);
    glutInitWindowPosition(0, 0);
    glutInitWindowSize(300, 300);
    glutCreateWindow("OpenGL 3D View");

    init();
    glutDisplayFunc(display);
    glutMainLoop();

    return 0;

}

```

编译程式时，执行以下指令：

```shell
$ gcc -o test test.c -lGL -lGLU -lglut
```

执行：

```shell