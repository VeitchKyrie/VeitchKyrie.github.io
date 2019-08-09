---
layout:     post
title:      shell bash常用工具教程（curl，jq）
date:       2019-08-09
author:     VK
catalog: true
tags:
    - shell
    - json
---

#  shell bash常用工具教程（curl，jq）

### 1.1 curl

> curl -h来查看请求参数的含义
> -v 显示请求的信息
> -X 选项指定其它协议



get:
    curl -v 192.168.33.1:8080/girls/age/18

post:
    curl -v 192.168.33.1:8080/girls -d 'age=14&cupSize=C'
    curl -v -X POST 192.168.33.1:8080/girls -d 'age=14&cupSize=C'

put:
    curl -v -X PUT -d "age=19&cupSize=C" 192.168.33.1:8080/girls/3

delete:
    curl -v -X DELETE 192.168.33.1:8080/girls/3


### 1.2 jq安装

    apt-get install jq

jq命令格式

jq [options] filter [files]
**options：**

> --version：输出jq的版本信息并退出
>
> --slurp/-s：读入整个输入流到一个数组。
> --raw-input/-R：不作为JSON解析，将每一行的文本作为字符串输出到屏幕。
> --null-input/ -n：不读取任何输入，过滤器运行使用null作为输入。一般用作从头构建JSON数据。
> --compact-output /-c：使输出紧凑，而不是把每一个JSON对象输出在一行。
> --colour-output / -C：打开颜色显示
> --monochrome-output / -M：关闭颜色显示
>
> --ascii-output /-a：指定输出格式为ASCII
>
> -raw-output /-r ：如果过滤的结果是一个字符串，那么直接写到标准输出（去掉字符串的引号）



**filter：**

> .   : 默认输出
>
> .foo: 输出指定属性，foo代表属性。
> .[foo] ：输出指定数组元素。foo代表数组下标。
> .[]：输出指定数组中全部元素
> ， ：指定多个属性作为过滤条件时，用逗号分隔
> | ： 将指定的数组元素中的某个属性作为过滤条件



**files：**JOSN格式文件。



格式化JSON

现在有一个字符串变量，来自文件或者curl的网络请求返回

    json_str=$(cat json_raw.txt) 

    echo ${json_str}

    {"name":"Google","location":{"street":"1600 Amphitheatre Parkway","city":"Mountain View","state":"California","country":"US"},"employees":[{"name":"Michael","division":"Engineering"},{"name":"Laura","division":"HR"},{"name":"Elise","division":"Marketing"}]}
​    

检查JSON的合法性，并把JSON格式化成更友好更可读的格式

    echo ${json_str} | jq -r '.'

如何根据key获取value

    echo ${json_str} | jq -r '.name'
    返回Google

解析不存在的元素，会返回null

    echo ${json_str} | jq -r '.name1'
    返回null

嵌套解析

    echo ${json_str} | jq -r '.location.state'
    返回California

解析数组

    echo ${json_str} | jq -r '.employees[1].name'
    返回Laura

遍历数组下的每个元素

`echo ${json_str} | jq -r '.employees[].name'`
或者

`echo ${json_str} | jq -r '.employees[]|.name'`
返回

    Michael
    Laura
    Elise

同时获取多个属性

`echo ${json_str} | jq -r '.employees[].name, .employees[].division'`
或者

`echo ${json_str} | jq -r '.employees[]|.name,.division'`
返回

    Michael
    Laura
    Elise
    Engineering
    HR
    Marketing

shell脚本遍历数组

    names=((echo {json_str} | jq -r '.employees[].name'))
    #如果 直接echo $names   将只打印第一个元素的值
    #${#names[@]}获取数组长度用于循环
    for(( i=0;i<${#names[@]};i++)) do
    echo ${names[i]}
    done

内建函数

`echo ${json_str} | jq 'has("name")'`
返回 true

    echo ${json_str} | jq 'keys'

返回

[
  "employees",
  "location",
  "name"
]