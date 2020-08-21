---
layout: post
title: 【整理】Hateful Speech论文
category: 学术
tags: 论文
keywords: Abuse
---
* content
{:toc}

# Detect All Abuse!Toward Universal Abusive Language Detection Models 

## 介绍
ALD - abusive language detection

许多ALD的研究，数据有主观性，不能通用。

构建四种不同类型的辱骂性语言方面嵌入：directed target, generalised target, explicit content, and implicit content。使用异质图来分析每个作者的语言行为，并利用图卷积网络（GCNs）学习单词和文档嵌入

贡献：
- 发现大多数ALD算法都无法在不同的在线社区中接受不同类型的辱骂性语言方面
- 提出了一种新的ALD算法，该算法能够将多个方面的辱骂语言进行显式整合，并能检测出不同方面和不同领域的通用性辱骂语言行为

## 数据集

Dataset|Source|Size|Composition
--|--|--|--
Waseem|Twitter|16.2k|Racism(11.97%), Sexism(19.43%), None(68.60%)
HatEval |Twitter|13k|Hateful(42.08%), Non-hateful(57.92%)
OffEval|Twitter|13.2k|Offensive(33.23%), Not-offensive(66.77%)
Davids|Twitter|24.8k|Hate(5.77%), Offensive(77.43%), Neither(16.80%)
Founta |Twitter|99k |Abusive(27.15%), Hateful(4.97%), Normal(53.85%), Spam(4.97%)
FNUC|Fox News Discussion Threads |1.5k|Hateful(28.50%), Non-hateful(71.50%)
StormW |Stormfront(forum)|10.7k|Hate(10.93%), NoHate(89.07%) 


## MACAS ALD模型

Multi-Aspect Cross Attention Super Joint 模型


## ALD模型

### TIS
TF-IDF + SVM,使用三个特征:
- TF-IDF的权重
- 代词和辱骂词汇的TF-IDF权重
- 与相邻帖子的相似性

RBF核来处理非线性超平面分离问题。min_df设置为2

### OTH
One-Two Steps Hybrid CNN

使用Chars2vec作为character嵌入，Glove作为word嵌入

### MFR
Multi-Features with RNN 

### TWL
Two-step Word-level LSTM

### LTC

Latent Topic Clustering with Bi-GRU 

### CBT
Character-based Transformer


## 引用


# Classification Methods for Hate Speech Diffusion: Detecting the Spread of HateSpeech on Twitter


基于网络图中的扩散来检测Twitter中hate speech的传播
