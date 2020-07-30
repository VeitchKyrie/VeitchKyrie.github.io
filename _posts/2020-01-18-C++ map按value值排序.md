---
layout:     post
title:      map按value值排序
date:       2020-01-18
author:     VK
catalog: true
tags:
    - C++
---



# map按value值排序

map默认是按key值从小到大排序的

按value值排序：

想直接用sort排序是做不到的，sort只支持数组、vetctor等的排序，所以我们可以先把map装进pair里，然后再放入vector，自定义sort实现排序

假设已有一组map<string,int>类型的数mp,则具体实现过程为：

```c++
#include <algorithm>

vector< pair<string,int> > vec;
for(map<string,int>::iterator it = mp.begin(); it != mp.end(); it++){
    vec.push_back( pair<string,int>(it->first,it->second) );
}

bool cmp(pair<string,int> a, pair<string,int> b) {
	return a.second < b.second;
}

sort(vec.begin(),vec.end(),cmp);

```




