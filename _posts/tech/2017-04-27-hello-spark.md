---
layout: single
title: Hello Spark
category: tech
tags: tech
description: Spark 入门
---
pyspark常用命令
## 常用命令

```
# pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import HiveContext

conf = SparkConf().setAppName('test task')
sc = SparkContext(conf=conf)
hc = HiveContext(sc)

# 在远程声明本地的py文件
sc.addPyFile(<path_to_local_py_file>)

# 载入hdfs文本文件
rdd = sc.textFile(<path_to_hdfs_file>)

# 载入hdfs的pikle文件
rdd = sc.pickleFile(<path_to_hdfs_file>)

# hivesql
rdd = hc.sql('select * from <table name>').rdd

# rdd join
rdd = rdd1.join(rdd2)
rdd = rdd1.leftOuterJoin(rdd2)
rdd = rdd1.fullOuterJoin(rdd2)

# broadcast 变量
sc.broadcast(your_list)

# filter
rdd = rdd.filter()

# 删除hdfs文件
import subprocess
subprocess.call(["hadoop", "fs", "-rm", "-r", <hdfs filename>])

# save to hdfs
rdd.saveAsTextFile(<filename>)

```
