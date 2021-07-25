---
layout: post
title: 【ML】使用Yolo v5进行目标检测
category: 技能
tags: ML
keywords: YOLO,目标检测
---
# 使用使用Yolo v5进行目标检测

## 环境
为了快速使用YOLO的环境，使用docker进行部署

拉取镜像
```
docker pull ultralytics/yolov5:latest
```
镜像比较大，有14.5GB，所以需要更改镜像源为阿里云的镜像（测试时只有这个比较快）
## 快速运行
启动docker，并将当前目录挂载到`/usr/src/coco`目录下
```
docker run --ipc=host -it -v "$(pwd)":/usr/src/coco ultralytics/yolov5:latest
```

然后利用预训练的模型对图像进行检测
```
python detect.py --source [文件名]
```
第一次运行会自动从github下载v5s模型，国内下载的速度比较慢，可以通过其他途径下好后放入`/usr/src/app`目录下
```
docker cp yolov5s.pt [容器名]:/usr/src/app
```

下载地址

- [yolo v5s](https://github.91chifun.workers.dev//https://github.com/ultralytics/yolov5/releases/download/v4.0/yolov5s.pt)
- [yolo v5m](https://github.91chifun.workers.dev//https://github.com/ultralytics/yolov5/releases/download/v4.0/yolov5m.pt)
- [yolo v5l](https://github.91chifun.workers.dev//https://github.com/ultralytics/yolov5/releases/download/v4.0/yolov5l.pt)
- [yolo v5x](https://github.91chifun.workers.dev//https://github.com/ultralytics/yolov5/releases/download/v4.0/yolov5x.pt)

结果放在`/usr/src/app/runs/detect/exp`目录中



### 自用命令

```
docker run --ipc=host -it --name=yolov5 -v "$(pwd)/input":/usr/src/input -v "$(pwd)/output":/usr/src/app/runs ultralytics/yolov5:latest
python detect.py --source /usr/src/input/[文件名]
```
## 训练

准备自己的数据集需要标注，在线标注工具：<https://www.makesense.ai/>

```
$ python train.py --data coco.yaml --cfg yolov5s.yaml --weights '' --batch-size 64
                                         yolov5m                                40
                                         yolov5l                                24
                                         yolov5x     
```                                   

## 参考
- <https://github.com/ultralytics/yolov5/wiki/Docker-Quickstart>
- <https://github.com/ultralytics/yolov5>
- <https://blog.csdn.net/ai_faker/article/details/107099907>