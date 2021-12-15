---
layout: post
title: 【Docker】web端容器监控Dashboard
category: 笔记
tags: docker
keywords: docker
---


## UI for Docker

拉取镜像
```
$ docker pull uifd/ui-for-docker
```

运行镜像

```
$ docker run -d -p 9000:9000 --privileged -v /var/run/docker.sock:/var/run/docker.sock uifd/ui-for-docker
```


## Portainer



```
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```