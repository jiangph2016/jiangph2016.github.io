---
layout: post
title: Curator:Self-Managing Storage for Enterprise Clusters
category: 学术
tags: 论文
keywords: curator,集群,存储
---

* content
{:toc}


类型   |内容  
-- |--  
标题 |Curator:Self-Managing Storage for Enterprise Clusters
时间 |2017
会议 |Symposium on Network System Design and Implementation
引用 |Cano I, Aiyar S, Arora V, et al. Curator: Self-managing storage for enterprise clusters[C]//14th {USENIX} Symposium on Networked Systems Design and Implementation ({NSDI} 17). 2017: 51-66.

## 概览

作者构建了一个名为Curator的分布式集群存储管理系统。
它拥有以下功能：
- 数据迁移
- 恢复任务
- 空间回收
- 数据转换

这套系统在客户的集群中应用后，有明显的性能提升（主要表现在IO延迟的减少）和稳定性的提升。


## 背景
作者组成：2名来自华盛顿大学，8名来自Nutanix公司。

[Nutanix](https://www.nutanix.cn/)（路坦力）公司在2009年成立，是一家提供超融合解决方案的设备厂商，他们的产品有两种形态：1、捆绑式的硬件 + 软件设备，2、纯软件模式  
它在2013年进入中国市场，2016年和联想合作
  
在2017年发表的文章[SDN实战团分享（三十三）：Nutanix超融合之架构设计](https://www.sdnlab.com/18555.html)里面就已经讲到了Curator，作为超融合集群中的一个组件来运行。

Curator将负责整个集群中存储的管理和分配，包括磁盘平衡、主动清理等任务。每个节点上d都会运行Curator进程，而且受主Curator的控制，主Curator会负责任务和作业的委派。

## 结构
![curator1](/assets/img/academic/curator1.png)
- DSF（distributed storage fabric）:分布式存储结构。
- vDisk: 映射到UVM中的虚拟块设备。（例如用户在虚拟机中看到磁盘大小是20G，但实际上这块磁盘并不存在，而是属于底层某一个真实的硬盘）
- extent：vDisk块中的数据。
- extent组：多个extent被分组为一个单元称为extent组。
![curator1](/assets/img/academic/curator2.png)
## 功能

### 恢复任务（Recovery）

当出现磁盘故障或者磁盘被人为移除时，Curator将启动元数据扫描。  
找到那些【故障磁盘】上存储的数据的备份。  
通知底层系统，对备份不足的数据进行备份。  
 
### 数据迁移（Data Migration）

**数据分层**：
目的：将最常用的数据仅保留在最快的存储层中，以减少用户访问的延迟。
向下迁移：将不常用的数据从SSD移动到HDD（或从HDD移动到云） 
向上迁移：在重复访问数据时开始迁移

**磁盘平衡**：

使不同磁盘的利用率接近于平均利用率。（数据横向迁移）

### 空间回收（Space Reclamation）

**垃圾回收（GC，Garbage Collection）**：即减少碎片空间
- 迁移extent：将活动的extent迁移到新的extent组，删除旧的extent组，然后回收旧的extent组的垃圾。 这个操作成本很高，因为它涉及数据读取和写入。 因此，Curator针对每个extent组执行成本收益分析，并仅选择收益（扩展组中的死区数量）大于成本（被迁移的有效extent的空间总和）的extent组进行迁移。
- 打包extent：尝试在一个extent组中打包尽可能多的活动extent。
- 截断extent组：通过截断extent组来回收空间，即减小其空间大小。

**数据删除（DR,Data Removal）**：即删除操作失败导致的空指针。

### 数据转换（Data Transformation）

目的：节省存储空间  
**压缩（C，Compression）和纠删码（EC，Erasure Coding）**
**重复数据删除（DD,Deduplication）**：在扫描时，Curator根据指纹检测出重复的数据，并通知DSF执行重复数据删除。
**快照树深度缩减（STR，Snapshot Tree Reduction）**：
如果打的快照过多，可能导致快照树很深，以至于读取效率很低。  
解决方法是将部分数据从父节点复制到子节点。  

### 执行策略

- 事件驱动：例如磁盘发生故障时，立即进行恢复任务
- 基于阈值：当个别磁盘使用率过高时，进行数据迁移
- 定期部分扫描
- 定期完整扫描


## 评估

### 评估恢复任务
大约60％的集群不存在数据备份不足的问题，95％的集群最多具有约为0.1％的备份不足的数据。显示了平均未复制数据的累积分布函数（CDF）占客户集群中总体存储容量的百分比（对数规模）。作者对剩余的40%集群进行观测，两周内并没有出现意外的停机事故。
![](/assets/img/academic/curator3.png)


### 评估数据分层
40％的集群的SSD利用率最多为70-75％。 在其余60％的集群中，大部分集群的SSD使用率在75%左右，这表明分层的任务已经被执行。  
HDD的利用率通常较低，其中80％的集群的HDD利用率低于50％。
![](/assets/img/academic/curatorf4.png)


### 评估空间回收
90％的集群的垃圾少于2％，这证明垃圾收集任务非常有效。
### 评估磁盘平衡
60％的SSD和80％的HDD的最大磁盘使用率几乎与平均值相同。
![](/assets/img/academic/curator5.png)

### 是否启用-对比评估



Curator关闭时，所有数据优先存入SSD，在125分钟左右时存满，数据开始存入HDD。  
在Curator启用时，当存入的数据到达一定阈值时，分层任务被启动，最终使SSD的使用率降到了70%。
![](/assets/img/academic/curator6.png)

当Curator打开时，延迟平均为12毫秒，关闭时，延迟平均为62毫秒。如果禁用Curator，随着时间的增长，延迟会逐渐增加。 推测这是因为SSD已经满了，所以新来的数据直接写入HDD，因此在读写操作时会产生较高的延迟。

当Curator打开时，CPU使用率会略高。 这是由于Curator内部的mapReduce架构导致的。
![1](/assets/img/academic/curator9.png)

## 基于机器学习的改进

作者使用Q-learning算法对Curator进行了改进，但是这项技术尚未部署到Curator上。

Q-learning方法在IOPS的数量方面有一定的增长，但延迟却显著的减少。
![1](/assets/img/academic/curator7.png)

## 不足

仅讲解了自己系统的设计方案和性能，而没有与其他的集群存储管理系统的性能进行横向对比






