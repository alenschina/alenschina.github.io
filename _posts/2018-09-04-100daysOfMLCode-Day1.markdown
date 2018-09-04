---
layout:     post
title:      "100天机器学习大作战 Day 1 - 数据预处理"
subtitle:   ""
date:       2018-09-04 19:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 100 Days of ML Code
---



## 第一天 数据预处理

<br/>

第一步：导入需要的库

> Numpy 是数学方法库

```python
import numpy as np
```

> pandas 是引入以及处理数据集合的库

```python
import pandas as pd
```

第二步：导入数据集合

> 使用 pandas 的 read_csv 方法引入 Data.csv 文件作为数据集合

```python
dataset = pd.read_csv('Data.csv')
```

> 然后从数据集合中生成独立变量（自变量）和依赖变量（因变量）的矩阵和向量

```python
# Create independent variable（独立变量）. 第一个冒号是所有行（row），第二个是除了最后一个的所有列（column）
X = dataset.iloc[:, :-1].values
# Create dependent variable（依赖变量）. 所有行的第三列
y = dataset.iloc[:, 3].values
```

第三步：处理缺失数据

我们收集到的数据集合很难是完整的（homogeneous）。由于各种原因，数据项可能缺失数据，所以我们需要对其进行处理以避免降低机器学习模型的性能。通常可以使用数据集合的中间值来作为填充数据。

> 引入需要的库

```python
from sklearn.preprocessing import Imputer
```

> 使用 sklearn.preprocessing 中的 Imputer 类来完成数据填充，这里使用取均值的策略

```python
imputer = Imputer(missing_values='NaN', strategy='mean', axis=0)
imputer = imputer.fit(X[:, 1:3])
X[:, 1:3] = imputer.transform(X[:, 1:3])
```

第四步：编码分类数据

分类数据是包含标签值的变量而不是数值，其值通常限于固定集合，例如“是”和“否”。由于这样的变量不能用于数学模型，所有需要将这样的变量编码成数字。

> 引入需要的库

```python
from sklearn.preprocessing import LabelEncoder
```

> 使用 sklearn.preprocessing 的 LabelEncoder 类来编码标签值

```python
labelencoder_X = LabelEncoder()
X[:, 0] = labelencoder_X.fit_transform(X[:, 0])
```

第五步：将数据集合分类为测试集合与训练集合

> 引入需要的库

```python
from sklearn.cross_validation import train_test_split
```

> 使用 sklearn.cross_validation 的 train_test_split 类来编码标签值， 取20%的测试集合和80%的训练集合

```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
```

第六步：特征缩放

大多数机器学习算法使用欧式距离来衡量两个数据点的的特征变化。相比于低量级特征，高量级特征会在距离计算中占有更大的权重。可以通过特征标准化或者Z分数正则化来解决。

> 引入需要的库

```python
from sklearn.preprocessing import StandardScaler
```

> 使用 sklearn.preprocessing 的 StandardScaler 类进行特征标准化

```python
sc_X = StandardScaler()
X_train = sc_X.fit_transform(X_train)
X_test = sc_X.fit_transform(X_test)
```





