---
layout: single
title: Hello Mongodb
category: tech
tags: mongodb
description: Mongodb 入门
---
MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。
MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

## 背景概念

数据库、按需求查询、数据库分类（SQL数据库-横向扩展不好，nosql数据库-redis、mongodb）

nosql：延迟性、没有join、没有表结构（简单）

## mongodb特点

- 没有表结构
- 完全索引支持（单建索引、数组索引、全文索引、**地理位置索引**）
- 数据安全（复制集）
- 扩展（扩容、分布式）

## mongodb相关概念

- mongodb：数据库
- mongo：mongodb的客户端
- 索引：就是索引
- 集合：类比mysql的table
- 复制集：用于数据安全
- 分片：用于分布式
- 数据均衡：用于分布式


## 单机搭建

[参考网址](https://www.liquidweb.com/kb/how-to-install-mongodb-on-centos-6/)


## 运行

### 打开服务

```
service mongod start  # 默认使用`/etc/mongod.conf`配置文件 即`/usr/bin/mongod -f /etc/mongod.conf`
mongod -f mongod.conf # 自己创建的mongod配置文件
```

### 连接服务器

方式1：使用mongo客户端

```
# 连接
mongo 127.0.0.1:12345
# 关闭连接
use admin
db.shutdownServer()
ctrl + C
```

方式2：使用各种驱动-以python为例

```
todo
```

## 使用

```
show dbs
use local
db.dropDatabase()
use test  # 自动创建collection
db.test_collection.insert({x:1})  # 插入之后collection才真正创建
db.test_collection.find()
db.test_collection.insert({x:2,_id:1})  # _id 在全局范围不能重复
for(i=3;i<100;i++) db.test_collection.insert({x:i})  # 多条插入

# 查询
db.test_collection.find().count()
db.test_collection.find().skip(3).limit(2).sort({x:1})  # skip前3条，输出两条，按x正序排序

# 更新
db.test_collection.update({x:1},{x:999})  # 默认更新只会更新第一条符合条件的数据
## 部分更新
db.test_collection.update({x:100},{$set:{y:99}})
## 更新或创建
db.test_collection.find({x:1000},{x:999},true)  # 会产生一条x=999的数据
## 更新符合条件的全部数据 -- 此时必须使用$set
db.test_collection.update({x:1},{$set:{x:2}},upsert=false,multisert=true)

# 删除
## 删除记录 -- 默认删除符合条件的所有记录
db.test_collection.remove({x:2})
## 删除表
db.test_collection.drop()
```

## 索引

```
# 查询目前的索引
db.test_collection.getIndexes()

# 删除索引
db.test_collection.dropIndex("x_1")  # 删除以x_1为名字的索引

# _id索引 自动建立

# 单键索引 不会自动创建
db.test_collection.ensureIndex({x:1})

# 多键索引
值具有多个记录，如list
创建方式与单键索引相同

# 复合索引
db.test_collection.ensureIndex({x:1, y:1})

# 过期索引 TTL
适合存储在一段时间后失效的数据，如登录数据、日志
存储的数据必须为时间或时间数组，如果是时间数组，则按最小的时间为准
删除时间不精确，有误差，mongodb每60s扫描一遍过期索引的数据，并且删除也需要一定的时间
db.test_collection.insert({time:new Date()})
db.test_collection.ensureIndex({z:1},{expireAfterSeconds:30})

# 全文索引
db.test_collection.ensureIndex({article:"text"})  # 对test集合的article列设置全文索引
全文索引也可以设置复合索引、全部列索引
db.test_collection.find({$text:{$search:"AA"}})
db.test_collection.find({$text:{$search:"AA BB"}})  # 查询包含AA或BB
db.test_collection.find({$text:{$search:"AA -BB"}})  # 查询包含AA且不包含BB
db.test_collection.find({$text:{$search:"\"AA\" \"BB\""}})  # 查询包含AA和BB

## 全文索引相似度查询 -- 常与sort一起使用
db.test_collection.find({$text:{$search:"AA BB"}}, {score:{$meta:"textScore"}})  # 添加一列：score，表示相似度
db.test_collection.find({$text:{$search:"AA BB"}}, {score:{$meta:"textScore"}}).sort({score:{$meta:"textScore"}})  # 按相似度降序排列
db.test_collection.find({$text:{$search:"AA BB"}}, {score:{$meta:"textScore"}}).sort({score:{$meta:"textScore"}}).limit(2)  # 限制只输出2条

## 全文索引的限制
每次查询只能使用一个$text
不能出现在$nor中
hint会因此失效
不支持中文
mongodb目前在国内主要用于数据仓库，不适合做数据库

# 地理位置索引
查找距离某一点一定距离内的点、查找一定区域内的点
存储方式：[经度,纬度]
不要插入超过经纬度范围的点，将会出现不可预知的错误
## 2D索引-平面索引，2Dsphere索引-球面索引 区别是距离的计算方式不同
##2D索引
db.location.ensureIndex({w:"2d"})  # 创建
db.location.insert({w:[1,2]})  #插入
db.location.find({w:{$near:[1,1]})  # 查询最近的100个点--默认为100
db.location.find({w:{$near:[1,1],$maxDistance:2}})  # 查询最大距离为2的点
db.location.find({w:{$geoWithin:{$box:[[0,0],[3,3]]}}})  # 查询位于某个矩形中的点[左上角，右下角]
db.location.find({w:{$geoWithin:{$center:[[0,0],3]}}})  # 查询以某个点为半径的一定距离的点[圆心，半径]
db.location.find({w:{$geoWithin:{$polygon:[[0,0],[0,2],[1,0],[1,2]]}}})  # 查询多边形矩阵内的点[左上角，右上角，左下角，右下角]
### geoNear
db.runCommand({geoNear:"location",near:[1,2],maxdistance:10,num:2})  # 查询location集合中，距离[1,2]点为10以内的最近的2个点

##2dspahere
db.location.ensureIndex({w:"2dspahere"})  # 创建
可以存储点、多边形等多种类型的数据
支持$minDistance $maxDistance等

# 给索引命名
索引名字长度限制：125字节
db.test_collection.ensureIndex({x:1}, {name:"main_index"})

# 唯一性：限制unique属性 表示该索引下不允许重复
db.test_collection.ensureIndex({x:1}, {unique:true})
BUG：设置多个unique索引会导致插入问题

# 稀疏性 spare：mongodb处理存在的和不存在的内容的不同方式，即：是否为不存在的空字段创建索引，默认为创建
db.test_collection.ensureIndex({x:1}, {spare:true})  # 设置为系数索引
注意：
此时使用 db.test_collection.find({m:{$exists:false}})会出现问题：显示没有m字段的行
解决：使用强制索引 hint(<索引名称>)
db.test_collection.find({m:{$exists:false}}).hint("m_1")
```

**索引的构建情况分析**

- mongostat  命令行运行`mongostat -h 127.0.0.1:12345` 可查看`insert query update delete getmore command dirty used flushes vsize   res qrw arw net_in net_out conn time` 其中比较重要的： `qr`是读队列 `qw`是写队列 `idx miss`查询没有命中索引的情况
- profile集合 mongo中运行`db.getProfilingStatus()` 有相关的设置选项 如mongodb记录哪些操作 profile主要用于测试和观察阶段，因为影响系统性能
- mongodb日志 启动日志时可设置日志的详细程度`verbose='VVVVV'` 5个v是最详细，一个v是最简单
- explain分析 mongo中运行`db.test_collection.find().explain()`

----

## 安全防护

### 安全级别

1. 物理级别-不现实
1. 网络隔离-内网
1. 防火墙隔离-只允许某些ip
1. 用户名密码

### 开启权限认证

#### 设置参数

1. 在`mongod.conf`中设置`auth=true`
1. 重启mongod
1. 重启后在日志中会看到`security: { authorization: "enabled" }`

#### 创建用户

mongodb创建权限需要在对应的db下面执行命令--还不是非常明白

先关闭auth权限，执行
```
mongo 127.0.0.1:12345
use admin
db.createUser(
... {
... user:"dba",pwd:"xxx",roles:[{role:"userAdminAnyDatabase",db:"admin"}]})
```
然后开启auth权限，执行
```
use admin
db.auth("dba","xxx")
use test
db.createUser({user:"finder",pwd:"finder",roles:[{role:"read",db:"test"}]})
```

详细用户角色：

- read  # 读写类
- readWrite
- dbAdmin
- dbOwener
- userAdmin
- clusterAdmin  # 集群类
- clusterManager
- backup  # 备份类
- DBAdminAnyDatabase # 特殊权限
- readAnyDatabse

创建用户角色：组合特定权限，细分权限组，用到的时候再看


## 相关网站

- 官网
- www.mongoing.com
- docs.mongoing.com
- github.com/mongodb
- jira.mongodb.com
- mongodb-user
- docker使用教程：<http://www.ifdattic.com/how-to-mongodb-nodejs-docker/>
