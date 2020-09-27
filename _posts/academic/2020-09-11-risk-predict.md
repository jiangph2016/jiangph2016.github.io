---
layout: post
title: 【精读】On the Effectiveness of Risk Prediction Based on Users Browsing Behavior
category: 学术
tags: 论文
keywords: behavior
---
* content
{:toc}

类型  |内容  
-- |--  
标题 | OTTer: A Scalable High-Resolution Encrypted Traffic Identification Engine
时间 | 2018
会议 | RAID
DOI | 10.1007/978-3-030-00470-5_15

## 简介

恶意网站的比例在增加，但是恶意网站并没有影响到所有人，有些人可能一直没有遇到过恶意网站。  
用户的浏览习惯与访问潜在有害页面之间存在相关性

使用Symantec收集的数据，在92天的时间里，对160229位用户访问的页面（仅包括浏览器访问的URL）进行了分析。用户分成两类，一类是从未访问过恶意网站的，另一类是访问过的。总共提取了74个特征用于总结用户浏览行为。

用户活动量是风险水平的最佳指标之一。 一个人每天浏览的页面越多，种类越多样化，越有可能访问恶意网站。   
在周末更容易遇到恶意页面
并且处于风险级别的人在夜间更活跃。 通过查看网站类别，我们发现其中某些类别（例如成人内容和缩短的URL）与存在风险的可能性呈正相关。 最后，我们执行的实验结果表明，仅通过分析浏览数据，就可以组合所有这些信息并训练分类器来预测用户是否有风险


## 数据集

数据来自Symantec，这些数据来自于愿意参加共享使用情况统计和遇到的威胁信息的用户。  
数据集包括2013年8月1日-2013年10月31日约160,000个用户的web请求。共有202,306,687次URL访问，涵盖了37,797,151个不同的URL。
研究范围是三个月内至少访问了100个页面的用户。  

### 数据标签

风险定义：从Norton SafeWeb和Google SafeBrowsing获取恶意URL数据集，并结合<amada.abuse.ch>，malc0de,<www.malwaredomainlist.com>
将URL分为三类：良性、恶意、域名在黑名单出现过的URL
### 风险分类
从未访问过的恶意域名的用户是安全用户，定义一个用户`at risk`通过他在三个月内访问了至少2个不同的恶意URL或者三个列入黑名单的域。否则都是属于uncertain类别


![](/assets/img/academic/risk_predict/T1.png)


## 分析

![](/assets/img/academic/risk_predict/F1.png)
在周末，总的网页访问的次数减少，但是访问恶意网站的次数增加了。访问到恶意网站的概率增加了10%

![](/assets/img/academic/risk_predict/F2.png)
在夜间访问恶意网站的百分比有所增加。

![](/assets/img/academic/risk_predict/T2.png)
日本恶意网站访问概率最低，风险用户的比例最低。

## 特征提取

一个人花在浏览Web上的时间越长，遇到恶意网站的可能性就越大。  
访问的不同URL数量，域名数量。  


网站类别：使用Symantec、Alexa、Open Directory Project提供的网站分类服务，对URL进行分类。
并使用一些第三方服务来补充，例如`http://longurl.org/services`,`http://www.tblop.com/`,` http://www.torrentresource.com`,`http://xboxpirate.eu/forums/topic/280-list-of-file-hosting-and-sharing-websites-137-entries/`

![](/assets/img/academic/risk_predict/T3.png)

## 评价

