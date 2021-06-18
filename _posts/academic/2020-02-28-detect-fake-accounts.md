---
layout: post
title: 【精读】在注册时检测微信上的恶意账户
category: 学术
tags: 论文
keywords: Social
---
* content
{:toc}

类型  | 内容  
-- | --  
标题 |Detecting Fake Accounts in Online Social Networks at the Time of Registrations  
时间 | 2019
会议 | CCS
DOI | 10.1145/3319535.3363198
引用 |Yuan D, Miao Y, Gong N Z, et al. Detecting Fake Accounts in Online Social Networks at the Time of Registrations[C]://Proceedings of the 2019 ACM SIGSAC Conference on Computer and Communications Security. 2019: 1423-1438.

## 论文信息
<https://zhuanlan.zhihu.com/p/33066384>
### 作者信息
Dong Yuan,Yuanli Miao,Neil Zhenqiang Gong,Zheng Yang,Qi Li,Dawn Song,Qian Wang,Xiao Liang 
清华大学网络科学与网络空间研究院
北京国家信息科学技术研究中心(BNRist)
杜克大学电气与计算机工程系
加州大学伯克利分校计算机科学系
武汉大学网络科学与工程学院
腾讯公司
## 摘要

社交网络上存在的虚假账户（Sybils），散布垃圾邮件、恶意软件和虚假信息。  
目前Sybils的检测存在延迟，在被检测到的时候已经执行了很多恶意活动。   
本论文提出名为Ianus的方法，利用账户注册信息来检测Sybil。  
Ianus的目标是在它们注册后立即捕获它们。首先，利用中国最大的在线社交网络微信（WeChat）上带有Sybils标签的真实注册数据集，对Sybils和良性用户的注册模式进行了测量研究。我们发现Sybils倾向于**synchronized**和**abnormal**注册模式。  
第二，根据测量结果，我们将Sybil检测建模为一个图推理问题，这使得我们能够集成异构特征。我们为每对帐户提取基于synchronized和异常的特征，使用这些特征构建一个图，其中Sybils彼此紧密连接，而良性用户与其他良性用户和Sybils隔离或解析连接，最后通过分析图的结构来检测Sybils。  
我们使用微信的真实注册数据集评估用户。此外，微信部署Ianus为每日运行，即微信每天都会使用Ianus分析新注册的账户，我们发现Ianus每天可以对40万个新注册的账户进行检测，通过微信安全团队的手动验证，平均精度超过96%。

## 介绍

如今，在线社交网络（例如Facebook和微信）特别受欢迎，这对我们的日常生活产生了巨大影响。 同时，它们也是攻击者的重要目标。 例如，在Sybil攻击中，攻击者注册并维护着大量的假帐户，以执行各种恶意活动，例如传播垃圾邮件，网络钓鱼URL，恶意软件和虚假信息以及窃取私人用户数据。  
目前已经开发了很多检测在线社交网络中的Sybils的方法。 这些方法利用了由Sybils生成的内容（例如URL in tweets），行为（例如，点击流，喜欢，照片上传）和/或社交图。 它们面临一个局限性：在检测Sybils时会产生大量延迟。   
即要求Sybils在检测到它们之前先生成丰富的内容，行为和/或社交图。 因此，Sybils在被发现之前可能已经进行了各种恶意活动。

**本文的目标是在注册时检测到Sybils。**  
首先，我们利用微信的一个匿名的注册数据集，对Sybils和良性用户的注册模式进行了系统的测量研究。数据集收集于2017年11月，其中良性用户有77万，Sybils用户有64.7万。每个注册都有一个属性列表，如IP地址、电话号码（可在微信中用作用户ID）、设备ID（如IMEI）、昵称等等。我们发现Sybils有相同的关于这些属性的synchronized注册模式。 例如，许多Sybils用24位相同前缀的IP地址进行注册，这意味着它们可能使用在同一局域网中的设备进行注册。
但是，synchronized不足以区分Sybils和良性用户。 尤其是某些良性用户还共享synchronized的注册属性，例如他们使用相同前缀的IP进行注册。

其次，我们设计了Ianus来通过注册信息检测Sybils，一个关键的挑战是如何集成同步和异常的注册模式，Ianus用图推理技术来解决这个挑战。我们构建图的方式为：每个节点都是一个帐户，Sybils相互紧密连接，而良性帐户之间稀疏连接，也与Sybils稀疏连接。 Ianus具有三个组成部分，即特征提取，图形构建和Sybil检测。 在特征提取中，对于一对帐户，我们提取基于二进制同步的特征（例如，两个帐户使用同一设备进行注册）和基于异常的特征（例如，共享设备异常）。

在图构建中，我们使用基于同步的特征和基于异常的特征在帐户之间构建加权图。 具体来说，我们根据其特征为一对帐户分配sync-anomaly分数。 sync-anomaly评分可表征两个帐户之间的同步和异常模式，如果一对帐户具有更多同步和异常注册特征，则它们具有较高的同步异常评分。为一对帐户分配sync-anomaly分数的一种方法是对它们的二进制特征求和。 但是这种基于特征和的方法不会考虑特征的权重。 因此，我们使用机器学习（尤其是逻辑回归）来学习自动加权不同特征的sync-anomaly分数。接下来我们使用sync-anomaly分数在帐户之间创建边。 我们要构建一个Sybil之间紧密连接的图，仅当两个帐户都根据其sync-anomaly分数被预测为Sybil时，才在两个帐户之间创建边。将sync-anomaly分数视为边的权重。

在Sybil检测组件中，社区检测是检测Sybil的自然选择，因为Sybil是紧密连接在我们的图中的。 此外，我们设计了一种基于加权节点度的简单方法来检测Sybils。 具体而言，如果图中的节点通过具有较大权重的边连接到更多Sybil，这表明该节点更有可能是Sybil。 因此，如果节点的加权度足够大，则Ianus可以将其预测为Sybil。

第三，我们使用微信的注册数据集对Ianus进行评估，Ianus能够检测出大部分的Sybils（召回率为80.2%），准确率高达92.4%。此外，我们对Ianus的每个组件的不同设计都进行了广泛评估了。 具体来说，在特征提取中，我们的结果表明，基于同步的特征和基于异常的特征是互补的，将它们结合起来确实可以提高检测精度。 在图的构建中，我们发现使用**逻辑回归**学习的sync-anomaly分数要优于基于**特征和**的sync-anomaly分数。  
在Sybil检测组件中，我们评估了流行的Louvain方法作为一种社区检测方法。实验结果表明，Louvain方法和基于加权节点度的方法具有很高的检测精度。然而，基于加权节点度的方法比基于Louvain的方法效率更高，例如，Louvain方法分析图需要40分钟，而基于加权节点度的方法只需要10分钟。微信每天都会部署Ianus，即用Ianus在每天注册的账户中检测Sybils。微信安全团队确认Ianus每天可以在百万个账户中检测到40万个Sybils，准确度超过96%。

贡献：
- 我们进行了大规模的测量研究，以表征Sybils和良性用户的注册模式。我们发现Sybils有同步和异常注册模式
- 我们提出了利用注册数据检测Sybils的方法，基于同步模式和异常模式建立帐户的加权图，然后通过分析图的结构来检测Sybils。
- 我们使用来自微信的注册数据集评估Ianus。此外微信已部署Ianus，展示了Ianus的工业影响力。

## 相关工作
现有的在线社交网络Sybil检测方法[^2][^3][^6][^10][^11][^12][^13][^16][^17][^18][^20][^27][^28][^29][^30][^33][^34][^37][^38][^39][^40][^47][^49][^50][^51][^54][^55][^56][^57][^58]主要利用Sybils生成的内容、行为和/或社交图。基于内容和行为的方法[^12][^16][^18][^30][^33][^38][^40][^44][^49]通常将Sybil检测建模为二进制分类问题，并利用机器学习技术。具体来说，他们首先从用户的内容中提取特征（例如，帖子，推特）或行为（发推，喜欢，照片上传和点击流的频率）。然后，他们通过有监督的机器学习技术（如logistic回归）或无监督的机器学习技术（如社区检测、聚类）分析特征来检测Sybils。例如，SynchroTrap[^12]利用集群技术检测基于同步用户行为的Sybils，例如照片上传。

EvilCohort [^34]利用用户的IP地址来检测Sybil。具体地说，给定用户的登录名，EvilCohort会构建一个用户IP双向图，其中用户与IP之间的边缘表示该用户曾经从IP登录。 然后，EvilCohort使用Louvain方法在用户IP图中检测Sybil。 EvilCohort可能需要多个用户登录，以便Sybils在用户IP双向图中形成社区。 在多次登录期间，Sybils可能已经执行了恶意活动。 除了注册，我们的方法不需要用户登录。

基于社交图的方法[^2][^6][^10][^11][^13][^17][^20][^27][^28][^37][^39][^50][^51][^54][^55][^57][^58]利用基于图的机器学习技术（例如随机游走[^6][^11][^13][^23][^28][^50][^51][^54][^55]，信念传播[^19][^20][^41][^42][^43]和社区发现[^10][^39]）来分析社会结构 用户之间的图表。 这些方法的主要局限性在于它们依赖于Sybils生成的丰富的内容，行为和/或社交图谱，从而导致检测Sybils的时间大大延迟。

Hao等人[^22]提出了PREDATOR在注册时检测恶意域名。 检测恶意域和检测Sybil需要不同的特征，例如Ianus利用了来自移动设备，操作系统，用户位置和昵称的特征。 Xie等人[^48]使用Souche尽早识别合法用户。 与Ianus不同，Souche依赖于用户之间的活动，例如互相发送消息。 Leontjeva等人[^26]提出了一种在Skype中检测Sybils的方法。Sybils不仅使用注册功能，还利用了多种功能，这些功能要求帐户生成足够的内容和社交数据。

Thomas等人的工作[^38]也许是与我们最相关的。 他们利用注册数据来检测Twitter中的Sybil。但是，他们利用了特定于Twitter的注册数据（例如注册流程，用户代理，表单提交时间），而Ianus利用了不同的注册数据，例如IP，电话号码，设备等等

###注册模式

### 微信和数据集
该数据集于2017年11月收集，共140万个注册帐户，包括大约77万个良性帐户和65万个Sybil帐户。   
标签是从微信现有的基于行为的Sybil检测系统（该系统会几个月后为账户打标签，因为它需要足够的帐户行为）获取标签和用户报告（用户可以在微信中报告其他人为Sybil）。 微信安全团队随机采样并手动检查了一些帐户，发现准确率超过95％。我们认为这种偏差很小，并且我们的测量结果具有代表性。

**注册属性**：每个注册都有一个属性列表，如表1所示。

Attribute| Example
--|--
IP|***.***.***.*** 
Phone Number|+86-157-7944-xxxx 
Timestamp|1499270558 
Nickname|*** 
WeChat Version | 6.6.7 
OS Version |iOS 10.3.2 
Hashed WiFi MAC | a9d0cf034aa4e113e8ca27e9110928c7 
Hashed Device ID | d5c027d91d1df579d6ad1bffbb638cee

WiFi-MAC是用于注册帐户的电话的接入点的MAC地址，而设备ID是用于注册帐户的电话的IMEI/Adsource。

**伦理和隐私考虑**：收集的数据已经在微信隐私政策中明确规定，用户在使用微信前同意。此外，为了保护用户隐私，微信对这些属性进行了匿名化处理，使其足以用于我们的分析。具体地说，一个IP地址有四个段，每个段都是单独散列的。电话号码的最后四位数字（即客户代码）将被删除。所有数据都存储在微信的服务器上，我们通过一个实习项目访问它们。

### Synchronization 

我们发现，Sybil帐户显示出常见的注册模式，例如，它们可能使用相同的IP、来自相同区域的电话号码、相同的设备（由其设备ID标识）以及具有相同模式的昵称。我们认为原因是攻击者只拥有有限的资源（即IP、电话号码和设备），并使用某些脚本自动注册Sybil帐户。稍微滥用一下术语，我们就称之为Synchronization注册模式。接下来描述我们的测量结果。

**IP**:微信目前只支持IPv4注册。IPv4地址有四个段。我们将前三个段（即前24位）视为本地网络标识符。在某些情况下（例如使用CIDR）前三段可能不代表本地网络，然而我们发现使用前三个段来表示IP对于Sybil检测是有用的。因此，如果两个IP具有相同的24位前缀，则我们将它们视为在相同网络下。我们的数据集中总共有264830个24位IP前缀。对于每个24位IP前缀，我们将使用IP前缀注册的良性（或Sybil）帐户组合在一起。因此，组的大小指示从相应的24位IP前缀注册的良性（或Sybil）帐户数。图1(a)显示了注册给定数量良性（或Sybil）帐户的IP前缀的数量。**我们观察到大多数IP前缀注册了少量帐户，而少量IP前缀注册了大量帐户。例如，34.5%和15.5%的IP前缀分别注册了80%的良性和Sybil帐户。**

此外，Sybil帐户更可能使用相同的IP前缀进行注册。 注册大量Sybil帐户的IP前缀要比注册大量良性帐户的IP前缀多得多。
图1（b）进一步显示了在注册了给定数量帐户的IP前缀中注册的帐户中Sybils的比例。 例如，当x轴是0-1时，我们找到注册了0-500个帐户的所有IP前缀，而y轴是这些帐户中Sybils的比例。一个IP前缀仅注册了少量的帐户（例如0-500），则很难仅仅根据这些帐户共享IP前缀的事实来判断它们是否是Sybils。然而当从同一IP前缀注册了大量（>2500）帐户时，这些帐户更可能是Sybils。
![](/assets/img/academic/sybil/f1.png)

**电话号码**:
图2（b）显示了从注册了给定数量帐户的电话号码前缀注册的帐户中Sybils的分数。和IP前缀一样，我们观察到电话号码前缀的类似同步模式。特别是大量的电话号码前缀注册了少量的账号，而少量的电话号码前缀注册了大量的账号；Sybil更可能用同样前缀的电话号码注册账号，如果同一个前缀注册了超过30个账户，那么这些账户很可能是Sybils。
![](/assets/img/academic/sybil/f2.png)
**设备**：与IP前缀和电话号码前缀类似，图3(a)显示注册给定数量帐户的设备（由IMEI或Adsource标识）的数量，而图3(b)显示注册给定数量帐户的设备注册的帐户中Sybils的比例。我们观察到类似的同步模式：Sybil很可能是从相同的设备注册的。

虽然攻击者可以伪造设备ID，例如修改Android应用程序框架。我们的结果表明，攻击者没有对Sybil帐户的注册执行这种设备欺骗。
![](/assets/img/academic/sybil/f3.png)

**昵称**：在这里，我们对昵称模式分析显示了一些定性结果。在第4.2节中，我们将使用自然语言处理工具从昵称中提取模式和特征。 表2显示了三个昵称模式，每个模式的一个示例，以及昵称遵循特定模式的帐户中Sybils的比例。数据集中的昵称被微信匿名化处理了，所以我们不能理解昵称的语义模式。表2中的结果是在微信安全工程师的帮助下得到的。我们观察到，一些昵称模式（例如，中文+数字）更有可能被良性账户使用，而一些昵称模式（如小写+数字、数字+小写+数字）更有可能被Sybils使用。我们认为Sybils共享昵称模式的原因是，它们是由脚本根据特定模式生成的昵称。

模式|举例|比例
--|--|--
中文+数字|李四2416|32.2% 
英文小写+数字|cii2133,vqu7868|94.4% 
数字+英文小写+数字|07740922a179|95.0% 


**含义**：一方面，我们的测量结果表明同步模式可用于检测Sybil。例如，我们可以设计一种简单的方法（我们将在第5节的实验中显示更多详细信息）来根据IP，电话号码或设备的普及程度来检测Sybil帐户。 给定其中一天中的所有账户注册信息，我们可以认为从同一IP注册数量超过一定阈值的账户都是Sybil。这种简单的检测器在我们的数据集中可以达到几乎100％的精度。 

另一方面，单个属性（例如IP，电话号码或设备）的同步不足以检测Sybil。 例如基于电话号码的检测器仅实现59％的召回率。 因此我们将结合多个属性的同步模式和异常模式一起进行检测。

### 异常模式

**注册时间**：图4显示了从同一IP前缀注册的帐户相对于时间的分布。 每个图显示20个示例IP前缀的结果。 我们将一天24小时划分为96个15分钟的间隔。 图表中的每条垂直线显示在这96个时间间隔中从某个IP前缀注册的帐户，其中点的大小与在相应时间间隔中注册的帐户数量成比例。 我们观察到，当使用相同的IP前缀注册良性帐户时，它们在白天被均匀注册，而少数在午夜注册。 但是当Sybil通过相同的IP前缀注册时，它们是在深夜集中注册的。 
![](/assets/img/academic/sybil/f4.png)

**地理位置不一致**：IP地址可以映射到地理位置，电话号码也可以使用其区号映射到地理位置。 我们发现，有65％的Sybils在注册时基于IP的位置和基于电话号码的位置不同。
此外，用户可以在注册帐户时指定其位置（例如国家/地区）作为其帐户资料的一部分,我们发现96%的Sybils指定的国家与基于IP的国家不一致。


**稀有和过时的微信和操作系统版本**：我们观察到从稀有和过时的微信和操作系统版本注册的帐户更有可能是Sybils。  例如某个Android版本仅在我们的数据集中注册了2K帐户，其中96.5％是Sybils。 同样，从iOS 8注册的帐户中有99％是Sybils。 可能的原因包括：攻击者使用微信和操作系统版本已过时的旧设备来注册Sybil，并且攻击者使用脚本自动注册了Sybil，而未在脚本中更新其微信和操作系统版本。

## IANUS的设计

### 概览

Ianus的目标是利用注册数据来检测Sybils。Ianus由三个关键部分组成，即特征提取、图形生成和Sybil检测。图5显示了这三个组件。在特征提取中，我们提取基于同步的特征和基于异常的特征。基于同步的特征描述一对注册是否具有相同的注册属性，而基于异常的特征进一步描述这些属性是否异常。

![](/assets/img/academic/sybil/f5.png)

图构建组件旨在构建一个加权图来集成同步模式和异常模式。我们称之为注册图。图中的每个节点是一次注册，我们的目标是使得Sybils通过权重较大的边紧密地连接在一起，而良性帐户则稀疏地连接在一起。为了构建这样一个图，我们首先使用logistic回归学习每对注册的sync-anomaly分数。我们使用同步异常评分来量化两个注册之间的同步和异常模式。较高的sync-anomaly分数意味着这两个注册更可能是Sybils。如果两个注册间的sync-anomaly分数足够大，我们在两个注册之间创建一个边。

在我们的注册图中，具有更高加权度的节点更有可能是Sybil。因此，如果一个节点的加权度大于阈值，我们就预测该节点为Sybil节点，而阈值是使用机器学习技术学习的。


### 特征提取

#### 同步特征
如果两个注册的属性具有相同的值，则对应的特征为1，否则该特征为0。


特征|Description (Both registrations...) 
--|--
S-IP24|use the same 24-bit IP prefix
S-IP32|use the same IP address
S-PN |use the same phone number prefix 
S-OS|use the same OS version 
S-WeChat|use the same WeChat version 
S-MAC| use the same WiFi MAC address 
S-Device|use the same device 
S-NP1 |have the same syntactic nickname pattern 
S-NP2 |have the same semantic nickname pattern   

**基于昵称的特征**：S-NP1和S-NP2特征分别基于句法和语义模式。我们从词汇V={C，L，U，D，···}中定义一个字符串作为句法模式，其中C表示中文字符，L和U分别表示小写和大写英文字母，D表示数字，而每个标点和特殊字符（例如，；，+）仍然是词汇中的一个字符。例如，昵称abAB12++的语法模式是CCLLUUDD++。如果两个注册的昵称具有相同的语法模式，则特征S-NP1为1。

句法模式考虑昵称的结构，但忽略其语义。例如，一个随机的中文字符串和一个中文名称可以有相同的语法模式，但语义不同。如果两个帐户的昵称都是随机的中文字符串，则会更同步，因为攻击者的脚本可能会自动生成随机的中文字符串。因此，我们进一步从昵称中提取语义模式。具体来说，我们定义了几种语义模式，包括汉语短语、随机汉语串、英语短语、汉语拼音和随机英语串。我们将这些语义模式中的汉语短语和随机汉语串提取出来，作为只使用汉语的昵称，并提取出了英语短语、汉语拼音和汉语拼音的语义模式，以及仅使用英文字母的昵称的随机英文字符串。汉语拼音是用英文字母表示汉字的一种方法。

为了提取这些语义模式，我们收集了一个由微信订阅的数十万篇文章组成的大型汉语语料库和一个包含数十万个英语单词和数百万个汉语拼音的大型英语语料库。然后，我们使用srilm[^32]和Jieba[^1]分别训练了汉语句子、英语句子和汉语拼音的n-gram模型。
最后，我们使用n-gram模型提取昵称的语义模式。如果昵称是中文字符串，则可能性很大，如果两个注册的昵称具有相同的语义模式，则其特征S-NP2为1。


#### 基于异常的特征
我们还为一对注册提取基于异常的特征。这描述了一对注册是否都具有异常属性。首先为每个注册提取异常的特征，然后将一对注册的特征串联在一起。我们可以提取基于异常的特征FA用于注册A，连接特征（FA，FB）作为两个注册的统一特征。然而这受到两者的顺序即（FA，FB）与（FB，FA）的影响。因此在提取基于异常的特征时，我们共同考虑这两对注册的属性，这不依赖于两个注册的顺序。
表4显示了基于异常的二进制特性。如果两个注册的对应属性都异常，则特征为1

Feature|Description(Both registrations...) 
--|--
A-Location |have different user-specified and IP-based countries 
A-OS|use rare or old OS versions 
A-WeChat|use rare or old WeChat versions 
A-Time |were registered at late night, i.e., 2am–5am 
A-NP |have the same nickname pattern that is abnormal 

- **地理位置不一致（A-Location）**：用户可以在个人资料中任意指定自己的位置。此位置信息将显示给朋友。有些sybil将它们的位置指定为一个特定的位置，目标是将用户定位在该位置，而不管sybil是从何处注册的。如果两个注册都有地理位置和IP位置不一致的现象，则为1.

- **稀有和过时的OS和微信版本（A-OS和A-WeChat）**：略
- **注册时间（A-Time）**： 都在深夜注册时为1（凌晨2点-5点）
- **昵称模式（A-NP）**：如如果两个注册共享一个昵称模式，并且昵称模式异常，则A-NP为1。

### 建立注册图


目标：构造一个加权图来表示帐户之间的关系，节点是一个帐户，Sybils通过具有较大权重的边彼此连接，良性帐户稀疏连接。 利用机器学习技术来使用它们的特征向量为每对注册学习一个分数。 较大的分数意味着这对注册共享更多的属性，更有可能是Sybils。 我们将该分数称为sync-anomaly分数。 

#### 学习sync-anomaly分数  
有监督的机器学习：logistics回归
一对注册：标签为正表示都是都是Sybils，为负表示都是良性。

**为注册对分配标签**：假设有一个特征向量fa,S(fa)为Sybils对的集合， T(fa)是所有注册对的集合。如果有一个特征fb，当fa等于1的时候，fb也是1，则拓展它。即S(fa)=S(fa)∪S(fb),T(fa)=T(fa)∪T(fb).  
最后，计算特征向量fa的支持比 S(fa)/T(fa),如果大于一个阈值，则给一个正标签，否则是一个负标签。
选择的阈值：0.98


**学习sync-anomaly得分**：
根据（特征向量，标签），学习一个logistics回归。

该分类器以特征向量为输入，输出具有正标签的概率。在使用历史训练数据集学习logistic回归分类器之后，我们可以在将来将该分类器应用于成对的注册。具体来说，对于一对注册，我们构造其特征向量，并使用分类器计算特征向量具有正标签的概率（即两个注册都是Sybils）。我们将这个概率视为这一对注册的sync-anomaly分数。从0到1不等。如果评分大于0.5，则分类器会预测它们都是Sybils。

#### 构造注册图


只有当得分大于0.5时，我们才在两个帐户之间创建一个边，并使用sync-anomaly得分作为边的权重。
如果把每一对注册都进行构造，一周有百万次的注册行为，那么需要计算10^14次  

将注册分成组，每一组内有相同的注册属性（IP前缀、电话号码前缀或设备ID），只计算组内的得分。

### 检测Sybils

如果一个节点在注册图中与更多的邻居连接，那么该节点更有可能是Sybil。

训练了一个基于加权节点度的二值分类器。以加权和作为输入。
因为加权和范围很宽，所以使用tanh进行规范化，使其范围在(-1,1)之间。
使用名为EasyEnsemble[^43]的分类器。

## 评估

### 实验准备
**数据集**：我们从微信获取了两个注册数据集，分别于10月和2017年11月收集。 数据集I包含10月的注册，而数据集II包含11月的注册，这大约是在数据集I之后的一周。  
标签由微信安全团队提供，经验证标签准确率大于95%。
数据集 |#Sybils |#Benign 
--|--|--
Dataset I |779k |681k 
Dataset II |647k |770k 

**方法比较**：
- Ianus  
Ianus同时使用同步和基于异常的特征；Ianus使用logistic回归来学习同步异常分数；Ianus使用基于加权节点度的方法来检测Sybils。具体来说，我们使用Spark中实现的logistic回归和默认参数设置。

- Ianus-Sync和Ianus-Anomaly  
Ianus-Sync和Ianus-Anomaly分别使用同步和基于异常的特征。

- Ianus-FS  
Ianus FS计算一对注册的同步异常得分，如果同步异常得分大于阈值，则在两个注册之间创建边。我们将研究不同阈值对Ianus-FS的影响

- Ianus-CD  
Ianus-CD使用Louvain方法检测注册图中的社区，并将大于阈值的社区视为Sybils。我们将研究不同的阈值


- Ianus-FS-CD  
此变体结合了Ianus-FS和Ianus-CD。 具体来说，Ianus-FS-CD将二元特征作为图构建组件中的sync-anomaly相加，并在Sybil检测组件中使用社区检测。 请注意，Ianus-FS-CD是一种无监督方法，因为它不需要历史训练数据集即可学习同步异常评分和基于节点度的分类器。



**基于流行度的方法**  
 第3.2节中的测量结果激励我们设计出基于流行度的简单方法来检测Sybil。 具体来说，对于属性值（例如电话号码前缀，设备ID），我们将其受欢迎程度计算为使用该属性值的测试数据集中的注册数量。 如果某个属性值的受欢迎程度大于阈值，则我们会将所有具有该属性值的注册预测为Sybils。

**评估指标**  
F-score是准确率和召回率的调和平均数。

## 结果

**Ianus有效性**：在数据集II上，Ianus分别达到准确率了92.4%，召回率80.2%，F-score 85.9%。Ianus之所以有效的一个关键原因是Sybils在我们构造的注册图中是紧密连接的，而良性帐户则不是。在注册图中，Sybil平均与280个Sybil连接，仅与1.3个良性节点连接，良性节点仅与4.5个良性节点连接。

在我们的注册图中，我们发现大约69%的假阳性出现在人数小于10的社区（通过Louvain方法检测），7%的假阳性出现在大小介于10到100之间的社区。这些误报的主要原因是它们的特征，如基于电话号码和基于IP的特征与Sybils相似。此外，我们观察到3%的假阳性出现在100到1000之间的社区，21%的假阳性出现在1000到10000之间的社区。这些误报的主要原因是它们是从同一个组织注册的，并且具有相似的模式
![](/assets/img/academic/sybil/f6.png)
（a） Sybil支持率阈值对Ianus的影响。  
（b） 训练数据集大小对Ianus的影响。  

![](/assets/img/academic/sybil/f7.png)
(a)Ianus，Ianus-Sync和Ianus-Anomaly的比较。   
(b)学习sync-anomaly分数的logistic回归分类器中特征的权重。


表7:Ianus预测在微信上部署后的某一周内，每天每百万个新注册账户的账号数,平均精度为96%。

Day 1|Day 2|Day 3 |Day 4| Day 5 |Day 6 |Day 7 
--|--|--|--|--|--|--
434K |477K| 454K| 372K |377K |327K |295K 

微信安全团队随机抽取的4万个在此期间注册的账户。他们检查了账户的公共信息，如个人资料图片、瞬间/帖子、好友邀请等。在将账户标记为Sybil时采用了保守的标准，如果一个帐户发布大量广告、频繁发送好友请求、发布色情内容等，则该帐户将被标记为Sybil。  
4万个帐户中，51.1%被标记为Sybils，其余48.9%被标记为良性。Ianus预测4万个账户中有39.9%是Sybils。Ianus的准确率达到96%，召回率达到75%。

## 讨论和限制

**规避Ianus**  
攻击者可以通过操纵Ianus使用的特征或生成假特征来规避。攻击者可以使用新的和流行的OS版本和微信版本、伪造设备ID和WiFi MAC，在白天而不是深夜进行注册，并指定与基于IP的位置一致的位置。

但是，攻击者可能需要付出更大的代价才能操作基于IP的功能、基于电话号码的功能和基于昵称的功能。仅使用这些难以回避的特性，Ianus仍然可以达到93.6%的精确度和45.1%的召回率。
攻击者可能通过招募大量良性用户来注册Sybils来逃避检测，这就是所谓的crowdturfing[^24]、[^25]、[^31]、[^45]、[^46]。然而crowdturfing可以通过其他方法检测[^57]。

**检测覆盖率**  
Ianus准确度介于92％到96％，但召回率约为80％（使用所有特征）和45％（使用难以逃避的特征）。 我们怀疑是某些Sybil被单独或手动注册，而没有明显的同步或异常模式。 为了解决这一局限性，Ianus可以与利用其他类型的数据（例如内容，行为和/或社交图谱）以增强覆盖范围的方法一起使用

**重新训练**  
Sybils的注册方式可能会随着时间而改变。 因此，当准确度显着下降时，Ianus可能需要重新训练。 具体来说，微信部署了多个系统（例如Ianus和基于行为的系统）来检测Sybil，并且微信用户还可以报告Sybil。如果基于行为的系统检测到的或用户报告的更多Sybils未被Ianus检测到，则Ianus可能需要重新训练。

**Ianus对其他在线社交网络的适用性**  
某些特征（例如基于IP，电话号码，时间戳，操作系统和昵称的功能）适用于其他在线社交网络（例如Facebook和Twitter），而基于设备ID，WiFi MAC的功能，以及“微信版本”可以进一步应用于为移动设备设计的其他在线社交网络。 将Ianus扩展到其他在线社交网络将是一个有趣的未来工作。

**注册速率限制**  
在给定的时间段内（例如1小时）限制每个IP或设备的注册数似乎是预防Sybils的一种简单方法。但是许多良性帐户也使用相同的IP地址进行注册。因此这种基于IP的速率限制方法将在很大程度上影响良性用户，攻击者也可以伪造设备ID以规避基于设备的速率限制。

## 引用
[^2]:LorenzoAlvisi,AllenClement,AlessandroEpasto,SilvioLattanzi,andAlessandroPanconesi. 2013. SoK: The Evolution of Sybil Defense via Social Networks. In IEEE S & P. 
[^3]:Fabrıcio Benevenuto, Gabriel Magno, Tiago Rodrigues, and Virgılio Almeida. 2010. Detecting spammers on twitter. In CEAS. 
[^4]: L.Bilge,T.Strufe,D.Balzarotti,andE.Kirda.2009.AllYourContactsAreBelong to Us: Automated Identity Theft Attacks on Social Networks. In WWW. 
[^5]: VincentDBlondel,JeanLoupGuillaume,RenaudLambiotte,andEtienneLefebvre.2008. Fast unfolding of communities in large networks. Journal of Statistical Mechanics-Theory and Experiment 2008, 10 (2008), 155–168. 
[^6]: YazanBoshmaf,DionysiosLogothetis,GeorgosSiganos,JorgeLería,JoseLorenzo,Matei Ripeanu, and Konstantin Beznosov. 2015. Integro: Leveraging Victim Prediction for Robust Fake Account Detection in OSNs.. In NDSS, Vol. 15. 8–11. 
[^7]: Elie Bursztein, Jonathan Aigrain, Angelika Moscicki, and John C. Mitchell. 2014. The end is nigh: Generic solving of text-based captchas. In WOOT. 
[^8]: Elie Bursztein, Romain Beauxis, Hristo Paskov, Daniele Perito, Celine Fabry, and JohnMitchell.2011. TheFailureofNoise-BasedNon-continuousAudioCaptchas.In IEEE Symposium on Security and Privacy. 19 – 31. 
[^9]: Elie Bursztein, Matthieu Martin, and John C. Mitchell. 2011. Text-based CAPTCHA Strengths and Weaknesses. In CCS. 125–138. 
[^10]: Zhuhua Cai and Christopher Jermaine. 2012. The Latent Community Model for Detecting Sybils in Social Networks. In NDSS. 
[^11]: Qiang Cao, Michael Sirivianos, Xiaowei Yang, and Tiago Pregueiro. 2012. Aiding the detection of fake accounts in large scale social online services. In NSDI. 
[^12]: Qiang Cao, Xiaowei Yang, Jieqi Yu, and Christopher Palow. 2014. Uncovering large groups of active malicious accounts in online social networks. In CCS. 477–488. 
[^13]: G. Danezis and P. Mittal. 2009. SybilInfer: Detecting Sybil Nodes using Social Networks. In NDSS. 
[^14]: John R. Douceur. 2002. The Sybil Attack. In IPTPS. 
[^15]: MatthewEdwards,GuillermoSuarez-Tangil,ClaudiaPeersman,GianlucaStringh-ini, Awais Rashid, and Monica Whitty. 2018. The Geography of Online Dating Fraud. In ConPro. 
[^16]: Manuel Egele, Gianluca Stringhini, Christopher Kruegel, and Giovanni Vigna. 2015. Towards Detecting Compromised Accounts on Social Networks. IEEE Transactions on Dependable and Secure Computing 12, 2 (2015), 447–460. 
[^17]: D.Freeman,M.Dürmuth,andB.Biggio.2016.Whoareyou?Astatisticalapproachto measuring user authenticity. In NDSS. 
[^18]: Hongyu Gao, Jun Hu, Christo Wilson, Zhichun Li, Yan Chen, and Ben Y Zhao. 2010. Detecting and characterizing social spam campaigns. In IMC. 35–47. 
[^19]: Peng Gao, Binghui Wang, Neil Zhenqiang Gong, Sanjeev R Kulkarni, Kurt Thomas, and Prateek Mittal. 2018. Sybilfuse: Combining local attributes with global structure to perform robust sybil detection. In 2018 IEEE Conference on Communications and Network Security (CNS). IEEE, 1–9. 
[^20]: Neil Zhenqiang Gong, Mario Frank, and Prateek Mittal. 2014. Sybilbelief: A semi-supervised learning approach for structure-based sybil detection. IEEE Transactions on Information Forensics and Security 9, 6 (2014), 976–987. 
[^21]: Hacking Financial Market. 2016. http://goo.gl/4AkWyt 
[^22]: Shuang Hao, Alex Kantchelian, Brad Miller, Vern Paxson, and Nick Feamster. 2016. PREDATOR: Proactive Recognition and Elimination of Domain Abuse at Time-Of-Registration. In CCS. 
[^23]: Jinyuan Jia, Binghui Wang, and Neil Zhenqiang Gong. 2017. Random walk based fake account detection in online social networks. In 2017 47th Annual IEEE/IFIP International Conference on Dependable Systems and Networks (DSN). IEEE, 273–284. 
[^24]: KyuminLee,PrithiviTamilarasan,andJamesCaverlee.2013.Crowdturfers,Cam-paigns, and Social Media: Tracking and Revealing Crowdsourced Manipulation of Social Media. In ICWSM. 
[^25]: Kyumin Lee, Steve Webb, and Hancheng Ge. 2014. The Dark Side of Micro-Task Marketplaces: Characterizing Fiverr and Automatically Detecting Crowdturfing. CoRR abs/1406.0574 (2014). 
[^26]: Anna Leontjeva, Moises Goldszmidt, Yinglian Xie, Fang Yu, and Martín Abadi. 2013. Early security classification of skype users via machine learning. In AISec. 
[^27]: ChangchangLiu,PengGao,MatthewWright,andPrateekMittal.2015.Exploitingtemporal dynamics in Sybil defenses. In CCS. 805–816. 
[^28]: Abedelaziz Mohaisen, Nicholas Hopper, and Yongdae Kim. 2011. Keep your friends close: Incorporating trust into social network-based Sybil defenses. In IEEE INFOCOM. 
[^29]: AbedelazizMohaisen,AaramYun,andYongdaeKim.2010.Measuringthemixing time of social graphs. In IMC. 
[^30]: Jonghyuk Song, Sangho Lee, and Jong Kim. 2011. Spam filtering in Twitter using sender-receiver relationship. In RAID. 
[^31]: Jonghyuk Song, Sangho Lee, and Jong Kim. 2015. CrowdTarget: Target-based Detection of Crowdturfing in Online Social Networks. In CCS. 793–804. 
[^32]: AndreasStolcke.2002.SRILM-anextensiblelanguagemodelingtoolkit.InSeventhinternational conference on spoken language processing. 
[^33]: Gianluca Stringhini, Christopher Kruegel, and Giovanni Vigna. 2010. Detecting spammers on social networks. In ACSAC. 
[^34]: Gianluca Stringhini, Pierre Mourlanne, Gregoire Jacob, Manuel Egele, Christo-pher Kruegel, and Giovanni Vigna. 2015. Evilcohort: detecting communities of malicious accounts on online services. In USENIX Security Symposium. 563–578. 
[^35]: KurtThomas,ChrisGrier,JustinMa,VernPaxson,andDawnSong.2011.Design and evaluation of a real-time url spam filtering service. In IEEE S & P. 
[^36]: Kurt Thomas, Danny Yuxing Huang, David Wang, Elie Bursztein, Chris Grier, ThomasJ.Holt,ChristopherKruegel,DamonMcCoy,StefanSavage,andGiovanniVigna. 2015. Framing Dependencies Introduced by Underground Commoditiza-tion. In WEIS. 
[^37]: Kurt Thomas, Frank Li, Chris Grier, and Vern Paxson. 2014. Consequences of connectivity: Characterizing account hijacking on twitter. In CCS. 489–500. 
[^38]: Kurt Thomas, Damon Mccoy, Alek Kolcz, Alek Kolcz, and Vern Paxson. 2013. Trafficking fraudulent accounts: the role of the underground market in Twitter spam and abuse. In Usenix Security Symposium. 195–210. 
[^39]: BimalViswanath,AnsleyPost,KrishnaP.Gummadi,andAlanMislove.2010.An Analysis of Social Network-Based Sybil Defenses. In ACM SIGCOMM. 
[^40]: Alex Hai Wang. 2010. Don’t Follow Me - Spam Detection in Twitter. In SECRYPT 2010. 
[^41]: Binghui Wang, Neil Zhenqiang Gong, and Hao Fu. 2017. GANG: Detecting fraudulent users in online social networks via guilt-by-association on directed graphs. In 2017 IEEE International Conference on Data Mining (ICDM). IEEE, 465–474. 
[^42]: BinghuiWang,JinyuanJia,andNeilZhenqiangGong.2018.Graph-basedsecurity and privacy analytics via collective classification with joint weight learning and propagation. arXiv preprint arXiv:1812.01661 (2018). 
[^43]: Binghui Wang, Le Zhang, and Neil Zhenqiang Gong. 2017. SybilSCAR: Sybil detection in online social networks via local rule based propagation. In IEEE INFOCOM 2017-IEEE Conference on Computer Communications. IEEE, 1–9. 
[^44]: Gang Wang, Tristan Konolige, Christo Wilson, Xiao Wang, Haitao Zheng, and BenYZhao.2013. Youarehowyouclick:Clickstreamanalysisforsybildetection.In USENIX Security Symposium. 241–256. 
[^45]: GangWang,TianyiWang,HaitaoZhang,andBenY.Zhao.2014.Manvs.machine:practical adversarial detection of malicious crowdsourcing workers. In USENIX Security Symposium. 239–254. 
[^46]: Gang Wang, Christo Wilson, Xiaohan Zhao, Yibo Zhu, Manish Mohanlal, Haitao Zheng, and Ben Y. Zhao. 2012. Serf and turf: crowdturfing for fun and profit. In WWW.
[^47]: Zenghua Xia, Chang Liu, Neil Zhenqiang Gong, Qi Li, Yong Cui, and Dawn Song. 2019. Characterizing and Detecting Malicious Accounts in Privacy-Centric Mobile Social Networks: A Case Study. In Proceedings of the 25th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining. ACM, 2012– 2022. 
[^48]: Yinglian Xie, Fang Yu, Qifa Ke, Martín Abadi, Eliot Gillum, Krish Vitaldevaria, Jason Walter, Junxian Huang, and Z. Morley Mao. 2012. Innocent by Association: Early Recognition of Legitimate Users. In CCS. 
[^49]: Chao Yang, Robert Harkreader, and Guofei Gu. 2011. Die Free or Live Hard? Empirical Evaluation and New Design for Fighting Evolving Twitter Spammers. In RAID. 
[^50]: Chao Yang, Robert Harkreader, Jialong Zhang, Seungwon Shin, and Guofei Gu. 2012. Analyzing Spammer’s Social Networks for Fun and Profit. In WWW. 
[^51]: Zhi Yang, Jilong Xue, Xiaoyong Yang, Xiao Wang, and Yafei Dai. 2016. VoteTrust: Leveraging Friend Invitation Graph to Defend against Social Network Sybils. IEEE Transactions on Dependable and Secure Computing 13, 4 (2016), 488–501. 
[^52]: Guixin Ye, Zhanyong Tang, Dingyi Fang, Zhanxing Zhu, Yansong Feng, Pengfei Xu, Xiaojiang Chen, and Zheng Wang. 2018. Yet another text captcha solver: A generative adversarial network based approach. In Proceedings of the 2018 ACM SIGSAC Conference on Computer and Communications Security. 
[^53]: Xu ying Liu, Jianxin Wu, Zhi hua Zhou, and Senior Member. 2009. Exploratory undersampling for class-imbalance learning. IEEE Transactions on Systems, Man, and Cybernetics, Part B (Cybernetics) 39, 2 (2009). 
[^54]: H.Yu,P.B.Gibbons,M.Kaminsky,andF.Xiao.2008.SybilLimit:ANear-Optimal Social Network Defense against Sybil Attacks. In IEEE S & P. 
[^55]: H. Yu, M. Kaminsky, P. B. Gibbons, and A. Flaxman. 2006. SybilGuard: Defending Against Sybil Attacks via Social Networks. In SIGCOMM. 
[^56]: Yao Zhao, Yinglian Xie, Fang Yu, Qifa Ke, Yuan Yu, Yan Chen, and Eliot Gillum. 2009. BotGraph: Large Scale Spamming Botnet Detection. In NSDI. 
[^57]: Haizhong Zheng, Minhui Xue, Hao Lu, Shuang Hao, Haojin Zhu, Xiaohui Liang, and Keith Ross. 2018. Smoke Screener or Straight Shooter: Detecting Elite Sybil Attacks in User-Review Social Networks. In Proceedings of the Network and Distributed System Security Symposium (NDSS). 
[^58]: Yang Zhi, Christo Wilson, Tingting Gao, Tingting Gao, Ben Y. Zhao, and Yafei Dai. 2011. Uncovering social network Sybils in the wild. Acm Transactions on Knowledge Discovery from Data 8, 1 (2011), 2.