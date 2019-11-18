---
layout: post
title: git命令笔记
category: 笔记
tags: git
keywords: git
---

* content
{:toc}

自用的命令总结，仅记录自己经常用到的以加深印象，遗忘的时候可以速查。

![](/assets/img/notes/git.jpg)
## 管理远程仓库

### 查看当前绑定的远程仓库
```
git remote -v
git remote show origin 
```

### 查看commit历史

```
git log --oneline 
```

参数： 

- -p 显示每次提交引入的差异
- --grep 只输出包含指定字符串的提交
- --since --after指定日期之后的提交
- --until --befofe 指定日期之前的提交
- --committer 查看指定作者的提交
- --name-only

### 添加远程仓库

```
git remote add origin git@github.com:jiangph2016/仓库名.git
```

### 从远程仓库拉取数据

```
git fetch
```


### 推送数据到远程仓库

```bash
git push origin master
```

### 取消本地目录下关联的远程仓库

```
git remote remove origin
```

### 从远程仓库下载到本地并合并

```
git fetch origin master
get merge origin/master
```
或
```
git pull origin master  
```

即git pull = git fetch + git merge


### 提交本地的修改到远程仓库

```
git add .
git commit -m "update"
git push origin master
```

如果出现403错误，则`vim .git/config`
修改，增加用户名和密码  
```
[remote "origin"]
        fetch = +refs/heads/*:refs/remotes/origin/*
        url = https://[用户名]:[密码]@github.com/[项目].git
```



### 创建新的分支

```
git branch [分支名]
```

### 版本回退

本地回退到某个版本
```
git reset --hard [commit ID] 
```
本地回退到上一个版本
```
git reset --hard HEAD^
```
强行把本地的提交到远程
```
git push -f -u origin master
```

## 本地操作

### 初始化本地项目

```
git init
```
这时候本地会多一个.git文件夹，项目已经创建完成了。  

再参考**添加远程仓库**

### 忽略文件

.gitignore文件中加入希望被忽略的文件  
可以使用通配符

### 撤销操作

撤销上一次提交  并将暂存区文件重新提交
```bash
git commit --amend
```



### 恢复文件到本地

```bash
git checkout [文件]
git checkout .
```




To be continue...