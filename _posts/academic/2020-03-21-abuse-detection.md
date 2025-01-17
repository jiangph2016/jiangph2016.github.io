---
layout: post
title: 【精读】侧写作者来检测仇恨语言
category: 学术
tags: 论文
keywords: abuse,social
---
* content
{:toc}

类型  |内容  
-- |--  
标题 |Author Profiling for Abuse Detection
时间 |2018
会议 |COLING
引用 |Mishra P, Del Tredici M, Yannakoudakis H, et al. Author profiling for abuse detection[C]//Proceedings of the 27th International Conference on Computational Linguistics. 2018: 1088-1098.


## 摘要
近年来，社交媒体的迅速发展导致了一些非常不受欢迎的现象，如网络上仇恨和攻击性语言的泛滥。先前的研究表明，这些内容往往来自于用户，他们都拥有固定的模式，并形成了社区。  
当前最先进的辱骂语言检测方法忽视用户和社区信息，完全依赖文本（即词汇和语义）。在本文中，我们提出了一种新的方法来解决这个问题，即结合推特用户基于社区的评测特征。通过对160k推特数据集的实验，我们发现我们的方法在辱骂语言检测方面明显优于当前的最新技术。

## 介绍
报告显示，40%的成年网民曾亲身经历过网络骚扰，60%的人曾目睹过使用攻击性的名字和咒骂。大多数（66%）遭受过骚扰的事件发生在社交网站或应用程序上。虽然这些网站和应用程序中的大多数都提供了标记攻击性和仇恨性内容的方式，但只有8.8%的受害者实际考虑过使用。这些统计数据表明，抑制辱骂语言内容（如标记）传播的被动或手动技术既不有效，也不容易衡量。 因此，自动检测和审核此类内容的努力已在自然语言处理（NLP）中获得普及。

几种辱骂语言检测方法证明了在监督分类设置词袋功能的有效性。 较新的方法（目前性能最好的方法）利用卷积神经网络（RNN）将内容转换为密集的低维语义表示形式，然后将其用于分类。 所有这些方法仅依赖于它们所应用的文本的词汇和语义特征。

作者分析因为NLP而成为一个强大的工具，可在诸如文本分类，情感分析和作者属性识别等多个任务中显着改善性能。 人们在现实生活中以及在互联网上都倾向于与那些看起来相似的人更多地联系在一起。 在这里，可以沿着不同的轴（例如，位置，年龄，语言等）定义相似性。
我们在这里要解决的问题是：有些作者及其所属的社区是否比其他作者更具辱骂性？ 并可以有效地利用这些信息来提高辱骂语言检测的性能。

## 相关工作
### 辱骂语言监测
Yin等人最早将监督学习应用于辱骂语言行为检测任务。 使用线性SVM分类器来识别包含骚扰的帖子，这些帖子基于本地（例如n-gram），上下文（某个帖子与其相邻帖子的相似性）和基于情感的（例如存在贬义词）特征。

Djuric对从Yahoo Finance门户网站提取的评论进行了实验，结果表明，在监督性的分类设置中，使用paragraph2vec学习的评论的分布式表示优于词袋（BOW）表示。 Nobata等人对Djuric等人的结果进行了改进。 通过训练他们的分类器来学习来自四个不同类别的特征的组合：linguistic（例如侮辱词计数），syntactic（例如POS tags）， distributional semantic（例如单词和注释嵌入）和基于BOW的（单词和字符 n-grams）。 他们报告说，虽然结合了所有特征都获得了最佳结果，但字符n-gram对性能的贡献比其他所有特征都大.  
Waseem和Hovy创建了一个包含种族主义、性别歧视的tweets数据集，并对其进行了实验。他们使用logistic回归分类器对这些数据集进行区分，发现特征图与用户的性别信息结合形成了最佳的特征集；另一方面，地理和字长分布特征几乎没有贡献.使用相同的数据集，Badjatiya等人。
Badjatiya等人在使用相同的数据集下，通过训练**梯度增强决策树（GBDT）**分类器来改善结果，该分类器使用随机嵌入初始化的LSTM网络学习的平均单词嵌入。

...

### 作者侧写

## 数据集
我们使用Waseem和Hovy（2016）的数据集进行了实验，其中包含手动注释辱骂语言的推文。 作者在两个月的时间内检索了大约13.6万条推文。 他们搜寻与宗教，性，性别和少数民族有关的常用诽谤和粗俗语来收集。他们确定了经常出现在辱骂性的推特上的术语和实体。
基于此样本，他们使用了公共Twitter API来收集ca的整个语料库的13.6万条推文。 在对种族主义，性别歧视或无种族主义类别的16914条推文的随机采样子集进行人工注释后，他们要求专家对其注释进行审核，以减轻任何偏见。 注释者之间的一致性被报告为 k= 0.84，其中85％的分歧发生在性别歧视中。
该数据集原始是16907个tweet ID及其对应的注释。 使用python的Tweepy库，我们能检索其中16202条推文，其他的不可见。 其中有1939（12％）条被标记为种族主义，3148（19.4％）条被标记为性别歧视，11115（68.6％）条被标记为种族歧视。 这种分布非常接近原始数据集（11.7％，20.0％，68.3％）。

在1875名作者中，我们能够提取1836名作者信息，包含16202条推特。 其余39位作者已停用其帐户。

种族主义的推文来自5位作者，而性别歧视的推文来自527位作者。

## 方法

### 表示作者

建立无向图，如果u关注了v,则在两者之间连接一条边。共有1836个节点和7561个边。大约有400个节点没有边，节点的平均度为3到8，其中近600个节点的平均度至少为5。该图整体稀疏，密度为0.0075。

使用node2vec框架为每个作者获取一个向量表示，即作者侧写嵌入。Node2vec将**skip-gram** 模型应用于图，以便基于每个节点的位置和它们的邻居为其创建表述。
独立的节点是0嵌入。

其中Ns(v)代表通过采样策略s生成的v节点的网络邻域。


### 内容分类
尝试了七种不同的方法将推特分类为种族主义、性别歧视或无种族歧视。

- **char n-grams(LR)** 
使用字符n-gram计数在Twitter数据集上训练logistic回归（LR）分类器。我们使用uni-grams、bi-grams、tri-grams和4-grams，L2规范化它们的计数。字符n-grams已被证明对滥用检测任务有效

- **Hidden-state (HS)** 

使用RNN方法，该方法将n个d维嵌入的单词表示为隐藏层h1-hn,后面是一个LR层，用上一隐藏层的结果对tweet分类。
1层GRU->2层GRU,LR层的sigmod->softmax。
使用Glove作为词向量，其中不可用的单词在±0.05之间随机初始化，
- **Word-sum**


> 论文来自  Deep Learning for Hate Speech Detection in Tweets 

GloVe是一个训练好的的词嵌入模型，地址：<https://nlp.stanford.edu/projects/glove/>  
LSTM + GLoVe + GBDT方法。原作者首先利用LSTM通过从LR层传回错误来对GLoVe初始化的单词嵌入进行任务调整。 他们训练一个**梯度提升决策树（GBDT）**分类器，根据组成词嵌入的平均值对文本进行分类。 我们对该方法进行了两个较小的修改：我们使用2层GRU而不是LSTM来调整嵌入，并根据词嵌入的L2归一化总和（而非平均值）训练GBDT分类器。


- **Author profile (AUTH)** 只用作者信息来分类他们生成的内容，GBDT分类器
- **Char n-grams + author profile (LR + AUTH)**

- **Hidden-state + author profile (HS + AUTH)**

- **Word-sum + author profile (WS + AUTH)**

## 实验结果

### 实验过程

所有单词小写，删除stopwords。
使用Lightgbm作为GBDT分类器