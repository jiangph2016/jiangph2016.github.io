---
layout: post
title: 【抓包】wireshark过滤器规则
category: 技巧
tags: web
keywords: wireshark
---
* content
{:toc}

查看官方的规则介绍在Wireshark中选择Help→Manual Pages→Wireshark Filter  

## 捕捉过滤器语法

### 示例

```
(host 10.4.1.12 or src net 10.6.0.0/16) and tcp dst portrange 200-10000 and dst net 10.0.0.0/8
```
捕捉IP为10.4.1.12或者源IP位于网络`10.6.0.0/16`，目的IP的TCP端口号在200至10000之间，并且目的IP位于网络 `10.0.0.0/8`内的所有封包。
### 字段详解：

**Protocol**:  
可能值: ether, fddi, ip, arp, rarp, decnet, lat, sca, moprc, mopdl, tcp and udp.  
如果没指明协议类型，则默认为捕捉所有支持的协议。  
注：在wireshark的HELP-Manual Pages-Wireshark Filter中查到其支持的协议。


**Direction**:
可能值: src, dst, src and dst, src or dst  
如果没指明方向，则默认使用 “`rc or dst` 作为关键字。  
`host 10.2.2.2`与`src or dst host 10.2.2.2`等价。  


**Host(s)**:
可能值： net, port, host, portrange.    
默认使用”host”关键字，`src 10.1.1.1`与`src host 10.1.1.1`等价。


**Logical Operations**（逻辑运算）:
可能值：not, and, or.  
否(“not”)具有最高的优先级。或(“or”)和与(“and”)具有相同的优先级，运算时从左至右进行。  
```not tcp port 3128 and tcp port 23```
与```(not tcp port 3128) and tcp port 23```等价。  
```
not tcp port 3128 and tcp port 23
```
与
```
not (tcp port 3128 and tcp port 23)
```
不等价。



## 显示过滤器语法

### 过滤地址
```
ip.addr==192.168.10.10    #过滤地址
ip.src==192.168.10.10     #过滤源地址
ip.dst==192.168.10.10     #过滤目的地址
```

### 过滤协议
直接输入协议名
```
icmp   
http
```
 
### 过滤协议和端口
```
tcp.port==80
tcp.srcport==80
tcp.dstport==80
```

### 过滤http协议的请求方式
```
http.request.method=="GET"
http.request.method=="POST"
http.request.uri contains admin   #url中包含admin的
http.request.code==404    #http请求状态码的
http contains "Content-Type:" #包含Content-Type的
```
### 连接符
```
&&  
||
and
or
```
通过连接符可以把上面的命令连接在一起，比如：
```
ip.src==192.168.10.10 and http.request.method=="POST"
```