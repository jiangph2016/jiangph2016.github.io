---
layout: post
title: Clickhouse使用
category: 笔记
tags: 笔记
keywords: Clickhouse
---
* content
{:toc}

官方文档:<https://clickhouse.tech/docs/zh/introduction/distinctive-features/>




## 查看信息
<https://blog.csdn.net/jarry_cm/article/details/106134994>
### 查看数据库的总容量

```
select
    sum(rows) as row,
    formatReadableSize(sum(data_uncompressed_bytes)) as uncompress,
    formatReadableSize(sum(data_compressed_bytes)) as compress,
    round(sum(data_compressed_bytes) / sum(data_uncompressed_bytes) * 100, 0)  as compress_rate
from system.parts
```
结果如图

<img src="https://gitee.com/greynius/picbed/raw/master/img/20201130172033.png"/>

## 表操作

```
select count() from <tablename>
```