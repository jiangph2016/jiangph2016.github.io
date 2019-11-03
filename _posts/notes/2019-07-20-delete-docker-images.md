---
layout: post
title: 批量删除docker镜像（不通过id删除原始镜像）
category: 笔记
tags: docker
keywords: docker
---

* content
{:toc}

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190720182910194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppYW5ncGgxMDAx,size_16,color_FFFFFF,t_70)
之前在公司的时候为了测试单个镜像的名字最长可以有多长，为了不破坏研发环境，在自己的亚马逊服务器上装了个docker，不停的打tag改名，结果现在已经忘了，大概是镜像名+tag不超过256个字符。
现在一用docker images命令查询本地的镜像，简直不忍直视。
看了一下，除了一个名为registry的镜像是有用的，其他的都是基于这个打的tag，实际上都是不占存储空间的
当时统一的名字都是以a123开头的，所以批量删除时，按照网上的教程用如下命令

> docker rmi --force `docker images | grep a123 | awk '{print $3}'`


![在这里插入图片描述](https://img-blog.csdnimg.cn/20190720184619788.png)效果如下，把不带a123字符串的registry镜像也给删了

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190720183523486.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppYW5ncGgxMDAx,size_16,color_FFFFFF,t_70)
后来仔细读了一下这个命令的后半段

> docker images |grep a123|awk  '{print $3}'

问题出在awk '{print $3}'这个阶段，返回的是Image id，也就是说删除的时候并不是按照镜像的名字来删除的，而是按照id，我这里面所有的镜像id都是一样的，所以就连最原始的registry镜像，哪怕名字中不带a123也难逃厄运。
所以这里需要改成

> docker images|grep a123| awk '{printf "%s:%s",$1,$2}'

其中$1为镜像名，$2为tag