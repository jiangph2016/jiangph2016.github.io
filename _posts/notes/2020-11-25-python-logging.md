---
layout: post
title: 【Python】logging模块打印日志
category: 笔记
tags: Python
keywords: logging
---
* content
{:toc}

参考:<https://www.cnblogs.com/deeper/p/7404190.html>

### 日志等级

- debug
- info
- warning
- error
- critical

**级别排序:CRITICAL > ERROR > WARNING > INFO > DEBUG**

### 代码

```
import logging,time

logger = logging.getLogger()
logger.setLevel(logging.INFO)

log_path = '/home/jiangph/'
logfile = log_path + time.strftime('%Y%m%d', time.localtime(time.time())) + '.log'

log_handler = logging.FileHandler(logfile, mode='a')
log_handler.setLevel(logging.DEBUG) 

formatter = logging.Formatter("%(asctime)s - %(levelname)s: %(message)s")
log_handler.setFormatter(formatter)

logger.addHandler(log_handler)

logger.info("test")
```