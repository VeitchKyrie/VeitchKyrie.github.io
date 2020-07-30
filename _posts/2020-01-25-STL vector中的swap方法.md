---
layout:     post
title:      关于STL vector中的swap
subtitle:   STL中swap的理解
date:       2020-01-25
author:     VK
catalog: true
tags:
    - C++
---

# STL vector中的swap

# std::[vector](http://www.cplusplus.com/reference/vector/vector/)::swap

```C++
void swap (vector& x);
```

Swap content

Exchanges the content of the container by the content of *x*, which is another [vector](http://www.cplusplus.com/vector) object of the same type. Sizes may differ.

与x交换其内容，x的类型要与该vector一样，大小可以不同。

After the call to this member function, the elements in this container are those which were in *x* before the call, and the elements of *x*are those which were in this. All iterators, references and pointers remain valid for the swapped objects.

调用该函数之后，存储在该容器内的元素是那些本来在x中的元素，x中的元素是原本存储在该容器中的元素(即交换内容)，所有的迭代器，引用以及指针在交换后依旧有效。

例子:

```C++
#include <iostream>
#include <vector>

using namespace std;

int main(int argc,char **argv)
{
	vector<int> v1={1,2,3,4};	
	vector<int> v2={111,222,333};
	cout<<"v1.data="<<v1.data()<<endl;
	cout<<"v1:";
	
	for(auto it=v1.begin();it!=v1.end();++it)
		cout<<*it<<" ";
	cout<<endl;

	cout<<"v2.data="<<v2.data()<<endl;
	cout<<"v2:";
	for(auto it=v2.begin();it!=v2.end();++it)
		cout<<*it<<" ";
	cout<<endl;

	auto i1=v1.begin();
	auto i2=v2.begin();
	cout<<"i1="<<*i1<<endl<<"i2="<<*i2<<endl;
	v1.swap(v2);
	cout<<"after v1.swap(v2)"<<endl;
	
	cout<<"v1.data="<<v1.data()<<endl;
	cout<<"v1:";
	for(auto it=v1.begin();it!=v1.end();++it)
		cout<<*it<<" ";
	cout<<endl;



	cout<<"v2.data="<<v2.data()<<endl;
	cout<<"v2:";
	for(auto it=v2.begin();it!=v2.end();++it)
		cout<<*it<<" ";
	cout<<endl;

	cout<<"i1="<<*i1<<endl<<"i2="<<*i2<<endl;
}

```

Notice that a non-member function exists with the same name, [swap](http://www.cplusplus.com/vector:swap), overloading that algorithm with an optimization that behaves like this member function.

需要注意的是在algorithm中，有一个非成员方法swap,优化重写了该方法，行为也相似。

- [C++98]()
- [C++11]()

Whether the container [allocators](http://www.cplusplus.com/vector::get_allocator) are also swapped is not defined, unless in the case the appropriate [allocator traits](http://www.cplusplus.com/allocator_traits) indicate explicitly that they shall [propagate](http://www.cplusplus.com/allocator_traits#types).
不肯定容器的内存分配器是否也会交换，除非内存分配器的特性特别指明其内存分配器应该被继承。

The `bool` specialization of [vector](http://www.cplusplus.com/vector) provides an additional overload for this function (see [vector::swap](http://www.cplusplus.com/vector%3Cbool%3E::swap)).
vector<bool>::swap特别重写了该方法。

### Parameters

- x

Another [vector](http://www.cplusplus.com/vector) container of the same type (i.e., instantiated with the same template parameters, `T` and `Alloc`) whose content is swapped with that of this container.

x是另一个将被交换的同类型的vector容器。

### Return value

none

### Example

| `1234567891011121314151617181920212223` | `// swap vectors#include <iostream>#include <vector>int main (){std::vector<int> foo (3,100); // three ints with a value of 100std::vector<int> bar (5,200); // five ints with a value of 200foo.swap(bar);std::cout << "foo contains:";for (unsigned i=0; i<foo.size(); i++)std::cout << ' ' << foo[i];std::cout << '\n';std::cout << "bar contains:";for (unsigned i=0; i<bar.size(); i++)std::cout << ' ' << bar[i];std::cout << '\n';return 0;}` | [Edit & Run](http://www.cplusplus.com/reference/vector/vector/swap/#) |
| --------------------------------------- | ---------------------------------------- | ---------------------------------------- |
|                                         |                                          |                                          |

Output:

### Complexity

Constant.

### Iterator validity

All iterators, pointers and references referring to elements in both containers remain valid, and are now referring to the same elements they referred to before the call, but in the other container, where they now iterate.

两个容器中所有的迭代器，指针以及引用都保持有效。

Note that the *end iterators* do not refer to elements and may be invalidated.

需要注意的是end得到的迭代器是不关联到实际的元素的，这可能会失效。

### Data races

Both the container and *x* are modified.
No contained elements are accessed by the call (although see *iterator validity* above).

x以及该容器都将被修改。

容器的元素不会被访问。

### Exception safety

If the allocators in both [vectors](http://www.cplusplus.com/vector) compare equal, or if their [allocator traits](http://www.cplusplus.com/allocator_traits) indicate that the allocators shall [propagate](http://www.cplusplus.com/allocator_traits#types), the function never throws exceptions (no-throw guarantee).

Otherwise, it causes *undefined behavior*.