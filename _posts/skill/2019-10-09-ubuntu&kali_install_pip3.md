---
layout: post
title: ubuntu系统或Kali系统快速安装pip3
category: 技巧
tags: python
keywords: pip3
---

我用的是WSL的Kali系统，系统里面只装了python2。  

python3应该是我自己后来装的，所以里面就只有pip，没有pip3

网上的教程普遍写的是使用setuptools，例如[https://www.jb51.net/article/137629.htm](https://www.jb51.net/article/137629.htm)  

实际上只需要一条命令就能解决

```bash
apt-get install python3-pip
```