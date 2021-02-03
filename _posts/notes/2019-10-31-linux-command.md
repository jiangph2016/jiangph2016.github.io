---
layout: post
title: Linux功能命令记录
category: 笔记
tags: Linux
keywords: Linux
---


* content
{:toc}

记录一些命令+参数的组合
## 运维
### 系统信息

- 查看当前文件夹下的各个文件的大小
```
du -h --max-depth=1
```

- 当前系统可用容量
```
df -h
```
- 当前内存使用量
```
free -mh
```

- 列举每根内存条及容量
```
dmidecode -t memory | grep -i size
```

- 计算机可以安装的最大内存
```
dmidecode -t memory | grep -i max
```

- 所有磁盘及其分区的大小
```
lsblk
```

- 默认网关和路由表
```
ip route | column -t
```
或
```
netstat -r
```

- 查看网络接口
```
ip link show
```
或
```
netstat -i
```

- 关于磁盘、扇区
```
fdisk -l
```

- 显示java进程

```
jps
```
### 检查配置文件语法 
```
jq . config.json
```
命令中的.不能省去

### 清除缓存

```
echo 1 > /proc/sys/vm/drop_caches
```
 用`free -h`差看效果

## 文件操作
**find命令**

最常见的按名称查找，格式：
```
find [位置] -name [匹配符]
```

**查找包含关键词的文件**
```
find .|xargs grep -ri "[字符串]"
```
或
```
grep -r [字符串] [路径]
```

**用于统计文件的字节数、字数、行数**

```
wc -l [文件名]  #文件的行数
wc -c [文件名]  #文件字节数
wc -w [文件名]  #文件字数
wc -L [文件名]  #文件中最长行的长度
wc --files0-from=[sample.txt] #从sample.txt中读取文件名列表
```

**文件分割**
按行数分割文件
```
split -l [行数] -d [文件名]
```
**文件编码**

查看文件编码（`file`命令在Mac下此命令查看不到编码）
```
file [文件名]
```
或
```
enca [文件名]
enca -L zh_CN [文件名]
```

文件编码转换`dos2unix`和`unix2dos`  
手动转换
```
enca -L zh_CN -x [目的编码] [文件名]
```
目的编码例如`UTF-8`,`GB2312`

## BASH

运行无输出
```
[命令] >/dev/null 2>&1
```