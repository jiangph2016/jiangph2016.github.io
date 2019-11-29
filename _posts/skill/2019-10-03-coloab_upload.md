---
layout: post
title: Colaboratory上传和下载文件
category: 技巧
tags: python
keywords: colab
---
* content
{:toc}

[Colaboratory](https://colab.research.google.com)，以下简称colab。相当于一个云端的带GPU的jupyter   
![colab](/assets/img/skill/colab.jpg)  
## 上传文件
在colab上写的python代码很多时候都有读取文件的需求，本以为将文件直接上传到Google Drive上就可以直接读取，但后来发现两个根本不在一套系统里。colab是每次启动的时候都会随机分配一个新的虚拟机给你，所以数据都不能保存  

最简单的方法就是调用colab的库，运行的时候手动选择要上传的文件
```python
from google.colab import files
uploaded = files.upload()
file_name = uploaded.keys()
print(file_name)
```
运行效果见下图  
![上传文件](/assets/img/skill/colab_upload_1.jpg)  
图中我选择了两个文件，upload函数返回值是一个字典，key为文件名，value是文件内容，file_name输出结果如下
>dict_keys(['感知器数据.csv', '课表.xlsx'])  

因为在python3中返回的key值不再直接是一个list了，所以无法直接索引。解决方法是强制类型转换为list，就可以索引了（这样我才能指定读第几个文件）

```python
file_list = list(file_name)
print(file_list)
```

输出是
>['感知器数据.csv', '课表.xlsx']
  
## 下载文件
```python
from google.colab import files
with open('123.txt', 'w') as f:
  f.write('some content')
 
files.download('123.txt')
```


---
参考：
1. [用谷歌的GPU跑你的代码----Colaboratory使用记录](https://blog.csdn.net/ssssdbucdbod/article/details/80397808)
2. [使用colab时遇到的各种坑--加载云盘里的文件](https://www.jianshu.com/p/1c1f47748827)
3. [Python dict字典keys()、values()和items()方法](http://c.biancheng.net/view/4384.html)
4. [【GPU使用】Google免费GPU_Colaboratory 常用命令汇总](https://blog.csdn.net/zsWang9/article/details/79958346)

