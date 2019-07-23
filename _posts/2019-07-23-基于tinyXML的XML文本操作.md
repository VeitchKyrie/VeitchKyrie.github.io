---
layout:     post
title:      基于tinyXML2的XML文本操作
subtitle:   采用直接从xml读取文本的方法，不但可以避免错误显示的发生，还大大提高工作效率，实现该方法后,只需导入客户更新的xml即可完成文本的更新显示，不需要做任何代码改动
date:       2019-07-23
author:     VK
catalog: true
tags:
    - C++
    - tinyXML2
    - Project Summary
---
# 基于tinyXML2 的 XML文本操作

## 1. 背景	

​	**在项目中，今天遇到客户给的`xml`文本和产品界面显示不一致的问题，这些问题往往都是因为开发人员对文本进行更新时，采用手动拷贝填写等方法，一时疏忽所造成的，并且每当客户更新`xml`文档，开发人员就要重新手动进行更新，这种方法是十分低效的，基于此，可采用直接从`xml`读取文本的方法，这样不但可以避免错误显示的发生，还能大大提高工作效率，每次客户更新`xml`，只需要导入`xml`即可，不需要做任何代码改动。**



## 2. 简介

**`tinyXML2` 是一款非常便捷高效的第三方操作xml文件的C++ XML文件解析库，可以非常方便的移植应用到现有的项目之中。非常适合存储简单数据，配置文件，对象序列化等数据量不是很大的操作。`tinyXML2`可以从[官网](https://sourceforge.net/projects/tinyxml/)去下载，也可以去[github](https://github.com/leethomason/tinyxml2)下载源码编译。**



## 3. `TinyXml-2` 的结构简介

**`TiXmlBase` 是所有类的基类，`TiXmlNode`、`TiXmlAttribute`两个类都继承来自 `TiXmlBase` 类，其中` TiXmlNode` 类指的是所有被<…>…<…/>包括的内容，而`xml`中的节点又具体分为以下几方面内容，分别是声明、注释、节点以及节点间的文本，因此在 `TiXmlNode `的基础上又衍生出这几个类 `TiXmlComment`、`TiXmlDeclaration`、`TiXmlDocument`、`TiXmlElement`、`TiXmlText`、`TiXmlUnknown`，分别用来指明具体是` xml `中的哪一部分。`TiXmlAttribute` 类不同于` TiXmlNode`，它指的是在尖括号里面的内容，像<… *=…>，其中*就是一个属性。**

### 3.1 `TinyXml-2` 结构图

![TinyXml2结构图](https://veitchkyrie.github.io/img/TiXmlTree.png)

### 3.2 `TinyXml-2` 结构说明

**这块我具体用一个`xml`文档说明一下，内容如下：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<LangDef langCode="0407" xsi:noNamespaceSchemaLocation="HMILinguist_Texts.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <LangID ID="LANG.PO_CONFIRM_ONLINE_SERVICE_TETHERING_ON.OK.BTN_*" Text="OK"/>
    <LangID ID="LANG.SWDL_MAIN.BTN_SW_Update.text" Text="Software update"/>
    <LangID ID="AM_Monitor_Fieldstrength_Value_Entry" Text="%1 dBµV"/>
    <LangID ID="FM_Monitor_Fieldstrength_Value_Entry" Text="%1 dBµV"/>
    <LangID ID="LANG.APPCONNECT_DOMAIN_ERROR.Info.text" Text="%1\nis currently not available."/>
```

**`TiXmlDeclaration `指的是<?xml version="1.0" encoding="UTF-8"?>**

**`TiXmlComment` 指的是注释<!-- TiXmlComment -->**

**`TiXmlDocument` 指的是整个`xml`文档，**

**`TiXmlElement `指的是<LangDef>、<LangID>等等这些节点，**

**`TiXmlText` 指的是‘*LANG.SWDL_MAIN.BTN_SW_Update.text’*、‘*Software update*’这些夹在`TiXmlElement`之间的文本内容**

**`TiXmlAttribute` 指的是*<?xml version=”1.0″ encoding=”UTF-8″?>*节点中version、encoding等**

**除此之外就是 `TiXmlUnknown`。**



## 4. 网上的简单教学例程

**假设，在一个视觉项目中需要使用多个相机，那么每个相机将会有`ID`、 `IP`地址、曝光时间、触发模式等属性。所以我们就想有类似下面的配置文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CameraI>
    <DevID>5</DevID>
    <IpAddress>178.26.85.83</IpAddress>
    <SubnetMask>123.45.67.89</SubnetMask>
    <ExposureAuto>false</ExposureAuto>
    <ExposureTime>200</ExposureTime>
    <TriggerMode>false</TriggerMode>
</CameraI>

<CameraII>
    <DevID>5</DevID>
    <IpAddress>178.26.85.83</IpAddress>
    <SubnetMask>123.45.67.89</SubnetMask>
    <ExposureAuto>false</ExposureAuto>
    <ExposureTime>200</ExposureTime>
    <TriggerMode>false</TriggerMode>
</CameraII>

<CameraIII>
    <DevID>5</DevID>
    <IpAddress>178.26.85.83</IpAddress>
    <SubnetMask>123.45.67.89</SubnetMask>
    <ExposureAuto>false</ExposureAuto>
    <ExposureTime>200</ExposureTime>
    <TriggerMode>false</TriggerMode>
</CameraIII>

```

**针对以上情况，我们得写一个怎么去读，怎么去写，怎么去修改属性的代码。下面将附上测试代码，希望读者通过调试来掌握`tinyXML2`的调用方法。**

```c++

/**
* 开发环境: VS2015 + TinyXML2 6.0.2 + Windows 10
* 硬件环境: CPU i7-8750H; RAM 8G; Gpu: GTX1050Ti
*
* 建议通过IDE调试来学习
*/
#include <iostream>
#include "tinyxml2_6.2.0/tinyxml2.h"

/** @brief 读取每个相机的属性

@param p 相机节点指针
*/
static void ReadOneCameraAttribute(const tinyxml2::XMLElement* p)
{
    int devIdContent = p->FirstChildElement("DevID")->IntText();
    const char* ipAddrContent = p->FirstChildElement("IpAddress")->GetText();
    const char* subnetMaskContent = p->FirstChildElement("SubnetMask")->GetText();
    const char* exposureAutoContent = p->FirstChildElement("ExposureAuto")->GetText();
    int64_t exposureTimeContent = p->FirstChildElement("ExposureTime")->Int64Text();
    bool triggerModeContent = p->FirstChildElement("TriggerMode")->BoolText();

    std::cout << "devIdContent(int):\t" << devIdContent << std::endl;
    std::cout << "ipAddrContent:\t" << ipAddrContent << std::endl;
    std::cout << "subnetMaskContent:\t" << subnetMaskContent << std::endl;
    std::cout << "exposureAutoContent:\t" << exposureAutoContent << std::endl;
    std::cout << "exposureTimeContent(int64_t):\t" << exposureTimeContent << std::endl;
    std::cout << "triggerModeContent(bool):\t" << ((triggerModeContent == true) ? "true" : "false") << std::endl;
}

/** @brief 读取解析某路径的xml文件

@param path xml文件路径
*/
static void ReadParam(const std::string& path)
{
    // 导入文件错误, 退出
    tinyxml2::XMLDocument doc;
    tinyxml2::XMLError error = doc.LoadFile(path.c_str());
    if (error != tinyxml2::XMLError::XML_SUCCESS) return;

    // 三个相机指针
    tinyxml2::XMLElement* pFirst = doc.FirstChildElement("CameraI");
    tinyxml2::XMLElement* pSecond = doc.FirstChildElement("CameraII");
    tinyxml2::XMLElement* pThree = doc.FirstChildElement("CameraIII");

    // 分别读取每个相机的各个属性
    ReadOneCameraAttribute(pFirst); std::cout << "------------------\n";
    ReadOneCameraAttribute(pSecond); std::cout << "------------------\n";
    ReadOneCameraAttribute(pThree); std::cout << "------------------\n";
}


/** @brief 修改相机属性

@param p 相机节点指针
*/
static void ModifyOneCamera(tinyxml2::XMLElement* p)
{
    int devId = 4;
    const char* ipAddr = "139.66.38.13";
    const char* subnetMask = "255.0.0.0";
    bool exposureAuto = false;
    int64_t exposureTime = 80;
    bool triggerMode = false;

    p->FirstChildElement("DevID")->SetText(devId);
    p->FirstChildElement("IpAddress")->SetText(ipAddr);
    p->FirstChildElement("SubnetMask")->SetText(subnetMask);
    p->FirstChildElement("ExposureAuto")->SetText(exposureAuto);
    p->FirstChildElement("ExposureTime")->SetText(exposureTime);
    p->FirstChildElement("TriggerMode")->SetText(triggerMode);
}

/** @brief 修改某xml文件的参数

@param path xml文件路径
*/
static void ModifyParam(const std::string& path)
{
    // 导入文件错误, 退出
    tinyxml2::XMLDocument doc;
    tinyxml2::XMLError error = doc.LoadFile(path.c_str());
    if (error != tinyxml2::XMLError::XML_SUCCESS) return;

    // 三个相机指针
    tinyxml2::XMLElement* pFirst = doc.FirstChildElement("CameraI");
    tinyxml2::XMLElement* pSecond = doc.FirstChildElement("CameraII");
    tinyxml2::XMLElement* pThree = doc.FirstChildElement("CameraIII");

    // 修改
    ModifyOneCamera(pFirst);
    ModifyOneCamera(pSecond);
    ModifyOneCamera(pThree);

    doc.SaveFile(path.c_str());
}



/** @brief 写相机属性. 产生完了之后将所有节点挂在p下面

@param p 某个相机节点
@param doc 所有的元素属性以及值均在doc的下面
*/
static void WriteOneCameraAttibute(tinyxml2::XMLElement* p, tinyxml2::XMLDocument& doc)
{
    const char* ipAddr = "178.26.85.83";
    const char* subnetMask = "123.45.67.89";
    int64_t exposureTime = 200;

    tinyxml2::XMLElement* pDevID = doc.NewElement("DevID");
    tinyxml2::XMLText* pDevIdText = doc.NewText("5");
    pDevID->InsertEndChild(pDevIdText);
    p->InsertEndChild(pDevID);

    tinyxml2::XMLElement* pIpAddr = doc.NewElement("IpAddress");
    tinyxml2::XMLText* pIpAddrText = doc.NewText(ipAddr);
    pIpAddr->InsertEndChild(pIpAddrText);
    p->InsertEndChild(pIpAddr);

    tinyxml2::XMLElement* pSubnetMask = doc.NewElement("SubnetMask");
    tinyxml2::XMLText* pSubnetMaskText = doc.NewText(subnetMask);
    pSubnetMask->InsertEndChild(pSubnetMaskText);
    p->InsertEndChild(pSubnetMask);

    tinyxml2::XMLElement* pExposureAuto = doc.NewElement("ExposureAuto");
    tinyxml2::XMLText* pExposureAutoText = doc.NewText("false");
    pExposureAuto->InsertEndChild(pExposureAutoText);
    p->InsertEndChild(pExposureAuto);

    tinyxml2::XMLElement* pExposureTime = doc.NewElement("ExposureTime");
    tinyxml2::XMLText* pExposureTimeText = doc.NewText("200");
    pExposureTime->InsertEndChild(pExposureTimeText);
    p->InsertEndChild(pExposureTime);

    tinyxml2::XMLElement* pTriggerMode = doc.NewElement("TriggerMode");
    tinyxml2::XMLText* pTriggerModeText = doc.NewText("false");
    pTriggerMode->InsertEndChild(pTriggerModeText);
    p->InsertEndChild(pTriggerMode);
}


/** @brief 将数据写到某个xml文件中

@param path xml文件路径
*/
static void WriteParam(const std::string& path)
{
    tinyxml2::XMLDocument doc;
    const char* declaration = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>";
    doc.Parse(declaration);

    tinyxml2::XMLElement* pFirst = doc.NewElement("CameraI");
    WriteOneCameraAttibute(pFirst, doc);
    doc.InsertEndChild(pFirst);

    tinyxml2::XMLElement* pSecond = doc.NewElement("CameraII");
    WriteOneCameraAttibute(pSecond, doc);
    doc.InsertEndChild(pSecond);

    tinyxml2::XMLElement* pThree = doc.NewElement("CameraIII");
    WriteOneCameraAttibute(pThree, doc);
    doc.InsertEndChild(pThree);

    doc.SaveFile(path.c_str());
}

void test_CameraXML()
{
    std::string path = "../Resources/camera_info.xml";
    std::cout << "---------------生成一个xml文件------------------" << std::endl;
    WriteParam(path);

    std::cout << "--------------写文件结束，读取生成的xml文件------------------" << std::endl;
    ReadParam(path);

    std::cout << "--------------读文件结束，修改文件开始------------------" << std::endl;
    ModifyParam(path);
}
```



## 5. 个人实际项目应用实例

### 5.1 `xml`文本实例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<LangDef langCode="0407" xsi:noNamespaceSchemaLocation="HMILinguist_Texts.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <LangID ID="LANG.PO_CONFIRM_ONLINE_SERVICE_TETHERING_ON.OK.BTN_*" Text="OK"/>
    <LangID ID="LANG.SWDL_MAIN.BTN_SW_Update.text" Text="Software update"/>
    <LangID ID="AM_Monitor_Fieldstrength_Value_Entry" Text="%1 dBµV"/>
    <LangID ID="FM_Monitor_Fieldstrength_Value_Entry" Text="%1 dBµV"/>
    <LangID ID="LANG.APPCONNECT_DOMAIN_ERROR.Info.text" Text="%1\nis currently not available."/>
    <LangID ID="LANG.APPCONNECT_INFO_VIEW.AppleCarPlayHint.text" Text="Apple CarPlay allows the secure use of an iPhone device in this car. The connection can be established via USB or wireless. The settings for a later wireless connection are configured automatically during the first connection via USB.  Alternatively, a wireless connection can be manually set. Please make sure that your Bluetooth access point is activated and connect the iPhone to the car. Please mind any necessary confirmations on your iPhone."/>
    <LangID ID="LANG.APPCONNECT_INFO_VIEW.BaiduCarLifeHint.text" Text="CarLife allows the secure use of a phone in this car. The connection can be established via USB or wireless. Please install the CarLife App from the App-Store on your phone. Please mind any necessary confirmations on your phone."/>
```

### 5.2 个人实际应用代码

```c++
/***********************
* file:TranslateXML.cpp
* function: prase xml file
* author: Veitch Kyrie
* Date: 2019-07-23
***********************/
#include "TranslateXML.hpp"

#include <tinyxml2.h>

#include <SVPApp.h>

const static char XML_PATH_EN_US[] = "/svp/share/translate/en_US.xml";
const static char XML_PATH_ZH_CN[] = "/svp/share/translate/zh_CN.xml";

static bool isCurrentLanguageEnglish()
{
	uint32_t lang = LID_UNKNOWN;
	SVP_GetLanguage(lang);
    return (lang == LID_EN_US);
}

TranslateXML & TranslateXML::getInstance()
{
    static TranslateXML instance;
    return instance;
}

std::string TranslateXML::getText(const std::string & lang_id)
{
    if (isCurrentLanguageEnglish()) {
        return getMappedText(lang_id, map_en_);
    } else {
        return getMappedText(lang_id, map_zh_);
    }
}

std::string TranslateXML::getMappedText(const std::string & lang_id,
        const std::unordered_map<LangID, LangText> & map)
{
    if (map.end() != map.find(lang_id))
        return map.find(lang_id)->second;
    else
        return "";
}

TranslateXML::TranslateXML()
{
    parse(XML_PATH_EN_US, map_en_);
    parse(XML_PATH_ZH_CN, map_zh_);
}

TranslateXML::~TranslateXML()
{
}

void TranslateXML::parse(const std::string & filepath,
        std::unordered_map<LangID, LangText> & map)
{
    tinyxml2::XMLDocument doc;
    const auto ret = doc.LoadFile(filepath.c_str());
    if (tinyxml2::XML_SUCCESS == ret) {
        const auto pLangDef = doc.FirstChildElement();
        if (nullptr == pLangDef) return;
        auto pLangID = pLangDef->FirstChildElement();
        while (nullptr != pLangID) {
            const auto ID(pLangID->Attribute("ID"));
            const auto Text(pLangID->Attribute("Text"));
            map[ID] = Text;
            pLangID = pLangID->NextSiblingElement();
        }
    }
}
```

```c++
/***********************
* file:TranslateXML.hpp
* function: attribution/funcitons declaration
* author: Veitch Kyrie
* Date: 2019-07-23
***********************/

#ifndef _TRANSLATE_XML_HPP_
#define _TRANSLATE_XML_HPP_

#include <string>
#include <unordered_map>

class TranslateXML
{
public:
    static TranslateXML & getInstance();

    std::string getText(const std::string & lang_id);
private:
    TranslateXML();
    ~TranslateXML();

    using LangID = std::string;
    using LangText = std::string;
    std::unordered_map<LangID, LangText> map_en_;
    std::unordered_map<LangID, LangText> map_zh_;

    static void parse(const std::string & filepath, std::unordered_map<LangID, LangText> & map);
    static std::string getMappedText(const std::string & lang_id, const std::unordered_map<LangID, LangText> & map);
};

#endif
```



## 6. 结语

​	**通过以上代码的应用，可以很好的提高代码的兼容性和移植性，减少了不必要的代码改动和错误。在此给大家提供参考。**

 

