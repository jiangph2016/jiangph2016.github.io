---
layout: post
title: 【NLP】使用gensim模块生成word2vec词向量
category: 技巧
tags: 机器学习
keywords: word2vec
---
* content
{:toc}
参考:<https://blog.csdn.net/qq_28840013/article/details/89681499>
## 安装
```
pip install --upgrade gensim
```
### 使用
```
import gensim
#sentences=[["a","b"],["b","c"] ... ]
sentences=word2vec.Text8Corpus("test.txt")  #text8为语料库文件名
#sentences是训练所需预料，可通过该方式加载，此处训练集为英文文本或分好词的中文文本
```

两种存储方式：
1. txt文本格式，一行一篇文章 空格分开
2. list嵌套格式`[ [第一篇文章分词结果] , [第二篇文章分词结果]， …]`



```
model=gensim.models.Word2Vec(sentences,sg=1,size=100,window=5,min_count=2,negative=3,sample=0.001,hs=1,workers=4)

#该步骤也可分解为以下三步（但没必要）：
#model=gensim.model.Word2Vec() 建立一个空的模型对象
#model.build_vocab(sentences) 遍历一次语料库建立词典
#model.train(sentences) 第二次遍历语料库建立神经网络模型

#sg=1是skip—gram算法，对低频词敏感，默认sg=0为CBOW算法
#size是神经网络层数，值太大则会耗内存并使算法计算变慢，一般值取为100到200之间。
#window是句子中当前词与目标词之间的最大距离，3表示在目标词前看3-b个词，后面看b个词（b在0-3之间随机）
#min_count是对词进行过滤，频率小于min-count的单词则会被忽视，默认值为5。
#negative和sample可根据训练结果进行微调，sample表示更高频率的词被随机下采样到所设置的阈值，默认值为1e-3,
#negative: 如果>0,则会采用negativesamping，用于设置多少个noise words
#hs=1表示层级softmax将会被使用，默认hs=0且negative不为0，则负采样将会被选择使用。
#workers是线程数，此参数只有在安装了Cpython后才有效，否则只能使用单核
```


保存模型
```
model.save("文本名")	#模型会保存到该 .py文件同级目录下，该模型打开为乱码
#model.wv.save_word2vec_format("文件名"，binary = "True/False")  #通过该方式保存的模型，能通过文本格式打开，也能通过设置binary是否保存为二进制文件。但该模型在保存时丢弃了树的保存形式（详情参加word2vec构建过程，以类似哈夫曼树的形式保存词），所以在后续不能对模型进行追加训练

```
加载模型

```
#对.sava保存的模型的加载：
model = gensim.models.Word2Vec.load("模型文件名")

#对..wv.save_word2vec_format保存的模型的加载：
model = model.wv.load_word2vec_format('模型文件名')

```

模型追加训练
```
model.train(more_sentences)

如果对..wv.save_word2vec_format加载的模型进行追加训练，会报错：
AttributeError: 'Word2VecKeyedVectors' object has no attribute 'train'
```

输出
```
计算一个词的最近似的词：
model.wv.most_similar("word",topn=10)	#计算与该 词最近似的词，topn指定排名前n的词

计算两个词的相似度：
model.similarity("word1","word2")  

获取词向量（有了这个不就意味着可以进行相关词语的加减等运算，虽然我不是太懂）：
model.wv['word']

```