---
layout: post
title: Clickhouse常用SQL命令
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

### 查看分区信息

```
select database,table,partition,partition_id,name,path from system.parts where table='visit'
```
结果
```
─database─┬─table──┬─partition─┬─partition_id─┬─name─────────┬─path───────────────────────────────────────────────────┐
│ datasets │ visits │ 202006    │ 202006       │ 202006_1_1_0 │ /var/lib/clickhouse/data/datasets/visits/202006_1_1_0/ │
│ datasets │ visits │ 202007    │ 202007       │ 202007_2_2_0 │ /var/lib/clickhouse/data/datasets/visits/202007_2_2_0/ │
│ datasets │ visits │ 202008    │ 202008       │ 202008_3_3_0 │ /var/lib/clickhouse/data/datasets/visits/202008_3_3_0/ │
└──────────┴────────┴───────────┴──────────────┴──────────────┴────────────────────────────────────────────────────────┘
```



## 表操作


### 创建

复制一个现有的表
```
create table <表名1> as <表名2> 
```

### 查看
#### 统计
```
select count() from <表名>
```
统计不同的值
```
select count(distinct <列名>) from <表名>
```
或
```
select countDistinct(<列名>) from <表名>c
```

### 改操作
#### 行列修改
列的增删
```
ALTER TABLE <表名> ADD COLUMN <列名1>, ADD COLUMN <列名2>;
ALTER TABLE <表名> DROP COLUMN <列名1>, DROP COLUMN <列名2>;
```
删行
```
ALTER <表名> DELETE WHERE <表达式>;
DELETE FROM <表名> WHERE <表达式>;
```
删表

```
drop table <表名>
```

#### 数据迁移
首先查看已有的分区
```
select partition from system.parts where table='<表名>' group by partition order by partition desc
```
通过复制分区的方式，将数据从一个表复制到另一个表，要求两个表的结构完全相同
```
alter table <目的表名> attach partition <分区名> from <来源表名> ;
```


如果是**跨数据源迁移**，需要将数据dettach出来
```
alter table <来源表名> detach partition <分区名>
```
然后复制到对应的存储目录下
```
alter table <目的表名> attach partition <分区名>
```
如果提示权限错误
```
chown -R clickhouse *
```


#### 通过sql进行数据迁移

```
insert into <目的表名> select * from <来源表名>
```

#### 数据导出
CSV必须大写
```
SELECT * FROM <表名> INTO OUTFILE '<文件名>' FORMAT CSVWithNames
```


### 数据导入

<https://clickhouse.tech/docs/en/interfaces/formats/#csvwithnames>  

推荐输入的时候使用`CSVWithNames`的方式，
```
clickhouse-client --host=1.1.1.1 --query='insert into <表名> format CSVWithNames' < data.csv
```


其他几种格式
- CSVWithNames
- TabSeparated 
- JSON
- JSONEachRow 将json缩写为一行


#### 跳过错误

导入的数据中可能个别行出现错误
加入参数
```
--input_format_allow_errors_num=1 
--input_format_allow_errors_ratio=0.1
```

