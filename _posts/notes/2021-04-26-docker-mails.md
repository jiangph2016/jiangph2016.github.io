---

layout: post
title: 【Docker】利用docker搭建邮件服务器
category: 笔记
tags: email
keywords: docker,email
---
* content
{:toc}


- <https://github.com/docker-mailserver/docker-mailserver>   
- <https://docker-mailserver.github.io/docker-mailserver/edge/config/setup.sh/>  
- <https://hub.docker.com/r/mailserver/docker-mailserver>
- <https://www.itmanbu.com/docker-mail-server.html>  

## 准备

- 一个带有公网ip的服务器
- 一个域名
- 一个ssl证书

## 域名解析

我这里域名购买自万网，需要添加三条解析记录

第一条解析记录，类型为A，使用通配符*，记录值为服务器的公网ip

![](assets/img/notes/email/image-20210426183945511.png)


第二条解析记录类型为MX，记录为@，代表我之后的邮箱服务器叫xxx@imapmax.xyz，如果叫mail，那么我以后的邮箱服务器就是xxx@mail.imapmax.xyz，这个的记录值我设为`mail.imapmax.xyz`，最后指向的就是我刚才设置A记录中的记录值

![](assets/img/notes/email/image-20210426184047416.png)

第三条是一个TXT记录，作用是配置SPF,记录值设置为`v=sopf1 a mx ~all`

![](assets/img/notes/email/image-20210426190105254.png)

## 安装

```
docker pull mailserver/docker-mailserver

wget -O .env https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/master/compose.env
wget https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/master/docker-compose.yml
wget https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/master/mailserver.env


wget https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/v9.0.1/setup.sh
chmod a+x ./setup.sh
```

## 配置

修改`docker-compose.yml`确保hostname+domainname是自己的邮箱服务器

```
    hostname: mail
    domainname: imapmax.xyz
    container_name: mail_server
```

启动

```bash
docker-compose up -d
```

添加一个用户

```
./setup.sh [-Z] email add <user@domain> [<password>]
```

然后就可以从第三方邮件客户端登上这个账户了

![image-20210426150606328](assets/img/notes/email/image-20210426150606328.png)



###  配置SSL证书

从阿里云就可以申请免费的SSL证书，也可以使用lets encrypted

![image-20210426150314186](assets/img/notes/email/image-20210426150314186.png)



![image-20210426151715648](assets/img/notes/email/image-20210426151715648.png)



根据文档，需要修改`docker-compose.yml`文件，将pem文件和key文件放到指定位置并映射，最后添加环境变量指定。

```
  - ./ssl/:/tmp/ssl:ro
environment:
  - SSL_TYPE=manual
  - SSL_CERT_PATH=/tmp/ssl/public.pem
  - SSL_KEY_PATH=/tmp/ssl/private.key
```





