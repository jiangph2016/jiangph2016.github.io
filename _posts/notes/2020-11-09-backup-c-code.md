---
layout: post
title: 【个人自用】已经写好的C代码-防止重复造轮子
category: 笔记
tags: C
keywords: C
---
* content
{:toc}
一些已经写好的C代码，可以直接拿来用的，防止重复造轮子


## KMP
```
void get_next_of_pat(const char *pat, int *next)
{
    next[0] = -1;
    int i = 0, j = -1, size_of_pat = strlen(pat);

    while (i < size_of_pat)
    {
        if (j == -1 || pat[i] == pat[j])
        {
            ++i;
            ++j;
            next[i] = j;
        }
        else
        {
            j = next[j];
        }
    }
}


int kmp(char *str, char *pat, int size_of_str)
{
    int i = 0, j = 0, size_of_pat = strlen(pat);
    int next[256];
    get_next_of_pat(pat, next);

    while (i < size_of_str && j < size_of_pat)
    {
        if (j == -1 || str[i] == pat[j])
        {
            ++i;
            ++j;
        }
        else
        {
            j = next[j];
        }
    }
    if (j == size_of_pat)
    {
        return i - j;
    }
    else
    {
        return -1;
    }
}
```

## 转换
将16进制的字符串翻译为10进制的数字，不区分大小写
```
int hexch_to_int(char ch)
{
    int res;
    if ((ch >= 'A') && (ch <='F'))
    {
        res = 10+ch-'A';
    }
    else if ((ch >= 'a') && (ch <='f'))
    {
        res = 10+ch-'a';
    }
    else{
        res = ch-'0';
    }
    return res;
}
```
例如输入A，返回10

