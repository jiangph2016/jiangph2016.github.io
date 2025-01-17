---
layout: post
title: 论文记录
category: 学术
tags: 粗读
keywords: 粗读
---
* content
{:toc}

此页用于记录一些看到的比较好的论文,作为自己的论文库


## 综述

### 深度包检测中正则表达式匹配的算法、应用和硬件平台的综述


2016 S&T  
原文地址：[A Survey on Regular Expression Matching for Deep Packet Inspection: Applications, Algorithms, and Hardware Platforms](https://ieeexplore.ieee.org/document/7468531)

摘要：
>本次调查从DPI的全面应用背景和正则表达式匹配的技术背景入手，为读者提供一个全局视图和基本知识。然后分析了有限状态自动机用于正则表达式匹配的状态爆炸对正则表达式匹配的挑战。详细分析了状态爆炸的本质，将最新的解决方案分为缓解状态爆炸的方法和避免状态爆炸的方法两大类，并提出了在不同场景下构建紧凑高效自动机的建议。此外，还介绍并深入讨论了采用并行平台（包括现场可编程门阵列、GPU、通用多处理器和三值内容寻址存储器）加速匹配过程的建议。我们还为每个平台的高效部署提供了指导。

### 基于模型的定量网络安全度量研究综述

2017 S&T
原文地址：[Model-Based Quantitative Network Security Metrics: A Survey](https://ieeexplore.ieee.org/document/8017389)  

摘要：
>基于模型的网络安全度量（NSMs）可以定量评估网络系统抵御攻击的总体弹性。因此，这些度量对于组织的安全相关决策过程非常重要。考虑到近20年来已经提出了一些基于模型的定量NSMs，本文对这些方案的研究现状进行了深入的综述。首先，为了将本调查中描述的安全度量与其他类型的安全度量区分开来，本文概述了安全度量及其分类。然后，详细介绍了现有的基于模型的定量NSMS，以及它们的优缺点。最后，对本次调查的相关特点进行了深入的探讨，提出了本次调查的建议和课题的开放性研究问题。

### 自适应比特率选择的综述

2017 S&T
原文地址：[Adaptive Bitrate Selection: A Survey](https://ieeexplore.ieee.org/document/7976298)

摘要：
>HTTP自适应流（HAS）是最新的视频质量自适应尝试。它支持廉价且易于实现的流媒体技术，而无需专用的基础设施。通过TCP和HTTP的组合，它具有所有为普通Web设计的现有技术的优点。同样重要的是，流量可以通过防火墙，并且在部署NAT时工作良好。HAS的速率自适应控制器，通常称为自适应比特率选择（ABR），目前正受到业界和学术界的广泛关注。然而，大多数研究工作都集中在某一特定方面或某一特定方法上，而没有考虑到整个背景。本文综述了基于客户端HTTP的自适应视频流领域最重要的研究活动。它首先将ABR模块分解为三个子组件，即：资源估计功能、块请求调度和自适应模块。每个子组件封装对ABR方案的操作至关重要的特定函数。对每一个子组件以及它们如何相互作用进行了回顾。此外，还讨论了已知对ABR模块的性能有直接影响的外部因素，例如内容性质、CDN和上下文。总之，本文为该领域的进一步研究提供了广泛的参考。


### 利用机器学习技术进行入侵检测的详细调查与分析

2018 S&T
原文地址: [A Detailed Investigation and Analysis of Using Machine Learning Techniques for Intrusion Detection](https://ieeexplore.ieee.org/document/8386762)



### 入侵检测系统：跨域综述

2019 S&T
原文地址：[Intrusion Detection Systems: A Cross-Domain Overview](https://ieeexplore.ieee.org/document/8735821)

摘要：
>如今，网络技术对于传输和存储用户、公司和行业的各种信息至关重要。然而，信息传输速率的增长扩大了攻击面，为入侵者提供了丰富的攻击环境。入侵检测系统（IDSs）是一种广泛应用的系统，它能够在一定的主机和网络范围内被动或主动地控制入侵活动。近年来，通过集成各种检测技术（通用的或适用于特定领域的）和攻击的性质，人们提出了不同的ids。网络安全领域处理的是大量多样的事件流，这些事件流以指数形式增加了攻击向量。事件流处理（ESP）方法似乎是利用事件流提供可操作的洞察力和更快的检测的解决方案。在这篇文章中，我们简要描述了最近的论文所基于的域（以及它们的漏洞）。我们还调查了脆弱性评估和攻击分类的标准。然后，我们对IDSs、评估指标和数据集进行分类。接下来，我们将提供技术细节，并对IDS技术和ESP方法的最新工作进行评估，这些技术涉及不同的维度（axes）：域、体系结构和本地通信技术。最后，我们讨论了在准确性、性能和健壮性方面改进IDS的挑战和策略


## NDSS

### Kitsune: An Ensemble of Autoencoders for Online Network Intrusion Detection.
NDSS 2018
关于IDS
<https://arxiv.xilesou.top/pdf/1802.09089>

## www2019


### A First Look at Deep Learning Apps on Smartphones

原文地址: <https://dl.acm.org/doi/abs/10.1145/3308558.3313591>

### Detect Rumors on Twitter by Promoting Information Campaigns with Generative Adversarial Learning
### Exploring User Behavior in Email Re-Finding Tasks
### Exploiting Ratings, Reviews and Relationships for Item Recommendations in Topic Based Social Networks
### Nonlinear diffusions for community detection and semi-supervised learning
### Graph Neural Networks for Social Recommendation
### Listening between the Lines: Learning Personal Attributes from Conversations
### Stereotypical Bias Removal for Hate Speech Detection Task using Knowledge-based Generalizations
### Cross-Network Embedding for Multi-Network Alignment
### Anomaly detection in the dynamics of web and social networks using associative memory
### From Small-scale to Large-scale Text Classification
### CommunityGAN: Community Detection with Generative Adversarial Nets
### BaG: Behavior-aware Group Detection in Crowded Urban Spaces using WiFi Probes
### ContraVis: Contrastive and Visual Topic Modeling for Comparing Document Collections
### Unsupervised Semantic Generative Adversarial Networks for Expert Retrieval

### Revisiting User Mobility and Social Relationships in LBSNs: A Hypergraph Embedding Approach
### To Return or to Explore: Modelling Human Mobility and Dynamics in Cyberspace
### Information Dissemination in WhatsApp: Gathering, Analyzing and Countermeasures
### Keyphrase Extraction from Disaster-related Tweets
### Unifying Knowledge Graph Learning and Recommendation: Towards a Better Understanding of User Preference
### What happened? The Spread of Fake News Publisher Content During the 2016 Election
### Quality Effects on User Preferences and Behaviors in Mobile News Streaming
### Aspect-level Sentiment Analysis using AS-Capsules
### The Silent Majority Speaks: Inferring Silent Users’ Opinions in Online Social Networks
通过活跃用户发布的文本内容和用户关系来腿推断沉默用户在社交网络中的意见
<https://dl.acm.org/doi/abs/10.1145/3308558.3313423>
### Your Style Your Identity: Leveraging Writing and Photography Styles for Drug Trafficker Identification in Darknet Markets over Attributed Heterogeneous Information Network
### Fuzzy Multi-task Learning for Hate Speech Type Identification
### Learning clusters through information diffusion
### A Novel Generative Topic Embedding Model by Introducing Network Communities
### With a little help from my friends (and their friends): Influence neighborhoods for social recommendations
---

### A First Look at Deep Learning Apps on Smartphones
### Web Experience in Operational Mobile Networks: Lessons from Two Million Page Visits
×
### Multistream Classification for Cyber Threat Data with Heterogeneous Feature Space
×
----


## WWW 2018
<https://dl.acm.org/doi/proceedings/10.5555/3178876>

### Session details: Social Network Analysis and Graph Algorithms for the Web
<https://dl.acm.org/doi/10.5555/3178876.3258518>

### RaRE: Social Rank Regulated Large-scale Network Embedding
<https://dl.acm.org/doi/10.1145/3178876.3186102>  
传统的网络节点嵌入算法根据节点的连接属性，但是有些受欢迎的节点例如名人有大量的followers，所以文章提出了social rank，并提出新的网络嵌入模型，同时考虑基于邻近度和基于social rank。


### Minimizing Polarization and Disagreement in Social Networks
<https://dl.acm.org/doi/10.1145/3178876.3186103>  
提出一个推荐系统来减少社交网络中的意见的两极分化

### SIR-Hawkes: Linking Epidemic Models and Hawkes Processes to Model Diffusions in Finite Populations

<https://dl.acm.org/doi/10.1145/3178876.3186108>

### A Correlation Clustering Framework for Community Detection
一个名为LAMBDACC的社区检测框架  
<https://dl.acm.org/doi/10.1145/3178876.3186110>

### Collective Classification of Spam Campaigners on Twitter: A Hierarchical Meta-Path Based Approach
检测Twitter上的垃圾邮件发送者
<https://dl.acm.org/doi/10.1145/3178876.3186119>

### Demarcating Endogenous and Exogenous Opinion Diffusion Process on Social Networks
OSN中的网络意见传播受两种类型的意见影响，分别是内在意见(endogenous opinion)和外在意见(exogenous opinion)。文章提出了CherryPick，对意见和用户进行划分  
<https://dl.acm.org/doi/10.1145/3178876.3186121>


## BM风险

### SoK: Cyber Insurance – Technical Challenges and a System Security Roadmap


## AsiaCCS
<https://dblp.uni-trier.de/db/conf/asiaccs/index.html>  

### TweetScore: Scoring Tweets via Social Attribute Relationships for Twitter Spammer Detection

<https://dl.acm.org/doi/10.1145/3321705.3329836>

### On the effectiveness of risk prediction based on users browsing behavior
<https://dl.acm.org/doi/10.1145/2590296.2590347>


## Other

### Cyber Profiles Based Risk Prediction of Application Systems for Effective Access Control
<https://ieeexplore.ieee.org/abstract/document/8969646>
