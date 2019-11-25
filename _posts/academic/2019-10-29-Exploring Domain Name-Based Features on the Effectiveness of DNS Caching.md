---
layout: post
title: Exploring Domain Name-Based Features on the Effectiveness of DNS Caching 
category: 学术
tags: 论文
keywords: domains dns once-used 一次性域名
---

* content
{:toc}


类型|内容
--|--
标题|Exploring Domain Name Based Features on the Effectiveness of DNS Caching
时间|2017
会议|ACM SIGCOMM Computer Communication Review
DOI|10.1145/3041027.3041032
引用信息|Hao S, Wang H. Exploring domain name based features on the effectiveness of dns caching[J]. ACM SIGCOMM Computer Communication Review, 2017, 47(1): 36-42.

这篇文章的题目翻译成中文应是《基于dns缓存有效性的域名特征研究》，读起来比较拗口，但其本质目的在于根据域名的特征将域名划分为两类：一次性域名（disposable domains/one-time-used domains/once-used domains）和可重用域名(reused domains)，并且让DNS缓存器不再缓存一次性域名以提高性能。

文章中提到DNS是互联网中的一项基本的服务，所以其在互联网中的流量很少受到阻塞。于是在互联网上就发现了对DNS的滥用情况，这种滥用情况就是利用大量的一次性域名来传递消息。

一次性域名不一定是恶意域名，它广泛存在于各类服务提供商中，包括流行的搜索引擎、社交网络、CDN和安全公司。但由于此类域名的使用越来越多，所以DNS缓存将充满这种一次性域名，而这种域名几乎不会被再次使用，这就导致了DNS缓存的性能下降。

一次性域名的特征一般比较明显，一个是长度会明显长于正常的域名，其次是具有较多的内容分隔符如“.和“-”，当“.”出现的次数多的时候也可以理解为有较多的子域。

