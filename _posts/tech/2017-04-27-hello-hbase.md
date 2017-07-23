---
layout: single
title: Hello Hbase
category: tech
tags: hbase
description: Hbase 入门
---
操作hbase的常用命令
HBase是一个分布式的、面向列的开源数据库

## 常用命令

### 使用phoniex连接hbase

```
/usr/hdp/2.5.3.0-37/phoenix/bin/sqlline.py
```

### 新建表

```
!describe ml.address_book_learning;
CREATE TABLE IF NOT EXISTS ml.address_book_learning (phone varchar primary key, cf1.location varchar);
```

### 新建列

```
ALTER TABLE ml.address_book_learning ADD cf1.career_info varchar;
```

### 添加数据

```
nohup spark-submit --queue ml --master yarn --driver-memory 40G  --executor-memory 40G --executor-cores 10 --num-executors 200 --conf spark.akka.frameSize=2000 --jars ./libs/hbase/hbase-common.jar,./libs/hbase/hbase-client.jar,./libs/hbase/hbase-protocol.jar,./libs/hbase/hbase-server.jar --class org.linkface.data.analysis.ml.LoadAddressBookLearningData2HBase ./analysis.jar CAREER_INFO /data/ml/address_book/career_info 2048
```
