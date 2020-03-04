---
layout: post
title: Detecting Fake Accounts in Online Social Networksat the Time of Registrations
category: 学术
tags: 论文
keywords: Social
---
* content
{:toc}

类型  |内容  
-- |--  
标题 |Detecting Fake Accounts in Online Social Networksat the Time of Registrations
时间 |2019
会议 |CCS
引用 |Yuan D, Miao Y, Gong N Z, et al. Detecting Fake Accounts in Online Social Networks at the Time of Registrations
[^C]://Proceedings of the 2019 ACM SIGSAC Conference on Computer and Communications Security. 2019: 1423-1438.


## 摘要

社交网络上存在的虚假账户（Sybils），散布垃圾邮件、恶意软件和虚假信息。  
目前Sybils的检测存在延迟，在被检测到的时候已经执行了很多恶意活动。   
本论文提出名为Ianus的方法，利用账户注册信息来检测Sybil。  
Ianus的目标是在它们注册后立即捕获它们。首先，利用中国最大的在线社交网络微信（WeChat）上带有Sybils标签的真实注册数据集，对Sybils和良性用户的注册模式进行了测量研究。我们发现Sybils倾向于**synchronized**和**abnormal**注册模式。  
第二，根据测量结果，我们将Sybil检测建模为一个图推理问题，这使得我们能够集成异构特征。我们为每对帐户提取基于synchronized和异常的特征，使用这些特征构建一个图，其中Sybils彼此紧密连接，而良性用户与其他良性用户和Sybils隔离或解析连接，最后通过分析图的结构来检测Sybils。  
我们使用微信的真实注册数据集评估用户。此外，微信部署Ianus为每日运行，即微信每天都会使用Ianus分析新注册的账户，我们发现Ianus每天可以对40万个新注册的账户进行检测，通过微信安全团队的手动验证，平均精度超过96%。

## 介绍

如今，在线社交网络（例如Facebook和微信）特别受欢迎，这对我们的日常生活产生了巨大影响。 同时，它们也是攻击者的重要目标。 例如，在Sybil攻击中，攻击者注册并维护着大量的假帐户，以执行各种恶意活动，例如传播垃圾邮件，网络钓鱼URL，恶意软件和虚假信息以及窃取私人用户数据。  
目前已经开发了很多检测在线社交网络中的Sybils的方法。 这些方法利用了由Sybils生成的内容（例如URL in tweets），行为（例如，点击流，喜欢，照片上传）和/或社交图）。 它们面临一个局限性：在检测Sybils时会产生大量延迟。   
即要求Sybils在检测到它们之前先生成丰富的内容，行为和/或社交图。 因此，Sybils在被发现之前可能已经进行了各种恶意活动。

**本文的目标是在注册时检测到Sybils。**  
首先，我们利用微信的一个匿名的注册数据集，对Sybils和良性用户的注册模式进行了系统的测量研究。数据集收集于2017年11月，其中良性用户有77万，Sybils用户有64.7万。每个注册都有一个属性列表，如IP地址、电话号码（可在微信中用作用户ID）、设备ID（如IMEI）、昵称等等。我们发现Sybils有相同的关于这些属性的synchronized注册模式。 例如，许多Sybils用24位相同前缀的IP地址进行注册，这意味着它们可能使用在同一局域网中的设备进行注册。
但是，synchronized不足以区分Sybils和良性用户。 尤其是某些良性用户还共享synchronized的注册属性，例如他们使用相同前缀的IP进行注册。

其次，我们设计了Ianus来通过注册信息检测Sybils，一个关键的挑战是如何集成同步和异常的注册模式，Ianus用图推理技术来解决这个挑战。我们构建图的方式为：每个节点都是一个帐户，Sybils相互紧密连接，而良性帐户之间稀疏连接，也与Sybils稀疏连接。 Ianus具有三个组成部分，即特征提取，图形构建和Sybil检测。 在特征提取中，对于一对帐户，我们提取基于二进制同步的特征（例如，两个帐户使用同一设备进行注册）和基于异常的特征（例如，共享设备异常）。

在图构建中，我们使用基于同步的特征和基于异常的特征在帐户之间构建加权图。 具体来说，我们根据其特征为一对帐户分配sync-anomaly分数。 sync-anomaly评分可表征两个帐户之间的同步和异常模式，如果一对帐户具有更多同步和异常注册特征，则它们具有较高的同步异常评分。为一对帐户分配sync-anomaly分数的一种方法是对它们的二进制特征求和。 但是这种基于特征和的方法不会考虑特征的权重。 因此，我们使用机器学习（尤其是逻辑回归）来学习自动加权不同特征的sync-anomaly分数。接下来我们使用sync-anomaly分数在帐户之间创建边。 我们要构建一个Sybil之间紧密连接的图，仅当两个帐户都根据其sync-anomaly分数被预测为Sybil时，才在两个帐户之间创建边。 此外，我们将sync-anomaly分数视为边的权重。

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

### 注册模式

### 微信和数据集
该数据集于2017年11月收集，共140万个注册帐户，包括大约77万个良性帐户和65万个Sybil帐户。 标签是从微信现有的基于行为的Sybil检测系统（该系统会在注册这些帐户几个月后打标签，因为它需要足够的帐户行为）获取标签和用户报告（用户可以在微信中报告其他人为Sybil）。 微信安全团队随机采样并手动检查了一些帐户，发现准确率超过95％。我们认为这种偏差很小，并且我们的测量结果具有代表性。

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

我们发现，Sybil帐户显示出常见的注册模式，例如，它们可能使用相同的IP、来自相同区域的电话号码、相同的设备（由其设备ID标识）以及具有相同模式的昵称。我们怀疑原因是攻击者拥有有限的资源（即IP、电话号码和设备），并使用某些脚本自动注册Sybil帐户。稍微滥用一下术语，我们就称之为同步注册模式。接下来，我们描述我们的测量结果。


【未完成】

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