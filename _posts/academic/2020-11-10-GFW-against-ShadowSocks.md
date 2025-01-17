---
layout: post
title: 【精读】How China Detects and Blocks Shadowsocks
category: 学术
tags: 论文
keywords: GFW
---
* content
{:toc}


类型 | 内容  
-- | --   
标题|How China Detects and Blocks Shadowsocks 
会议|IMC2020  
DOI| <10.1145/3419394.3423644>  
原文地址|<https://dl.acm.org/doi/abs/10.1145/3419394.3423644>  


# GFW检测和屏蔽ShadowSocks的方法

## 介绍
ShdowSocks是一种常见的代理协议，文章通过实验发现了目前检测和屏蔽ShadowSocks的方法，并给出了一些建议用来规避检测。
自从2017年10月以来，有些用户开始报告自己的shadowsocks服务器开始不可用，尤其是在某些特别时期。  

根据作者的研究发现，GFW使用被动流量分析和主动探测组合来识别shadosocks服务器。

## 背景 

shadowsock协议通过对数据加密使其显示为随机的字节流。  
ShadowSocks使用两种密码学构造,分别是`stream ciphers`和 `AEAD ciphers`

>流加密：单纯的对称加密算法，其解密步骤是无法确认密钥是否正确的。也就是说，加密后的数据可以用任何密钥执行解密运算，得到一组疑似原始数据，而不知道密钥是否是正确的，也不知道解密出来的原始数据是否正确
```
[variable-length IV][encrypted payload...] 
```

>AEAD(authenticated encryption with associated data)是同时具备保密性，完整性和可认证性的加密形式

![](https://pic1.zhimg.com/80/v2-27436bd00918fb8ba1325e4fe30c8f44_1440w.png)
```
[variable-length salt]   
[2-byte encrypted length][16-byte length tag]  
[encrypted payload][16-byte payload tag]  
[2-byte encrypted length][16-byte length tag]  
[encrypted payload][16-byte payload tag] 
```

对于AEAD密码，网络流是一系列长度固定的块。

在两种构造中，发送的第一条数据是和Socks协议一样的目标规范。
```
[0x01][4-byte IPv4 address][2-byte port]  
[0x03][1-byte length][hostname][2-byte port]  
[0x04][16-byte IPv6 address][2-byte port] 
```

在实验中使用了Shadowsocks-libev和OutlineVPN两个版本进行实验。


## 探针

回答以下问题：
- 在什么情况下观察到了何种类型的探针
- 探针的来源
- 探针是否有指纹信息
- 连接建立后多久会收到探针


### 实验
搭建服务器，实验从2019年9月29日到2020年1月21日，进行了4个月。
使用不同的加密算法，和两个版本Shadowsocks-libev和OutlineVPN 来扩大覆盖范围。

**Shadowsocks-libev**:在腾讯云北京数据中心上的5个VPS安装了SS客户端，在英国的Digital Ocean数据中心上安装了5个SS服务端。每个客户端都仅连接到其中一台服务器。并且设置了一个空白的对照，没有启动服务，仅仅捕获传入流量。  
使用curl产生流量，并且以给定频率访问以下三个网站:https://www.wikipedia.org, http://example.com,https://gfw.report


**OutlineVPN**:在一个美国大学的网络中安装了OutlineVPN v1.0.7服务端，客户端位于中国的住宅网络中，使用Firefox自动流量Alexa Top100万中的被审查的网站。


**局限**:位置缺乏多样性

### 探针类型

一共观察到51837种探针，有5种基于重放的探针

- R1 完全相同的重放
- R2 修改0字节后重放
- R3 修改0-7字节和62-63字节后重放
- R4 修改16字节后重放
- R5 修改6-16字节后重放 

其中R3,R4,R5的探针仅仅发送到OutlineVPN服务器，R5探针仅仅观察到两种

两种探针类型看起来随机，长度各异

- NR1 长度在7-9,11-113,15-17,21-23,32-34,40-42,48-50
- NR2 长度正好是221字节

### 探针来源
![](/assets/img/academic/SS/F3.png)

这些探针来自于12300个不同的IP地址，超过75%的地址发送了超过1个探针，最常见的发送探针的IP地址总结如下表  
![](/assets/img/academic/SS/T2.png)  
作者将观察到的ip地址与之前关于Tor服务器相关工作检测到的探针进行了比较，如下图
![](/assets/img/academic/SS/F4.png)  
发现仅有少量重叠。


**自治域**:如表格所示，统计了探针的所属的自治域
![](/assets/img/academic/SS/T3.png)

### 对探针进行指纹识别

- IP ID  : 找不到清晰的模式
- TTL    :  在46-50范围内
- TCP端口 : 大约90%的探针来自32768-60999端口，从没有探针使用低于1024的端口（前人的工作观察到所有的端口都在使用）
- TCP timestamp 一个32位计数器，以固定的速率增加,增长率随着操作系统不同而有所不同。


根据观察发现，虽然有些很多的数据包来自于不同的IP地址，但是他们拥有几乎非常相似的TSvals，这种情况的发生除非是两个进程同时启动。
![](/assets/img/academic/SS/F6.png)
### 探针的延迟

下图显示了从第一次建立连接开始，收到探针的延迟。  
最短的延迟仅有0.28s,最长的有570小时。超过20%的重放探针在1秒钟内到达，50%的在一分钟内到达，75%的在15分钟内到达。  
![](/assets/img/academic/SS/F7.png)  


## 触发探针
作者猜测有两种方式发送探针，第一种是大规模的端口测试，但是实验中那些没有启动SSR的服务器并没有收到探针，所以这种可能性排除。第二种是仅仅对可疑的ShadowSocks服务器发送探针。

- 触发探针需要多少流量
- 为什么R3,R4,R5的探针仅仅发送到OutlineVPN服务器
- 是否考虑数据包的长度
- 是否考虑数据包载荷的熵
- 由外到内的shadowsocks连接是否会产生一样多的探测


### 实验

设计了一个TCP客户端连接到服务器，仅发送一个指定长度和指定香农熵的数据包  
TCP服务器有两种模式，sink和respond，前者不响应任何数据，后者响应1-1000字节的随机数据  

Exp # |Client Length (bytes) |Client Entropy |Server Mode
--|--|--|--
1.a |[1,1000]| > 7  |sink 
1.b |[1,1000]| > 7  |responding 
2 |[1,1000]  |< 2   |sink 
3 |[1,2000]  |[0,8] |sink

### 实验结果

在TCP握手后，**一个数据包**足以触发探针。  

**仅有特定长度的数据包会触发探针**




## 随机探针的目的


GFW发送这些探针可以获得哪些信息?  
SS服务器和普通服务器对于探针的响应一定是不同的   

所以作者开发了模拟器来发送探针，来查看服务器对于探针的响应，并阅读了SS的源码，对其机制进行分析

### 实验

模拟器发送实验中发现的七种探针


选择了各种SS的版本：

Shadowsocks-libev
- v3.0.8
- v3.1.3
- v3.2.5
- v3.3.1
- v3.3.3

OutlineVPN
- v1.0.6
- v1.0.7
- v1.0.8
### 实验结果


下图展示了不同SS服务器对随机字节探针的响应  

![](/assets/img/academic/SS/F10.png)

服务器的反应主要有TIME-OUT,RST,FIN/ACK组成

TIME-OUT意味着服务器等待接收更多的数据，直到探针或者自己超时，而GFW的探针一般在不到10s内超时，SS的实现是默认60秒才超时。所以，一般都是探测器先发送FIN/ACK来关闭连接。

FIN/ACK和RST意味着服务器立刻发送了这个响应，发什么一般取决于操作系统级别的socket处理  
(在Linux系统中，如果应用已经读完了socket buffer中的数据，那么就发送FIN/ACK，否则发送RST)

### 分析

#### Stream Cipher
当SS服务器收到1-8字节的探针时，会一直处于等待状态。因为v3.0.8–v3.2.5使用8 byte 初始化向量。  
当探针的长度是9-14字节时，SS会立即发送RST，因为没有收到完整的目标规范。

当探针的长度大于等于15时，服务器可能响应为3种中的其中一种。 具体取决于随机的字节是否可以解密为有效的规范，例如地址类型必须是0x01,0x03,0x04中的一个，其他值都会立即产生RST。

这个字段是使用1byte而不是1bit存储的，所以理论是应该有253/256的概率观察到RST，但实际观察到有13/16的情况是观察到RST的，这是因为SS服务器校验地址字段时只看后四位。

收到完整的目标规范时，SS会尝试根据地址规范向制定目标发送SYN包，但是这种情况下解析出来的目标名是一个无效的，所以连接总是失败的。这种情况下远程主机是没有响应的，而GFW会发送一个FIN/ACK来关闭连接。


#### AEAD ciphers
使用AEAD密码时，服务器会有一组不同的可指纹的反应。

当salt是16字节时，服务器会期望足够的salt 数据（16字节），加密地址前缀(2字节)，加密地址tag（16字节），和另外16字节的标签关于第一个加密数据的有效载荷。  
一旦受到超过51个字节的数据，服务器会尝试解密受到的数据，但是由于验证错误会立即发送RST

v3.3.1-v3.3.3的服务器的区别是总是处于TIME-OUT状态。


OutlineVPN  v1.0.6用一下结构来解析受到的数据包。  
```
[32-byte salt]   
[2-byte encrypted length][16-byte length tag] 
```
稍微有点不同的是，它会在收到正好50个字节时立刻发送FIN/ACK。  

从v1.0.7-v1.0.8开始，开发人员已经修复了这个问题，探测长度不管是多少，都始终会超时。

### 攻击者如何利用这些信息

攻击者可以使用长度不同的随机探针对服务器的响应进行统计分析，当收集到足够的响应后，就可以较高概率判定一个服务器是否是SS服务器。

甚至可以倒推初始化向量的长度，当初始化向量长度为12时，可以知道使用的加密算法是`chacha20-ietf`。甚至倒推所使用的SS服务器版本。

如果RST的概率是13/16或者253/256，那么可以推断是使用的stream ciphers

### 观察 
GFW的服务器会将一组NR1和NR2类型的探针发送到同一个服务器，但不是一次性发送，而是每小时发送一部分。


## 重放探针的目的
服务器对重放探针的反应如下
![](/assets/img/academic/SS/T5.png)

OutlineVPN v1.0.6-1.0.8对R1类型的探针没有防御机制，会立即发送FIN/ACK来关闭连接。

攻击者可以通过响应的长度来猜测基础消息是http响应或者是Server Hello

探针R2、R3、R5更改的字节是包含初始化向量和salt字段的。所以SS服务器对这些探针的反应是和随机探针的反应相同的。

R4探针是针对初始化向量是16字节的SS服务器，通过枚举255个可能的字节值来获取每种反映的概率。

可能SS服务器是存在重放筛选器的，那么可以通过发送两次相同内容的探针来检测，这里观察到有10%的NR2探针会被发送超过一次。


## 屏蔽方式

从2019年7月以来，作者在63个位置进行了实验，包括中国、美国、英国、荷兰和新加坡。但是只有三个服务器被屏蔽了。

根据观察有的是屏蔽了端口，而有的是屏蔽了IP。但是不管是何种方式，仅屏蔽了S2C的流量。

目前已知GFW是每隔12小时探测一次被阻塞的Tor服务，当Tor不再运行时解除屏蔽。但是在作者的实验中，并没有看到GFW有对被屏蔽的SS服务器进行后续的检测。

在其中一台服务器被屏蔽一个星期以后，就被自动解除屏蔽了，解除屏蔽之前也没收到任何探针来检测。

并且收到探针与是否被屏蔽之间没有很大的联系，作者的其中一台服务器仅仅运行了15分钟就被屏蔽了，但还没有收到足够多的探针。

三台被屏蔽的服务器运行的SS版本是ShadoSocksR和ShadowSocks-python，但是根据其他用户的报告libev和OutlineVPN版本也是会被屏蔽的。

猜想是，探针会用来维护一个SS列表，具体是否会屏蔽是由人为决定的。

## 防御措施

### 防御流量识别
**更改C2S流量中的有效负载长度**：GFW会考虑连接中第一个数据包的长度来识别SS流量。


**Brdgrd**：bridge guard是一个可以在SS服务器上运行的软件，可以强制将SS的握手数据包切分为多个部分，这样子GFW就需要进行复杂的TCP重组才能还原里面的信息。

作者测试了在开启和关闭Bedgrd情况下收到的探针数量。

![](/assets/img/academic/SS/F11.png)

在开启brdgrd时收到探针是因为，即使客户端已经停止连接了，SS服务器依然会受到探针。也设置了一个从一开始就使用brdgrd的服务器，该服务器收到的探针数量更少。

但是brdgrd也不会是一个长久的解决方法，因为这样子是更改TCP窗口大小，频繁随机的更改窗口大小会被识别，且窗口大小过小也会显得异常。另一个就是数据包切得太小，可能会导致某些SS版本连接失败。

### 防御主动探测

**正确的身份构造**：建议用户仅使用AEAD密码，且完全放弃未经身份验证的加密方法。

**基于定时的重放过滤**：
不需要永远记住可能被重放的数据包，只需要在有限的时间内记住即可。


## 不足
- 实验中访问的网站非常有限，仅有3个
- 实验中服务端与客户端是1对1服务，而现实中大多数是1对n的服务
