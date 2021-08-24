---
layout: post
title: 【刷题】LeetCode解题技巧
category: 技巧
tags: docker
keywords: mysql,docker,数据库
---
* content
{:toc}



## Python解题模板

```
class Solution:
    def run(self):
        pass
    def stdin(self):
        a = int(input())
        array = list(map(int,input().split()))
        self.run()

T = int(input())
ins = Solution()
while T>0:
    ins.stdin()
    T-=1
```

## Python 重定向输入
用于进行测试样例的输入
```
class Solution:
    def run(self):
        pass
    def stdin(self):
        pass

with open(r"2.in", 'r') as file:
    sys.stdin = file
    s1 = Solution()
    T = int(input())
    while T>0:
        s1.stdin()
        T-=1
```