---
layout: post
title: matplotlib画水平线/竖直线
category: 技巧
tags: Python
keywords: matplotlib
---

用matplotlib画图像，首先导入两个必要的包
```python

import matplotlib.pyplot as plt
import numpy as np

```

首先使用np.linspace或者np.arange指定x的范围，然后将y表示为x的函数即可


```python
plt.figure()
x = np.linspace(0,1) #或x = np.arange(0,1,0.1)
y = x
plt.plot(x,y)
plt.show()
```

![图片1](/assets/img/skill/plot_1.png)

但是在画水平或竖直的图像是，例如x=5或者y=5的时候，下面这么写就会报错
![错误1](/assets/img/skill/plot_err_1.jpg)

网上搜了一下画水平或竖直的线的方法，但是主要以特定的函数为主，如下
```python

plt.vlines(0, 0, 0.5, colors = "r") #竖线
plt.hlines(0, 0, 0.5, colors = "r") #横线
```
运行结果如下

![图片4](/assets/img/skill/plot_4.jpg)

但感觉这并不是我想要的，因为这个图像与函数无关  

后来找到了解决方法，其实很简单，即使y与x无关，但是依然要写成有关的形式，后面加上0*x就可
```python
plt.figure()
x = np.linspace(0,1)
y = 5 + 0*x
plt.plot(x,y)
plt.show()
```
效果如下图所示
![图片2](/assets/img/skill/plot_2.jpg)

将x,y的位置互换一下，变成plot(y,x),即可画出x=5的图像

![图片3](/assets/img/skill/plot_3.jpg)