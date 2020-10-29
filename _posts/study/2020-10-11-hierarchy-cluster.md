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

||BALLOONS|BASKETS|BATHROOM ACCESSORIES|BUNTING
--|--|--|--|--
0|False|False|False|False
1|False|False|True|False
2|False|False|False|False


### apriori
然后调用mlxtend库，参考官方简介
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

然后将ap_out转换成一个距离矩阵
```
df3 = pd.DataFrame(data=None,columns = df2.columns,index=df2.columns)
for index,row in ap_out.iterrows():
    (a,b) = row['itemsets']
    df3.at[a,b] = row['distance']
    df3.at[b,a] = row['distance']

# df3 is a redundant distance matrix
df3.fillna(0,inplace=True)
```

但是这个矩阵是一个稀疏矩阵，需要将其转换成一个压缩矩阵（见后面）

矩阵压缩函数的用法参考->
[squareform](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.squareform.html#scipy-spatial-distance-squareform),该函数传输一个稀疏距离矩阵(redundant distance matrix)，则返回一个压缩距离矩阵(condensed distance matrix)，反之亦然。
```
np3 = np.array(df3)
cnp3 = scipy.spatial.distance.squareform(np3)
```


### hierarchy

[hierarchy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.cluster.hierarchy.linkage.html#scipy.cluster.hierarchy.linkage)

这里聚类的函数要求第一个输入是一个压缩距离矩阵，且没有NaN

```
from scipy.cluster import hierarchy
lm1 = hierarchy.linkage(cnp3, method='ward')
```

然后计算在聚类数量不同时的轮廓系数
```
from sklearn.metrics import silhouette_score
def compute_silhouette_scores(lm,df3, k_list, verbose=True):
    ss = []
    for k in k_list:
        if verbose:
            print(f'Computing groupings for k={k}')
        groupings = hierarchy.cut_tree(lm, n_clusters=k).ravel()
        if verbose:
            print(f'Computing score for k={k}') 
        ss.append(silhouette_score(df3, groupings,metric="precomputed"))
    return(ss)

compute_silhouette_scores(lm1,df3,[2,3,4,5,6,7,8])
```

输出
>[0.5021835198792926,  
 0.4764783329149694,  
 0.42445840929007844,  
 0.4062197533885259,  
 0.3983880437502872,  
 0.38570122111314165,  
 0.38527753495620637]  

根据输出看到聚两类的效果是最好的，在0.5

## VIF


第二题是有关房价回归的，回归部分略过  
详情参考ipynb文件即可  
其中要求检查变量的多重共线性(multicollinearity)


官方文档：[VIF](https://www.statsmodels.org/stable/generated/statsmodels.stats.outliers_influence.variance_inflation_factor.html#statsmodels.stats.outliers_influence.variance_inflation_factor)里面介绍的特别简略，提到了一个名为`variance_inflation_factor(exog, exog_idx)`的函数


这里参考了另一篇文章才学会用[用 VIF 方法消除多维数据中的多重共线性](https://blog.csdn.net/BF02jgtRS00XKtCx/article/details/108877945)

```
def process(data, col):
    data = data.loc[:, col] 
    vif = [oinf.variance_inflation_factor(data.values, i) for i in range(data.shape[1])][1:] 
    print(vif) 
```

传入的参数分别是数据及需要分析的列名
```
cols = ['house_age','num_stores','Xs','Ys','ldist']
process(X2, cols)
cols = ['house_age','num_stores','Xs','Ys','ldist','center_dist']
process(X2, cols)
```
一般方差膨胀系数小于10则说明没有多重共线性

输出
>[3.3418834972692837, 1.4297213177270032, 1.3761057588135224, 4.789304922202392]  
[3.3589746292417284, 1.6924566866673723, 1.467614362904407, 13.71490460884603, 9.30539302867035]

这里`ldist`代表距离地铁站的距离，`center_dist`代表距离市中心的距离，可以看到在没有加入`center_dist`的情况下，5个变量打方差膨胀系数都是小于5的，没有出现多重共线性。  
而在加入`center_dist`后，两个变量的方差膨胀系数分别是13.7和9.3,出现了多重共线性。