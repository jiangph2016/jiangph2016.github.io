---
layout: post
title: Python库argparse
category: 技巧
tags: python
keywords: python,argparse
---
* content
{:toc}

## 简介
这个库用于命令行参数解析  

```python
import argparse

# 初始化解析器
parser = argparse.ArgumentParser(
    description='示例',
)
# 添加解析的参数,可以多个
parser.add_argument("-a","--any",help="message",action='store',default='1')

# 开始解析
args = parser.parse_args()

# 直接可以访问这个变量
print(args.a)
```


## 参数中的可选的设置

### default

设置默认值

### action
- store：默认action模式，存储值到指定变量。
- store_const：存储值在参数的const部分指定，多用于实现非布尔的命令行flag。
- store_true / store_false：布尔开关。可以2个参数对应一个变量。
- append：存储值到列表，该参数可以重复使用。
- append_const：存储值到列表，存储值在参数的const部分指定。
- count: 参数出现的次数
- version 输出版本信息然后退出。

其中store_true代表这个参数后面不用跟东西，是一个开关的作用
### type

输入的结果进行类型转换

### choice

设置允许的参数值
例如
```
choice=[1,2,3]
```