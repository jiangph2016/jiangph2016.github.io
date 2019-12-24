---
layout: post
title: Git命令笔记
category: 笔记
tags: git
keywords: git
---

* content
{:toc}

自用的命令总结，仅记录自己经常用到的以加深印象，遗忘的时候可以速查。


四个位置：
- 本地文件
- 暂存区
- 本地仓库
- 远程仓库
![](/assets/img/notes/git.jpg)
## 管理远程仓库

### 添加远程仓库

```
git remote add origin git@github.com:jiangph2016/仓库名.git
```

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



### 从远程仓库拉取数据

```
git fetch
```

### 推送数据到远程仓库

会把master分支推送到远程仓库上的master分支

```bash
git push origin master
```

### 取消本地目录下关联的远程仓库

```
git remote remove origin
```

### 从远程仓库下载到本地并合并
分支名默认为master
```
git fetch origin [分支名]
get merge origin/[分支名]
```
或
```
git pull origin [远程分支名]:[本地分支名]
```
其中如果要操作的就是当前分支，那么[本地分支名可以不写]  
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


### 忽略文件

.gitignore文件中加入希望被忽略的文件  
可以使用通配符

### 版本回退

本地仓库回退到某个版本
```
git reset --hard [commit ID] 
```
本地回退到上一个版本，HEAD^代表上一个版本，HEAD^^表示上上个版本
```
git reset --hard HEAD^
```
强行把本地的提交到远程
```
git push -f origin master
```

## 管理本地仓库

### 初始化本地项目

```
git init
```
这时候本地会多一个.git文件夹，项目已经创建完成了。  

再参考**添加远程仓库**

### 撤销commit

撤销上一次提交  并将暂存区文件重新提交
```bash
git commit --amend
```

### commit管理
管理最近4次commit
```
git rebase -i HEAD~4
```
此命令会进入vi编辑器，有如下命令：
- p, pick :默认选项就是这个
- r, reword = use commit, but edit the commit message
- e, edit = use commit, but stop for amending
- s, squash :把这一次的commit合并到上面那个commit中
- f, fixup = like “squash”, but discard this commit’s log message
- x, exec = run command (the rest of the line) using shell
- d, drop :丢弃掉这一次commit（此操作会导致这次commit修改的内容也丢失！）


## 管理本地文件和暂存区

### 撤销add命令

撤销全部的文件
```bash
git reset HEAD
```
撤销指定的文件
```bash
git reset HEAD [文件名]
```
或
```bash
git rm --cached [文件名]
```

### 恢复文件到本地
误删本地文件后,从本地仓库恢复文件

```bash
git checkout [文件名]
git checkout .
```

### 删除文件

某个文件不需要了，仅仅使用rm是删掉的本地文件  
使用git rm删掉仓库中的文件(从本地和暂存区删除文件)
```
git rm [文件名]
```
而`git rm --cached [文件名]`仅仅从暂存区删掉文件

### 暂存stash

在不提交当前内容到仓库的情况下，将当前的文件内容入栈  
此操作会导致当前的文件内容被立即更改
```
git stash
git stash save "注释"
```
查看栈里面的内容
```
git stash list
```
将栈中的内容弹出(最近的)，应用到当前分支对应的工作目录
```
git stash pop
```
只应用，不弹出
```
git stash apply
```
清除stash
```
git stash drop + 名称 #清除指定的
git stash clear #全部清除
```
查看stash和当前目录的差异
```
git stash show
```

### 按操作回退

先查看每一步操作的历史
```
git reflog
git reset --hard <ID>
```


## 分支操作

### 查询分支

```
git branch    # 仅查看本地分支
git branch -a # 查看所有的分支（包括远程分支）
```
### 创建新的分支
```
git branch [分支名]
```
### 切换分支
```
git checkout [分支名]
```
这时候本地的文件会被立刻修改！  

### 拉取远程分支

```
git pull origin [远程分支名]:[本地分支名]
```
下面那条命令上次执行时出了问题，待确认
```
git checkout -b [分支名] origin/[分支名]
``` 

### 合并分支

在当前的分支上合并某分支
```
git merge [分支名]
```

### 删除分支
```
git branch -d [分支名]
```

## 案例

1. 当前正在dev分支上开发，master的代码更新了,需要合并
```
git checkout master 
git pull #此操作使本地master分支和远程保持一致
git checkout dev
git merge master
git push origin dev
```

2. 当前的操作出现错误，想回退到远程仓库的样子

第一种方法：
```
git stash
git stash drop + 名称
```
第二种方法：
```
git fetch --all
git reset --hard origin/master
git pull
```
git fetch 只是下载远程的库的内容，不做任何的合并   
git reset 把HEAD指向刚刚下载的最新的版本
