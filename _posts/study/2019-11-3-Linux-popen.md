---
layout: post
title: 【C】Linux下的popen使用
category: 学习
tags: C
keywords: Linux,popen,C
---

* content
{:toc}

popen()函数可以用来实行shell的命令并获取其返回结果。

## 原理

popen先执行fork，然后调用exec执行cmdstring，并返回一个标准的I/O文件指针。  
**头文件**：`#include<stdio.h>`

**原型**：
```
FILE *popen(const char *cmdstring, const char *type)
``` 
**cmdstring**：包含shell命令字符串

**type**：
1. 为”r”时，则文件指针连接到cmdstring的标准输出，也就是代表指向执行shell命令返回的消息，也可以认为链接到stdout

2. 为”w”时，则文件指针连接到cmdstring的标准输入，也可以认为链接stdin

```
int pclose(FILE *fp);
```
**fp**：为ponen返回的文件指针

**返回值**：成功返回cmdstring的终止状态；出错返回-1


## 用法

### 将输出结果打印
```
int command(const char * cmd)
{
    FILE *pp = popen(cmd, "r"); //建立管道
    if (!pp) {
        return -1;
    }
    char data[1024]; //设置一个合适的长度，以存储每一行输出
    while (fgets(data, sizeof(data), pp) != NULL)
	{
		printf("%s", data);
	}
    pclose(pp); //关闭管道
    return 0;
}
```


### 将输出结果存储



## 参考

<https://blog.csdn.net/qq_27664167/article/details/82194391>


