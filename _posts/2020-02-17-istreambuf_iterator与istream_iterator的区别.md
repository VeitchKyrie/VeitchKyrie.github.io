---
layout:     post
title:      C++：istreambuf_iterator与istream_iterator的区别
subtitle:   关于2个流迭代器的理解和区别
date:       2020-02-17
author:     VK
catalog: true
tags:
    - C++
---



# C++：istreambuf_iterator与istream_iterator的区别

```c++
	std::vector<uint8_t> data;    
	std::ifstream stm(ECU_CA_KEY_PATH);
    if (false == stm.is_open()) return false;
    data.clear();
    std::copy(std::istreambuf_iterator<char>(stm), std::istreambuf_iterator<char>(),  //不带参数默认返回eof的迭代器，相当于end
    std::back_inserter(data));
    stm.close();
```

在C++中，流（stream）也可以看做是容器，因而也有相应的iterator来遍历流中的内容，其中就有本文要介绍的两个流迭代器：istreambuf_iterator和istream_iterator，这两个迭代器的用法和区别可以用一下两段代码来体现：

例1：istreambuf_iterator

```c++
#include <fstream>
#include <iostream>
#include <iterator>
using namespace std;
 
int main(){
 
	ifstream in("test.cpp");
	istreambuf_iterator<char> isb(in),end;
	ostreambuf_iterator<char> osb(cout);
	while(isb!=end)
		*osb++ = *isb++;
	cout<<endl;
	return 0;
}
```

这段代码的意思是把test.cpp中的内容读出来并打印到终端上，输出的结果原分不动地保留了test.cpp的格式，下面我们来看另外一个例子：

```c++
#include <fstream>
#include <iostream>
#include <iterator>
using namespace std;
 
int main(){
 
	ifstream in("test.cpp");
	istream_iterator<char> isb(in),end;
	ostream_iterator<char> osb(cout);
	while(isb!=end)
		*osb++ = *isb++;
	cout<<endl;
	return 0;
}

```

这段代码的输出舍弃了test.cpp中的所有空白！所以打印在终端上一堆字符。区分这两个iterator也很简单，只要记住带“buf”的更接近底层，所以原分不动地把所有字符都读了进来。
