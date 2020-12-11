---
layout: post
title: Clickhouse性能测试记录
category: 学习
tags: Linux
keywords: TPC
---
* content
{:toc}

地址:<http://tpc.org/information/benchmarks5.asp>



## TPC-DS

TPC-DS采用星型、雪花型等多维数据模式。它包含7张事实表，17张纬度表平均每张表含有18列。其工作负载包含99个SQL查询，覆盖SQL99和2003的核心部分以及OLAP。这个测试集包含对大数据集的统计、报表生成、联机查询、数据挖掘等复杂应用，测试用的数据和值是有倾斜的，与真实数据一致。可以说TPC-DS是与真实场景非常接近的一个测试集，也是难度较大的一个测试集。

## TPC-H
TPC-H 是一个决策支持benchmark。它由一套面向业务的即席查询和并发数据修改组成。数据库中的查询和数据被选择具有广泛的行业范围的相关性。这个基准测试说明了决策支持系统，这些系统检查大量数据，执行高度复杂的查询，并给出关键业务问题的答案。

TPC-H报告的性能度量称为TPC-H复合查询每小时性能度量(Query-per-hour Performance Metric,QphH@size)，反映了系统处理查询的能力的多个方面。这些方面包括执行查询所依据的选定数据库大小、由单个流提交查询时的查询处理能力以及当多个并发用户提交查询时的查询吞吐量。TPC-H价格/性能指标表示为$/QphH@size。
