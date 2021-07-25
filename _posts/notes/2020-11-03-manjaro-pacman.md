---
layout: post
title: 【Linux】Manjaro的pacman源配置
category: 笔记
tags: Linux
keywords: pcaman,Manjaro
---
* content
{:toc}



下载中国地区的镜像源列表

```
wget -O /etc/pacman.d/mirrorlist https://www.archlinux.org/mirrorlist/?country=CN
```
打开`/etc/pacman.d/mirrorlist`，取消前面的#注释
```
vim /etc/pacman.d/mirrorlist
```
刷新镜像列表
```
pacman -Syyu
```

