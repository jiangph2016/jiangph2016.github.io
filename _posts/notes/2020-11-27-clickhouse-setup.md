---
layout: post
title: Clickhouse配置&启动记录
category: 笔记
tags: 笔记
keywords: Clickhouse
---
* content
{:toc}


## Docker
### 安装


```
docker pull yandex/clickhouse-server:latest
docker pull yandex/clickhouse-client:latest
```
### 启动


```
docker run -d --name mych_server --ulimit nofile=262144:262144 --volume=[本地存储路径]:/var/lib/clickhouse -p 8123:8123 -p 9000:9000 -p 9009:9009 yandex/clickhouse-server
```

使用自己的配置文件运行
```
docker run -d --name mych_server --ulimit nofile=262144:262144 --volume=/home/win4/clickhouse/data:/var/lib/clickhouse -v=/etc/clickhouse-server:/etc/clickhouse-server -p 8123:8123 -p 9000:9000 -p 9009:9009 yandex/clickhouse-server
```


## 连接

```
docker run -it --rm --link mych_server:clickhouse-server yandex/clickhouse-client --host clickhouse-server
```

也可以
```
docker exec -it mych_server /bin/bash
clickhouse-client
```

连接远程的clickhouse-server
```
docker run -it --rm yandex/clickhouse-client --host 192.168.123.60 --user default  -m 
docker run -it -d --volume /Volumes/TR200/storage:/home/ yandex/clickhouse-client --host 192.168.123.60 --user default  -m 
```


## 常规方式

### 安装
添加Clickhouse源
```
curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo bash
```
安装server和client
```
sudo yum install -y clickhouse-server clickhouse-client
```


### 配置

#### 默认目录
- 参数 `/etc/clickhouse-server/config.xml`
- 日志 `/var/log/clickhouse-server`
- 存储目录 `/var/lib/clickhouse`

#### 配置文件

>config.xml
- listen_host 限制访问数据库的来源
- path 数据目录


>users.xml




### 启动
#### 服务端
```
sudo service clickhouse-server start
```


#### 客户端
```
clickhouse-client --host 127.0.0.1 --port 9001 --user default --password "111111"
```
多行模式需要再加一个参数'-m'，每一条命令后面需要跟一个`;`


### 卸载

```
yum list installed | grep clickhouse
yum remove -y clickhouse-common-static
yum remove -y clickhouse-server-common
rm -rf /var/lib/clickhouse
rm -rf /etc/clickhouse-*
rm -rf /var/log/clickhouse-server
```