---
layout:     post
title:      设计模式(八) C++ 组合(Composite)模式
subtitle:   每天一个设计模式
date:       2020-02-11
author:     VK
catalog: true
tags:
    - C++
---

# 设计模式(八) C++ 组合(Composite)模式

我们PC用到的文件系统，其实就是我们数据结构里的树形结构，我们处理树中的每个节点时，其实不用考虑他是叶子节点还是根节点，因为他们的成员函数都是一样的，这个就是组合模式的精髓。他模糊了简单元素和复杂元素的概念，客户程序可以向处理简单元素一样来处理复杂元素,从而使得客户程序与复杂元素的内部结构解耦。

将对象组合成树形结构以表示“部分-整体”的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。

注明：树形结构里的叶子节点也有左右孩子，只不过他的孩子都是空。

组合模式的实现根据所实现接口的区别分为两种形式，分别称为安全模式和透明模式。组合模式可以不提供父对象的管理方法，但组合模式必须在合适的地方提供子对象的管理方法(诸如：add、remove、getChild等)。

1.透明方式

作为第一种选择，在Component里面声明所有的用来管理子类对象的方法，包括add()、remove()，以及getChild()方法。这样做的好处是所有的构件类都有相同的接口。在客户端看来，树叶类对象与合成类对象的区别起码在接口层次上消失了，客户端可以同等同的对待所有的对象。这就是透明形式的组合模式。这个选择的缺点是不够安全，因为树叶类对象和合成类对象在本质上是有区别的。树叶类对象不可能有下一个层次的对象，因此add()、remove()以及getChild()方法没有意义，是在编译时期不会出错，而只会在运行时期才会出错或者说识别出来。


2.安全方式

第二种选择是在Composite类里面声明所有的用来管理子类对象的方法。这样的做法是安全的做法，因为树叶类型的对象根本就没有管理子类对象的方法，因此，如果客户端对树叶类对象使用这些方法时，程序会在编译时期出错。这个选择的缺点是不够透明，因为树叶类和合成类将具有不同的接口。这两个形式各有优缺点，需要根据软件的具体情况做出取舍决定。

思想：将对象组合成树形结构以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性。

场景：该模式的应用场景极其类似，比如像图形系统，如电路设计、UML建模系统，或者像web的显示元素等，都是那种需要整体和部分具有使用接口上的一定的一致性的需求的结构，实际上，我觉得这样的系统如果不使用Composite模式将会是惨不忍睹的。

以下情况下适用组合模式：

1．你想表示对象的部分-整体层次结构。

2．你希望用户忽略组合对象与单个对象的不同，用户将统一地使用组合结构中的所有对象。

实现：该模式的实现主要就是要表示整体或部分的所有类都继承自同一的基类或接口，从而拥有使用接口上一定的一致性。


1．组合模式采用树形结构来实现普遍存在的对象容器，从而将“一对多”的关系转化“一对一”的关系，使得客户代码可以一致地处理对象和对象容器，无需关心处理的是单个的对象，还是组合的对象容器。

2．将“客户代码与复杂的对象容器结构”解耦是组合模式的核心思想，解耦之后，客户代码将与纯粹的抽象接口——而非对象容器的复内部实现结构——发生依赖关系，从而更能“应对变化”。

3．组合模式中，是将“Add和Remove等和对象容器相关的方法”定义在“表示抽象对象的Component类”中，还是将其定义在“表示对象容器的Composite类”中，是一个关乎“透明性”和“安全性”的两难问题，需要仔细权衡。这里有可能违背面向对象的“单一职责原则”，但是对于这种特殊结构，这又是必须付出的代价。

4．组合模式在具体实现中，可以让父对象中的子对象反向追溯；如果父对象有频繁的遍历需求，可使用缓存技巧来改善效率。

5． 客户端尽量不要直接调用树叶类的方法，而是借助其父类(Component)的多态性完成调用，这样可以增加代码的复用性。


**实例**：这里给出安全方式的组合模式的类图结构和样例实现，透明方式就是在叶子节点的add()/remove()/GetChild()均有实现，不过是无意义的实现。大部分应用都是基于透明模式的，因为这样代码可以重用。

1.安全方式的组合模式

![组合模式](D:\share\MD文档整理\组合模式.jpg)

这种形式涉及到三个角色：

抽象构件(Component)角色：这是一个抽象角色，它给参加组合的对象定义出公共的接口及其默认行为，可以用来管理所有的子对象。在安全式的合成模式里，构件角色并不是定义出管理子对象的方法，这一定义由树枝构件对象给出。

树叶构件(Leaf)角色：树叶对象是没有下级子对象的对象，定义出参加组合的原始对象的行为。

树枝构件(Composite)角色：代表参加组合的有下级子对象的对象。树枝对象给出所有的管理子对象的方法，如add()、remove()、getChild()等。

```c++
//Menu.h
#include <string>
 
class Menu  
{
public:
    virtual ~Menu();
 
    virtual void Add(Menu*);
    virtual void Remove(Menu*);
    virtual Menu* GetChild(int);
    virtual void Display() = 0;
protected:
    Menu();
    Menu(std::string);
    std::string m_strName;
};
 
//Menu.cpp
#include "stdafx.h"
#include "Menu.h"
 
Menu::Menu()
{
 
}
 
Menu::Menu(std::string strName) : m_strName(strName)
{
 
}
 
Menu::~Menu()
{
 
}
 
void Menu::Add(Menu* pMenu)
{}
 
void Menu::Remove(Menu* pMenu)
{}
 
Menu* Menu::GetChild(int index)
{
    return NULL;
}
 
//SubMenu.h
#include "Menu.h"
 
class SubMenu : public Menu  
{
public:
    SubMenu();
    SubMenu(std::string);
    virtual ~SubMenu();
 
    void Display();
};
 
//SubMenu.cpp
#include "stdafx.h"
#include "SubMenu.h"
#include <iostream>
 
using namespace std;
 
SubMenu::SubMenu()
{
 
}
 
SubMenu::SubMenu(string strName) : Menu(strName)
{
 
}
 
SubMenu::~SubMenu()
{
 
}
 
void SubMenu::Display()
{
    cout << m_strName << endl;
}
 
//CompositMenu.h
#include "Menu.h"
#include <vector>
 
class CompositMenu : public Menu
{
public:
    CompositMenu();
    CompositMenu(std::string);
    virtual ~CompositMenu();
 
    void Add(Menu*);
    void Remove(Menu*);
    Menu* GetChild(int);
    void Display();
private:
    std::vector<Menu*> m_vMenu;
};
 
//CompositMenu.cpp
#include "stdafx.h"
#include "CompositMenu.h"
#include <iostream>
 
using namespace std;
 
CompositMenu::CompositMenu()
{
    
}
 
CompositMenu::CompositMenu(string strName) : Menu(strName)
{
 
}
 
CompositMenu::~CompositMenu()
{
 
}
 
void CompositMenu::Add(Menu* pMenu)
{
    m_vMenu.push_back(pMenu);
}
 
void CompositMenu::Remove(Menu* pMenu)
{
    m_vMenu.erase(&pMenu);
}
 
Menu* CompositMenu::GetChild(int index)
{
    return m_vMenu[index];
}
 
void CompositMenu::Display()
{
    cout << "+" << m_strName << endl;
    vector<Menu*>::iterator it = m_vMenu.begin();
    for (; it != m_vMenu.end(); ++it)
    {
        cout << "|-";
        (*it)->Display();
    }
}
 
#include "stdafx.h"
#include "Menu.h"
#include "SubMenu.h"
#include "CompositMenu.h"
 
int main(int argc, char* argv[])
{
    Menu* pMenu = new CompositMenu("国内新闻");
    pMenu->Add(new SubMenu("时事新闻"));
    pMenu->Add(new SubMenu("社会新闻"));
    pMenu->Display();
    pMenu = new CompositMenu("国际新闻");
    pMenu->Add(new SubMenu("国际要闻"));
    pMenu->Add(new SubMenu("环球视野"));
    pMenu->Display();
 
    return 0;
}
```

#### 实现要点：

1．组合模式采用树形结构来实现普遍存在的对象容器，从而将“一对多”的关系转化“一对一”的关系，使得客户代码可以一致地处理对象和对象容器，无需关心处理的是单个的对象，还是组合的对象容器。

2．将“客户代码与复杂的对象容器结构”解耦是组合模式的核心思想，解耦之后，客户代码将与纯粹的抽象接口——而非对象容器的复内部实现结构——发生依赖关系，从而更能“应对变化”。

3．组合模式中，是将“Add和Remove等和对象容器相关的方法”定义在“表示抽象对象的Component类”中，还是将其定义在“表示对象容器的Composite类”中，是一个关乎“透明性”和“安全性”的两难问题，需要仔细权衡。这里有可能违背面向对象的“单一职责原则”，但是对于这种特殊结构，这又是必须付出的代价。

4．组合模式在具体实现中，可以让父对象中的子对象反向追溯；如果父对象有频繁的遍历需求，可使用缓存技巧来改善效率。

5． 客户端尽量不要直接调用树叶类的方法，而是借助其父类（Component）的多态性完成调用，这样可以增加代码的复用性。

#### 使用场景：

以下情况下适用组合模式：

1．你想表示对象的部分-整体层次结构。

2．你希望用户忽略组合对象与单个对象的不同，用户将统一地使用组合结构中的所有对象。



