---
title: 线性回归和logestic回归
date: 2016-12-07T17:47:14+00:00
layout: single
category: data
tags:
  - 回归
---

## 回归分析

百度百科：回归分析（regression analysis)是确定两种或两种以上变量间相互依赖的定量关系的一种统计分析方法。运用十分广泛，回归分析按照涉及的变量的多少，分为一元回归和多元回归分析；在线性回归中，按照因变量的多少，可分为简单回归分析和多重回归分析；按照自变量和因变量之间的关系类型，可分为线性回归分析和非线性回归分析。

特点：

- 利用连续型数据的有监督学习方式
- 最简单的机器学习方法之一
- 可处理多维数据
- 应用广泛的数据分析方法

<!--more-->

## 线性回归
h(x) = a0 + a1 * x + a2 * x ...
ai 为参数

## 检查回归有效性

即检查ai是否比较好
使用损失函数 loss function
使用错误函数 error function -- 是估计值与真实值的离差平方和的一半
损失函数越小越好

## 最小二乘法

[百度百科](http://baike.baidu.com/link?url=wsjzSnGv8mm3UI9kr6JscedYJSsDH0LDkt_kiKdz1a1YuuWrp9WlkgFcJ_9KXy94tg9sItxoJmjDLBvTJ6EYFQQrgbc6QmZFsCRDoO7etrVZ8P7VUlRs_JAdNNyTgFoEb3cJdSmq6aoWWmlGlLqqo_)
要求：X是满秩矩阵，求矩阵的逆比较慢

## 梯度下降法

原理：线性回归问题转化为最小值问题，寻找梯度下降最快的点
迭代更新方式：批梯度下降，增量梯度下降
问题：收敛速度慢

带权重的线性回归中，权重是指损失函数中离差平方和的权重

## logestic回归

logestic回归可以用于分类，0-1分类
logestic本质上是线性回归，只是在特征到结果之间增加了映射
h(x) = 1 / (1 + exp(-(a0 + a1 * x + a2 * x ...)))
采用最大似然估计求解

## 线性回归与logestic回归区别

- 线性回归要求因变量必须是连续性数据变量；
- logistic回归要求因变量必须是分类变量，二分类或者多分类的；

比如要分析性别、年龄、身高、饮食习惯对于体重的影响，
如果这个体重是属于实际的重量，是连续性的数据变量，这个时候就用线性回归来做；
如果将体重分类，分成了高、中、低这三种体重类型作为因变量，则采用logistic回归


## 参考博文

<http://blog.csdn.net/statdm/article/details/7585153>
