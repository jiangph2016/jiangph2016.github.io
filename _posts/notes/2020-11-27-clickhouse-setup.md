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
docker run -d --name mych_sever --ulimit nofile=262144:262144 --volume=[本地存储路径]:/var/lib/clickhouse yandex/clickhouse-server
```

## 连接

```
docker run -it --rm --link mych_sever:clickhouse-server yandex/clickhouse-client --host clickhouse-server
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

#### 配置说明

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