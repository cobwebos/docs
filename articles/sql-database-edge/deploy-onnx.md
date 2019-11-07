---
title: 在 SQL 数据库边缘预览版中通过 ONNX 进行部署和预测
description: 了解如何定型模型，如何将其转换为 ONNX，将其部署到 Azure SQL 数据库边缘预览版，并使用上传的 ONNX 模型对数据运行本机预测。
keywords: 部署 sql 数据库边缘
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: 37fc04919b844d1edf87be62a587c34de4a8c4d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692337"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-in-sql-database-edge-preview"></a>使用 SQL 数据库边缘预览版中的 ONNX 模型进行部署和预测

在本快速入门中，你将了解如何定型模型，如何将其转换为 ONNX，如何将其部署到 Azure SQL 数据库边缘预览版，并使用上传的 ONNX 模型对数据运行本机预测。 有关详细信息，请参阅[在 SQL 数据库边缘预览中通过 ONNX 进行机器学习和 AI](onnx-overview.md)。

本快速入门基于**scikit-learn-了解**并使用[波士顿机架数据集](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)。

## <a name="before-you-begin"></a>开始之前

* 如果尚未部署 Azure SQL 数据库边缘模块，请按照[使用 Azure 门户部署 SQL 数据库边缘预览](deploy-portal.md)中的步骤操作。

* 安装[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download)。

* 打开 Azure Data Studio 并按照以下步骤安装此快速入门所需的包：

    1. 打开连接到 Python 3 内核的[新笔记本](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks)。 
    1. 单击 "**管理包**" 并在 "**添加新**项" 下，搜索**spark-sklearn**，并安装 scikit-learn-了解包。 
    1. 同时，安装**onnxmltools**、 **onnxruntime**、 **skl2onnx**和**sqlalchemy**包。
    
* 对于下面的每个脚本部分，请将其输入到 Azure Data Studio 笔记本中的单元格中，然后运行单元。

## <a name="train-a-pipeline"></a>训练管道

将数据集拆分为使用功能来预测房子的中间值。

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])

# x contains all predictors (features)
x = df.drop(['MEDV'], axis = 1)

# y is what we are trying to predict - the median value
y = df.iloc[:,-1]


# Split the data frame into features and target
x_train = df.drop(['MEDV'], axis = 1)
y_train = df.iloc[:,-1]

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**输出**：

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

创建一个管道来训练 LinearRegression 模型。 您还可以使用其他回归模型。

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

检查模型的准确性，然后计算 R2 评分和平均平方误差。

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**输出**：

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>将模型转换为 ONNX

将数据类型转换为支持的 SQL 数据类型。 还需要对其他 dataframes 进行此转换。

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

使用 `skl2onnx`将 LinearRegression 模型转换为 ONNX 格式，并将其保存在本地。

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>测试 ONNX 模型

将模型转换为 ONNX 格式后，对模型进行评分，使其不会降低性能。

> [!NOTE]
> ONNX 运行时使用浮动，而不是双精度型，因此可能会出现小差异。

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**输出**：

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>插入 ONNX 模型

将模型存储在数据库 `onnx`的 `models` 表中的 Azure SQL 数据库边缘。 在 "连接字符串" 中，指定**服务器地址**、**用户名**和**密码**。

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>加载数据

将数据加载到 Azure SQL 数据库边缘。

首先，创建两个表：**功能**和**目标**，以存储波士顿机架数据集的子集。

* **功能**包含用于预测目标值的所有数据。 
* **目标**包含数据集中每个记录的中值。 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

最后，使用 `sqlalchemy` 将 `x_train` 并 `y_train` pandas dataframes 分别插入表 `features` 和 `target`。 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

现在您可以查看数据库中的数据了。

## <a name="run-predict-using-the-onnx-model"></a>使用 ONNX 模型运行预测

使用 Azure SQL 数据库边缘的模型，使用上传的 ONNX 模型对数据运行本机预测。

> [!NOTE]
> 将笔记本内核更改为 SQL 以运行剩余的单元格。

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [onnx].[dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input) WITH (variable1 FLOAT) AS p
```

## <a name="next-steps"></a>后续步骤

* [在 SQL 数据库边缘中通过 ONNX 机器学习和 AI](onnx-overview.md)
