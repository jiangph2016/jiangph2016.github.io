---
layout: post
title: 使用Python调用有道api实现文本翻译
category: 技巧
tags: Python
keywords: joblib,变量
---
* content
{:toc}

本来pypi库里有一个名为translate的库，看[文档介绍](https://pypi.org/project/translate/)也非常的简单易用。
![](/assets/img/skill/python_translate.png)

但是实际安装时却出现了不少的问题，conda里面找不到这个包，pip安装也失败（原因是我电脑中安装的依赖包，比它所需要的依赖包的版本要新）  
最后一看，这个库上次的更新时间是2017年，而且只支持到Python 3.5，而我目前用的是Python 3.7，虽说Anaconda提供了虚拟环境的管理，但是我目前需要把翻译功能加到目前在做的一个项目中，为此修改环境太折腾。  
于是决定直接调用现成的有道API来进行翻译，代码来自于<https://segmentfault.com/a/1190000015643320>
---

## 有道API

有道API地址 ：<http://fanyi.youdao.com/openapi?path=data-mode>



## 代码
```
import json
import requests

# 翻译函数，word 需要翻译的内容
def translate(word):
    # 有道词典 api
    url = 'http://fanyi.youdao.com/translate?smartresult=dict&smartresult=rule&smartresult=ugc&sessionFrom=null'
    # 传输的参数，其中 i 为需要翻译的内容
    key = {
        'type': "AUTO",
        'i': word,
        "doctype": "json",
        "version": "2.1",
        "keyfrom": "fanyi.web",
        "ue": "UTF-8",
        "action": "FY_BY_CLICKBUTTON",
        "typoResult": "true"
    }
    # key 这个字典为发送给有道词典服务器的内容
    response = requests.post(url, data=key)
    # 判断服务器是否相应成功
    if response.status_code == 200:
        # 然后相应的结果
        return response.text
    else:
        print("有道词典调用失败")
        # 相应失败就返回空
        return None

def get_reuslt(repsonse):
    # 通过 json.loads 把返回的结果加载成 json 格式
    result = json.loads(repsonse)
    print ("输入的词为：%s" % result['translateResult'][0][0]['src'])
    print ("翻译结果为：%s" % result['translateResult'][0][0]['tgt'])
```

直接运行translate,返回的是一个Json串，如下图所示
```
' 
{
    "type":"EN2ZH_CN",
    "errorCode":0,
    "elapsedTime":1,
    "translateResult":[[
        {"src":"hi","tgt":"嗨"}
        ]]
    }\n'
```