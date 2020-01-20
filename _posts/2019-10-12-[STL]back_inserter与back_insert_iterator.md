---
layout:     post
title:      [STL]back_inserter 与back_insert_iterator
subtitle:   inserter迭代器
date:       2019-10-12
author:     VK
catalog: true
tags:
    - c++
---

# [STL]back_inserter 与back_insert_iterator

back_inserter一个成员函数，返回值是back_insert_iterator, 本质上是push_back进行操作的, 返回值back_insert_iterator, 并实现其自增.

```c++
	std::vector<int> firstvector, secondvector;
	for (int i=1; i<=5; i++)
	{ 
		firstvector.push_back(i); 
		secondvector.push_back(i*10);
	}
 
	// 在firstvector向量后面加入secondvector的值.
	std::copy(secondvector.begin(), secondvector.end(), std::back_inserter(firstvector));
 
	std::vector<int>::iterator it;
	for ( it = firstvector.begin(); it!= firstvector.end(); ++it )
		std::cout << *it << " ";
	std::cout << std::endl;
```

下面是一个back_insert_iterator的例子

```c++
	std::vector<int> vec;
	for (int i = 0 ; i < 3; ++i)
		vec.push_back(i);
 
	std::vector <int>::iterator vIter;
	std::cout << "The initial vector vec is: ( ";
	for (vIter = vec.begin(); vIter != vec.end(); vIter++)
		std::cout << *vIter << " ";
	std::cout << ")." << std::endl;
 
	// Insertions can be done with template function
	std::back_insert_iterator<std::vector<int> > backiter(vec);
	*backiter = 30;
	backiter++;
	*backiter = 40;
 
	// Alternatively, insertions can be done with the
	// back_insert_iterator member function
	std::back_inserter(vec) = 500;
	std::back_inserter(vec) = 600;
 
	std::cout << "After the insertions, the vector vec is: ( ";
	for (vIter = vec.begin(); vIter != vec.end(); vIter++)
		std::cout << *vIter << " ";
	std::cout << ")." << std::endl;
```

