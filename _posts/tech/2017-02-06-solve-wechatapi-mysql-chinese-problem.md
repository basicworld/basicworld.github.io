---
title: 处理微信接口+MySQLdb的中文乱码
date: 2017-02-06T16:40:53+00:00
layout: single
category: tech
tags:
  - wechat
---
微信接口+MySQLdb时，查询中文总会出现乱码问题，把python设置为utf8、mysql的varaibles设置为utf8也没用，最后发现是微信接口返回数据作怪：

微信接口中，中文字符的编码格式是：

`{'confidence': 0.73, 'encoding': 'windows-1252'}`

所以，微信返回数据时需要设置一下：

`content = content.encode('utf8')`
