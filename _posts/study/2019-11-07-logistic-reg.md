---
layout: post
title: 【ML】关于logistic回归
category: 学习
tags: 机器学习
keywords: PRML,logistc
---

* content
{:toc}  


目前公式会不显示，复制到其他markdown编辑器查看

普通的线性回归模型:$f(x)=w^Tx+b$  

logist回归其实是一个二分类问题，就是根据线性回归模型预测的值判断属于哪一类。  

因为希望输出的值 $y\in (0,1)$ ,那么就要对f(x)进行变换，这里使用的是Sigmod函数  

​             $y = \frac {1}{1+e^{-z}} $ 

将线性回归模型套入得  

​            $y = \frac{1}{1+e^{-(w^Tx+b)}}$   (1)  

进行变换得到  

​           $\ln \frac{y}{1-y} = w^Tx+b $    (2)  

$\because P(y=1|x) = 1-P(y=0|x)$ , 将（2）中的y视为后验概率$P(y=1|x)$



$\therefore \ln \frac {P(y=1|x)}{P(y=0|x)} = w^Tx+b$



求解得：

$P(y=1|x)=\frac {e^{w^Tx+b}}{1+e^{w^Tx+b}}=h_w(x) $    (3)

则$P(y=0|x) = 1-h_w(x)$     (4)

因为y只取0或1，联合（3）（4）得：

$P(y|x) = (h_w(x))^y(1-h_w(x))^{1-y}$ （5）



根据（5）得似然函数：

$L(w)=\prod_{i=1}^{m}{P(y|x)} = \prod_{i=1}^{m}{(h_w(x))^y(1-h_w(x))^{1-y}}$  （6）

对（6）取对数得：

$ LL(w) = \ln L(w) = \sum_{i=1}^{m}{(y^i\ln h_w(x^i)+(1-y^i)\ln (1-h_w(x^i)))}$  （7）

接下来就是对$LL(w)$求极大值，就可以获得w的估计值

$\frac {\partial }{\partial w_j}LL(w) = (y-f_w(x))x_j$





参考：

[logistic回归原理解析及Python应用实例](https://blog.csdn.net/feilong_csdn/article/details/64128443)