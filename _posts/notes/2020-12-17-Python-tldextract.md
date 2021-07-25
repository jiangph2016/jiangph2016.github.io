---
layout: post
title: 【Python】tldextract模块
category: 笔记
tags: Python
keywords: tldextract
---
* content
{:toc}


## 用途

用于从url中提取域名
## 安装
```
pip3 install tldextract
```

## 使用

```
import tldextract

tld = tldextract.extract('http://news.baidu.com/')
print(tld)
# result: ExtractResult(subdomain='news', domain='baidu', suffix='com')
```
