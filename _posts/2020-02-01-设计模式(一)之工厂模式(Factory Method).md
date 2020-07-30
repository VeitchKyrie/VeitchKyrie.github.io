---
layout:     post
title:      设计模式(一)之工厂模式（Factory Method）
subtitle:   每天一个设计模式
date:       2020-02-01
author:     VK
catalog: true
tags:
    - C++
---

# 设计模式(一)之工厂模式（Factory Method）

**思想**：Factory Method的主要思想是使一个类的实例化延迟到其子类。

**场景**：典型的应用场景如：在某个系统开发的较早阶段，有某些类的实例化过程，实例化方式可能还不是很确定，或者实际实例化的对象（可能是需要对象的某个子类中的一 个）不确定，或者比较容易变化。此时，如果直接将实例化过程写在某个函数中，那么一般就是if-else或select-case代 码。如果，候选项的数目较少、类型基本确定，那么这样的if-else还是可以接受的，一旦情形变 得复杂、不确定性增加，更甚至包含这个构造过程的函数所在的类包含几个甚至更多类似的函数时，这样的if-else代 码就会变得比较不那么容易维护了。此时，应用本模式，可以将这种复杂情形隔离开，即将这类不确定的对象的实例化过程延迟到子类。
**实现**：该模式的典型实现方法就是将调用类定义为一个虚类，在调用类定义一个专门用于构造不确定的对象实例的虚函数，再将实际的对象实例化代码留到调用类的子类来实现。如果，被构造的对象比较复杂的话，同时可以将这个对象定义为可以继承、甚至虚类，再在不同的调用类的子类中按需返回被构造类的子类。


```c++
#include <iostream>

using namespace std;

class iHuman{
public:
  iHuman(){}
  virtual ~iHuman(){cout<<"iHuman out"<<endl;}
  
  virtual void smile() = 0;
  virtual void talk() = 0;
};


class WhiteHuman:public iHuman{
public:
	WhiteHuman(){}
	~WhiteHuman(){cout<<"WhiteHuman out"<<endl;}
	
    void smile(){
    	cout<<"White Human smile"<<endl;
    }
    
     void talk(){
    	cout<<"White Human talk"<<endl;
    }
};


class YelloHuman:public iHuman{
public:
	YelloHuman(){}
	~YelloHuman(){}
	
    void smile(){
    	cout<<"Yello Human smile"<<endl;
    }
    
     void talk(){
    	cout<<"Yello Human talk"<<endl;
    }
};




class iHumanFactory{
public:
  iHumanFactory(){}
  virtual ~iHumanFactory(){}
  
  virtual iHuman* createHuman() = 0;
};

class WhiteHumanFactory:public iHumanFactory{
public:
  WhiteHumanFactory(){}
  ~WhiteHumanFactory(){}
  
  iHuman* createHuman(){
    return new WhiteHuman();
  }
};

class YelloHumanFactory:public iHumanFactory{
public:
  YelloHumanFactory(){}
  ~YelloHumanFactory(){}
  
  iHuman* createHuman(){
    	return new YelloHuman();
  }
};


int main()
{
  iHumanFactory* phf = new WhiteHumanFactory();
  iHuman* ph = phf->createHuman();
  ph->talk();
  delete phf;
  delete ph;
  return 0;
}





```

**重构成本：**低。该模式的重构成本实际上还与调用类自己的实例化方式相关。如果调用类是通过Factory方 式（此处“Factory方式”泛指对象的实例化通过Factory Method或Abstract Factory这样的相对独立出来的 方式构造）构造的，那么，重构成本相对就会更低。否则，重构时可能除了增加调用类的子类，还要将所有实例化调用类的地方，修改为以新增的子类代替。可能这 样的子类还不止一个，那就可以考虑迭代应用模式来改善调用类的实例化代码。


**工厂模式的好处：**工厂模式就相当于创建实例对象的new，我们经常要根据类Class生成实例对象，如A a=new A(). 工厂模式也是用来创建实例对象的，可能多做一些工作，但会给你系统带来更大的可扩展性和尽量少的修改量。 类Sample为例,要创建Sample的实例对象: Sample sample=new Sample(); 可是，实际情况是，通常我们都要在创建sample实例时做点初始化的工作,比如赋值 查询数据库等 .首先，我们想到的是，可以使用Sample的构造函数，这样生成实例就写成: Sample sample=new Sample(参数); 但是，如果创建sample实例时所做的初始化工作不是象赋值这样简单的事，可能是很长一段代码，如果也写入构造函数中，那你的代码很难看了 .

初始化工作如果是很长一段代码，说明要做的工作很多，将很多工作装入一个方法中，相当于将很多鸡蛋放在一个篮子里，是很危险的，这也是有背于Java面向对象的原则，面向对象的封装(Encapsulation)和分派(Delegation)告诉我们，尽量将长的代码分派“切割”成每段，将每段再“封装”起来(减少段和段之间偶合联系性)，这样，就会将风险分散，以后如果需要修改，只要更改每段，不会再发生牵一动百的事情。
我们需要将创建实例的工作与使用实例的工作分开, 也就是说，让创建实例所需要的大量初始化工作从Sample的构造函数中分离出去。 你想如果有多个类似的类，我们就需要实例化出来多个类。这样代码管理起来就太复杂了。 这个时候你就可以采用工厂方法来封装这个问题。 不能再用上面简单new Sample(参数)。还有,如果Sample有个继承如MySample, 按照面向接口编程,我们需要将Sample抽象成一个接口.现在Sample是接口,有两个子类MySample 和HisSample

```c++
Sample mysample=new MySample();
Sample hissample=new HisSample();
```


**采用工厂封装：**

```c++
public class Factory{
　　public static Sample creator(int which){
　　      //getClass 产生Sample 一般可使用动态类装载装入类。
　　      if (which==1)
　　　　  	return new SampleA();
　　      else if (which==2)
　　　　    return new SampleB();
　　}
}

```

那么在你的程序中,如果要实例化Sample时.就使用 Sample sampleA=Factory.creator(1); 举个更实际的例子，比如你写了个应用，里面用到了数据库的封装，你的应用可以今后需要在不同的数据库环境下运行，可能是oracle,db2,sql server等，那么连接数据库的代码是不一样的，你用传统的方法，就不得不进行代码修改来适应不同的环境，非常麻烦，但是如果你采用工厂类的话，将各种可能的数据库连接全部实现在工厂类里面，通过你配置文件的修改来达到连接的是不同的数据库，那么你今后做迁移的时候代码就不用进行修改了。 我通常都是用xml的配置文件配置许多类型的数据库连接，非常的方便。PS：工厂模式在这方面的使用较多。

