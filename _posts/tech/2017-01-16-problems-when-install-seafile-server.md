---
title: 安装seafile的一些问题
date: 2017-01-16T12:57:57+00:00
layout: single
category: tech
tag:
  - seafile
---

## seafile

seafile是优秀的开源私服网盘，全平台客户端免费

搭建私人云存储服务的原因：价格便宜，没有文件审查问题，安全

seafile基于python的django技术，维护起来也比较方便

官网：<https://www.seafile.com/home/>

一个很全面的安装教程：<http://www.tecmint.com/install-seafile-in-linux/>

## 启用服务后可以上传但是不能同步：一直连接服务器

最可能是linux的iptables服务问题

在iptables中开启seafile对应的端口即可，记得重启linux的iptables服务

seafile需要8000,8082,等端口，配置服务时不建议更改8082端口，目测在我的centos上更改后有bug

## 重新安装seafile后网页端css错误

最可能是上一个版本遗留的tmp文件问题，在`linux的/tmp/`中删除seafile文件即可

