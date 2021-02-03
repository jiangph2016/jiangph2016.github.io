---
layout: post
title: 【Python】一些小技巧
category: 笔记
tags: Python
keywords: 技巧
---
* content
{:toc}


随用随记
## 读取stop_words

```
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
stop_words = set(open(os.path.join(BASE_DIR, 'stopwords.txt'), 'r').read().split())
```
## 字符串bu前缀转换

`u'V'` 与 `'V'`和`u'\u0056'`等价，即Unicode码,类型为string

`b'\x56'` 与 `b'V'`等价，但只能放ASCII码，否则会报错,类型为bytes

```
b = str.encode('V')  # u 2 b 
a = bytes.decode(b'V') # b 2 u
```