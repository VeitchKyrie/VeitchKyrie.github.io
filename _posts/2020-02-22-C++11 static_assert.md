---
layout:     post
title:      C++11 static_assert
subtitle:   编译期间的断言，因此叫做静态断言。
date:       2020-02-22
author:     VK
catalog: true
tags:
    - C++
---



# [C++11 static_assert](https://www.cnblogs.com/DswCnblog/p/6369576.html)

C++0x中引入了**static_assert**这个关键字，用来做编译期间的断言，因此叫做静态断言。

其语法：**static_assert**(常量表达式，提示字符串)。

如果第一个参数常量表达式的值为false，会产生一条编译错误，错误位置就是该**static_assert**语句所在行，第二个参数就是错误提示字符串。

使用**static_assert**，我们可以在编译期间发现更多的错误，用编译器来强制保证一些契约，并帮助我们改善编译信息的可读性，尤其是用于模板的时候。

**static_assert**可以用在全局作用域中，命名空间中，类作用域中，函数作用域中，几乎可以不受限制的使用。

编译器在遇到一个**static_assert**语句时，通常立刻将其第一个参数作为常量表达式进行演算，但如果该常量表达式依赖于某些模板参数，则延迟到模板实例化时再进行演算，这就让检查模板参数成为了可能。

由于之前有望加入C++0x标准的**concepts**提案最终被否决了，因此对于检查模板参数是否符合期望的重任，就要靠**static_assert**来完成了，所以如何构造适当的常量表达式，将是一个值得探讨的话题。

性能方面，由于是**static_assert**编译期间断言，不生成目标代码，因此**static_assert**不会造成任何运行期性能损失。

 

简单范例：

```c++
static_assert(sizeof(void *) == 4, "64-bit code generation is not supported.");
```

该**static_assert**用来确保编译仅在32位的平台上进行，不支持64位的平台，该语句可以放在文件的开头处，这样可以尽早检查，以节省失败情况下的编译时间。

另一个范例：

```c++
#include <cassert>
#include <cstring>
using namespace std;

template <typename T, typename U> int bit_copy(T& a, U& b){
    //assert(sizeof(b) == sizeof(a));
　　static_assert(sizeof(b) == sizeof(a), "template parameter size no equal!");
    memcpy(&a,&b,sizeof(b));
};

int main()
{
    int aaa = 0x2468;
    double bbb;
    bit_copy(aaa, bbb);
    getchar();
    return 0;
}
```



这里使用assert运行时断言，但如果bit_copy不被调用，我们将无法触发该断言，实际上正确产生断言的时机应该在模版实例化时，即编译时期。

使用static_assert替换assert再次编译，即可获得一个错误并显示我们指定的错误信息。

上述代码在编译时会报错，即使错误的代码不会被执行到。断言失败信息如下：

```shell
g++ -o test.o -c -g -std=c++11 test.cc
test.cc: In instantiation of 'void bit_copy(T&, U&) [with T = double; U = int]':
test.cc:13:16:   required from here
test.cc:6:3: error: static assertion failed: data size error
   static_assert(sizeof(a) == sizeof(b), "data size error");
```

注意：static_assert的断言表达式的结果必须是在编译时期可以计算的表达式,即必须是常量表达式。如果使用变量，则会导致错误。

由于静态断言是在编译器进行检查的，静态断言使用的值必须是在编译期可以确定的。看如下代码：

```c++
int positive(const int n) {
    static_assert(n > 0, "value must > 0");
    return 0;
}
```

 上述代码会编译失败，信息如下：

```shell
g++ -o test.o -c -g -std=c++11 test.cc
test.cc: In function 'int positive(int)':
test.cc:4:5: error: non-constant condition for static assertion
     static_assert(n > 0, "val must > 0");
     ^
test.cc:4:5: error: 'n' is not a constant expression
```

而编译器可以确定的值通常包括宏定义的数值、 sizeof()、全局作用域内的 const 值等。下面的代码是可以编译通过的：

```c++
int positive(const int n) {
    static_assert(sizeof(n) > 4, "val must > 4");
}

int main(void) {
}
```



**和Assert，#error比较**

我们知道，C++现有的标准中，就有**assert、#error**两个设施，也是用来检查错误的，还有一些第三方的静态断言实现。

**assert**是运行期断言，它用来发现运行期间的错误，不能提前到编译期发现错误，也不具有强制性，也谈不上改善编译信息的可读性，既然是运行期检查，对性能当然是有影响的，所以经常在发行版本中，assert都会被关掉；

**#error**可看做预编译期断言，甚至都算不上断言，仅仅能在预编译时显示一个错误信息，它能做的不多，可以配合#ifdef/ifndef参与预编译的条件检查，由于它无法获得编译信息，当然就做不了更进一步分析了。

在**stastic_assert**提交到C++0x标准之前，为了弥补**assert**和**#error**的不足，出现了一些第三方解决方案，可以作编译期的静态检查，例如:**BOOST_STATIC_ASSERT**和**LOKI_STATIC_CHECK**，但由于它们都是利用了一些编译器的隐晦特性实现的trick，可移植性、简便性都不是太好，还会降低编译速度，而且功能也不够完善，例如**BOOST_STATIC_ASSERT**就不能定义错误提示文字，而**LOKI_STATIC_CHECK**则要求提示文字满足C++类型定义的语法。

分类: [C++11](https://www.cnblogs.com/DswCnblog/category/847018.html)

[好文要顶](javascript:void(0);) [关注我](javascript:void(0);) [收藏该文](javascript:void(0);)