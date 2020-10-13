---
title: 教程：使用 Azure 自动化 ML 运行试验
description: 该教程介绍如何使用 Apache Spark 和 Azure 自动化 ML 运行机器学习试验
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: da4cef50610b219689e2271e9f70fd1adb1a235f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540500"
---
# <a name="tutorial-run-experiments-using-azure-automated-ml-and-apache-spark"></a>教程：使用 Azure 自动化 ML 和 Apache Spark 运行试验

Azure 机器学习是一种基于云的环境，可以对机器学习模型进行训练、部署、自动化、管理和跟踪。 

在本教程中，你将使用 Azure 机器学习中的[自动机器学习](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)来创建一个用于预测纽约市出租车收费价格的回归模型。 此过程接受定型数据和配置设置，并自动循环访问不同特征规范化/标准化方法、模型和超参数设置的组合，以实现最佳模型。

在本教程中，你将学习如何执行以下任务：
- 使用 Apache Spark 和 Azure 开放数据集下载数据
- 使用 Apache Spark 数据帧转换和清理数据
- 定型自动化机器学习回归模型
- 计算模型准确度

### <a name="before-you-begin"></a>在开始之前
- 按照[创建 Apache Spark 池教程](../quickstart-create-apache-spark-pool-studio.md)创建 Apache Spark 池。
- 如果还没有 Azure 机器学习工作区，请完成 [Azure 机器学习工作区设置教程](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup)。 

### <a name="understand-regression-models"></a>了解回归模型
*回归模型*基于独立的预测因子预测输出的数值。 在回归中，目标是通过估计一个变量对其他变量的影响，帮助建立这些独立预测因子变量之间的关系。  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>NYC 出租车数据的回归分析示例
在此示例中，使用 Spark 对纽约的出租车小费数据执行一些分析。 数据通过 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)提供。 此数据集的子集包含有关黄色出租车行程的信息，其中包括有关每次行程、开始和结束时间、位置、成本和其他感兴趣属性的信息。

> [!IMPORTANT]
> 
> 从存储位置拉取这些数据可能会产生额外的费用。 在以下步骤中，将开发一个模型来预测 NYC 出租车乘坐价格。 
> 

##  <a name="download-and-prepare-the-data"></a>下载并准备数据

1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅[创建笔记本](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > 由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 上下文。
   >

2. 由于原始数据是 Parquet 格式，因此可以使用 Spark 上下文直接将文件作为数据帧提取到内存中。 通过使用开放数据集 API 检索数据，创建 Spark 数据帧。 在这里，使用 Spark 数据帧“基于读取的架构”属性来推断数据类型和架构。 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. 根据 Spark 池（预览版）的大小，原始数据可能太大或需要花费太长时间来操作。 可以使用 ```start_date``` 和 ```end_date``` 筛选器将此数据筛选到更小的范围。 这会应用一个可返回一个月数据的筛选器。 完成筛选后，还会对新的数据帧运行 ```describe()``` 函数，以查看每个字段的摘要统计信息。 

   根据摘要统计信息，可以看到数据中存在一些非正常值和离群值。 例如，统计信息显示最小行程距离小于 0。 我们需要将这些非正常数据点筛选掉。
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. 现在，通过从“上车日期时间”字段中选择一组列并创建各种基于时间的特征，从数据集中生成特征。 还要筛选掉之前步骤中标识出的离群值，然后删除最后几个在训练中不需要的列。
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
可以看到，这会创建一个新的数据帧，其中包含额外几个列，分别表示几月几号、上车时间、星期几和总行程时间。 

![出租车数据帧的图片。](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>生成测试和验证数据集

获得最终数据集后，可以使用 Spark ```random_ split ``` 函数将数据拆分为训练集和测试集。 此函数使用提供的权重将数据随机拆分为用于模型训练的训练数据集和用于测试的验证数据集。

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

此步骤可确保用于测试已完成模型的数据点未曾用于模型训练。 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>连接到 Azure 机器学习工作区
在 Azure 机器学习中，工作区是一个接受 Azure 订阅和资源信息的类。 它还可创建云资源来监视和跟踪模型运行。 在此步骤中，从现有 Azure 机器学习工作区创建一个工作区对象。
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>将数据帧转换为 Azure 机器学习数据集
为提交远程试验，需要将数据集转换为 Azure 机器学习 ```TabularDatset```。 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 通过分析提供的文件，以表格格式表示数据。

以下代码获取现有工作区和默认 Azure 机器学习默认数据存储。 然后它将数据存储和文件位置传递给 path 参数，以创建新的 ```TabularDataset```。 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![上传的数据集的图片。](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>提交自动 ML 试验

#### <a name="define-training-settings"></a>定义训练设置

1. 若要提交试验，需要定义用于训练的试验参数和模型设置。 可以在[此处](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)查看完整的设置列表。

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

2. 现在，将定义的训练设置作为 \*\*kwargs 参数传递给 AutoMLConfig 对象。 由于在 Spark 中训练，因此还需要传递 Spark 上下文，该上下文可自动变为 ```sc``` 变量可访问的状态。 此外，还将指定训练数据和模型类型，在本例中是回归模型。

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
> 自动机器学习预处理步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为基础模型的一部分。 使用模型进行预测时，训练期间应用的相同预处理步骤将自动应用于输入数据。

#### <a name="train-the-automatic-regression-model"></a>训练自动回归模型 
现在，在 Azure 机器学习工作区中创建一个试验对象。 试验充当单个运行的容器。 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

试验完成后，输出将返回有关已完成的迭代的详细信息。 可以看到每次迭代的模型类型、运行持续时间以及训练准确性。 字段 BEST 根据指标类型跟踪运行情况最好的训练分数。

![模型输出的屏幕截图。](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> 提交后，AutoML 实验将运行各种迭代和模型类型。 此运行通常需要 1 到 1.5 个小时。 

#### <a name="retrieve-the-best-model"></a>检索最佳模型
为了从迭代中选择最佳模型，使用 ```get_output``` 函数返回最佳运行和拟合模型。 以下代码针对任何记录的指标或特定的迭代来检索最佳运行和拟合的模型。

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>测试模型准确度

1. 为了测试模型准确性，使用最佳模型对测试数据集运行出租车费用预测。 ```predict``` 函数使用最佳模型并预测验证数据集中 y（费用）的值。 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. 均方根误差 (RMSE) 是经常用来度量模型预测的样本值与观察到的值之间的偏差的度量指标。 通过将 y_test 数据帧与模型预测的值进行比较，计算结果的均方根误差。 

   函数 ```mean_squared_error``` 接受两个数组，并计算两个数组之间的平均平方误差。 然后，采用结果的平方根。 此指标大致指出了出租车费预测值与实际费用值之间有多大的差距。

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   均方根误差是用于度量模型的响应结果预测准确度的不错指标。 从结果来看，该模型可以很好地根据数据集的特性来预测出租车费用，误差通常在 2.00 美元上下

3. 运行以下代码，计算平均绝对百分比误差 (MAPE)。 此指标以误差的百分比来表示准确性。 该指标实现此目的的方式是计算每个预测值和实际值之间的绝对差，然后将所有差值加总。 然后，它将这一总和表示为实际值总和的百分比。

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   从这两个预测准确率指标来看，该模型可以很好地根据数据集的特性来预测出租车费用。 

4. 在拟合出一个线性回归模型后，现在需要确定模型与数据的拟合程度。 为此，根据预测的输出绘制实际的费用值。 此外，还将计算 R 平方度量值，以了解数据与拟合的回归线的接近程度。

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![回归图的屏幕截图。](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   从结果可以看到，R 平方度量值占方差的 95%。 这同时可通过实际值与观察值的比较图得到验证。 回归模型在方差中的占比越大，数据点就越接近于拟合的回归线。  

## <a name="register-model-to-azure-machine-learning"></a>将模型注册到 Azure 机器学习
验证完最佳模型后，可将模型注册到 Azure 机器学习。 注册模型后，可下载或部署注册的模型，然后就能收到注册的所有文件。

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>查看 Azure 机器学习中的结果
最后，还可以通过导航到 Azure 机器学习工作区中的试验来访问迭代的结果。 在这里，可以了解有关运行状态、尝试的模型和其他模型指标的更多详细信息。 

![AML 工作区的屏幕截图。](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>后续步骤
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark MLlib 教程](./apache-spark-machine-learning-mllib-notebook.md)
