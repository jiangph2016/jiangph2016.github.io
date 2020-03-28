---
layout: post
title: 【精读】在微信加密流量中识别用户行为
category: 学术
tags: 论文
keywords: traffic
---
* content
{:toc}


类型  |内容  
-- |--  
标题 |Classifying User Activities in the Encrypted WeChat Traffic
时间 |2018
会议 |IPCCC
引用 |C. Hou, J. Shi, C. Kang, Z. Cao and X. Gang, "Classifying User Activities in the Encrypted WeChat Traffic," 2018 IEEE 37th International Performance Computing and Communications Conference (IPCCC), Orlando, FL, USA, 2018, pp. 1-8.


## 摘要
微信使用名为MMTLS的专有加密协议进行通信，该协议基于TLS 1.3。我们探索了MMTLS加密通道内典型用户活动的细粒度分类，并将MMTLS与微信中同样出现的HTTPS进行了比较（例如流量持续时间和数据包大小）。
发现MMTLS适用于低延迟和轻量级消息传递的场景。 根据不同用户从不同平台（Android，iOS）和设备（华为，三星，iPhone，iPad等）收集的微信流量，我们对七种典型活动（如付款、广告点击、浏览等）进行了分类。 实验结果表明，平均精度和召回率均可以达到92％以上。 我们是第一个对该专有加密协议进行分类并了解MMTLS和TLS之间区别的。

## 介绍
在不解密用户内容的情况下，通过流量分析识别应用程序内部的用户行为。  
SSL有很多的开发库，如OpenSSL、GNUTLS和LibreSSL。  
许多社交应用程序使用非SSL安全协议来提高效率，增强隐私和安全性，包括微信MMTLS协议、电报MTP协议以及社交应用程序（如Signal、WhatsApp和Messenger）使用的信号协议。
微信使用了轻量级的MMTLS来提高资源使用效率和节省服务器资源，因为维持长时间的网络连接对服务器来说是一个负担，且许多会话都是短暂的。  
微信6.6.x以上版本在TLS 1.3草案的基础上，采用了一种新的专有安全协议MMTLS作为主要的加密协议。MMTLS握手保留了TLS机制，包括0-RTT模式和1-RTT模式。微信在不同的服务场景中混合使用MMTLS和SSL/TLS。  
首先，我们研究MMTLS协议和由MMTLS加密的微信流量。 然后，我们比较HTTPS和MMTLS之间的网络特征，并通过流量分析进行细粒度的用户活动分类。 结果表明，使用流量的基本统计属性可以准确识别用户活动。 该论文的贡献有两个方面：一方面，深入分析了MMTLS的安全性和使用场景，并与HTTPS进行了比较。我们发现MMTLS协议主要是用于对非web访问的用户活动进行加密。另一方面，我们使用五个分类模型来进行细粒度的用户活动分类并比较它们的性能。分类器有决策树、随机森林、朴素贝叶斯、logistic回归和支持向量机。结果表明，随机森林分类器的性能最好，达到了92%以上的准确率和召回率。

## 相关工作
相关工作分为加密流量分析，包括加密网站指纹识别、加密应用识别和加密用户活动分类

在以往的文献中，对加密流量的分析一直是研究加密网站指纹识别的重点。文献[^7]首先研究了加密的网络标识。通过收集目标网站的请求大小和html文件大小，推断出用户访问的网页。后续的文献[^8]计算从攻击者和受害者获取的观察到的网页对象的Jaccard系数。随后的研究扩展到VPN（例如SSH隧道）和Tor[^9][^10][^11][^12]中的指纹网站传输，其中Liberatore和Levine使用了朴素Bayes分类器[^11]。在[^9]中，提取包序列信息以增强对网站的指纹识别。Panchenko等人。提出了一种称为CUMUL的方法，它利用了累积的包长度[12]。冈萨雷斯等人。通过在第一级页面而不是主页上指纹来执行更精确的分析[13]。Hayes和Danezis提出了一种将捕获指纹的汉明距离与目标指纹进行比较的方法[14]。


