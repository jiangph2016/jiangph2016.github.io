---
layout: post
title: 【Python】csv文件的读写
category: 技巧
tags: Python
keywords: python,csv
---
* content
{:toc}


## 使用传统csv模块

其中newline在linux/mac系统下可不加，用于去除windows系统下的多余换行
```
import csv
with open(csvname,"a+",newline="") as file:
    writer = csv.writer(file)
    writer.writerow(content)
```


## 使用numpy

```
import numpy as np
with open("1.csv","r") as f:
    res = np.loadtxt(f,delimiter = ",")
```

- delimiter 分隔符，默认是空格
- skiprows 跳过前N行，默认为0
- comments 设置评论行，如果值为'#',则#开头的行不读取
- usecols 只使用其中几列，值为一个tuple,如(0,2)
- unpack 如果为True，则不返回二维矩阵，把每一列当成一个向量输出，使用一个tuple来接受返回值
- dtype 类型


### 例子
例如某csv文件
```
#AA BB CC
A B C
1 2 3 
4 5 6
7 8 9 
```
读取代码
```
(a,b) = np.loadtxt('1.csv',delimiter=' ',comments='#,usecols=(0,2),unpack=True)
print(a,b)
```
输出
```
[1 4 7] [3 6 9]
```

### converters函数

对指定列的数据进行预处理的参数
```
def do_something(x):
    pass
# 对第0列的数据进行处理
converters={0:do_someting}
``` 
### numpy切片

主要是对二维数组按列进行切片，使用省略号来进行切片。

```
a = np.array([[1,2,3],[3,4,5],[4,5,6]])  
print (a[...,1])   # 第2列元素
print (a[1,...])   # 第2行元素
print (a[...,1:])  # 第2列及剩下的所有元素
```

一维的切片方式为：
```
a = np.arange(10)  
# 从索引 2 开始到索引 7 停止，间隔为 2
b = a[2:7:2]   
print(b)
# 输出：2 4 6
```


### pandas

```
import pandas as pd

df = pd.read_csv(filename,usecols=[要取的列名])
df = pd.read_csv('1.csv',usecols=['teacher','student'])
```
**参数**：

- **header** 指定行数作为列名，如果文件中没有列名，则默认为0