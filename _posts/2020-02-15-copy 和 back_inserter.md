---
layout:     post
title:      std::copy 和 std::back_inserter
date:       2020-02-15
author:     VK
catalog: true
tags:
    - C++
---



# std::copy 和 std::back_inserter

```c++
#include <iostream>
#include <vector>

using namespace std;

#define print_vector(v1) \
    for(auto iter = v1.begin();iter != v1.end();iter++) \
        cout<<*iter<<" "; \
    cout<<endl;

int main() 
{
    std::vector<int> v1(3,10);
    std::vector<int> v2(100,9);
   print_vector(v1);
    print_vector(v2);
    //std::copy(v1.begin(),v1.end(),v2.begin());//把v1 copy到v2。v1的个数少于v2，这样是可以的
    //std::copy(v2.begin(),v2.end(),v1.begin()); //把v2  copy到v1 这样v1的个数不路以容纳，会崩溃 
    //可以下std::back_insert函数
    auto iter = std::back_inserter(v1);
    std::copy(v2.begin(),v2.end(),iter);//这样的copy是追加到v1的后面了
    print_vector(v1);
}
```

