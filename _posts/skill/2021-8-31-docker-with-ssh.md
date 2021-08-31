---
layout: post
title: 【Docker】给镜像附带ssh，并特权启动
category: 技巧
tags: linux
keywords: docker,容器
---
* content
{:toc}


## 简介

创建一个容器，提供ssh访问，像一个虚拟机一样持续提供服务。  
需求是这个容器**不对外提供web服务**，而是作为一个独立的开发环境，需要远程连上进行开发

## 构建镜像

编写`Dockerfile`，基于原来的纯开发镜像构建  
在原先的开发环境里安装ssh，并拷贝相应的sshd_config(例如修改ssh端口号22为其他的)
```
FROM sapp:latest
RUN yum install -y openssl openssh-server
WORKDIR /etc/ssh
COPY sshd_config .
WORKDIR /
ENTRYPOINT /usr/sbin/init
```

构建镜像
```
docker build -t sapp-ssh:v1.0 .
```
## 启动

### host网络模式

编写`docker-compose.yml`
```
version: '3'
services: 
  sapp:
    image: sapp-ssh:v1.0
    container_name: sapp-ssh
    network_mode: host
    privileged: true
    command: tail -f /dev/null
```
这里选择`network_mode: host`是因为要监听宿主机的网卡，启动以后会和宿主机共用一个ip  
所以需要将`sshd_cofig`中监听的端口进行修改，例如改为10022

### macvlan网络模式

将容器的ip设置为和宿主机在同一个网关下，但拥有一个独立的ip，相当于vmware的桥接模式

这里我宿主机的ip是192.168.11.195，网关是192.168.10.1，子网掩码是255.255.254.0  

先创建一个docker网络，名叫macvlan
```
docker network create -d macvlan --subnet 192.168.10.0/23 --gateway 192.168.10.1 -o parent=eno1 -o macvlan_mode=bridge macnet
```

启动容器，指定IP为`192.168.11.193`
```
docker run -d  --net macnet --ip 192.168.11.193 --restart always --privileged=true [镜像名] /usr/sbin/init
```
其中`--privileged=true`和`/usr/sbin/init`是为了让容器拥有真正的sudo权限