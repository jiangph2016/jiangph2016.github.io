---
layout: post
title: 【Python】vader-sentiment使用
category: 技巧
tags: Python
keywords: vader
---
* content
{:toc}


## 简介
VADER（Valence-Aware Dictionary and mootion Reasoner）是一个词汇和基于规则的情感分析工具。


### 论文
名称：VADER: A Parsimonious Rule-Based Model for Sentiment Analysis of Social Media Text   
论文地址:<https://www.aaai.org/ocs/index.php/ICWSM/ICWSM14/paper/viewPaper/8109>  
发表于AAAI 2014  
 
### 安装
地址：<https://pypi.org/project/vaderSentiment/>  
Github源码：<https://github.com/cjhutto/vaderSentiment>  
安装方法: `pip install vaderSentiment`


## 使用


### 使用方法
```
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer

sentences = ["This is awful","I like it"]

analyzer = SentimentIntensityAnalyzer()
for sentence in sentences:
    vs = analyzer.polarity_scores(sentence)
    print("{:-<65} {}".format(sentence, str(vs)))
```


### 输出
```
This is awful---------------- {'neg': 0.6, 'neu': 0.4, 'pos': 0.0, 'compound': -0.4588}
I like it-------------------- {'neg': 0.0, 'neu': 0.286, 'pos': 0.714, 'compound': 0.3612}
```

存储形式是字典，通过字典调用各个变量
### 解释

- neg 负面情绪
- neu 中立情绪
- pos 积极情绪
- compound 指的是将前三个指标相加，并调整为(-1,1)的一个值