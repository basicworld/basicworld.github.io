---
layout: single
title: Hello Scrapy
category: tech
tags: tech
description: Scrapy 入门
---

## 远程部署

### 服务器端配置

```
# 创建用户
useradd wlfei
passwd wlfei

# 解决依赖
# 需要scrapyd purl
sudo yum install -y python-devel libevent-devel openssl gcc openssl-devel vim
wget https://bootstrap.pypa.io/get-pip.py
sudo /usr/bin/python get-pip.py
sudo /usr/bin/pip install scrapy scrapyd scrapyd-client purl

# 开启服务
# 部署位置为开启scrapyd服务的用户根目录
nohup scrapyd > scrapyd.log &

```


### 客户机端配置

```
## 部署
scrapyd-deploy 192.168.2.161

## 运行
curl http://192.168.2.161:6800/schedule.json -d project=<project name> -d spider=<spider name> -d setting=DOWNLOAD_DELAY=1

## 取消
curl http://192.168.2.161:6800/cancel.json -d project=<project name> -d job=<job id>
curl http://192.168.2.161:6800/cancel.json -d project=<project name> -d spider=<spidername>


```
