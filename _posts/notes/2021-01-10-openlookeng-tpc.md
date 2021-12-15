---
layout: post
title: 【测试】OpenLookeng-Clickhouse连接器TPC性能测试
category: 笔记
tags: 测试
keywords: clickhouse
---
* content
{:toc}

原始版本发布在InfoQ上: [OpenLookeng 连接器 -Clickhouse connector 性能测试报告](https://xie.infoq.cn/article/25b2a716e993c6c9106c12ba0)


## 测试背景

OpenLooKeng是一款开源的高性能数据虚拟化引擎，提供统一SQL接口，可以实现对多个数据库的跨源异构和跨域跨DC查询。

ClickHouse是Yandex开源的一个用于实时数据分析的基于列存储的数据库，其工作速度比传统方法快100-1000倍，性能超过了目前市场上的列式存储数据库[^4]。

ClickHouse connector[^5]是为OpenLookeng开发的用于访问Clickhouse数据源的连接器，截止测试时最新版本更新至2020-12-24日的`b2162c5`，已支持对常见数据类型和函数的映射，详细支持列表可见<https://gitee.com/heatao/hetu-core/blob/48650aa794c90a871df15661bf77f25225fd09c5/hetu-docs/zh/connector/clickhouse.md>

## 测试目的

使用SSB测试基准提供的数据和SQL查询语句，对OpenLookeng的ClickHouse connector的性能进行评估。

## 测试环境

### 系统信息

| IP             | 操作系统                      | 内核版本                    | 文件系统类型 |
| -------------- | ----------------------------- | --------------------------- | ------------ |
| 192.168.40.152 | CentOS Linux release 7.4.1708 | 3.10.0-693.el7.x86_64       | xfs          |
| 192.168.40.223 | CentOS Linux release 7.7.1908 | 3.10.0-1062.12.1.el7.x86_64 | xfs          |

### 硬件信息

|      | 192.168.40.152                             | 192.168.40.223                           |
| ---- | ------------------------------------------ | ---------------------------------------- |
| CPU  | Intel(R) Xeon(R) Silver 4114 CPU @ 2.20GHz | Intel(R) Xeon(R) CPU E5-2698 v3 @2.30GHz |
| 内存 | 128G                                       | 128G                                     |

### 部署情况

| 软件        | 位置           | 版本        |
| ----------- | -------------- | ----------- |
| OpenLookeng | 192.168.40.152 | 1.0.1       |
| Clickhouse  | 192.168.40.152 | 20.3.12.112 |
| Clickhouse  | 192.168.40.223 | 20.3.12.112 |



## 数据集

### 规模

按照Clickhouse官方文档中提到的Star Schema测试集生成数据[^1]。

通过ssb-dbgen工具设置参数`10`和`1000`分别生成了两种规模的测试集，其规模如下，分别称为`ssb-10`和`ssb-1000`

| table          | size       | bytes_on_disk | data_uncompressed_bytes | data_compressed_bytes | compress_rate | rows     |
| -------------- | ---------- | ------------- | ----------------------- | --------------------- | ------------- | -------- |
| supplier       | 771.98 KiB | 771.98 KiB    | 1.11 MiB                | 771.02 KiB            | 67.9131432    | 20000    |
| part           | 13.79 MiB  | 13.79 MiB     | 19.59 MiB               | 13.76 MiB             | 70.2197571    | 800000   |
| customer       | 11.50 MiB  | 11.50 MiB     | 16.89 MiB               | 11.49 MiB             | 67.9983266    | 300000   |
| lineorder      | 1.65 GiB   | 1.65 GiB      | 2.40 GiB                | 1.64 GiB              | 68.34114      | 59986052 |
| lineorder_flat | 5.19 GiB   | 5.19 GiB      | 9.70 GiB                | 5.18 GiB              | 53.3959248    | 59986052 |

`ssb-10`的数据占用空间为5.18G，包含59986052条数据

| table          | size       | bytes_on_disk | data_uncompressed_bytes | data_compressed_bytes | compress_rate | rows       |
| -------------- | ---------- | ------------- | ----------------------- | --------------------- | ------------- | ---------- |
| supplier       | 75.39 MiB  | 75.39 MiB     | 110.64 MiB              | 75.33 MiB             | 68.08099618   | 2000000    |
| part           | 34.47 MiB  | 34.47 MiB     | 48.97 MiB               | 34.39 MiB             | 70.22835711   | 2000000    |
| customer       | 1.12 GiB   | 1.12 GiB      | 1.65 GiB                | 1.12 GiB              | 67.94630597   | 30000000   |
| lineorder      | 123.00 GiB | 123.00 GiB    | 176.19 GiB              | 122.76 GiB            | 69.67630518   | 4398761522 |
| lineorder_flat | 396.40 GiB | 396.40 GiB    | 711.53 GiB              | 395.76 GiB            | 55.62024948   | 4398761522 |

`ssb-1000`占用的空间为395.76G，数据量为4398761522

### 测试语句

SSB测试语句总共包含13条测试语句，如下

>Q1.1: SELECT sum(LO_EXTENDEDPRICE * LO_DISCOUNT) AS revenue  FROM lineorder_flat  WHERE toYear(LO_ORDERDATE) = 1993 AND LO_DISCOUNT BETWEEN 1 AND 3 AND LO_QUANTITY < 25; 
>
>Q1.2: SELECT sum(LO_EXTENDEDPRICE * LO_DISCOUNT) AS revenue  FROM lineorder_flat  WHERE toYYYYMM(LO_ORDERDATE) = 199401 AND LO_DISCOUNT BETWEEN 4 AND 6 AND LO_QUANTITY BETWEEN 26 AND 35; 
>
>Q1.3: SELECT sum(LO_EXTENDEDPRICE * LO_DISCOUNT) AS revenue  FROM lineorder_flat  WHERE toISOWeek(LO_ORDERDATE) = 6 AND toYear(LO_ORDERDATE) = 1994 AND LO_DISCOUNT BETWEEN 5 AND 7 AND LO_QUANTITY BETWEEN 26 AND 35; 
>
>Q2.1: SELECT sum(LO_REVENUE)  toYear(LO_ORDERDATE) AS year  P_BRAND  FROM lineorder_flat  WHERE P_CATEGORY = 'MFGR#12' AND S_REGION = 'AMERICA'  GROUP BY  year  P_BRAND  ORDER BY  year  P_BRAND; 
>
>Q2.2: SELECT sum(LO_REVENUE)  toYear(LO_ORDERDATE) AS year  P_BRAND  FROM lineorder_flat  WHERE P_BRAND >= 'MFGR#2221' AND P_BRAND <= 'MFGR#2228' AND S_REGION = 'ASIA'  GROUP BY  year  P_BRAND  ORDER BY  year  P_BRAND; 
>
>Q2.3: SELECT sum(LO_REVENUE)  toYear(LO_ORDERDATE) AS year  P_BRAND  FROM lineorder_flat  WHERE P_BRAND = 'MFGR#2239' AND S_REGION = 'EUROPE'  GROUP BY  year  P_BRAND  ORDER BY  year  P_BRAND; 
>
>Q3.1: SELECT C_NATION  S_NATION  toYear(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE C_REGION = 'ASIA' AND S_REGION = 'ASIA' AND year >= 1992 AND year <= 1997  GROUP BY  C_NATION  S_NATION  year  ORDER BY  year ASC  revenue DESC; 
>
>Q3.2: SELECT C_CITY  S_CITY  toYear(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE C_NATION = 'UNITED STATES' AND S_NATION = 'UNITED STATES' AND year >= 1992 AND year <= 1997  GROUP BY  C_CITY  S_CITY  year  ORDER BY  year ASC  revenue DESC; 
>
>Q3.3: SELECT C_CITY  S_CITY  toYear(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE (C_CITY = 'UNITED KI1' OR C_CITY = 'UNITED KI5') AND (S_CITY = 'UNITED KI1' OR S_CITY = 'UNITED KI5') AND year >= 1992 AND year <= 1997  GROUP BY  C_CITY  S_CITY  year  ORDER BY  year ASC  revenue DESC;
>
>Q3.4: SELECT C_CITY  S_CITY  toYear(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE (C_CITY = 'UNITED KI1' OR C_CITY = 'UNITED KI5') AND (S_CITY = 'UNITED KI1' OR S_CITY = 'UNITED KI5') AND toYYYYMM(LO_ORDERDATE) = 199712  GROUP BY  C_CITY  S_CITY  year  ORDER BY  year ASC  revenue DESC;
>
>Q4.1: SELECT toYear(LO_ORDERDATE) AS year  C_NATION  sum(LO_REVENUE - LO_SUPPLYCOST) AS profit  FROM lineorder_flat  WHERE C_REGION = 'AMERICA' AND S_REGION = 'AMERICA' AND (P_MFGR = 'MFGR#1' OR P_MFGR = 'MFGR#2')  GROUP BY  year  C_NATION  ORDER BY  year ASC  C_NATION ASC; 
>
>Q4.2: SELECT toYear(LO_ORDERDATE) AS year  S_NATION  P_CATEGORY  sum(LO_REVENUE - LO_SUPPLYCOST) AS profit  FROM lineorder_flat  WHERE C_REGION = 'AMERICA' AND S_REGION = 'AMERICA' AND (year = 1997 OR year = 1998) AND (P_MFGR = 'MFGR#1' OR P_MFGR = 'MFGR#2')  GROUP BY  year  S_NATION  P_CATEGORY  ORDER BY  year ASC  S_NATION ASC  P_CATEGORY ASC;
>
>Q4.3: SELECT toYear(LO_ORDERDATE) AS year  S_CITY  P_BRAND  sum(LO_REVENUE - LO_SUPPLYCOST) AS profit  FROM lineorder_flat  WHERE S_NATION = 'UNITED STATES' AND (year = 1997 OR year = 1998) AND P_CATEGORY = 'MFGR#14'  GROUP BY  year  S_CITY  P_BRAND  ORDER BY  year ASC  S_CITY ASC  P_BRAND ASC;



根据OpenLookeng的语法，在功能不变的情况下对Clickhouse语法的sql语句进行一定的修改，主要为一下几点

- 将`toYear()`函数统一修改为`year()`
- 将`toISOWeek()`函数修改为`week()`
- 将`toYYYYMM(A)`替换为` year(A)*100+month(A)`
- 将语法为`select expr(A) as B group by B` 等修改为`select expr(A) as B group by expr(A)`，即去掉对AS的使用

修改后的语法为

>Q1.1 : SELECT sum(LO_EXTENDEDPRICE * LO_DISCOUNT) AS revenue  FROM lineorder_flat  WHERE year(LO_ORDERDATE) = 1993 AND LO_DISCOUNT BETWEEN 1 AND 3 AND LO_QUANTITY < 25;
>
>Q1.2 : SELECT sum(LO_EXTENDEDPRICE * LO_DISCOUNT) AS revenue  FROM lineorder_flat  WHERE year(LO_ORDERDATE)*100+month(LO_ORDERDATE) = 199401 AND LO_DISCOUNT BETWEEN 4 AND 6 AND LO_QUANTITY BETWEEN 26 AND 35;
>
>Q1.3 : SELECT sum(LO_EXTENDEDPRICE * LO_DISCOUNT) AS revenue  FROM lineorder_flat  WHERE week(LO_ORDERDATE) = 6 AND year(LO_ORDERDATE) = 1994 AND LO_DISCOUNT BETWEEN 5 AND 7 AND LO_QUANTITY BETWEEN 26 AND 35;
>
>Q2.1 : SELECT sum(LO_REVENUE)  year(LO_ORDERDATE) AS year  P_BRAND  FROM lineorder_flat  WHERE P_CATEGORY = 'MFGR#12' AND S_REGION = 'AMERICA'  GROUP BY  year(LO_ORDERDATE)  P_BRAND  ORDER BY  year(LO_ORDERDATE)  P_BRAND;
>
>Q2.2 : SELECT sum(LO_REVENUE)  year(LO_ORDERDATE) AS year  P_BRAND  FROM lineorder_flat  WHERE P_BRAND >= 'MFGR#2221' AND P_BRAND <= 'MFGR#2228' AND S_REGION = 'ASIA'  GROUP BY  year(LO_ORDERDATE)  P_BRAND  ORDER BY  year(LO_ORDERDATE)  P_BRAND;
>
>Q2.3 : SELECT sum(LO_REVENUE)  year(LO_ORDERDATE) AS year  P_BRAND  FROM lineorder_flat  WHERE P_BRAND = 'MFGR#2239' AND S_REGION = 'EUROPE'  GROUP BY  year(LO_ORDERDATE)  P_BRAND  ORDER BY  year(LO_ORDERDATE)  P_BRAND;
>
>Q3.1 : SELECT C_NATION  S_NATION  year(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE C_REGION = 'ASIA' AND S_REGION = 'ASIA' AND year(LO_ORDERDATE) >= 1992 AND year(LO_ORDERDATE) <= 1997  GROUP BY  C_NATION  S_NATION  year(LO_ORDERDATE)  ORDER BY  year(LO_ORDERDATE) ASC  revenue DESC;
>
>Q3.2 : SELECT C_CITY  S_CITY  year(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE C_NATION = 'UNITED STATES' AND S_NATION = 'UNITED STATES' AND year(LO_ORDERDATE) >= 1992 AND year(LO_ORDERDATE) <= 1997  GROUP BY  C_CITY  S_CITY  year(LO_ORDERDATE)  ORDER BY  year(LO_ORDERDATE) ASC  revenue DESC;
>
>Q3.3 : SELECT C_CITY  S_CITY  year(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE (C_CITY = 'UNITED KI1' OR C_CITY = 'UNITED KI5') AND (S_CITY = 'UNITED KI1' OR S_CITY = 'UNITED KI5') AND year(LO_ORDERDATE) >= 1992 AND year(LO_ORDERDATE) <= 1997  GROUP BY  C_CITY  S_CITY  year(LO_ORDERDATE)  ORDER BY  year(LO_ORDERDATE) ASC  revenue DESC;
>
>Q3.4 : SELECT C_CITY  S_CITY  year(LO_ORDERDATE) AS year  sum(LO_REVENUE) AS revenue  FROM lineorder_flat  WHERE (C_CITY = 'UNITED KI1' OR C_CITY = 'UNITED KI5') AND (S_CITY = 'UNITED KI1' OR S_CITY = 'UNITED KI5') AND year(LO_ORDERDATE)*100+month(LO_ORDERDATE) = 199712  GROUP BY  C_CITY  S_CITY  year(LO_ORDERDATE)  ORDER BY  year(LO_ORDERDATE) ASC  revenue DESC;
>
>Q4.1 : SELECT year(LO_ORDERDATE) AS year  C_NATION  sum(LO_REVENUE - LO_SUPPLYCOST) AS profit  FROM lineorder_flat  WHERE C_REGION = 'AMERICA' AND S_REGION = 'AMERICA' AND (P_MFGR = 'MFGR#1' OR P_MFGR = 'MFGR#2')  GROUP BY  year(LO_ORDERDATE)  C_NATION  ORDER BY  year(LO_ORDERDATE) ASC  C_NATION ASC;
>
>Q4.2 : SELECT year(LO_ORDERDATE) AS year  S_NATION  P_CATEGORY  sum(LO_REVENUE - LO_SUPPLYCOST) AS profit  FROM lineorder_flat  WHERE C_REGION = 'AMERICA' AND S_REGION = 'AMERICA' AND (year(LO_ORDERDATE) = 1997 OR year(LO_ORDERDATE) = 1998) AND (P_MFGR = 'MFGR#1' OR P_MFGR = 'MFGR#2')  GROUP BY  year(LO_ORDERDATE)  S_NATION  P_CATEGORY  ORDER BY  year(LO_ORDERDATE) ASC  S_NATION ASC  P_CATEGORY ASC;
>
>Q4.3 : SELECT year(LO_ORDERDATE) AS year  S_CITY  P_BRAND  sum(LO_REVENUE - LO_SUPPLYCOST) AS profit  FROM lineorder_flat  WHERE S_NATION = 'UNITED STATES' AND (year(LO_ORDERDATE) = 1997 OR year(LO_ORDERDATE) = 1998) AND P_CATEGORY = 'MFGR#14'  GROUP BY  year(LO_ORDERDATE)  S_CITY  P_BRAND  ORDER BY  year(LO_ORDERDATE) ASC  S_CITY ASC  P_BRAND ASC;


## 测试方法

将`ssb-10`数据到192.168.40.152节点上，将`ssb-1000`数据导入到192.168.40.223节点上

 使用python脚本分别在Clickhouse和OpenLookeng上执行脚本，每条SQL语句执行间隔为10秒,多次执行取平均值

其中Clickhouse的驱动使用的是开源库clickhouse-driver[^2],OpenLookeng的驱动来源于对web端API的调用，代码已发布于github[^3]



## 测试结果

### ssb-1000

**在OpenLookeng上的执行结果**

| -    | Q1.1    | Q1.2   | Q1.3   | Q2.1     | Q2.2     | Q2.3     | Q3.1     | Q3.2     | Q3.3     | Q3.4   | Q4.1     | Q4.2    | Q4.3    | 总计      |
| ---- | ------- | ------ | ------ | -------- | -------- | -------- | -------- | -------- | -------- | ------ | -------- | ------- | ------- | --------- |
| 1    | 16.480s | 2.610s | 0.636s | 242.400s | 207.000s | 204.600s | 373.800  | 246.600s | 166.800s | 1.930s | 346.800s | 85.200s | 29.890s | 1924.746s |
| 2    | 15.990s | 2.440s | 0.689s | 226.200s | 191.400s | 189.000s | 372.000s | 226.800s | 164.400s | 2.160s | 351.000s | 85.200s | 18.390s | 1845.669s |
| 3    | 16.270s | 2.520s | 0.621s | 231.000s | 199.200s | 193.800s | 369.600s | 241.800s | 169.800s | 2.450s | 346.800s | 74.400s | 27.110s | 1875.371s |

**在Clickhouse上的执行结果**

| -    | Q1.1    | Q1.2   | Q1.3   | Q2.1     | Q2.2    | Q2.3    | Q3.1     | Q3.2     | Q3.3    | Q3.4   | Q4.1     | Q4.2    | Q4.3    | Clickhouse total_use |
| ---- | ------- | ------ | ------ | -------- | ------- | ------- | -------- | -------- | ------- | ------ | -------- | ------- | ------- | -------------------- |
| 1    | 14.036s | 1.491s | 0.388s | 119.684s | 81.551s | 74.901s | 103.358s | 111.153s | 67.420s | 0.564s | 167.159s | 51.108s | 60.336s | 853.149s             |
| 2    | 16.413s | 2.771s | 0.399s | 111.475s | 74.204s | 72.538s | 98.875s  | 108.742s | 61.698s | 0.539s | 149.521s | 48.743s | 49.649s | 795.568              |
| 3    | 17.785s | 1.591s | 0.524s | 117.368s | 79.059s | 70.913s | 100.280s | 103.644s | 57.612s | 0.255s | 154.977s | 43.169s | 16.046s | 763.222s             |


## 结果分析

### ssb-1000

| 序号 | Clickhouse | OpenLookeng | 差异     | 百分比   |
| ---- | ---------- | ----------- | -------- | -------- |
| Q1.1 | 16.08      | 16.25       | -0.17    | -1.05%   |
| Q1.2 | 1.95       | 2.52        | -0.57    | -29.34%  |
| Q1.3 | 0.44       | 0.65        | -0.21    | -48.44%  |
| Q2.1 | 116.18     | 233.20      | -117.02  | -100.73% |
| Q2.2 | 78.27      | 199.20      | -120.93  | -154.50% |
| Q2.3 | 72.78      | 195.80      | -123.02  | -169.02% |
| Q3.1 | 100.84     | 371.80      | -270.96  | -268.71% |
| Q3.2 | 107.85     | 238.40      | -130.55  | -121.06% |
| Q3.3 | 62.24      | 167.00      | -104.76  | -168.30% |
| Q3.4 | 0.45       | 2.18        | -1.73    | -381.59% |
| Q4.1 | 157.22     | 348.20      | -190.98  | -121.47% |
| Q4.2 | 47.67      | 81.60       | -33.93   | -71.16%  |
| Q4.3 | 42.01      | 25.13       | 16.88    | 40.18%   |
| 总计 | 803.98     | 1881.93     | -1077.95 | -134.08% |

![image-20210120002928398](assets/img/notes/tpc/image-20210120002928398.png)

## 执行情况分析

根据查询clickhouse中`system.query_log`表，对后台实际执行的语句进行了分析，发现每在OpenLookeng执行一条sql语句，会在clickhouse中产生2-3条相关查询（忽略获取表时区的查询）

例如Q3.3会对应三次查询，第一次和第三次功能几乎一致，分别如下

```sql
--- 第一次
(SELECT *
FROM (
	SELECT LO_ORDERDATE AS lo_orderdate, LO_REVENUE AS lo_revenue, C_CITY AS c_city, S_CITY AS s_city
	FROM ssb.lineorder_flat
)
WHERE (c_city = CAST('UNITED KI1' AS varchar)
		OR c_city = CAST('UNITED KI5' AS varchar))
	AND (s_city = CAST('UNITED KI1' AS varchar)
		OR s_city = CAST('UNITED KI5' AS varchar))
	AND (toYear(lo_orderdate) >= 1992
		AND toYear(lo_orderdate) <= 1997))
FORMAT TabSeparatedWithNamesAndTypes;


--- 第二次
(SELECT year, c_city, s_city, CAST(sum(lo_revenue) AS bigint) AS sum
FROM (
	SELECT c_city, s_city, toYear(lo_orderdate) AS year, lo_revenue
	FROM (
		SELECT *
		FROM (
			SELECT LO_ORDERDATE AS lo_orderdate, LO_REVENUE AS lo_revenue, C_CITY AS c_city, S_CITY AS s_city
			FROM ssb.lineorder_flat
		)
		WHERE (c_city = CAST('UNITED KI1' AS varchar)
				OR c_city = CAST('UNITED KI5' AS varchar))
			AND (s_city = CAST('UNITED KI1' AS varchar)
				OR s_city = CAST('UNITED KI5' AS varchar))
			AND (toYear(lo_orderdate) >= 1992
				AND toYear(lo_orderdate) <= 1997)
	)
)
GROUP BY year, c_city, s_city)
FORMAT TabSeparatedWithNamesAndTypes;

--- 第三次
SELECT lo_orderdate, lo_revenue, c_city, s_city
FROM (
	SELECT *
	FROM (
		SELECT LO_ORDERDATE AS lo_orderdate, LO_REVENUE AS lo_revenue, C_CITY AS c_city, S_CITY AS s_city
		FROM ssb.lineorder_flat
	)
	WHERE (c_city = CAST('UNITED KI1' AS varchar)
			OR c_city = CAST('UNITED KI5' AS varchar))
		AND (s_city = CAST('UNITED KI1' AS varchar)
			OR s_city = CAST('UNITED KI5' AS varchar))
		AND (toYear(lo_orderdate) >= 1992
			AND toYear(lo_orderdate) <= 1997)
)
FORMAT TabSeparatedWithNamesAndTypes;
```

具体详情见[OpenLookeng-clickhouse connector测试](https://docs.qq.com/sheet/DVWhGUXd0UXdmalJ6?scene=zg7fg4Go5RX1Nh6fg4OBFfg4f0e9n1&tab=BB08J2)

## 结论

在千万量级的数据下执行sql查询，OpenLookeng相对Clickhouse的查询时间平均慢193.07%，最好情况下慢85%，最差情况下慢778%。

在十亿量级下执行sql查询，平均慢134.08%，最好的情况优于clickhouse 40%，最差的情况比clickhouse慢381.59%




[^1]: https://github.com/vadimtk/ssb-dbgen
[^2]: https://github.com/mymarilyn/clickhouse-driver
[^3]: https://github.com/jiangph1001/OpenLookeng-driver
[^4]: https://clickhouse.tech/benchmark/dbms/
[^5]: https://gitee.com/heatao/hetu-core

