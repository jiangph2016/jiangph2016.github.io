---
layout: post
title: BloomFilter使用
category: 笔记
tags: 开发
keywords: redis,bloom
---


* content
{:toc}

参考：<https://www.cnblogs.com/yscl/p/12003359.html>


## 部署

```
docker pull redislabs/rebloom:latest
docker run -p 6379:6379 --name redis-redisbloom redislabs/rebloom:latest
docker exec -it redis-redisbloom /bin/bash
```





## Python调用

源码：<https://github.com/RedisBloom/redisbloom-py>

安装
```
pip3 install redisbloom
```

使用
```python
from redisbloom.client import Client

rb.bfCreate('urls', 0.01, 1000) # 可不创建


# 添加一个值
rb.bfAdd('urls', 'baidu.com')  

rb.bfExists('urls', 'baidu.com') # 1

# 删除一个值
rb.delete("urls","google")

# 删除所有值
rb.delete("urls")
```


## 其他工具

BloomFilter内存计算工具:<https://krisives.github.io/bloom-calculator/>