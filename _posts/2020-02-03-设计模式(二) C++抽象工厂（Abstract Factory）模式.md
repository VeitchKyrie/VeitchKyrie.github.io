---
layout:     post
title:      程序设计模式(二) C++抽象工厂（Abstract Factory）模式
subtitle:   每天一个设计模式
date:       2020-02-03
author:     VK
catalog: true
tags:
    - C++
---



# 程序设计模式(二) C++抽象工厂（Abstract Factory）模式

抽象工厂跟工厂方法模式可能区分有点模糊：工厂方法模式针对的是一个产品等级结构；而抽象工厂模式针对的是多个产品等级结构。抽象工厂模式主要用来实现生产一系列的产品。

**思想**：不直接通过对象的具体实现类，而是通过使用专门的类来负责一组相关联的对象的创建。

**场景**：最典型的应用场景是：您只想暴露对象的接口而不想暴露具体的实现类，但是又想提供实例化对象的接口给用户；或者，您希望所有的对象能够 集中在一个或一组类（通常称作工厂类）来创建，从而可以更方便的对对象的实例化过程进行动态配置（此时只需要修改工厂类的代码或配置）。

**实现**：该模式的实现是比较清晰简单的，如上图，就是定义创建和返回各种类对象实例的工厂类。在最复杂而灵活的情形，无论工厂类本身还是被创建 的对象类都可能需要有一个继承体系。简单情形其实可以只是一个工厂类和需要被创建的对象类。不一定非要像上图中结构那么完备（累赘）。实例:抽象工厂（Abstract Factory）模式是为了提供一系列相关或相互依赖对象的接口。对象创建型模式的一种。

**实例**：抽象工厂（Abstract Factory）模式是为了提供一系列相关或相互依赖对象的接口。对象创建型模式的一种。

- 客户Client


- 抽象工厂接口AbstractFactory
- 抽象工厂的实现类ConcreteFactory
- 抽象产品接口AbstractProduct
- 产品实现类ConcreteProduct



我们要生产两个系列四种产品，分别是ConcreteProductA1/ConcreteProductA2/ConcreteProductB1/ConcreteProductB2。各个系列产品的启动和退出方式相同，但是运行方式不同。这里分别用一个具体工厂ConcreteFactory1和ConcreteFactory2的对象来生产多种产品。

![C++抽象工厂（Abstract Factory）模式](D:\share\MD文档整理\C++抽象工厂（Abstract Factory）模式.jpg)

```c++
#include <iostream>

using namespace std;

class AbstractProductA{
public:	
  	AbstractProductA(){}
  	virtual ~AbstractProductA(){}
    void start(){
       cout<<"<---------------------A类产品是这样启动的----------------------->"<<endl;
    }
  	virtual void execute() = 0;
    void quit(){
       cout<<"<---------------------A类产品是这样退出的----------------------->"<<endl;
    }
};

class AbstractProductB{
public:	
  	AbstractProductB(){}
  	virtual ~AbstractProductB(){}
	 void start(){
       cout<<"<---------------------B类产品是这样启动的----------------------->"<<endl;
    }
  	virtual void execute() = 0;
    void quit(){
       cout<<"<---------------------B类产品是这样退出的----------------------->"<<endl;
    }
};


class ConcreteProductA1:public AbstractProductA{
public:
  	ConcreteProductA1(){}
    ~ConcreteProductA1(){}
    void execute(){
    	cout<<"<---------------------产品A1是这样运行的----------------------->"<<endl;
    }
  
};


class ConcreteProductA2:public AbstractProductA{
public:
  	ConcreteProductA2(){}
    ~ConcreteProductA2(){}
    void execute(){
    	cout<<"<---------------------产品A2是这样运行的----------------------->"<<endl;
    }
  
};

class ConcreteProductB1:public AbstractProductB{
public:
  	ConcreteProductB1(){}
    ~ConcreteProductB1(){}
    void execute(){
    	cout<<"<---------------------产品B1是这样运行的----------------------->"<<endl;
    }
  
};


class ConcreteProductB2:public AbstractProductB{
public:
  	ConcreteProductB2(){}
    ~ConcreteProductB2(){}
    void execute(){
    	cout<<"<---------------------产品B2是这样运行的----------------------->"<<endl;
    }
};



class AbstractFactory{
public:
  	AbstractFactory(){}
    virtual ~AbstractFactory(){}
    virtual AbstractProductA* CreateProductA() = 0;
    virtual AbstractProductB* CreateProductB() = 0;
};

class ConcreteFactory1:public AbstractFactory{
public:
  	ConcreteFactory1(){}
  	virtual ~ConcreteFactory1(){}
    AbstractProductA* CreateProductA(){
        return new ConcreteProductA1();
    } 
    AbstractProductB* CreateProductB(){
        return new ConcreteProductB1();
    }
};

class ConcreteFactory2:public AbstractFactory{
public:
  	ConcreteFactory2(){}
  	virtual ~ConcreteFactory2(){}
    AbstractProductA* CreateProductA(){
        return new ConcreteProductA2();
    } 
    AbstractProductB* CreateProductB(){
        return new ConcreteProductB2();
    }
};



int main()
{
  AbstractFactory *factory1 = new ConcreteFactory1();
  AbstractProductA *pA1 = factory1->CreateProductA();
  pA1->start();
  pA1->execute();
  pA1->quit();
  
  /*********************生产产品B1****************************/
  AbstractProductB *pB1 = factory1->CreateProductB();
  pB1->start();        //B是这么启动的
  pB1->execute();        //B1是这样运行的
  pB1->quit();        //B是这样退出的
  
  return 0;
}

```

**重构成本**：中。如果一开始所有的对象都是直接创建，例如通过new实例化的， 而之后想重构为Abstract Factory模式，那么，很自然的我们需要替换所有直接的new实 例化代码为对工厂类对象创建方法的调用。考虑到像Resharper这样的重构工具的支持，找出对 某个方法或构造函数的调用位置这样的操作相对还是比较容易，重构成本也不是非常高。同时，重构成本还和被创建对象的构造函数的重载数量相关。您需要根据实 际情况考虑，是否工厂类要映射被创建对象的所有重载版本的构造函数。
