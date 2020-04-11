---
layout: post
title: 【Python】一些精炼的语句
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
