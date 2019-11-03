---
layout: post
title: Linux C下编程可能用到的命令
category: 笔记
tags: Linux
keywords: Linux,C
---

* content
{:toc}

## getcwd

```
char *getcwd(char *buf, size_t size);
```
### 用法
- __头文件__: unistd.h
- __*buf__：保存当前目录的缓冲区  
- __size__：在现代linux 中，buf 的长度至少可以为255 字节  
- __返回值__：成功返回指向当前目录的指针，和buf 的值一样，错误返回NULL  
### 示例

```
void printCWD()
{
    char path[50];
    if(!getcwd(path,50))
    {
        perror("getced error");
        exit(1);
    }
    puts(path);
}
```

## opendir

## readdir