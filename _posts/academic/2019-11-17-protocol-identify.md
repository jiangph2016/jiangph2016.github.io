---
layout: post
title: 【调研】协议识别技术
category: 学术
tags: 调研
keywords: 协议,protocol
---

* content
{:toc}

# 协议识别调研

## 简介

互联网中使用的协议按TCP/IP模型划分有很多种，网络层使用的IP、ARP等协议，传输层使用的TCP/UDP协议，而应用层则有大量如HTTP、FTP等常见协议和一些私有的协议。

![](/assets/img/academic/protocol/tcp.jpg)

IP数据报中，存在一个协议字段，在这个字段中可以根据协议号识别出在网络层或传输层使用的协议，比较常见的有6号是TCP、17号是UDP协议。

![](/assets/img/academic/protocol/ip.png)

在<https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml>中可以看到具体协议号对应的协议。

![](/assets/img/academic/protocol/protocol2.png)

如下图所示，在TCP或UDP的数据报中，一般使用端口号来区分具体的应用层协议，这些端口号由IANA规定，比较常见的有ssh协议使用端口号22，http协议使用端口号80。  其中0~1023是系统端口，1024~49151是用户端口，49152~65535是动态端口。

![](/assets/img/academic/protocol/协议分用.png) 



在<https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml?>可以查看到应用层的各协议及其端口号。[^protocol]

![](/assets/img/academic/protocol/protocol1.png)

在wireshark的提供的网站[^wiki]中给出了各种常见协议族和协议的描述：https://wiki.wireshark.org/ProtocolReference。大致如下图所示

![](/assets/img/academic/protocol/wire-proto1.png)

![](/assets/img/academic/protocol/wire-proto2.png)

协议识别一般用作于对流量成分的分析，以及对恶意流量的发现。



## 常见方法

### 基于端口的分类

传统的协议识别是通过数据包使用的端口号来进行分类，这种方式有较高的实时性且可以应用于高速网络环境下。

这种方式的缺点是能识别的协议有限：随着互联网的发展，出现了很多未知协议和加密协议，这些协议并不使用固定的端口，还有可能占用常见协议的端口，导致将未知的协议错误的分类为已知协议。



### 基于负载的分类

对应用层协议中数据包含的特定字符串进行提取和匹配，可以识别出很多商业或者开放源码的协议，也是一种主流的协议识别方法。

缺点是无法适用于加密流量的处理。且随着协议的增多，所需的规则也会越来越复杂，需要大量的工作来收集协议的关键词。

目前**深度包检测技术（DPI）**就是通过对负载进行分析，并识别出其所用的应用层协议，以实行相应的策略。



### 基于主机行为的分类

通过对主机行为的观察和分析，对协议进行分类。


### 基于统计机器学习方法的分类

使用多种机器学习的方法，可以对加密流量中的使用的协议进行识别。



## 相关研究

在04年或更早就有关于协议识别研究的论文了，但大部分都是基于负载对协议进行分类的、

以下是检索的关于协议识别相关的论文，关键词有“协议识别”、“协议指纹”、和“DPI”，每篇论文具体的来源都已经标了引用，按照论文发表时间排序。



**Toward the Accurate Identication of Network Applications**[^accurate]

文章对多种协议识别方法的准确度进行了分析。

![](/assets/img/academic/protocol/acc1.png")

可以看到仅使用端口识别方法的时候识别精度只有69.27%，而多种方法结合可以提高精度。这篇文章发表于2005年，但是可以看到基于端口进行协议识别的方法的有效程度已经很低了。

![](/assets/img/academic/protocol/acc2.png)

**Application‐level traffic monitoring and an analysis on IP networks**[^NG_MON]

设计了一个流量分析系统NG_MON,基于应用程序对流量进行分类。



**ACAS: automated construction of application signatures.** [^ACAS]

一个自动化的协议指纹构建方法:ACAS，通过分析网络数据流的前64byte作为协议特征，使用了三种机器学习方法（朴素贝叶斯、AdaBoost、Regularized Maximun Entropy）构建分类模型。



**Unexpected means of protocol inference**[^1]

论文提出了一种基于无监督学习的协议推理方法，利用聚类方法实现对网络协议的自动识别。



**基于特征串的应用层协议识别**[^特征串]

使用特征串匹配的方式，快速识别七种应用层协议：BT、eDonkey、MSN、SMTP、POP3、FTP和HTTP。



**Discoverer: Automatic Protocol Reverse Engineering from Network Traces**[^Discoverer]

利用统计学习和数据挖掘的研究方法，自动地从特定协议的网络数据流中提取该协议的格式信息。



**Tupni: Automatic reverse engineering of input formats**[^Tupni]

一个对输入数据流进行逆向工程的工具，可以对格式信息自动地提取，从而实现协议解析。



**The study of DPI identification technology based on sampling**[^8]

DPI技术可以用于识别P2P数据，但是速度很慢不适用于高速网络，文章提出六种采样策略来提高DPI识别的效率。



**Kiss: Stochastic packet inspection classifier for udp traffic**[^KISS]

提出一个基于负载的协议分类系统。使用假设检验和机器学习，通过构建统计化的协议指纹信息实现对UDP数据包的快速识别。



**A Survey of Application-Level Protocol Identification Based on Machine Learning**[^2]

文章发布于2011年，比较和总结了前人使用机器学习进行应用层协议识别的方法。



**A Formal Methodology for Network Protocol Fingerprinting**[^finger]

一个关于协议指纹识别的文章。



**基于主动学习和SVM方法的网络协议识别技术**[^ProLearner]

一种基于主动学习的未知网络协议识别方法，属于基于负载的分类。使用SVM对训练样本的数据载荷部分进行学习，构建协议识别模型。



**Unknown Network Protocol Classification Method based on Semi-Supervised Learning**[^4]

使用半监督的聚类分析算法，可以有效的对未知协议进行分类。



**Feature identification of unknown protocol**[^6]

文章有关协议识别，主要分析了模式匹配和数据挖掘的算法



**An Optimized Solution of Application Layer Protocol Identification Based on Regular Expressions** [^7]

文章关于优化DPI引擎的性能，提出了一种基于正则表达式的优化解决方案。



**ProDigger: Towards Robust Automatic NetworkProtocol Fingerprint Learning via Byte Embedding**[^prodigger]

文章提出了名为ProDigger的自动协议指纹识别框架，用于DPI流量识别。



**The Research of Protocol Identification Based on Traffic Analysis**[^5]

文章中总结了各种基于流量分析的协议识别方法。



**Identification of unknown protocol traffic based on deep learning**[^9]

提出一种基于深度学习的未知流量识别方法，采用基于Kears框架的卷积神经网络来训练模型，最后模型的准确率为86.05%。



**A semi-supervised method for classifying unknown protocols**[^3]

对已知的应用层协议，使用深度神经网络统计特征，然后使用半监督聚类算法将未知的协议分类。



## 协议识别的产品

### WireShark

#### 简介

**Wireshark**（前称Ethereal）是一个网络封包分析软件。网络封包分析软件的功能是撷取网络封包，并尽可能显示出最为详细的网络封包资料。**Wireshark**使用WinPCAP作为接口，直接与网卡进行数据报文交换。

#### 方法

**Wireshark**目前是通过加载解码器的方式实现对各个协议的识别，**Wireshark**会尝试为数据包选择合适的解码器，在github中的epan/dissectors目录下存放着各种协议的解码器：

<https://github.com/wireshark/wireshark/tree/master/epan/dissectors>

![](/assets/img/academic/protocol/111.png)

在Wireshark中选择Analyze->Enabled Protols，可以设置Wireshark要解析的协议,如下图所示，里面列出了Wireshark所支持的各种协议。在wireshark的3.0.6版本上，共支持3000种协议[^dfref]。

![](/assets/img/academic/protocol/wireshark.png)

可能会出现因为某些协议使用了标准协议的端口导致被错误解码的情况，所以有时候要手动选择合适的解码器。我尝试在mysql常用的3306端口启动了http服务，由下图可以看到wireshark默认选择了MySQL的解析器进行解析。[^decode]

![](/assets/img/academic/protocol/wrong.png)



### 科来[^科来]

#### 简介

**科来网络分析系统(CSNAS)**是一款集数据包采集、协议解码与分析、流量统计、故障诊断与性能管理等多种功能为一体的便携式网络分析产品。

其协议分析的主要功能有：

- 采用基于CSPAE分析引擎地实时网络数据包和协议分析
- 实时的分析和重组TCP数据流
- HTTP高级分析模块
- Email高级分析模块
- FTP高级分析模块
- HTTP请求、邮件以及FTP传输日志
- 图形显示分析结果

在**科来网络全流量分析系统（TSA）**的页面，根据介绍可以识别2000余种网络协议

#### 使用

在官网可以免费下载到科来网络分析系统(技术交流版)，该软件目前只支持Windows系统，在8G内存的电脑上运行时经常出现因为内存不够导致停止流量分析。

在使用前选择要监听的网卡。

![](/assets/img/academic/protocol/kelai1.png)

在协议一栏可以查看目前识别到的协议的详情，包括流量和各协议占比。

![](/assets/img/academic/protocol/kelai2.png)

在数据包一栏中，可以具体看到每个数据包使用的协议。

![](/assets/img/academic/protocol/kelai3.png)

### Stealthwatch[^Cisco]

#### 简介

思科 **Stealthwatch** 是一个保障内部网络安全的系统，它提供了一个加密流量分析系统，通过机器学习的方法分析是否是恶意流量。

#### 方法[^思科白皮书]

主要方法：**机器学习和行为建模**

**Stealthwatch**的加密流量分析提取四个主要数据元素：数据包长度与数据包到达间隔时间顺序、字节分布、TLS 特定特性和初始数据包。

- **数据包长度与数据包到达间隔时间顺序 (SPLT)**：SPLT 反映了数据流的前几个数据包中每个数据包的应用负载长度（字节数）以及这些数据包的到达间隔时间。SPLT 可用一组数据包大小（字节）和一组时间（毫秒）来表示，其中时间代表的是该数据包与前一个数据包之间的时间间隔。
- **字节分布**：字节分布表示某特定字节值出现在数据流内数据包负载中的概率。数据流的字节分布可通过一组计数器计算。与字节分布相关的主要数据类型为完整的字节分布、字节熵和字节的平均/标准偏差。例如每个字节值使用一个计数器，HTTP GET 请求“HTTP/1.1.”可通过增加“H”对应的计数器一次，然后增加两个连续的“T”对应的计数器两次来计算。虽然字节分布采用的是一组计数器的方式，按照总字节数进行归一化后便可将其转化为正常的分布。
-  **初始数据包 (IDP)**：IDP 用于从数据流的第一个数据包中获取数据包数据。它可以提取相关数据，例如 HTTP URL、DNS 主机名/地址和其他数据元素。TLS 握手可通过几条消息完成，这些信息中包含用于提取数据元素（例如密码套件、TLS 版本和客户端的公共密钥长度）的相关未加密元数据。

### NBAR[^NBAR]

#### 简介

Network-Based Application Recognition（NBAR）是Cisco IOS中提供的功能，是一个可以识别各种应用程序的分类引擎。

该程序能识别的协议在[NBAR](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/network-based-application-recognition-nbar/prod_case_study09186a00800ad0ca.html)的表2-表4已列出，数量非常有限。



### Sniper[^sniper]

#### 简介

​	浩瀚深度提供了一个名为**Sniper**的嵌入式DPI引擎，可以运行在x86,ARM,MIPS平台上，系统要求为Linux，直接以软件动态库的形式提供给用户。

> Sniper是一款高速、精准、安全、跨平台的、嵌入式网络流量识别引擎，提供2到7层网络可视化的嵌入开发能力。Sniper采用被动监听的方式，识别每条五元组会话上承载的应用信息，提供协议和应用的元数据。用户可通过API实时获得相关的应用识别结果和元数据。
>
> Sniper致力于为5G、边缘计算、SDWAN、NFV、IOT等产业领域提供2-7层的应用可视化能力，服务于运营商、通信设备商、网络安全服务商等，缩短产品开发周期，提升用户的产品价值，让网络可视化能力下沉到每个终端。

![](/assets/img/academic/protocol/cpgs.png)

根据介绍可以识别出2000余种应用特征，并且允许用户自定义特征和匹配规则。目前Demo版允许免费下载。

#### 方法

Demo版里提供了一个使用说明书，包含了其中的原理、自定义规则方法和详细的API介绍。

业务分类和特征规则：

> 所使用的识别规则(一般称为“特征库”)有其特定语法，不但支持以正则表达式表示的特征串，还支 
>
>持报文长度等其他特征表达方式。识别某些业务的特殊算法实现在源代码中，不在特征库中体现。 
>
>协议分析人员以文本文件方式编辑部分特征库(一般称为“特征文件”)，sniper初始化时将特征文件“编译”为内部特征库，编译成功后，此内部特征库可以被序列化并保存为文件， sniper初始化时也可以直接加载此文件，不用进行“编译”过程，节省时间。 

识别过程：

>用户程序(即使用sniper库的程序)负责报文收发和流表维护，并将报文送给sniper进行业务识别。sniper通过多种方式识别报文内容，给出识别结果。用户程序负责维护各条流的识别结果和识别过程所需要的“私有数据”。 即使流已经被识别，用户程序还可以调用sniper对HTTP报文进行解析，提􏰀取其中某些重点HTTP域(我们一般称为“HTTP锚点”) 的值。 

API的目录部分大致截图如下：

![](/assets/img/academic/protocol/api1.png)



![](/assets/img/academic/protocol/api2.png)

## DPI[^DPI]

### 简介

DPI（深度包检测技术）技术是一种基于应用层的流量检测和控制技术，对**应用层**数据的协议识别，数据包内容检测与深度解码。

![](/assets/img/academic/protocol/dpi.jpg)

功能：**业务识别**，**业务控制**，**业务统计**

主要通过三种模式进行流量的特征识别：**提取流量的特征字符串**，**正则表达式**，**特定比特流**

目前也发展出了DFI（Deep/Dynamic Flow Inspection，深度/动态流检测），采用基于流量行为的应用识别技术。

### 厂商

#### 浩瀚深度

浩瀚深度成立于1994年，是一家为中国互联网提供网络流量监测控制系统、网络大数据采集平台和数据应用服务的厂商。

根据其官网介绍的HDT5000流量管理系统，可以识别超过2000种物联网协议和应用，并且可以在线升级业务配置文件。性能方面：其单个业务处理板卡可以处理400Gbps流量，满配最高可以到1.2Tbps。

![](/assets/img/academic/protocol/hdt.png)

浩瀚深度提供了名为Sniper的DPI引擎，在前面协议分析的产品中进行了介绍。

#### 微智信业

微智信业成立于2003年，专注于互联网网络安全及信息安全、通信业务安全及互联网业务安全两大领域研究。

根据天眼查信息显示，其在2015年已被东方通公司收购。

他们的DPI设备MV-DPI-20/40采用了DPI和DFI技术，对网络流量进行深度检测和分析，主要功能有流向分析统计、协议及业务识别、网站业务质量分析、串并接控制、DdoS攻击监测、图文还原、数据回填回放等。

性能方面：在2U的高度下，单台可以实时处理40Gbps的网络流量[^微智信业 ]


#### 百卓[^百卓]

北京百卓网络技术有限公司，成立于2005年4月，总部位于北京，是一家致力于构建下一代安全互联网的高新技术企业。

其网络可视化产品如T7600、T9612提供如下功能：

- 报文分类和过滤
- 流量的分发与复制
- 隧道协议识别
- 数据报文处理
- 流管理和流统计

性能方面可以提供400Gbps的深度报文处理能力。

#### 华为

根据历年的新闻可以看到中国移动在DPI设备招标时，华为都会大量中标，但新闻上均未说中标的是哪个系列的设备。

![](/assets/img/academic/protocol/hw.png)

SIG系列的业务网关，如SIG9800，能识别2000+种应用/协议，性能方面最高支持100Gbps的带宽。[^sig]

USG系列的虚拟综合业务网关，如USG6000V，则声称可以识别6000+种应用，精度细到区分应用的功能：例如微信的文字和语音。[^usg]

### 总结

关于协议识别的技术目前已经有了相当多的研究。随着协议数量的增加，网络上未知协议和加密协议的比例也一直在增长，所以协议识别的难度一直在提升，研究方向也从最早期的基于负载到现在的基于深度学习。

目前主流的协议识别方法主要还是基于端口号和基于负载的结合，需要人为的去写解码器，能识别的协议基本在2000余种左右，除了华为的USG系列产品可以识别6000种，可能也是对主流的2000种进行了进一步的划分。

对于未知协议和加密协议，有很多论文提出使用机器学习或深度学习的方法进行分类，但可能因为成本和效率的原因，真正应用的比较少。也有很多公司的态势安全产品则使用了基于行为分析的方法对流量进行分类。


### 参考

[^Cisco]:思科 Stealthwatch 企业版 https://www.cisco.com/c/zh_cn/products/security/stealthwatch/index.html
[^思科白皮书]:思科白皮书 - 加密流量分析 https://engage2demand.cisco.com/LP=5911?oid=wprsc000821
[^科来]: http://www.colasoft.com.cn/products/capsa.php
[^protocol]:[协议的分用以及wireshark对协议的识别. https://www.cnblogs.com/Leo_wl/p/3308958.html
[^ProLearner]:王一鹏, 云晓春, 张永铮, et al. 基于主动学习和SVM方法的网络协议识别技术[J]. 通信学报, 2013(10):135-142.
[^ACAS]:Haffner P, Sen S, Spatscheck O, Wang D. ACAS: automated construction of application signatures. Proceedings of the 2005 ACM SIGCOMM workshop on Mining network data; Philadelphia, Pennsylvania, USA. 1080183: ACM; 2005. p. 197-202.
[^1]:Ma J, Levchenko K, Kreibich C, Savage S, Voelker GM. Unexpected means of protocol inference. Proceedings of the 6th ACM SIGCOMM conference on Internet measurement; Rio de Janeriro, Brazil. 1177123: ACM; 2006. p. 313-26.
[^Discoverer]:Cui W, Kannan J, Wang H J. Discoverer: Automatic Protocol Reverse Engineering from Network Traces[C]//USENIX Security Symposium. 2007: 1-14.
[^KISS]:Finamore A, Mellia M, Meo M, Rossi D. Kiss: Stochastic packet inspection classifier for udp traffic. IEEE/ACM Transactions on Networking (TON). 2010;18(5):1505-15.
[^Tupni]:Cui W, Peinado M, Chen K, Wang HJ, Irun-Briz L, editors. Tupni: Automatic reverse engineering of input formats. Proceedings of the 15th ACM conference on Computer and communications security; 2008: ACM.
[^NG_MON]:Kim MS, Won YJ, Hong JWK. Application‐level traffic monitoring and an analysis on IP networks. ETRI journal. 2005;27(1):22-42.
[^特征串]:陈亮, 龚俭, 徐选. 基于特征串的应用层协议识别[J]. 计算机工程与应用, 2006(24):20-23+90.
[^2]:Amei W, Huailin D, Qingfeng W, et al. A survey of application-level protocol identification based on machine learning[C]//2011 International Conference on Information Management, Innovation Management and Industrial Engineering. IEEE, 2011, 3: 201-204.
[^3]:Zhu P, Zhang S, Luo H, et al. A semi-supervised method for classifying unknown protocols[C]//2019 IEEE 3rd Information Technology, Networking, Electronic and Automation Control Conference (ITNEC). IEEE, 2019: 1246-1250.
[^decode]:https://www.wireshark.org/docs/wsug_html_chunked/ChCustProtocolDissectionSection.html
[^wiki]:https://wiki.wireshark.org/ProtocolReference
[^5]:Zuozhi S, Yue Y, Yunlang M, editors. The Research of Protocol Identification Based on Traffic Analysis. 2017 10th International Conference on Intelligent Computation Technology and Automation (ICICTA); 2017: IEEE.
[^4]:Lin R, Li O, Li Q, Liu Y, editors. Unknown network protocol classification method based on semi-supervised learning. 2015 IEEE International Conference on Computer and Communications (ICCC); 2015: IEEE.
[^6]:Jie Z, Jianping L, editors. Feature identification of unknown protocol. 2016 13th International Computer Conference on Wavelet Active Media Technology and Information Processing (ICCWAMTIP); 2016: IEEE.
[^7]:Chen T, Liao X, editors. An optimized solution of application layer protocol identification based on regular expressions. 2016 18th Asia-Pacific Network Operations and Management Symposium (APNOMS); 2016: IEEE.
[^8]:Chen H, You F, Zhou X, Wang C, editors. The study of DPI identification technology based on sampling. 2009 International Conference on Information Engineering and Computer Science; 2009: IEEE.
[^finger]:Shu G, Lee D. A formal methodology for network protocol fingerprinting. IEEE Transactions on Parallel and Distributed Systems. 2011;22(11):1813-25.
[^prodigger]:Sang Y, Li S, Zhang Y, Xu T, editors. Prodigger: Towards robust automatic network protocol fingerprint learning via byte embedding. 2016 IEEE Trustcom/BigDataSE/ISPA; 2016: IEEE.
[^9]:Ma R, Qin S, editors. Identification of unknown protocol traffic based on deep learning. 2017 3rd IEEE International Conference on Computer and Communications (ICCC); 2017: IEEE.
[^accurate]:Moore AW, Papagiannaki K, editors. Toward the accurate identification of network applications. International Workshop on Passive and Active Network Measurement; 2005: Springer
[^DPI]:深度包检测(DPI)详细介绍.https://www.cnblogs.com/vancasola/p/9692249.html
[^百卓]:北京百卓网络技术有限公司.http://www.byzoro.com/
[^sniper]:Sniper: 嵌入式DPI引擎.https://sniper.haohandata.com/index.html
[^Sig]: SIG9800 业务网关.https://carrier.huawei.com/cn/products/fixed-network/b2b/Security/sig9800

[^usg]:USG 6000V虚拟综合业务网关.https://e.huawei.com/cn/products/enterprise-networking/security/firewall-gateway/usg6000v

[^微智信业 ]: 北京微智信业科技有限公司.http://www.mvtech.com.cn/
[^NBAR]:Network-Based Application Recognition.https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/network-based-application-recognition-nbar/prod_case_study09186a00800ad0ca.html
[^dfref]:https://www.wireshark.org/docs/dfref/