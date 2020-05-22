---
title: 使用 ONNX 在 Azure SQL Edge 上部署 ML 模型
description: 本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第三部分中，你将在 SQL Edge 上运行 ONNX 机器学习模型。
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 64594267dc51fa42dabcc3083d18d631904a9cab
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593539"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>使用 ONNX 在 Azure SQL Edge 上部署 ML 模型 

本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第三部分中，你将：

1. 使用 Azure Data Studio 连接到 Azure SQL Edge 实例中的 SQL 数据库。
2. 在 Azure SQL Edge 中通过 ONNX 预测铁矿石杂质。

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>连接到 Azure SQl Edge 实例中的 SQL 数据库

1. 打开 Azure Data Studio。

2. 在“欢迎使用”选项卡中，使用以下详细信息启动一个新连接：

   |_字段_|_值_|
   |-------|-------|
   |连接类型| Microsoft SQL Server|
   |服务|为此演示创建的 VM 中提及的公共 IP 地址|
   |用户名|sa|
   |密码|创建 Azure SQL Edge 实例时使用的强密码|
   |数据库|默认|
   |服务器组|默认|
   |名称（可选）|提供可选名称|

3. 单击“连接”

4. 在“文件”部分中，打开一个新笔记本或使用键盘快捷方式 Alt + Windows + N。 

5. 将内核设置为 Python 3。

## <a name="predict-iron-ore-impurities-with-onnx"></a>通过 ONNX 预测铁矿石杂质

在 Azure Data Studio 笔记本中输入以下 Python 代码，然后运行该代码。

1. 首先，安装并导入所需的包。

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. 为回归试验定义 Azure AutoML 工作区和 AutoML 试验配置。

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. 将数据集导入 panda 帧。 出于模型定型的目的，请使用来自 Kaggle 的定型数据集[挖掘进程中的质量预测](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process)。 下载数据文件，然后将其保存到本地开发计算机上。 你将使用此数据来预测精矿中的杂质含量。

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. 分析数据以确定任何偏度。 在此过程中，查看数据帧中每列的分布和倾斜信息。

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. 检查并修复数据的偏度级别。

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. 检查其他功能与预测功能的关联性。 如果关联不高，则删除这些功能。

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. 启动 AzureML 试验，查找并训练最佳算法。 在这种情况下，将使用所有回归算法进行测试，其主要指标为标准均方根误差 (NRMSE)。 有关详细信息，请参阅 [Azure ML 试验主要指标](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric)。 以下代码将启动在本地运行 ML 试验。

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. 在 Azure SQL Edge 数据库中加载用于本地评分的模型。

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. 最后，使用 Azure SQL Edge 模型通过定型模型执行预测。

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. 使用 Python，创建相对于铁料、日期时间和硅料的预测二氧化硅百分比图表。

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>后续步骤

有关在 Azure SQL Edge 中使用 ONNX 模型的详细信息，请参阅[在 SQL Edge 中使用 ONNX 实现机器学习和 AI（预览版）](onnx-overview.md)。
