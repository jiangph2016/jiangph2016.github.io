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
![](/assets/img/notes/git-map.jpg)
## 远程仓库操作
### 配置用户信息
此步骤仅仅用于commit，即使输入一个不存在的邮箱也无所谓，但是push到github上以后，显示的也是那个错误的信息
如果设置为别人的邮箱，就会显示成别人commit的
```
git config --list
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```
我这里因为有多个github账号需要管理，所以不能设置global的
```
git config --local user.email "you@example.com"
git config --local user.name "Your Name"
```

### 配置ssh登录
1. 在本地的~/.ssh文件夹下，生成SSH Key
```
ssh-keygen -t rsa -C "登录的邮箱"
```
2. 生成并添加SSH Key(此步骤不做也可以)
```
ssh-add ~/.ssh/id_rsa
```
3. 查看SSH Key并复制到Github账户的设置中
```
cat id_rsa.pub
```
4. 测试
```
ssh -T git@github.com
```
5. 如果还是无法正常提交 
则可能是clone的时候用的是https，所以需要更改配置文件
```
vim .git/config
```
将url的格式改为`git@github.com:[仓库地址]`即可
### 添加远程仓库

```
git remote add origin git@github.com:jiangph2016/仓库名.git
```

### 查看当前绑定的远程仓库
```
git remote -v
git remote show origin 
```

### 从远程仓库拉取数据
数据会到暂存区，需要再用merge来进行合并

```
git fetch
```

### 推送数据到远程仓库

会把master分支推送到远程仓库上的master分支

```
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

如果出现403错误，则`vim .git/config` 或`git config --local -e`
修改，增加用户名和密码  
```
[remote "origin"]
        fetch = +refs/heads/*:refs/remotes/origin/*
        url = https://[用户名]:[密码]@github.com/[项目].git
```


### 忽略文件

.gitignore文件中加入希望被忽略的文件  
可以使用通配符



## 管理本地仓库

### 初始化本地项目

```
git init
```
这时候本地会多一个.git文件夹，项目已经创建完成了。  

再参考**添加远程仓库**

### 重新commit

重新commit，此次提交会覆盖上一次commit
```bash
git commit --amend
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


## 误操作处理

### 关于reset
reset命令将HEAD指向给定的输入，如HEAD^,HEAD^^  

使用`--hard`对本地文件和暂存区进行重置
```
git reset --hard HEAD
git reset --hard HEAD^
```
使用`--soft`时则暂存区和本地文件不会修改  

如果不加东西则默认为`mixed`模式，暂存区更改，本地文件不更改  
注意reset只影响被track过的文件，所以需要clean命令来清除没有track过的文件  

```
git clean -n #显示要删除的文件，但不真正的删除
git clean -f #删除没被track的文件（不包含gitignore)
git clean -df #删除文件和文件夹
git clean -xf #删除一切没有被track的文件
```

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
### 版本回退

本地回退到某个版本
```
git reset --hard [commit ID] 
```
本地回退到上一个版本，HEAD^代表上一个版本，HEAD^^表示上上个版本
```
git reset --hard HEAD^
git reset --hard HEAD1
```
在执行完上面的命令后，HEAD也会跟着改变  
强行提交到远程仓库
```
git push -f origin master
```

### 按操作回退

先查看每一步操作的历史
```
git reflog
git reset --hard <ID>
```

## 本地仓库操作

### 删除文件

某个文件不需要了，仅仅使用rm是删掉的本地文件  
使用git rm删掉仓库中的文件(从本地和暂存区删除文件)
```
git rm [文件名]
```
### 取消对文件的跟踪
仅仅从暂存区删掉文件，本地文件不动
```
git rm --cached [文件名]
```
取消当前目录下所有文件的跟踪
```
git rm -r --cached .
```


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
这时候本地文件会立刻切换成该分支的状态！  
需要保证此时所有文件都被提交过，否则会丢失，需要用stash命令来暂存    

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
删除本地分支
```
git branch -d [分支名]
```
删除远程分支
```
git push origin :[分支名]
```

## 案例

### 案例1-分支合并
当前正在dev分支上开发，master的代码更新了,需要合并
```
git checkout master 
git pull #此操作使本地master分支和远程保持一致
git checkout dev
git merge master
git push origin dev
```

### 案例2-错误回退
当前的操作出现错误，想回退到远程仓库的状态
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

### 案例3-误上传大文件（或敏感文件） 


在git里想永久删除一个文件真的是很麻烦的一个事情，如果只是用`git rm`来操作的话，文件只是在当前的commit里被删除了  
实际上它还存在仓库里面，一是占据不必要的存储空间，二是别人拉取的速度会变慢（特指大文件）！！  

官方的方法介绍在此:[Removing sensitive data from a repository](https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository)  

第一种方法是使用**BFG**工具，这是一个git清理工具，下载地址：<https://rtyley.github.io/bfg-repo-cleaner/>
```
java -jar bfg-1.13.0.jar --delete-files <文件名>
即
bfg --delete-files <文件名>
```
第二种方法是使用filter-branch  
下面这条命令由github给出，特别的长
```
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch <完整的文件路径名！>" \
  --prune-empty --tag-name-filter cat -- --all
```
如果看到rewrite说明成功了，如果出现unchanged则没找到该文件  
最后`-f`强制推送到远程仓库  

最后强制解除对本地存储库中的所有对象的引用（暂时读不太懂这句话）和执行垃圾回收操作：
```
git for-each-ref --format="delete %(refname)" refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now
```

### 案例4-撤销git add

使用`git status`查看一下被add的文件  

撤销所有的修改
```
git reset HEAD  
```
撤销对指定文件的修改
```
git reset HEAD [文件名]
```

### 切换分支出错


提示
>error: The following untracked working tree files would be overwritten by checkout:

解决方案：删除原先的分支，再创建
```
git branch -a 
git branch -d [想要切换的分支名]
git branch [分支名]
```