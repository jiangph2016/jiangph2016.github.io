---
layout: post
title: 【Docker】docker快速搭建mysql数据库服务
category: 技巧
tags: docker
keywords: mysql,docker,数据库
---
* content
{:toc}

## MySQL

### 配置docker-compose
将`docker-compose.yml`放在根目录下
```
version: '3'
services:
  mysql:
    restart: always
    container_name: mysql-docker        # 指定容器的名称
    image: mysql:latest                   # 指定镜像和版本
    ports:
      - 3306:3306
    privileged: true
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_ROOT_HOST: 192.168.123.54
      TZ: Asia/Shanghai     # 指定时区
    volumes:
      - "./data:/var/lib/mysql"           # 挂载数据目录
      - "./config:/etc/mysql/conf.d"      # 挂载配置文件目
      - "./conf/my.cnf:/etc/my.cnf"

```

### 配置文件

创建`conf/my.conf`
```
[mysqld]
character-set-server=utf8mb4
default-time-zone='+8:00'
innodb_rollback_on_timeout='ON'
max_connections=500
secure-file-priv=/
innodb_lock_wait_timeout=500
```

### 允许root远程访问

```
use mysql
select host,user from user;
update user set host='%' where user='root' and host='192.168.40.223';
flush privileges;
```