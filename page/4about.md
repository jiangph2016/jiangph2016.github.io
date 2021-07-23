---
layout: page
title: About
permalink: /about/
icon: heart
type: page
---


## 个人总结
---
- 研二在读， 擅长Python、C/C++、Java的开发， 熟练使用Docker、Git等工具
- 掌握机器学习、深度学习的基础知识和应用能力， 参与过多个相关项目和竞赛
- 学习能力强， 可以快速上手各种工具， 熟练运用已有技能解决具体问题

## 教育经历
---
- **中国科学院大学-网络空间安全学院** &emsp;&emsp;&emsp;&emsp;计算机技术 硕士 2019年9月-2022年7月
- **青岛大学** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; 计算机科学与技术 本科   2014年09月 - 2018年06月

## 工作经历

---
**中科曙光国际信息产业有限公司 HPC研发部门**    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; *2018年03月 - 2018年10月*  
- 参与GridView 4.1和SothisAI 2.0项目的开发工作， 协助售后解决GridView出现的技术问题
- 管理HPC部门在青岛的服务器， 为团队人员分配虚拟机以满足开发和测试需求


## 项目经历
---
**集群调度监控系统GridView**  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;     *2018年3月-2018年7月*
- 对中科曙光GridView产品中的vnc模块进行维护 
- 独立负责将开源工具noVNC集成进GridView 4.1版本， 实现了基于浏览器进行vnc会话 

**深度学习平台SothisAI**  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; *2018年07月 - 2018年10月*  
- 参与中科曙光深度学习平台SothisAI 2.0的开发， 主要负责基于Harbor搭建私有docker镜像仓库， 对镜像进行管理
- 负责jupyter各个版本docker镜像的集成 

**流量解析平台** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; *2020年09月 - 2020年12月* 
- 基于组内流量处理平台开发插件， 对应用层的流量进行识别， 提取并组装流量中传输的文件 
- flume插件开发：从Kafka消费日志， 根据需求对数据进行处理和补全， 按照接口定义的数据结构进行生产 

**恶意用户识别**  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; *2020年03月 - 2020年06月* 
- 基于爬取的YouTube评论数据和Twitter数据， 使用word2vec词向量等方法对文本提取特征， 进行恶意用户检测的实验 
- 发表论文Predicting user influence in the propagation of toxic information于KSEM 2020（三作），并获最佳学生论文亚军 

**数据虚拟化引擎** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; *2021年01月 - 2021年07月* 
- 对OpenLookeng进行了SSB性能基准测试， 并在生产环境下对OpenLookeng进行了POC测试以验证其性能和可靠性 
- 测试报告于发布InfoQ上：<https://xie.infoq.cn/article/25b2a716e993c6c9106c12ba0>

## 获奖经历
---
**2021年第三届“金风杯”能源创新挑战赛-基于AI图像识别算法的风机运维质检技术 一等奖** 
- 使用YOLO v5 + SVM算法训练模型， 对风机运维图像进行分类， 并在决赛时进行PPT讲解和答辩 DataCon 2020大数据安全分析竞赛-加密流量方向 决赛优胜奖  
- 使用机器学习算法对已标注好的正常流量/恶意流量进行学习， 并在未知流量中识别出恶意流量 

**2020年中国高校计算机大赛-网络技术挑战赛 全国二等奖**  
- 提交作品《SDN流量防御和分流辅助器》， 在控制器中使用机器学习算法对恶意流量进行识别

**第七届、第八届蓝桥杯全国软件大赛C/C++组 省赛1等奖 决赛3等奖** 

## 开源代码
---
**flow-feature** —— <https://github.com/jiangph1001/flow-feature>
- 基于scapy开发的流量特征提取工具， 按照5元组分流并提取统计特征， 主要用于在机器学习过程中为加密流量提取特征 
- 撰写专利： 一种轻量级可拓展的网络流量特征提取工具和方法 CN202110249037.8 

**Servette** —— https://github.com/jiangph1001/Servette 
- 基于C语言socket编程开发的http服务器， 实现了post/get请求， 支持分块传输和持久连接， 基于libevent实现多路并发   

**OpenLookeng-driver** —— https://github.com/jiangph1001/OpenLookeng-driver 
- 编写基于Python的OpenLookeng驱动， 用于进行自动化测试 

**gaodun_download** —— https://github.com/jiangph1001/gaodun_download   
- 解析har文件， 对某网课平台的m3u8视频资源地址和密钥进行提取， 解密并拼接为完整的视频文件