---
layout: post
title: 【Linux】共享目录挂载
category: 技巧
tags: Linux
keywords: mount,smb
---


### Windows开启SMB

具体过程略过  
【注意事项】Windows电脑的名称不要设置为中文，不然会导致Mac无法挂载


### 挂载
例如挂载到`/home/smb`目录
```
mount -t cifs  //192.168.11.123/D /home/smb   -o username=administrator,password=111111 
```

上述命令挂载以后，该目录的权限是755，其他用户没有修改权限，需要通过以下命令增加权限
```
mount -t cifs //192.168.11.123/D /home/smb -o username=administrator,password=111111,dir_mode=0777,file_mode=0777
```

