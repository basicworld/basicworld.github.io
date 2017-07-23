---
title: 使用python进行回归分析-线性回归-房价预测
date: 2016-12-07T16:25:15+00:00
layout: single
category: data
tags:
  - 回归
---
简介：数据分析入门，使用线性回归介绍，及示例：房价预测。

## python模块

`sklearn` `numpy` `pandas`

## 输入数据

![](http://odhzhpju9.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-12-07%2016.16.33.png)

<!--more-->
## 计算过程

```python
# -*- encoding:utf8 -*-

import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from sklearn import datasets, linear_model

def get_data(filename, x_name, y_name):
    """
    获取名称为 squrefeet, price 的两列值
    """
    data = pd.read_csv(filename)
    # return data.squrefeet, data.price
    x_values = [] # 矩阵
    y_values = [] # 列表
    for i, j in zip(data[x_name], data[y_name]):
    x_values.append([float(i)]) #
    y_values.append(float(j))

    return x_values, y_values

def linear_predict(x_values, y_values, predict_value):
    """
    线性回归拟合
    """
    regr = linear_model.LinearRegression(fit_intercept=False)
    regr.fit(x_values, y_values)
    predict_outcome = regr.predict(predict_value)
    predictions = {}
    predictions['intercept_'] = regr.intercept_ # 截距
    predictions['coefficient'] = regr.coef_ # 回归系数
    predictions['predict_outcome'] = predict_outcome #

    # 画图
    fig, ax = plt.subplots()
    ax.scatter(x_values, y_values, color='blue')
    ax.plot(x_values, regr.predict(x_values), color='red', linewidth=2)
    ax.set_xlabel('squrefeet')
    ax.set_ylabel('price')
    # plt.show()
    plt.savefig(filename='linear_predict_houseprice_output.jpg')

    return predictions

if __name__ == '__main__':
    xs, ys = get_data('house_price.csv', 'squrefeet', 'price')
    print linear_predict(xs, ys, 700)

```

## 结果输出

```python
{'coefficient': array([ 33.42565598]),

'predict_outcome': array([ 23397.95918367]),

'intercept_': 0.0}

```

### 方程

`y = 33.42565598 * x`
`y(x=700) = 23397.95918367`

### 画图

![](http://odhzhpju9.bkt.clouddn.com/linear_predict_houseprice_output.jpg)
