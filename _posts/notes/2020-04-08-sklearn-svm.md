---
layout: post
title: sklearn的SVM
category: 笔记
tags: 机器学习
keywords: sklearn,SVM
---
* content
{:toc}
参考：<https://www.cnblogs.com/Yanjy-OnlyOne/p/11368253.html>

```
from sklearn.svm import LinearSVC
model = LinearSVC()
model.fit(x_train,y_train)
```


参数：
名称 | 介绍
-- | --
C | 默认为1，错误项的惩罚系数，C越大，对分错样本的惩罚越大，训练样本的准确率越高。但是泛化能力减低，对测试数据的准确率下降。如果减小C的话，容许训练样本中分类错误样本，泛化能力强。一般选择后者
kernel | 默认为rbf，采用核函数的类型可选的有：linear--线性核函数 ;poly--多项式核函数；rbf--径向核函数/高斯函数；sigmod:sigmod核函数；precomputed:矩阵函数
max_iter : int, optional (default=-1) | 最大迭代次数，默认-1表示不限制
loss | 字符串。表示损失函数。可取值为'hinge'：合页损失函数:'squared_hinge'：合页损失函数的平方



属性
名称 | 介绍
-- | --
support_ : array-like, shape = [n_SV] | 支持向量索引
support_vectors_ : array-like, shape = [n_SV, n_features] | 支持向量
n_support_ : array-like, dtype=int32, shape = [n_class] | 每个类的支持向量数
