---
layout: post
title: Linux C下编程可能用到的命令
category: 笔记
tags: Linux
keywords: Linux,C
---

* content
{:toc}

## getcwd()

__功能__：输出当前所在的目录  
__原型__:
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

## opendir()
__功能__:打开目录  
__原型__:
```
DIR *opendir(const char *path);
```

- __头文件__:dirent.h


## readdir()

__功能__:读取文件目录(相当于ls) 
__原型__:
```
struct dirent *readdir(DIR *dirp);
```

### 原理

```
struct dirent
{
    ino_t d_ino;  //此目录进入点的inode
    ff_t d_off;  //目录文件开头到此目录进入点的位移
    signed  short int d_reclem; //_name的长度，不包含NULL字符
    unsigned  char d_type;   // d_name所指的文件类型
    char d_name[256];  //文件名
};
```

### 用法
```
void fun_ls(const char * path)
{
    DIR * dp;
    struct dirent *dirp;

    dp=opendir(path); //打开文件夹
    if(dp==NULL)
    {
        exit(-1);
    }
    while((dirp=readdir(dp))!=NULL)
    {
        printf("%s\n",dirp->d_name); //输出文件名
    }
    closedir(dp); //关闭文件夹
}
```