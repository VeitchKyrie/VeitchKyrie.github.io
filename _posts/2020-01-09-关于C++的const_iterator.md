---
layout:     post
title:      const_iterator
subtitle:   
date:       2020-01-09
author:     VK
catalog: true
tags:
    - C++
---



# 关于C++的const_iterator

c.begin() 返回一个迭代器，它指向容器c的第一个元素

c.end() 返回一个迭代器，它指向容器c的最后一个元素的下一个位置

c.rbegin() 返回一个逆序迭代器，它指向容器c的最后一个元素

c.rend() 返回一个逆序迭代器，它指向容器c的第一个元素前面的位置



1.iterator,const_iterator作用：遍历容器内的元素，并访问这些元素的值。iterator可以改元素值,但const_iterator不可改。跟C的指针有点像。
2.const_iterator 对象可以用于const vector 或非 const vector,它自身的值可以改(可以指向其他元素),但不能改写其指向的元素值。
3.cbegin()和cend()是C++11新增的，它们返回一个const的迭代器，不能用于修改元素。

```c++
auto i1 = Container.begin();  // i1 is Container<T>::iterator 
auto i2 = Container.cbegin(); // i2 is Container<T>::const_iterator
```



remove_if 函数的第三个参数用于判断要移除的逻辑，会将前面容器中的子元素作为_1的数据，传入bind(greater<>{},\_1,2)中

```c++
numbers.erase(remove_if(begin(numbers),end(numbers),bind(greater<>{},_1,2)),end(numbers));
```




