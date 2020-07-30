---
layout:     post
title:      设计模式（四） C++ 原型（Prototype）模式
subtitle:   每天一个设计模式
date:       2020-02-05
author:     VK
catalog: true
tags:
    - C++
---

# 设计模式（四） C++ 原型（Prototype）模式

原型(Prototype)模式是用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。

**思想**：克 隆一个已有的类的实例（大家相比都用过甚至写过类的Clone实现，应该很容易理解了）。

**场景**：应 用Clone的场景应该说非常多，理想情况下我当然希望任何类都能Clone， 需要的时候就能Clone一份一模一样的出来。

**实现**：这 里将的实现主要之实现的表现形式，而不是如何用具体的语言来实现。因此，只要为需要Clone能力 的类定义一个Clone方法就行。当然，一般，主流的程序语言框架都已经定义了通用的Clone接 口（当然也可以自己定义），继承并实现该接口和方法就好。

**实例** ：Prototype模式提供了一个通过已存在对象进行新对象创建的接口（Clone）， Clone()实现和具体的语言相关，在C++中通过拷贝构造函数实现。

![设计模式四](D:\share\MD文档整理\设计模式四.jpg)

```c++
#include<iostream>

using namespace std;

class Prototype{
public:
	Prototype(){ cout<< "Prototype"<<endl;}
	virtual ~Prototype(){ cout<<"~Prototype"<<endl;}
	virtual Prototype* clone() const = 0;
};


class ConcretePrototype1:public Prototype{
public:
	ConcretePrototype1(){cout<<"ConcretePrototype1"<<endl;}
	~ConcretePrototype1(){cout<<"~ConcretePrototype1"<<endl;}
	ConcretePrototype1(const ConcretePrototype1&){cout<<"ConcretePrototype1 copy"<<endl;}
	virtual Prototype* clone() const;
};

Prototype* ConcretePrototype1::clone() const{
	return new ConcretePrototype1(*this);
}

class ConcretePrototype2:public Prototype{
public:
	ConcretePrototype2(){ cout<<"ConcretePrototype2"<<endl;}
	~ConcretePrototype2(){cout<<"~ConcretePrototype2"<<endl;}
	ConcretePrototype2(const ConcretePrototype2&){cout<<"ConcretePrototype2 copy"<<endl;}
	virtual Prototype* clone() const;
};

Prototype* ConcretePrototype2::clone() const{
	return new ConcretePrototype2(*this);
}

int main()
{
	Prototype *p1 = new ConcretePrototype1();
	Prototype *p2 = p1->clone();
	cout<< "------------------------" << endl;

	Prototype *p3 = new ConcretePrototype2();
	Prototype *p4 = p3->clone();

	cout<< "------------------------" << endl;
	delete p1;
	delete p2;
	cout<< "------------------------" << endl;
	delete p3;
	delete p4;
}
```

Prototype模式和Builder模式、AbstractFactory模式都是通过一个类（对象实例）来专门负责对象的创建工作（工厂对象），它们之间的区别是：Builder模式重在复杂对象的一步步创建（并不直接返回对象），AbstractFactory模式重在产生多个相互依赖类的对象，而Prototype模式重在从自身复制自己创建新类。