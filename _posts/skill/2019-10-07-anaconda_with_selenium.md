---
layout: post
title: 在Annaconda下使用Selenium
category: 技巧
tags: web
keywords: selenium,Anaconda
---

最近学的比较杂，早就知道selenium这个自动化测试工具了，但是一直没有亲自使用过，因为最近有想爬微博的想法，所以使用了一下，最后发现还是不太适合我的需求，其中在知乎上也看过了这篇文章[为什么不推荐Selenium写爬虫](https://zhangslob.github.io/2018/02/02/%E4%B8%BA%E4%BB%80%E4%B9%88%E4%B8%8D%E6%8E%A8%E8%8D%90Selenium%E5%86%99%E7%88%AC%E8%99%AB/),写的挺好的，但也批判性的接受吧。

讲一下使用的过程，首先我在Windows下的python环境都是Anaconda帮我搭的，所以安装selenium的过程只需要在Anaconda中搜索selenium即可。

![图1](/assets/img/skill/selenium_1.jpg)

因为我准备在Chrome浏览器下使用，所以接下来是下载chrome驱动的，下载地址：
[http://chromedriver.storage.googleapis.com/index.html](http://chromedriver.storage.googleapis.com/index.html),下载下来后，会解压出一个名为chromedriver.exe的文件，将他直接放入Anaconda的安装目录下即可（不用改环境变量就能用！）
简单的测试一下selenium是否能正常运行，代码来源：[https://github.com/xuyichenmo/selenium-document](https://github.com/xuyichenmo/selenium-document)
```python
from selenium import webdriver
driver=webdriver.Chrome()
driver.get("https://www.baidu.com/")
```
运行结果如下，新开了一个chrome浏览器的窗口,显示正在受到自动测试软件的控制
![图2](/assets/img/skill/selenium_2.jpg)

