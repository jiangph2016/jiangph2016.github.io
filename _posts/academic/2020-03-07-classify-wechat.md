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