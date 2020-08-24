---
layout:     post
title:      程序设计之C++ 装饰(Decorator)模式
subtitle:   装饰器模式就是基于对象组合的方式，可以很灵活的给对象添加所需要的功能
date:       2020-03-20
author:     VK
catalog: true
tags:
    - C++
---

# 程序设计模式之 C++ 装饰(Decorator)模式

装饰模式提供了更加灵活的向对象添加职责的方式。可以用添加和分离的方法，用装饰在运行时刻增加和删除职责。装饰模式提供了一种“即用即付”的方法来添加职责。它并不试图在一个复杂的可定制的类中支持所有可预见的特征，相反，你可以定义一个简单的类，并且用装饰类给它逐渐地添加功能。可以从简单的部件组合出复杂的功能。

装饰器模式就是基于对象组合的方式，可以很灵活的给对象添加所需要的功能。它把需要装饰的功能放在单独的类中，并让这个类包装它所要装饰的对象，动态的给一个对象添加一些额外的职责。就增加功能来说，Decorator模式相比生成子类模式更为灵活。

**思想：**为一个对象已有的子类添加一些额外的职责，具有以下优点：

​       1、比静态继承更为灵活。继承机制会产生许多新类，增加了系统的复杂度。而装饰可以使你对一些职责进行混合和匹配。

​       2、避免在层次结构高层的类有太多的特征。扩展一个复杂类的时候，很可能会暴露出与添加职责无关的细节。你可以定义一个简单的类，并且用装饰逐渐的添加功能。

​       3、会产生许多小对象。

​       4、Decorator与Component不一样，Decorator是一个透明的包装。如果我们从对象标识的观点出发，一个被装饰了的组件与这个组件是有差别的，因此，使用装饰时不应该依赖对象标识。

**场景：**该模式的使用场景，主要是有的时候我们不愿意定义逻辑上新的子类，因为没有新的逻辑含义上的子类概念，而只是想为一个已存在的子类附加一些职责。

​       1、在不影响其它对象的情况下，以动态、透明的方式给单个对象添加职责。

​       2、处理那些可以撤销的职责。

​       3、希望为某个对象而不是一整个类添加一些功能时。

​       4、当不能采用生成子类的方法进行扩充时。一种情况是，可能有大量独立的扩展，为支持每一种组合将产生大量的子类，使得子类数呈爆炸性增长。另一种情况可能是因为类定义被隐藏，或类定义不能用于生成子类。

**实现：**该模式的实现主要就是定义一个物理上的新的子类，但是，它只是包含要附加职责的类，传递外部对相同接口的调用，在这个传递调用的通道上附加额外的功能。使用时注意：

​       1、接口的一致性。装饰对象的接口必须与它所装饰的Component的接口是一致的。

​       2、省略抽象的Docorator类。当你仅需要添加一个职责的时，没有必要定义抽象Decorator类。你常常需要处理显存的类层次结构而不是设计一个新系统，这时你可以把Decorator向Component转发请求的职责合并到ConcreteDecorator中。

​       3、保持Component类的简单性。为了保证接口的一致性，组件和装饰必须有一个共同的Component父类。因此保持这个类的简单些是很重要的。

```c++
#include <iostream>  
using namespace std;  
  
class Component  
{  
public:  
    virtual void Operation() = 0;  
};  
  
class concreteComponent :public Component  
{  
public:  
    void Operation()  
    {  
        cout << "this is a concreteComponent, not a decorator." << endl;  
    }  
};  
  
class Decorator :public Component  
{  
public:  
    Decorator(Component *p) : p_Component(p) {}  
    void Operation()  
    {  
        if (p_Component != NULL)  
        {  
            p_Component->Operation();  
        }  
    }  
  
private:  
    Component *p_Component;  
};  
  
class DecoratorA :public Decorator  
{  
public:  
    DecoratorA(Component *p) : Decorator(p) {}  
    void Operation()  
    {  
        add_status();  
        Decorator::Operation();  
    }  
  
    void add_status()  
    {  
        cout << "I am DecoratorA. " << endl;  
    }  
};  
  
class DecoratorB :public Decorator  
{  
public:  
    DecoratorB(Component *p) : Decorator(p) {}  
    void Operation()  
    {  
        add_bahavior();  
        Decorator::Operation();  
    }  
  
    void add_bahavior()  
    {  
        cout << "I am DecoratorB. " << endl;  
    }  
};  
  
int main(int argc, char*argv[])  
{  
    Component* object = new concreteComponent();  
    Decorator *a = new DecoratorA(object);  
    a->Operation();  
    cout << "-----------------------------------------------------" << endl;  
  
    Decorator *b = new DecoratorB(object);  
    b->Operation();  
    cout << "------------------------------------------------------" << endl;  
  
    Decorator *ab = new DecoratorB(a);  
    ab->Operation();  
}  
```

我们来看一个实际的具体的例子：

​          比如有一个手机，允许你为手机添加特性，比如增加挂件、屏幕贴膜等。一种灵活的设计方式是，将手机嵌入到另一对象中，由这个对象完成特性的添加，我们称这个嵌入的对象为装饰。这个装饰与它所装饰的组件接口一致，因此它对使用该组件的客户透明。![Decorator模式](D:\share\MD文档整理\Decorator模式.jpg)

```c++
#include <iostream>
#include <string>

using namespace std;



class Phone{
public:
	virtual void show() = 0;
};


class DecoratorPhone:public Phone{
public:
	DecoratorPhone(Phone* p):m_phone(p){}
	void show(){
		m_phone->show();
	}

private:
	Phone *m_phone;
};




class DecoratorPhoneA:public DecoratorPhone{
public:
	DecoratorPhoneA(Phone* p):DecoratorPhone(p){}
	void show(){
		DecoratorPhone::show();
		add_plus();
	}

private:
	void add_plus(){cout<<"plus"<<endl;}
};


class DecoratorPhoneB:public DecoratorPhone{
public:
	DecoratorPhoneB(Phone* p):DecoratorPhone(p){}
	void show(){
		DecoratorPhone::show();
		add_super();
	}

private:
	void add_super(){cout<<"super"<<endl;}
};

class Nokia:public Phone{
public:
	Nokia(string name){
		this->name = name;
	}
	void show(){
		cout<<"i am "<<name<<endl;
	}

private:
	string name;

};


int main()
{
	Phone *pa =new  DecoratorPhoneA(new Nokia("Nokia"));
	Phone *pb =new  DecoratorPhoneB(pa);
	pa->show();
	pb->show();
}

```

