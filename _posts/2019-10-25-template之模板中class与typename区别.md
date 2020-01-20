---
layout:     post
title:      template之模板中class与typename区别
subtitle:   C++关键字
date:       2019-10-25
author:     VK
catalog: true
tags:
    - c++
---

# template之模板中class与typename区别

前言
在分析traits编程之前, 我们需要对模板参数类型tempname和class有一定的了解, 要明白他们在哪些方面不同, 哪些方面相同, 这样才能对体会到traits编程的核心. 如果你已经明白了两者, 那么你可以直接看下一篇了.

相同之处
一般对模板参数类型typename和class认为是一样的. 这两者在参数类型中确实是一样的. 你可以写成

```c++
template<class T> 
class point {};
```

也可以写成

```c++
template<typename T>
class point {};
```

这两者都是一样的, 没有区别. 两者typename和class在参数类型中没有不同

既然相同又为什么定义这两个符号呢?

最开始定义定义模板的方法就是template<class T> , 但是class毕竟都认为是一个类, 在使用时难免会有些点混淆, 也就定义了typename来标志参数类型
最重要关于 typename可以使用嵌套依赖类型, 也就是类型可以嵌套使用. 这也是两个的不同之处.
不同之处
typename可以用在嵌套依赖中, 并且表示其类型, 而class并没有这样的功能.

什么是嵌套依赖? 我们以一个简单的实例来看

```c++
template<class T>
class people
{
public:
    	typedef T	value_type;
    	typedef T*	pointer;
    	typedef T&	reference;
};

template<class T>
struct man 
{
public:
    	typedef typename T::value_type	value_type;
    	typedef typename T::pointer		pointer;
    	typedef typename T::reference	reference;
  
  /* //or 直接使用去声明变量：
  		typename T::value_type	val;
    	typename T::pointer		p;
    	typename T::reference	ref;
  */
    	void print()
    	{
    		cout << "man" << endl;
    	}
};

int main()
{
    man<people<int>> Man;
    Man.print();
    exit(0);
}
```

以上就是typename的嵌套使用. typename告诉编译器这不是一个函数, 也不是一个变量而是一个类型. 这里使用typedef又将参数类型重新定义一次, 1. 增加了一层间接性, 2. 使用的时候也不需要在写很长的代码.

这里typename是对people类中定义的类型进行了一次提取, 这里将typename改为class就会出错.

typename主要的作用:

对于模板参数是类的时候, typename能够提取出该类所定义的参数类型.
并不是所有的嵌套依赖类型都要加上typename, 有一个例外 : 当继承列表或成员初始化列表中对基类进行初始化的时候, 可以去掉typename关键字

```c++
man(int x) : T::value_type(x) {}
```

总结
这里对typename做了一个浅显的分析, 这也足够我们可以分析traits编程的基础了. 我再将以上的分析做一个归纳.

typename和class在作为参数类型时用法一样, 没有区别
typename主要用于对嵌套依赖类型进行提取(萃取). 而class没有这样的功能.
typename提取的一个例外是在继承或成员初始化列表中对基类进行初始化时不用加typename关键字



当子类中运用到父类的typedef定义的类型时，可采用如下的typename告诉编译器，使用的是父类中的类型而不是成员，方法如下：`typename Stack<T>::type_int a;`

```c++
#include <iostream>
#include <vector>
#include <cstdlib>
#include <stdexcept>
#include <unistd.h>


using namespace std;


template<class T>
class Stack{
public:
    void push(const T&t);
    void pop(void); 
    bool empty();
	const T& top();
	const T& bottom();

	typedef T type_int;
private:
    vector<T> elems;
    
};

template<class T>
void Stack<T>::push(const T&t)
{
	cout<<"push "<<t<<endl;
	elems.push_back(t);
}

template<class T>
void Stack<T>::pop()
{
	if(!empty())
	{
		elems.pop_back();
	}
	else
	{
		throw out_of_range("Stack<T>::pop(): empty stack");
	}
}


template<class T>
bool Stack<T>::empty()
{
	return elems.empty();	
}


template<class T>
const T& Stack<T>::top()
{
	if(!empty())
		return elems.back();
	else
		throw out_of_range("Stack<T>::top():empty stack");	
}


template<class T>
const T&Stack<T>::bottom()
{
	if(!empty())
	{
		return elems.front();
	}
	else{
		throw out_of_range("Stack<T>::bottom():empty stack");
	}


}


template<typename T>
class Stack_Derived:public Stack<T>
{
public:
	typename Stack<T>::type_int a;//使用在父类中声明的类型
private:
	Stack<T> s;

};


int main()
{
	Stack<int> s;
	try{
	s.push(1);
	s.push(2);
	s.push(3);

	cout<< s.top()<<endl;
	cout<<s.bottom()<<endl;
	s.pop();
	s.pop();
	s.pop();
	//	cout<<s.bottom()<<endl;
	//s.pop(); //for exception
	Stack_Derived<int> sd;
	sd.a = 5;
	cout<<sd.a<<endl;
	sd.push(1);
	sd.pop();
	sd.pop();
	}catch(const exception &ex){
		cerr<<"Exception:"<<ex.what()<<endl;
		return -1;
	}
	pause();
}
```

