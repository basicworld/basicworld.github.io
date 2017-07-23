---
title: logistic回归和python实现-鸢尾花数据示例
date: 2016-12-07T22:34:21+00:00
layout: single
category: data
tags:
  - 回归
---
简介：数据分析入门，logistic回归。

## 鸢尾花数据
Iris数据集是常用的分类实验数据集，由Fisher, 1936收集整理。Iris也称鸢尾花卉数据集，是一类多重变量分析的数据集。数据集包含150个数据集，分为3类，每类50个数据，每个数据包含4个属性。可通过花萼长度，花萼宽度，花瓣长度，花瓣宽度4个属性预测鸢尾花卉属于（Setosa，Versicolour，Virginica）三个种类中的哪一类。

下面使用logistic为经典的鸢尾花数据分类

<!--more-->
## 分类结果
![](http://odhzhpju9.bkt.clouddn.com/flower_classify_by_logistic_regress.jpg)

```python
# 分类
[0, 1, 2]

# 截距
[ 77.73711825, 8.02399007, -14.19811218]

# 系数
[[-30.61879527, 27.54963779]
[ 0.14041199, -3.21392459]
[ 2.60373147, -0.74348327]]
```

## 代码及注释

```python
# -*- encoding:utf8 -*-
"""
logistic回归
使用鸢尾花数据库
"""

print (__doc__)

import numpy as np
import pandas as pd
from sklearn import linear_model, datasets # logestic
import matplotlib.pyplot as plt

# 加载数据
iris = datasets.load_iris()
# 矩阵
X = iris.data[:, :2]
# 列表
Y = iris.target
logreg = linear_model.LogisticRegression(C=1e5)
logreg.fit(X, Y)

# 画图
h = 0.2
x_min, x_max = X[:, 0].min() - 0.5, X[:, 0].max() + 0.5 # 坐标
y_min, y_max = X[:, 1].min() - 0.5, X[:, 1].max() + 0.5
xx, yy = np.meshgrid(np.arange(x_min, x_max, h), np.arange(y_min, y_max, h)) # 网格
Z = logreg.predict(np.c_[xx.ravel(), yy.ravel()]) # ravel 数组扁平化 c_类似于python的zip()

# 画区块
Z = Z.reshape(xx.shape)
plt.figure(1, figsize=(4, 3)) # 一张图， 4，3
plt.pcolormesh(xx, yy, Z, cmap=plt.cm.Paired)

# 画训练集散点图
plt.scatter(X[:, 0], X[:, 1], c=Y, edgecolors='k', cmap=plt.cm.Paired)
plt.xlabel('sepal length')
plt.ylabel('sepal width')

# 设置绘图范围
plt.xlim(xx.min(), xx.max())
plt.ylim(yy.min(), yy.max())
plt.xticks(())
plt.yticks(())

#
plt.savefig('flower_classify_by_logistic_regress.jpg')
```

[代码来源](http://scikit-learn.org/stable/auto_examples/linear_model/plot_iris_logistic.html#sphx-glr-auto-examples-linear-model-plot-iris-logistic-py)
