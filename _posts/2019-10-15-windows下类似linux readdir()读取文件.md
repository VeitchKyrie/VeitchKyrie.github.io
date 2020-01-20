---
layout:     post
title:      windows下类似linux readdir()读取文件
subtitle:   仿写linux opendir等
date:       2019-10-15
author:     VK
catalog: true
tags:
    - linux
---

## windows下类似linux readdir()读取文件

仿写头文件 dirent.h
模拟linux下头文件 myDirent.h

```c
ifndef MYDIRENT_H
define MYDIRENT_H

typedef struct _dirdesc {
int     dd_fd;      /** file descriptor associated with directory */
long    dd_loc;     /** offset in current buffer */
long    dd_size;    /** amount of data returned by getdirentries */
char    *dd_buf;    /** data buffer */
int     dd_len;     /** size of data buffer */
long    dd_seek;    /** magic cookie returned by getdirentries */
} DIR;

define __dirfd(dp)    ((dp)->dd_fd)

DIR *opendir(const char *);

struct dirent *readdir(DIR *);

void rewinddir(DIR *);

int closedir(DIR *);

include <sys/types.h>

struct dirent
{
    long d_ino;              /* inode number*/
    off_t d_off;             /* offset to this dirent*/
    unsigned short d_reclen; /* length of this d_name*/
    unsigned char d_type;    /* the type of d_name*/
    char d_name[1];          /* file name (null-terminated)*/
};
endif

```

实现函数 cpp
windows下仿写opendir() readdir() closedir()
利用FindFirstFile() FindNextFile() 这两个windows函数

myDirent.cpp

```c
include <iostream>
include <string>
include <stdio.h>
include <windows.h>
include "myDirent.h"
static HANDLE hFind;

DIR *opendir(const char *name)
{
DIR *dir;
WIN32_FIND_DATA FindData;
char namebuf[512];

//int sprintf ( char * str, const char * format, ... ); Write formatted data to string
sprintf(namebuf, "%s\\*.*", name);

hFind = FindFirstFile(namebuf, &FindData);
if (hFind == INVALID_HANDLE_VALUE)
{
    printf("FindFirstFile failed (%d)\n", GetLastError());
    return 0;
}

dir = (DIR *)malloc(sizeof(DIR));
if (!dir)
{
    printf("DIR memory allocate fail\n");
    return 0;
}

memset(dir, 0, sizeof(DIR));
dir->dd_fd = 0;   // simulate return  

return dir;
}

struct dirent *readdir(DIR *d)

{
int i;

BOOL bf;
WIN32_FIND_DATA FileData;
if (!d)
{
    return 0;
}

bf = FindNextFile(hFind, &FileData);
//fail or end  
if (!bf)
{
    return 0;
}

struct dirent *dir = (struct dirent *)malloc(sizeof(struct dirent) + sizeof(FileData.cFileName));

for (i = 0; i < 256; i++)
{
    dir->d_name[i] = FileData.cFileName[i];
    if (FileData.cFileName[i] == '\0') break;
}
dir->d_reclen = i;
dir->d_reclen = FileData.nFileSizeLow;

//check there is file or directory  
if (FileData.dwFileAttributes & FILE_ATTRIBUTE_DIRECTORY)
{
    dir->d_type = 2;
}
else
{
    dir->d_type = 1;
}

return dir;
}

int closedir(DIR *d)
{
}if (!d) 
    return -1;
hFind = 0;
free(d);
return 0;
}
```


测试一下
```c
int main()
{
// use as readdir() in Linux
DIR *dir;
struct dirent *ptr;

char *flow[65536];
int num = 0, i = 0;

if ((dir = opendir("E:\\data_of_weibo\\data_washed\\event_max_time")) == NULL) {
    std::cerr << "open dir error." << std::endl;
    return false;
}

while ((ptr = readdir(dir)) != NULL) {
    flow[num] = (char *)malloc(sizeof(char));
    strcpy(flow[num], ptr->d_name);             //char d_name[1];
    num++;
}

// output test
for (i = 0; i < num; ++i) {
    if (strcmp(flow[i], "..") == 0 || strcmp(flow[i], ".") == 0)
        std::cout << "find last directory." << std::endl;
    else
        std::cout << std::string(flow[i]) << std::endl;
}
std::cout << "total file: " << num - 1 << std::endl;

closedir(dir);

std::cout << "Press any key to continue.." << std::endl;
getchar();

return 0;
}
```


update:

//上面的写法，strcpy拷贝之后free flow数组会出问题，可能是覆盖了维护数据结构，改一下

```c++
int main()
{
// use as readdir() in Linux
DIR *dir;
struct dirent *ptr;

std::vector<std::string> resVec;

int num = 0, i = 0;

if ((dir = opendir("E:\\data_of_weibo\\data_washed\\event_max_time")) == NULL) {
    std::cerr << "open dir error." << std::endl;
    return false;
}

while ((ptr = readdir(dir)) != NULL)
    resVec.push_back(std::string(ptr->d_name));     //char d_name[1];

for (unsigned j = 0; j < resVec.size(); j++)
    std::cout << resVec.at(j) << std::endl;     //包含 ".." 上级目录

std::cout << resVec.size() << std::endl;

closedir(dir);

std::cout << "Press any key to continue.." << std::endl;
getchar();

return 0;
}
```

