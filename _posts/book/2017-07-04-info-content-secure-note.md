---
layout: single
title: 《信息内容安全》阅读笔记
date: 2017-07-04
category: book
tags: book

description: 我是笔记
---
简介：信息安全是指信息系统（包括硬件、软件、数据、人、物理环境及其基础设施）受到保护，不受偶然的或者恶意的原因而遭到破坏、更改、泄露，系统连续可靠正常地运行，信息服务不中断，最终实现业务连续性。信息安全主要包括以下五方面的内容，即需保证信息的保密性、真实性、完整性、未授权拷贝和所寄生系统的安全性。
<!-- add content here -->

# 第三章 文本内容安全

## 3.2 文本内容分析

### 3.2.1 文本语法分析

pass

### 3.2.2文本语义分析方法

定义：将文本转化为计算机可以理解的语言

分析方法包括：

**潜在语义模型**

使用矩阵的奇异值分解SVD来挖掘语义，方法复杂，不便于直观理解

**词义消歧**

解决多义词问题

有指导的机器学习方法：贝叶斯--分类问题

无指导的机器学习方法：基于义原同现频率的汉语消歧方法



**信息抽取**

抽取内容：

1. 实体，如：人、公司、地址
2. 属性，实体的特征，如：人的年龄、头衔
3. 关系（事实），实体之间的联系，如：员工与公司之间的关系
4. 事件，实体的行为或活动，如：参加秋游、两公司合并、一次突发意外;

**情感倾向分析**

咨询公司的自动化分析

自然语言处理技术的热点

分析内容：单词、句子、篇章

### 文本语用分析方法

语用学是研究如何用语言来达成一定目的的学科，即利用语用学进行文本分析，针对句子请开展高端分析

主要包括：话题跟踪检测、文本分类

## 3.3 文本内容安全应用

### 3.3.1 基于内容的网页过滤

方法：

1. 基于分级标注的方法，浏览器自带，ICRA PICS等
2. 基于URL过滤。最早
3. 数据包过滤。按IP。由硬件完成
4. 关键词过滤
5. 基于内容分析的过滤

### 3.3.2 基于内容的网络监控

关键词：国家、情感色彩、政治立场

# 4 网络多媒体内容安全

目前针对不良多媒体的过滤方法：

1. 分级标注
2. URL
3. 关键词
4. 内容分析

**不良图像识别**

基于皮肤裸露（肤色检测、纹理分析，首选）、基于敏感部位（复杂）、猥亵的人体姿态（研究少）

**不良视频识别**

视频分类：

- 一类视频：传统BS模型的在线网络视频，如优酷土豆
- 二类视频：P2P视频

视频发现：视频属于应用层服务

视频流量获取：

1. 端口识别
2. 基于净荷特征的方法（便于理解、升级方便、维护简单、精度高，开销大、只能识别签名视频）
3. 基于统计特征的流量识别方法（机器学习、数据挖掘）

**不良音频识别**

因马尔科夫模型：考虑动态特征

# 5 电子邮件内容安全

概念

原理：异步通讯、存储转发

邮件发送的三个重要部件：

1. MUA mail user agent 邮件用户代理，即用户使用的客户度，foxmail等（用户到服务器）
2. MTA mail transfer agent 邮件传输代理，用于在邮件服务提供商之间传输邮件（服务器到服务器）
3. MDA mail deliver agent 邮件递交代理，将邮件递送到邮箱（服务器到邮箱）

邮件格式标准：RFC822（纯文本邮件）、MIME（多媒体邮件）

邮件传输协议：

1. SMTP simple mail transfer protocal-发送协议
2. POP3 post office protocal 3-传输协议，只保持一段时间的链接
3. IMAP internet message access protocal-传输协议，支持断开和链接两种操作模式 端口143 TCP/IP

垃圾邮件定义：

1. 收件人没有提出要求或同意
2. 收件人无法拒绝
3. 隐藏发件人信息
4. 虚假信息
5. 病毒木马邮件

垃圾邮件产生原因：

1. SMTP缺陷-主要技术原因
2. 商业原因

垃圾邮件危害

1. 网络资源浪费
2. 用户时间浪费
3. 网络安全威胁

反垃圾邮件技术的三个阶段

1. 规则过滤、地址列表、统计过滤
2. 行为识别-利用小偷心理
3. 电子邮件认证

垃圾邮件过滤技术：

客户端过滤、服务器端过滤

[贝叶斯过滤技术](http://blog.csdn.net/sdf000/article/details/17020367)

# 6 手机短信内容安全

手机短信发展历程：

1. 文本短信 SMS
2. 增强型短信 EMS
3. 彩信 MMS

# 7 网络内容安全态势评估

# 8 信息内容安全与对抗

中文主动干扰：利用中文特点发布违规信息

应对方法：柔性中文串匹配算法、基于粗糙集和贝叶斯的网页过滤技术

粗糙集：保持分类能力的前提下，通过知识简约，导出问题的决策或分类方法



# NOTE

中科院分词软件：ICTCLAS
