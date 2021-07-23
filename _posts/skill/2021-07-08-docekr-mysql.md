---
layout: post
title: 【Linux】docker快速搭建数据库服务
category: 技巧
tags: Linux
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
    container_name: mysql-docker        # 指定容器的名称
    image: mysql:latest                   # 指定镜像和版本
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_ROOT_HOST: ${MYSQL_ROOT_HOST}
      TZ: Asia/Shanghai     # 指定时区
    volumes:
      - "${MYSQL_DIR}/data:/var/lib/mysql"           # 挂载数据目录
      - "${MYSQL_DIR}/config:/etc/mysql/conf.d"      # 挂载配置文件目录

```

### 环境变量

生成`.env`文件放在根目录下
```
MYSQL_ROOT_PASSWORD=123456
MYSQL_ROOT_HOST=192.168.123.60
MYSQL_DIR=./
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

