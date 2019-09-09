---
layout:     post
title:      C++语法之override
subtitle:   c++语法笔记
date:       2019-08-31
author:     VK
catalog: true
tags:
    - c++
---

# C++语法之override

如果不写@override注解去直接重写方法，编译器是不会判断你是不是正确重写了父类中的方法的。如重写方法时参数与父类不同，程序是不会提示报错的。这会留下一个潜在的bug。当你写了@override注解时，程序会判断你是否正确的重写了父类的对应方法。而且加上此注解后，程序会自动屏蔽父类的方法。

简而言之，只要子类定义了和父类一样的方法名，不管是直接重载还是override重写，那么父类中所有同名方法都会被子类屏蔽。

```c++
#include<iostream>
using namespace std;

class base
{
public:
	virtual void printout(){cout<<"1231231231"<<endl;};
	virtual void printout(int i){};
  	void test(){cout<<"test"<<endl;}; //没有声明为virtual的函数不能override

};

class derived:public base
{
public:
	void printout(int i){ cout<<"sdfsf"<<endl;};//这里重写后，子类将无法调用父类的同名方法实现	
	void test()override{cout<<"sub test"<<endl;}; //报错，因为父类该方法没有声明为virtual，编译器找不到父类对应的方法去override重写
#if 1
	void test(){cout<<"sub test"<<endl;};//但这样是可以的，因为相当于在子类重载了该方法，如果子类没有重载该方法，那么子类对象将调用父类的方法，只要重载了，子类就调用自己的方法
#else
	void test(int i){cout<<"sub test"<<endl;};//如果子类将该方法重载为参数与父类方法不同，那么子类对象也将只能调用子类中定义的方法，传递子类方法中定义的参数，而无法再调用父类中定义的方法及参数
	//derived obj;obj.test();会报错找不到函数
	
#endif

};


int main()
{
	derived d;
  	d.test(); 
	d.printout(); //报错
	return 0;
}

```

- 如果基类声明被重载了，则应该在派生类中重新定义所有的基类版本。
- 如果在派生类中只重新定义一个版本，其他版本将会被隐藏，派生类对象将无法使用它们。

简而言之，重新定义函数，并不是重载。在派生类中定义函数，将不是使用相同的函数特征标覆盖基类声明，而是隐藏同名的基类方法，不管参数的特征标如何。

举例：

```c++
    class A{
        virtual print(void);
        virtual print(int a);
    }

    class B : public A{
        virtual print(float a);
    }

    int main(){
        B* b = new B();
        b->print(); //这里会报参数过少的错误
    }
```

如同《C++Primer Plus》 中说的，B类重新定义了print(float a)，不管是重载还是重写，A类定义的两个print都被隐藏无法使用了

但是有时我们确实有这样的需求，父类的提供的方法重载不能满足我们的要求，我们要在子类拓展该方法，但是我们又不想全部重写，那这时改怎么办呢？

加一句话：**using A::print;**

```c++
    class A{
    public:
        virtual print(void);
        virtual print(int a);
    }

    class B : public A{
    public:
        using A::print; //<======加这一句
        virtual print(float a);
        virtual print(int a)override;
    }

    int main(){
        B* b = new B();
        b->print(); //这里就可以用了
        b->print(3); //这里将调用子类override后的print而不是调用父类的print(int a)，尽管使用了using A::print，因为子类进行了重写
    }
```

这句话的作用，其实就是把父类print的作用域拓展到子类

所以说，这种做法正确的说法应该是：子类扩展父类的方法 

