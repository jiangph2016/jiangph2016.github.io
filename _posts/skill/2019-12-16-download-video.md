---
layout: post
title: 爬取高顿网校中受版权保护的视频
category: 技巧
tags: Python
keywords: python,爬虫,视频,下载
---
* content
{:toc}

朋友让我帮一个忙，下载某个网校中的网课视频，一开始本以为用传统的IDM之类的下载器，是可以捕获到这个视频流的。但是实际上根本捕不到，chrome上的各种插件也试了，都是无法识别的。

但是我在开发者模式上看收到的包，是有一堆序号连续的.ts文件的，请求的url格式大致为。  
```
http://video1-cdn.gaodun.com/pub/09ohwP0Q0f1wJhRL/HD/1.ts
```
根据多次试验发现，pub后面那段字符与课程有关，固定不变。而HD代表着视频的清晰度是高清。 
百度了一下ts格式的视频，上面说ts格式的特点是无论从哪一段开始都可以直接进行解析。  
于是直接请求下载下来一个ts文件，但尝试播放却失败了，提示无法解析。

最后去了解了一下相关的原理。  

>M3U8 是 Unicode 版本的 M3U，用 UTF-8 编码。"M3U" 和 "M3U8" 文件都是苹果公司使用的 HTTP Live Streaming（HLS） 协议格式的基础，这种协议格式可以在 iPhone 和 Macbook 等设备播放。
>HLS 的工作原理是把整个流分成一个个小的基于 HTTP 的文件来下载，每次只下载一些。当媒体流正在播放时，客户端可以选择从许多不同的备用源中以不同的速率下载同样的资源，允许流媒体会话适应不同的数据速率。在开始一个流媒体会话时，客户端会下载一个包含元数据的 extended M3U (m3u8) playlist文件，用于寻找可用的媒体流。
>HLS 只请求基本的 HTTP 报文，与实时传输协议（RTP）不同，HLS 可以穿过任何允许 HTTP 数据通过的防火墙或者代理服务器。它也很容易使用内容分发网络来传输媒体流。

于是我返回去看chrome上记录的请求，果然看到有一个请求返回的response是m3u8，大致如下格式
```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:YES
#EXT-X-TARGETDURATION:37
#EXT-X-KEY:METHOD=AES-128,URI="http://live-hz.gaodun.com/player/authorize?id=09ohwP0Q0f1wJhRL&session=44512&token=1i48ds24mba12",IV=0x763f74c0066ef2a3e040262827d55c29
#EXTINF:30.080000,
http://video1-cdn.gaodun.com/pub/09ohwP0Q0f1wJhRL/HD/0.ts
#EXTINF:30.000000,
http://video1-cdn.gaodun.com/pub/09ohwP0Q0f1wJhRL/HD/1.ts
#EXTINF:30.000000,
http://video1-cdn.gaodun.com/pub/09ohwP0Q0f1wJhRL/HD/2.ts
#EXTINF:30.000000,
http://video1-cdn.gaodun.com/pub/09ohwP0Q0f1wJhRL/HD/3.ts
```
其中能大概看到的是EXTINF代表视频的时长，而后面的链接则代表视频的地址。那这个相当于播放列表的文件，就给我下载视频提供了方便。  
但是我也注意到里面提到了`AES-128`,这是一个对称加密的算法，URI中存的链接，就是获取秘钥的方法。


于是我根据网上的资料，以及别人关于解密的代码，整理出来一版解密并下载视频的  
注意：需要安装pycrypto库（pip3 install pycrypto）
```
import requests
from Crypto.Cipher import AES
 
def read_m3u8(filename):
    m3u8_file = open(filename,"r")
    all_content = m3u8_file.read();
    if "#EXTM3U" not in all_content:
        raise BaseException("非M3U8的链接")
        
    file_line = all_content.split("\n")
    unknow = True
    key = ""
    for index, line in enumerate(file_line): # 第二层
        if "#EXT-X-KEY" in line:  # 找解密Key
            method_pos = line.find("METHOD")
            comma_pos = line.find(",")
            method = line[method_pos:comma_pos].split('=')[1]
            print("Decode Method：", method)
            
            uri_pos = line.find("URI")
            quotation_mark_pos = line.rfind('"')
            key_path = line[uri_pos:quotation_mark_pos].split('"')[1]
            
            key_url = key_path # 拼出key解密密钥URL
            res = requests.get(key_url)
            key = res.content
            print("key_url：",key_url)
            print("key:",key)
            
        if "EXTINF" in line: # 找ts地址并下载
            unknow = False
            pd_url = file_line[index + 1] # 拼出ts片段的URL
            print(pd_url)
            
            res = requests.get(pd_url)
            c_fule_name = file_line[index + 1].rsplit("/", 1)[-1]
            
            if len(key): # AES 解密
                cryptor = AES.new(key, AES.MODE_CBC, key)  
                with open(c_fule_name + ".tmp", 'ab') as f:
                    f.write(cryptor.decrypt(res.content))
            else:
                with open(c_fule_name, 'ab') as f:
                    f.write(res.content)
                    f.flush()
```

下载下来的是一堆.tmp文件(其实是.ts)，直接进行用二进制进行合并即可。


代码待更新，届时以github为准。