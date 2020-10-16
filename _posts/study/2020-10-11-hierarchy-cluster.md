---
layout: post
title: 【赏金】ward层次聚类解题
category: 学习
tags: 机器学习
keywords: pandas
---
* content
{:toc}

## Market Basket Analysis
给定数据集`online_gift_trans.csv`,`InvoiceNo`为订单编号,`desc2`为商品名称  
用于相同订单号的商品属于在同一个订单一起被购买的。
题目要求根据商品之间的support，进行ward聚类  


首先是对数据处理，转换成订单的形式,每一行是一个订单，columns是商品名，Value是布尔值，代表在这个订单中商品是否被购买。
```
trans_list = all_trans2.groupby('InvoiceNo').apply(lambda x: x.desc2.to_list()).to_list()
te = TransactionEncoder()
te_ary = te.fit(trans_list).transform(trans_list, sparse=True)
df2 = pd.DataFrame.sparse.from_spmatrix(te_ary, columns=te.columns_)
```
处理后的结果大致如下所示

·|BALLOONS|BASKETS|BATHROOM ACCESSORIES|BUNTING
--|--|--|--|--
0|False|False|False|False
1|False|False|True|False
2|False|False|False|False


### apriori
然后调用mlxtend库，官方简介如下
[apriori](http://rasbt.github.io/mlxtend/user_guide/frequent_patterns/apriori/)    


```
from mlxtend.preprocessing import TransactionEncoder
from mlxtend.frequent_patterns import apriori,association_rules
ap_out = apriori(df2, min_support=0.0001, use_colnames=True, max_len=2, verbose=True)
```
max_len是最大计算几个商品的支持度，这里只计算两个
ap_out是一个DataFrame记录着一个or两个商品的支持度，这里只需要用到两个商品之间的支持度，所以进行一次筛选
```
ap_out = ap_out[ap_out['itemsets'].apply(lambda x: len(x)) == 2]
```
一般两个商品的支持度越大，则同时购买的可能性越高，关联性越大。但在聚类时需要表现成距离越小  
所以生成新的指标distance,并做归一化处理，让他们范围在0-1000内
```
ap_out['distance']  = ap_out['support'].apply(lambda x: (x - ap_out['support'].min())/(ap_out['support'].max()-ap_out['support'].min()))
ap_out['distance']  = ap_out['distance'].apply(lambda x:x*1000)
```
结果如下
·|support|itemsets|	distance|
--|--|--|--
34	|0.017846	|(BALLOONS, BASKETS)	|206.408346
35	|0.010938	|(BATHROOM ACCESSORIES, BALLOONS)	|125.931446
36	|0.019893	|(BUNTING, BALLOONS)	|230.253353
37	|0.010938	|(BALLOONS, CLOCKS)	|125.931446
38	|0.004030	|(BALLOONS, COASTERS)	|45.454545


### matrix


矩阵压缩
[squareform](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.squareform.html#scipy-spatial-distance-squareform)


### hierarchy

[hierarchy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.cluster.hierarchy.linkage.html#scipy.cluster.hierarchy.linkage)









### VIF

官方文档：[VIF](https://www.statsmodels.org/stable/generated/statsmodels.stats.outliers_influence.variance_inflation_factor.html#statsmodels.stats.outliers_influence.variance_inflation_factor)

用法参考：[用 VIF 方法消除多维数据中的多重共线性](https://blog.csdn.net/BF02jgtRS00XKtCx/article/details/108877945)