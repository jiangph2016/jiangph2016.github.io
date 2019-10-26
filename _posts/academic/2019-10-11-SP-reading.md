---
layout: post
title: 2019 IEEE Symsium on Security and Privac论文粗读
category: 学术
tags: 论文
keywords: S&P
---
## Hardware Security

### Spectre Attacks:  Exploiting Speculative Execution

现代的处理器通过分支预测和 __推测执行__ 来加快处理速度，比如猜测将要运行的某条指令，如果猜中了就提交这条指令的运行结果。这就对应着题目中说的Speculative Execution，翻译成中文是投机性的执行。  
幽灵攻击指的就是诱导受害者去 __推测执行__ 一些恶意的指令,获取受害者的信息并通过侧通道将信息泄露出去。  
目前已经在Intel、AMD和ARM的处理器中都发现了易受攻击的 __推测执行__  ，而解决方案是对处理器的设计进行修复，更新指令集。所以这个的解决看起来还比较麻烦

### SoK: The Challenges, Pitfalls, and Perils of Using Hardware Performance Counters for Security

这篇文章探讨了有关硬件性能计数器（简称HPC，和高性能计算机的简称一样）的安全问题，文章主要是对之前有关于HPC的论文都进行了研究并总结。

### Theory and Practice of Finding Eviction Sets

文章中讲了一个更快的找到逐出集（Eviction sets）的方法，但我并不了解逐出集，所以没有细看。

### Exploiting Correcting Codes: On the Effectiveness of ECC Memory Against Rowhammer Attacks

这篇文章探讨的是ECC内存是否能够抵抗Rowhammer攻击。关于Rowhammer,我看了一篇文章名为[神乎其神的Rowhammer：用比特位翻转实现云虚拟机夺权](https://www.freebuf.com/column/133871.html)，大致意思是通过比特位反转实现攻击手段，而ecc（纠错码）内存会检测到错误，并立即修补反转。所以这两者一个在破坏，一个在修补，最后作者的结论是，针对ecc的攻击虽然很难，但仍然是可行的。

### Self-encrypting deception: weaknesses in theencryption of solid state drives

这篇文章感觉是应该是一篇比较有影响力的文章，假如作者没有夸大危害性的话。  日常生活中买的硬盘一般都会提供硬件加密的功能，虽然我一直很少用这个功能，但感觉只要全盘加密后，数据基本不会被获取。但是这篇文章指出多个型号的固态硬盘（SSD）的加密模型存在安全漏洞，这些漏洞可以导致数据在没有密钥的情况下被恢复出来。  

### RIDL: Rogue In-Flight Data Load

这片文章和第一篇文章的方向一样也是关于 __推测执行__ 的攻击，这里起名为RIDL

## Blockchain & Cryptocurrency

### Perun: Virtual Payment Hubs over Cryptocurrencies

背景：最近出现了名为Payment channels的加密货币交易方法，可以在链下支付，速度快，但是需要中介参与。  
文章中介绍了一种新的技术名为Perun，是一个virtual Payment Channels，可以避免中介参与而进行加密货币的交易。
  
### Redactable Blockchain in the Permissionless Setting.

文章里提到比特币是一个不可修改的没有权限的区块链系统，所以很多应用程序都利用这个不可更改性来作为公告板使用。这里我理解的是很多人都可以在比特币上发布一些不可更改的数据，并且没有权限控制。于是比特币区块链中就出现了很多的非法数据。  
文章中提出了一个可重写的区块链设置，并且可以整合到比特币中，修改的机制是基于投票，只要收到足够的票数，一个修改就可以在链上执行。

### Proof-of-Stake Sidechains

这里提到了一个从来没有听过的名词叫Sidechains（侧链），百度了一下这个词，意思是将不同的区块链互联在一起，这样就可实现数字资产在不同区块链之间的互相转移（不太理解这是怎么做到的）  
这篇文章就是给侧链有了一个安全性定义。

### Ouroboros Crypsinous: Privacy-Preserving Proof-of-Stake

这篇文章的题目中和上篇文章都用到了Proof-of-Stake这个词，专门百度了一下叫“权益证明”，简写是PoS，是一种公共区块链的共识算法。  
参考：  
[Proof of Stake FAQ](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ)  
[区块链共识算法Proof-of-Stake](https://blog.csdn.net/omnispace/article/details/80248132)

这篇文章是介绍了一个名为Ouroboros Crypsinous的一个隐私保护权益证明的区块链协议。

### Lay Down the Common Metrics: EvaluatingProof-of-Work Consensus Protocols’ Security

这篇文章提到了Proof of Work（PoW）即工作量证明，也是区块链这个部分的专有名词，这篇文章大意是分析了PoW的安全性，并给出了建议。

### XCLAIM: Trustless, Interoperable,Cryptocurrency-Backed Assets

这篇文章和侧链那篇一样也是在讲跨区块链交易，但是文章中并没有直接提到sidechains这个词，只有在参考文献中参考了几个有关侧链的文章。文章提到目前accs（atomic cross-chain swaps）是唯一已知的支持跨区块链的加密货币交易机制，接着提到了它的缺点：速度慢，效率低、成本高。所以文章中提出了XCLAIM，与accs相比速度更快，成本更低

## Web Security

### Does Certificate Transparency Break the Web?Measuring Adoption and Error Rate

文章讲到证书透明性（CT）是一个13年标准化，到现在已经被广泛支持的一个系统，用途是用来发现恶意或发行错误的数字证书。 
介绍中讲了一下CT的原理：将CA证书记录在公开审核的日志中，如果有的网站没有正确的执行CT，那么浏览器会给出警告。而频繁的警告会导致警告疲劳，以影响用户的正确判断。
文章对CT在web上的采用情况进行了分析，并探讨了警告的错误率。

### *EmPoWeb: Empowering Web Applications with Browser Extensions

背景：  
1. 浏览器拓展插件不受同源策略的约束，可以读写web应用程序上的数据，可以访问敏感的用户信息，甚至存储数据到电脑上。  
2. 拓展插件可以和web应用通信，web应用可以利用拓展插件的功能

结合以上的背景，文章对Chrome、Firefox和Opera浏览器中拓展插件和web应用的通信接口进行了分析，发现web应用程序可以通过浏览器插件绕过同源策略来访问敏感的数据。

### “If HTTPS Were Secure, I Wouldn’t Need 2FA”-End User and Administrator Mental Models ofHTTPS

这篇文章是分析了用户和管理员对于HTTPS的心理模型,大意是说用户会将加密和身份验证混淆，而管理员则不理解功能协议组件之间的相互作用。

题目中的2FA指的是2-Factor-Authentication，也就是双重验证，例如用户在登录时输入完密码后还要求输入手机的验证码。所以题目中的“If HTTPS Were Secure, I Wouldn’t Need 2FA”就是在说用户对加密和身份验证的混淆，他们认为网站是https的，所以就不需要双重验证了。

### Fidelius: Protecting User Secretsfrom Compromised Browsers

一个新的架构，名叫Fidelius，通过集成到浏览器中受信任的硬件区域来保护用户的数据。即使浏览器和操作系统都被恶意控制了，这个Fidelius也能提供保护。
在性能开销方面，对安全页面的加载和交互会有用户可接受的开销，在非安全页面上的开销没有影响。
看了一下它用的硬件区域，是一个名为Hardware enclaves（enclave翻译成中文：飞地(某国或某市境内隶属外国或外市，具有不同宗教、文化或民族的领土)）的地方，比如Intel处理器的SGX。

### *Postcards from the Post-HTTP World:Amplification of HTTPS Vulnerabilities in the Web Ecosystem 

文章中分析了web应用在https上的安全性，https并不能完全的提供安全性保证，例如未正确部署的https（比如没有采用HSTS）可以让攻击者识别出https并且避开安全保证。
所以即使采用了https进行加密，但还是存在漏洞让攻击者有机可乘，文章对web应用的不安全性第一次进行了一个系统定量评估。

## Privacy

### Towards Practical Differentially Private Convex Optimization

这篇文章的方向和之前在Usenix中读到的  __Evaluating Differentially Private Machine Learning in Practice__ 一样，提到了即使训练数据没有公开过，但是模型也有可能泄漏训练时的隐私数据。所以最近将Differentially Private（DP）作为敏感数据执行学习任务的黄金标准。
文章的贡献是对DP凸优化（并不理解什么事）的见解和提供了新的算法。

### PrivKV: Key-Value Data Collection with Local Differential Privacy

文章关于一个新的技术叫局部差分隐私（LDP），是一个保护隐私的分布式数据采集技术。

### *Differentially Private Model Publishing for Deep Learning

这篇文章也是关于差分隐私，里面提出了一个差分隐私方法来训练神经网络。

### KHyperLogLog: Estimating Reidentifiability and Joinability of Large Data at Scale

文章中使用了一种算法名为KHyperLogLog，来评估超大型数据库的reidentifiability和joinability（不理解这两个特性的实际含义），算法的优点是线性的时间复杂度和使用了最小的内存空间。

### Characterizing Pixel Tracking through the Lens of Disposable Email Services

目前很流行临时电子邮箱服务，用户可以在一些网站注册账户时不留下自己的真实邮箱，而是使用临时邮接收验证码。文章中对这种一次性电子邮件服务进行了研究，得出的结论是通过这种方式注册的账户很容易被劫持，导致信息泄露等问题。  
其实我觉得使用临时邮箱服务的账户不安全是必然的，因为这就相当于把邮箱找回密码的权限送给了别人，而且往往用户也不会在意这种临时邮箱注册的账户。

## Program Analysis

### Asm2Vec: Boosting Static Representation Robustness for Binary Clone Search against Code Obfuscation and Compiler Optimization

文章关于逆向工程，作者们开发了名为Asm2Vec的汇编代码表示学习模型，只需要输入汇编代码，就可以在代码中出现的标记之间自动找到匹配的语义关系

### Iodine: Fast Dynamic Taint Tracking Using Rollback-free Optimistic Hybrid Analysis

里面提到了一个之前没听说过的概念：动态信息流跟踪（DIFT），可以检测各种安全性攻击，因为使用时必须监视每条指令，所以会有巨大的性能开销。而静态程序分析发现安全性问题的能力不够强。所以这篇文章提出了乐观混合分析（OHA），可以实时分析安全性问题，而性能开销相比于DIFT降低到了9%。

### CaSym: Cache Aware Symbolic Execution for Side Channel Detection and Mitigation

文章关于基于cache的侧通道攻击，作者建议使用CaSym，并讲了其中独特的功能（然而我并不理解）。

### Towards Automated Safety Vetting of PLC Codein Real-World Plants

文章关于可编程逻辑控制器（PLC）的安全问题。

### Using Safety Properties to Generate Vulnerability Patches

当软件出现漏洞时，程序员需要尽快发布漏洞补丁，所以就出现了自动程序修复（APR）的需求。然而目前的APR都不能满足需求，反而可能引入新的bug。所以作者提出了基于property的APR名为SenX，可以有效的生成补丁。
## Protocols and Authentication

### Reasoning Analytically About Password-Cracking Software

之前有大量的文献都是通过密码破解算法来估算密码强度的，但是仅仅适用于概率密码模型。文章中作者分析了基于transformation的密码破解软件，做到了可以不通过枚举猜测的方法开来估算密码强度。

### True2F: Backdoor-resistant authentication tokens
用硬件作为双重身份验证是目前很常用的一种方法，但是如果硬件设计的有漏洞或者有后门，会给账户安全性带来问题，比如攻击者可以仿造别人的硬件凭证。  
所以文章中提出了一个关于双重身份验证的系统，名为True2F，可以解决令牌故障或者存在后门的问题。

### *Beyond Credential Stuffing:Password Similarity Models using Neural Networks

撞库攻击很普遍，因为很多人都会选择在很多的网站上使用相同的密码。这篇文章的作者训练了一个密码相似性模型，构造了一个目前最具破坏性的攻击。只要知道用户其中的一个密码，就可以在不到1000次猜测中破坏超过16%的账户。

### The 9 Lives of Bleichenbacher’s CAT:New Cache ATtacks on TLS Implementations

文章基于Bleichenbacher之前的一篇论文，主题是padding  oracle  attack，不太理解什么意思，反正是实现了一种新的TLS攻击。

### An Extensive Formal Security Analysis of the OpenID Financial-grade API

目前银行需要基于web的API向第三方服务开放一些在线业务，这就要求api的安全性很高。目前有一个标准名为FAPI，文章对FAPI的安全性进行了系统化的分析，发现了部分漏洞并给出了缓解方法，为FAPI的发展作出了重要的贡献。

## Mobile and Location Security

### Short Text, Large Effect: Measuring the Impact of User Reviews on Android App Security & Privacy

文章与技术的相关性较低。文章中分析了Google Play中应用的评论，筛选出了其中的与安全、隐私相关的评论（SPR，security and privacy relevant reviews）,发现运行时要求许可权限的应用会收到更多的SPR，而SPR也可以促进开发人员遵守规范，让应用程序的行为更加透明。

### Demystifying Hidden Privacy Settings in Mobile Apps

移动应用中有很多的隐私设置，而这些隐私设置很难被找到或者会迷惑用户。作者使用了名为Hound的工具，在Google Play和第三方应用市场上最新的10000个App进行了分析，发现有超过三分之一的APP中的隐私设置是存在问题的。  
这篇文章的意义在于让人知道目前的App都会倾向于利用用户的隐私，并且故意把设置藏的很深。而且有超过82.16%的APP中关于隐私分享的设置是默认打开的。

### Security of GPS/INS based On-road Location Tracking Systems

GPS是目前常用的定位系统，但是容易受到信号欺骗攻击，所以就有惯性导航系统（INS）作为补充。文章评估了INS+GPS的安全性，并研发了一种算法使得陀螺仪和加速度计不起作用，最终可以误导受害者到达另一个目的地。最后提出了对策可以一定程度上抵抗攻击者的攻击。  
读完这篇文章我突然有一种感觉，好像前面也有很多文章的思路都是类似于这种，先研究出一种攻击方法，再针对这种攻击方法给出个解决方案（甚至可能只是缓解方案，因为不能完全解决这个问题）。虽然我承认这样子算是未雨绸缪，也是有贡献的，但可能也在一定程度上促进了攻击的发展，怀有恶意的人可以通过学习这篇论文而快速掌握攻击方法并应用，然而论文提出的防御方案可能还需要很长的的时间才被应用到现实中。

### Understanding the Security of ARM Debugging Features

这篇文章偏向于硬件，关于ARM调试平台的安全问题。

### SENSORID: Sensor Calibration Fingerprinting for Smartphones

目前安卓设备上应用程序可以通过多种方法来识别（Fingerprinting）一个手机，然而iOS设备上还没有很好的一个方法来识别一个设备。文章提出了一种识别方法名为calibrationfingerprinting，通过分析iOS设备中的传感器数据就可以推断出设备的出厂校准数据，用这种方法就可以获取设备唯一的指纹，并且这个指纹永远不会改变。

### Tap 'n Ghost: A Compilation of Novel Attack Techniques against Smartphone Touchscreens

文章讲到了一个名为Tap‘n Ghost的攻击，可以攻击具有NFC技术的手机的触摸屏。文章中举了一个例子：在某处嵌入一个NFC卡，NFC卡接触到用户手机时，会尝试连接到攻击者的蓝牙鼠标，这时候再利用一个技术名为ghost touch generator，改变用户的选择以连接到蓝牙鼠标上，这时候攻击者就获得了用户手机的控制权限。

## Machine Learning

### Certified Robustness to Adversarial Examples with Differential Privacy

这篇文章关于对抗性的样本对机器学习的干扰。目前针对这种攻击的防御大多都是尽力而为，并且容易遭受到更复杂的攻击。目前已有的认证的防御措施（不理解什么是叫认证的防御措施）无法扩展到大型的数据集，所以作者提出了一个新的经过认证的防御措施名为PixelDP，可以拓展到大型的网络和数据集。

### DEEPSEC: A Uniform Platform for Security Analysis of Deep Learning Model

这篇文章的方向与上一篇一样，也是关于深度学习的模型会受到对抗性样本的攻击。这篇文章提出了一个平台名为DEEPSEC，对对抗攻击和防御进行了综合的评估。

### Exploiting Unintended Feature Leakage in Collaborative Learning

协作式机器学习，允许多人进行局部训练和定期交换模型来构建联合模型。文章中提到这种方式会泄露参与者的信息。

### *Neural Cleanse: Identifying and MitigatingBackdoor Attacks in Neural Networks

深度神经网络（DNN）就像一个黑匣子，缺乏透明性和可解释性。假如在DNN训练的时候插入后门，这样经过训练后的系统可能在识别到某个特定的符号时做出错误的判断（文章中举例一个面部识别的模型，假如在脸上识别到指定的符号，就可以将此人判定为比尔盖茨）。这种后门一般难以发现，只有被触发时才能发现，而且对整体分类结果的正确性不会有太大的干扰。  
文章中提出了一个可以在DNN中后门攻击检测和缓解的系统，能识别到后门并重建可能的触发器。

### Helen: Maliciously Secure Coopetitive Learning for LinearModels
    
有很多组织希望合作在合并的数据集上共同训练模型，但又不愿意暴露自己的纯数据，所以这篇文章设计了Helen，是一个允许多方训练线性模型而又不泄漏数据的系统，这也被成为竞争性学习（coopetitive Learning）。根据评估显示Helen相比较于以前的安全的多方计算框架，性能提升了5个数量级（数量级：orders of magnitude）
    
### Comprehensive Privacy Analysis of Deep Learning: Passive and Active White-box Inference Attacks against Centralized and Federated Learning

文章讲的是深度学习记住训练数据中的隐私数据，可能会遭到推理攻击(类似于28th USENIX的 __The Secret Sharer: Evaluating and Testing Unintended Memorization in Neural Networks__ )。文章设计了白盒推理攻击来对深度学习模型进行全面的隐私分析。

## Fuzzing

### Razzer: Finding Kernel Race Bugs through Fuzzing

内核中的数据竞争是一类重要的错误，会影响系统的可靠性。
>数据竞争（data race）是指在非线程安全的情况下，多线程对同一个地址空间进行写操作。一般来说，我们都会通过线程同步方法来保证数据的安全，比如采用互斥量或者读写锁。但是由于某些笔误或者设计的缺陷，还是存在data race的可能性的。  
来自[数据竞争（data race）问题分析的利器——valgrind的Helgrind](https://blog.csdn.net/breaksoftware/article/details/81369152)

文章提出了Razzer，将模糊测试引导到内存中潜在的数据竞争点，即在内核中查找竞争错误。
### ProFuzzer: On-the-fly Input Type Probing for Better Zero-Day Vulnerability Discovery

文章中提出了一个实施探测技术名为ProFuzzer，可以更大概率的发现零日漏洞。

### Full-Speed Fuzzing: Reducing Fuzzing Overhead through Coverage-Guided Tracing.

这篇文章讲的是覆盖率指导的模糊测试的概念及相关的实现，关于模糊测试应该是一篇很好的文章，可惜里面读不懂的名词太多，所以没有仔细去看。

### NEUZZ: Efficient Fuzzing with Neural Program Smoothing

文章里提到目前最流行的模糊器使用进化指导来生成输入，但是进化算法容易实现却容易陷入毫无结果的随机突变序列中。梯度引导技术已经被证明性能上优于进化算法，但不能直接用于模糊测试，因为现实中的程序行为存在很多的不连续性、平稳段和隆起（这句话对我来说太抽象了，反正就是知道梯度引导不能直接用在模糊测试上就行了），文章中使用了一个平滑的替代函数来解决这个问题。  
这篇文章更偏向于神经网络的算法。

### Fuzzing File Systems via Two-Dimensional Input Space Exploration. 

文章中介绍了一个发现文件系统bug的模糊测试器JANUS，作者在八个文件系统上评估了JANUX，在Linux内核中共发现了90个错误，其中62个已经被确认。

##  Side Channels and Data Leakage

### F-BLEAU: Fast Black-Box Leakage Estimation. 

关于黑盒的信息泄漏和测量的一篇文章，不只是没读懂，想讲什么我也不太清楚……

### Synesthesia: Detecting Screen Content via Remote Acoustic Side Channels. 

计算机屏幕内部发出的声音可以用来检测屏幕上显示的内容，所以只要通过各种渠道拾取到声音就能获取屏幕上显示的图像。


### Port Contention for Fun and Profit.

文章中使用同步多线程架构（SMT）作为侧通道泄漏源，在具有超线程功能的英特尔的Skylake和KabyLake架构上进行了端到端攻击。

### Attack Directories, Not Caches: Side Channel Attacks in a Non-Inclusive World. 

云计算上的多台虚拟机是共享一个物理机的，所以这就导致可以通过共享的资源发动攻击，虽然目前已经进行了VM之间的虚拟内存隔离，但是文章提出即使两个虚拟机在不同的处理器内核上，也可以通过处理器上最后一级缓存（LLC）发动攻击。

### Hard Drive of Hearing: Disks that Eavesdrop with a Synthesized Microphone. 

虽然很多注重隐私的人会禁用设备中的传感器来防止信息的泄露，但磁性硬盘中的机械组件可以充当麦克风，并且有足够的精度提取和解析人类的语言。（感觉这个实现起来应该很麻烦，但是如果真的实现了则很容易暴露隐私。）

## Systems and Applied Security

### "Should I Worry?" A Cross-Cultural Examination of Account Security Incident Response.

这篇文章是对用户的账户受到威胁时的反应进行了分析，来探索如何更好的保护用户安全。

### Bitcoin vs. Bitcoin Cash: Coexistence or Downfall of Bitcoin Cash?

在2017年8月以后，出现了比特币现金（BCH），矿工可以在两种币种之间进行选择，以获得更高的利润。文章分析了两个币种之间挖矿能力的的平衡性（涉及到两个币种之间不同的规则，而且BCH的挖矿规则会动态的调整）。

### Stealthy Porn: Understanding Real-World Adversarial Images for Illicit Online Promotion.

文章关于对抗性机器学习。目前使用深度学习的方法可以检测出色情图像，这也导致了对抗性促销色情图片（APPI）的出现，可以逃避色情图像的检测。文章从社交媒体上抓取了4042690张图像，从中发现了4000多个APPI，然后揭示了它们用于规避检测的技术以及这些技术起作用的原因。

### LBM: A Security Framework for Peripherals within the Linux Kernel.

文章提出了一个安全框架名为LBM，提供了一个统一的API，保护Linux的内核不受恶意外接设备（如恶意USB、蓝牙、NFC）的损害。这个框架的开销在1μ秒以内，可以忽略不计。这是第一个为Linux内核中的恶意外接设备提供保护的安全框架。

### SoK: Shining Light on Shadow Stacks.

文章关于内存、寄存器的安全，读不懂。

### Kiss from a Rogue: Evaluating Detectability of Pay-at-the-Pump Card Skimmers. 

跟USENIX中的一篇文章一样，也是关于加油站中的撇油器的安全问题。

## Cryptography & Encrypted Data
这个系列关于加密，基本都没读懂。

### Blind Certificate Authorities.
关于证书颁发机构（CA），文章探索建立一个匿名的CA，在这个系统中任何一方都不知道谁在参与。

### Data Recovery on Encrypted Databases with k-Nearest Neighbor Query Leakage.
文章中对支持一维k近邻查询的加密数据库进行了数据恢复的攻击。

### Threshold ECDSA from ECDSA Assumptions: The Multiparty Case. 

文章关于使用阈值协议的ECDSA签名，因为并不知道这是什么，略过。

### Learning to Reconstruct: Statistical Learning Theory and Encrypted Database Attacks.

关于统计学习理论和加密数据库的攻击

### On the Security of Two-Round Multi-Signatures.

文章关于两轮多重签名方案存在的安全问题。

### New Primitives for Actively-Secure MPC over Rings with Applications to Private Machine Learning.
在CRYPTO 2018上有人提出了一个名为SPDZ2k的协议，是一个基于秘密共享的协议。这篇文章是对此的补充，并实现了新的协议。

## Network Security
### Breaking LTE on Layer Two

题目中的第二层指的是数据链路层，数据链路层协议是目前LTE网络安全研究中的盲点，文章中对此进行了全面的分析，并确定了三种攻击媒介。

### HOLMES: Real-Time APT Detection through Correlation of Suspicious Information Flows.

文章中提出了一个系统名为HOLEMS，使用了一种新的方法来检测APT。

### Touching the Untouchables: Dynamic Security Analysis of the LTE Control Plane. 

文章对LTE网络的控制平面的安全性进行了研究，并用开源的LTE软件实现了一个半自动测试工具，名为LTEFuzz，利用这个工具发现了36个漏洞。使用这些漏洞发起的攻击可能会让用户无法使用LTE服务，伪造短信和窃听用户的数据。

### On the Feasibility of Rerouting-Based DDoS Defenses.
基于僵尸网络的泛洪攻击可以造成巨大的破坏，然而著名的防止泛洪攻击的端到端可用性保证要求在自治系统之间进行代价高昂的全局协调。最近出现了名为routing around congestion（RAC）的建议可以无需跨AS协调，文章对RAC进行了分析。

### Resident Evil: Understanding Residential IP Proxy as a Dark Service. 

第一次听到Residential Proxy（RESIP）住宅代理这个词，与之对应的是数据中心代理，看了这篇文章大概理解了两者之间的区别[数据(Data center)代理与(Residential)住宅代理的区别](https://zhuanlan.zhihu.com/p/47556159)。作者使用了一个渗透框架，对600万个resip进行了研究，发现很多住宅代理的服务器并不完全是自愿加入的，有物联网设备或者被非法操作的主机。

##  Program Languages
这一节虽然关于编程语言，但也是几乎都没读懂
### Simple High-Level Code for Cryptographic Arithmetic - With Proofs, Without Compromises.

文章研究了由硬件和软件中的推测机制引起的信息流。

### SoK: General Purpose Compilers for Secure Multi-Party Computation
安全多方计算（MPC）允许多方计算一个联合的函数，而不显示除了结果以外的任何信息。这个协议目前已经存在了数十年，而且发展很快，以至于专家也很难追踪到现代框架的各种功能。作者的工作是调查了用于安全多方计算的编译器，进行了评估并给出了建议。

### The Code That Never Ran: Modeling Attacks on Speculative Evaluation. 

文章介绍了一种新的方法可以使用机器检查的功能，在简短的高级代码中实现密码算法。作者最后提出了椭圆曲线P-256的高性能实现，并经过了验证。

### Formally Verified Cryptographic Web Applications in WebAssembly.

WebAssembly中经过正式验证的加密Web应用程序。

### SoK: Sanitizing for Security.

C/C++编程语言是不安全的，所以开发人员通常会使用多张方法来发现安全问题（包括手动，静态和动态程序分析）。动态错误查找工具，作者称其为“sanitizers”，可以观察程序的实际执行情况，在发生错误时直接观察程序的错误行为。文章提供了sanitizers的系统概述以及它在发现安全问题中的作用。
## Web and Cloud Security
### Why Does Your Data Leak? Uncovering the Data Leakage in Cloud from Mobile Apps. 

目前越来越多的移动APP使用云作为后端，但是已经出现了大量的数据泄露。根据作者的研究表明，缺乏身份验证、身份验证中滥用秘钥、授权用户的权限配置错误是数据泄露的根本原因，于是作者们设计了一套自动化的程序分析技术，对Google Play中160万个移动APP进行了评估，发现有大约1.5万个APP存在数据泄露的漏洞。
文章的三个作者都来自俄亥俄州大学，但根据姓名判断都是华人，写出来的英文也比较好读，不需要借助翻译软件也能快速的读完并理解意思。

### Measuring and Analyzing Search Engine Poisoning of Linguistic Collisions. 

读到后面才知道search poisoning是什么意思，文章的大意是比如用户想搜索Adobe，但由于拼写错误输入成了idobe，这类拼写错误会占据搜索引擎的很多流量，所以就有不法分子想把拼写错误的搜索结果引导到他们非法的网站上，这就是Search Poisoning。  
但是有很多拼写错误实际上也是合法的，比如idobe在尼日利亚语中也是合法的单词，这里称为语言冲突搜索（linguistic-collision  search）。作者设计了一种深度学习模来收集语言冲突搜索词，然后发现冲突的搜索词在搜索引擎上的滥用情况很普遍，在Google和百度上约有1.19%的搜索词在第一页上会指向恶意网站。

### How Well Do My Results Generalize? Comparing Security and Privacy Survey Results from MTurk, Web, and Telephone Samples. 

文章关于Amazon Mechanical Turk（MTurk），安全和隐私研究人员通常依靠从MTurk收集的数据来评估安全工具，了解用户的隐私偏好并评估他们在网络上的行为。

### PhishFarm: A Scalable Framework for Measuring the Effectiveness of Evasion Techniques against Browser Phishing Blacklists. 

网络钓鱼的数量越来越多，并且技术也日益成熟，会使用多种技术来避免安全基础架构的检测。文章介绍了PhishFarm，是一个用来检测反钓鱼实体和钓鱼网站黑名单有效性的框架，并且可以测试未来钓鱼网站用来逃避检测的技术。  
根据作者的实验发现，目前的安全基础架构存在缺陷，这个缺陷会使得钓鱼网站不被发现，以至于受害者可以正常访问。而且有一个目前很严重的问题：在手机上的浏览器（如chrome，Firefox和Safari，也就是说安卓和苹果都包括在内了）是无法使用钓鱼网站的黑名单机制来保护用户的。

## IoT Security

### *SoK: Security Evaluation of Home-Based IoT Deployments. 

目前物联网设备的安全问题很多，并且没有一个统一的解决措施。文章的贡献是对基于家庭的物联网设备评估了安全状况，对相关文献进行系统化收集并建立了一个门户网站。

### Dangerous Skills: Understanding and Mitigating Security Risks of Voice-Controlled Third-Party Functions on Virtual Personal Assistant Systems.

关于虚拟个人助理（VPA）的安全问题，文章中提到的Alexa和Google Assistan在国内虽然几乎用不到，但也有类似的替代者。这里面提到的安全问题是指让语音助理误解用户的指令，或者伪造语音来获取用户的信息。根据作者的实验，这些攻击确实构成了威胁，并且这个威胁也被亚马逊和谷歌承认了。

### Drones' Cryptanalysis - Smashing Cryptography with a Flicker.

无人机可能被用来偷窥，所以需要一种方法来知道天空中的无人机是否是出于正当的目的，这个作者解决问题的脑洞挺大的。
步骤：首先对可能被监视的物体施加一个周期性的物理刺激，只要摄像机在拍这个物体，那么拍摄的视频中就会出现特定的水印，然后再侦听传输视频的加密流量，检测其中是否存在水印。如果出现了水印，那么则证明无人机正在拍摄这个物体。
### Dominance as a New Trusted Computing Primitive for the Internet of Things. 

文章介绍了一个名为CIDER的系统，用来控制大量的物联网设备，包括对物联网设备的指定固件进行更新。




