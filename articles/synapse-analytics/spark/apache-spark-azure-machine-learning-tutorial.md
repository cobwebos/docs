---
title: 使用 Azure 自动化 ML 运行试验
description: 使用 Apache Spark 和 Azure 自动 ML 运行机器学习试验
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: 57830ae14c5d6653f6a2225924160fcb75646c12
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590596"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>使用 Azure 自动 ML ML 和 Apache Spark 运行试验

Azure 机器学习是一种基于云的环境，可让你对机器学习模型进行定型、部署、自动化、管理和跟踪。 

在本教程中，将使用 Azure 机器学习中的 [自动机器学习](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) 来创建回归模型，以便预测 NYC 出租车费用价格。 此过程接受定型数据和配置设置，并自动循环访问不同功能标准化/标准化方法、模型和超参数设置，以达到最佳模型。

在本教程中，你将学习如何执行以下任务：
- 使用 Apache Spark 和 Azure 开放式数据集下载数据
- 使用 Apache Spark dataframes 转换和清理数据
- 定型自动化机器学习回归模型
- 计算模型准确度

### <a name="before-you-begin"></a>开始之前
- 按照 [创建 Apache Spark 池教程](../quickstart-create-apache-spark-pool-studio.md)创建 Apache Spark 池。
- 如果没有现有 Azure 机器学习工作区，请完成 [Azure 机器学习工作区设置教程](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) 。 

### <a name="understand-regression-models"></a>了解回归模型
*回归模型* 基于独立预测值预测数值输出值。 在回归中，目标是通过估计一个变量对其他变量的影响，帮助建立这些独立预测因子变量之间的关系。  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>NYC 出租车数据的回归分析示例
在此示例中，你将使用 Spark 对纽约的出租车行程提示数据执行一些分析。 数据通过 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)提供。 此数据集的子集包含有关黄色出租车行程的信息，其中包括有关每次行程、开始和结束时间、位置、成本和其他感兴趣属性的信息。

> [!IMPORTANT]
> 
> 从存储位置拉取这些数据可能会产生额外的费用。 在以下步骤中，将开发一个模型来预测 NYC 出租车费用价格。 
> 

##  <a name="download-and-prepare-the-data"></a>下载并准备数据

1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅 [创建笔记本](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > 由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 上下文。
   >

2. 由于原始数据是 Parquet 格式，因此可以使用 Spark 上下文直接将文件作为数据帧提取到内存中。 通过打开的数据集 API 检索数据，创建 Spark 数据帧。 在这里，我们将使用 Spark 数据帧 *架构读取* 属性来推断数据类型和架构。 
   
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

3. 根据 Spark 池（预览版）的大小，原始数据可能太大或需要花费太长时间来操作。 您可以使用和筛选器将此数据筛选为较小的部分 ```start_date``` ```end_date``` 。 这会应用返回一个月数据的筛选器。 获得筛选数据帧后，我们还将 ```describe()``` 在新的数据帧上运行该函数，以查看每个字段的摘要统计信息。 

   根据摘要统计信息，我们可以看到数据中有一些不规则和离群值。 例如，统计信息显示最小行程距离小于0。 我们需要对这些不规则的数据点进行筛选。
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. 现在，我们将通过从 "分拣日期时间" 字段中选择一组列并创建各种基于时间的功能，从数据集中生成功能。 我们还将筛选出之前的步骤中已标识的离群值，然后删除最后几列（无需定型）。
   
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
   
正如您所看到的，这将创建一个新的数据帧，其中包含每月的某一天的其他列、提货小时、工作日和总行程时间。 

![出租车数据帧的图片。](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>生成测试和验证数据集

获得最终数据集后，即可使用 Spark 函数将数据拆分为定型集和测试集 ```random_ split ``` 。 此函数使用提供的权重将数据随机拆分为模型定型的定型数据集和用于测试的验证数据集。

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

此步骤可确保数据点用于测试未用于定型模型的已完成模型。 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>连接到 Azure 机器学习工作区
在 Azure 机器学习中，  **工作区** 是接受 Azure 订阅和资源信息的类。 它还可创建云资源来监视和跟踪模型运行。 在此步骤中，我们将从现有 Azure 机器学习工作区创建一个工作区对象。
   
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
若要提交远程试验，需要将数据集转换为 Azure 机器学习 ```TabularDatset``` 。 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通过分析提供的文件，以表格格式表示数据。

以下代码获取现有工作区和默认数据存储 Azure 机器学习默认数据存储。 然后，它将数据存储和文件位置传递到 path 参数，以创建新的 ```TabularDataset``` 。 

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

1. 若要提交试验，需要为定型定义试验参数和模型设置。 您可以在 [此处](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)查看设置的完整列表。

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

2. 现在，我们会将定义的训练设置作为 \* \* kwargs 参数传递给 AutoMLConfig 对象。 由于我们是 Spark 中的培训，因此还必须传递 Spark 上下文，该上下文可由变量自动访问 ```sc``` 。 此外，我们还将指定定型数据和模型的类型，在这种情况下，这是一种回归模型。

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
现在，我们将在 Azure 机器学习工作区中创建一个试验对象。 试验充当单个运行的容器。 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

试验完成后，输出将返回有关已完成迭代的详细信息。 可以看到每次迭代的模型类型、运行持续时间以及训练准确性。 此字段根据指标类型最大程度地跟踪最佳运行训练分数。

![模型输出的屏幕截图。](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> 提交后，AutoML 实验将运行各种迭代和模型类型。 此运行通常需要1到1.5 小时。 

#### <a name="retrieve-the-best-model"></a>检索最佳模型
要从迭代中选择最佳模型，我们将使用 ```get_output``` 函数返回最佳运行和拟合模型。 下面的代码将检索任何记录的指标或特定迭代的最佳运行和拟合模型。

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>测试模型准确度

1. 为了测试模型准确性，我们将使用最佳模型来对测试数据集运行出租车费用预测。 ```predict```函数使用最佳模型并预测验证数据集中) 的 y (费用量的值。 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2.  (RMSE 的根本均值误差) 是模型预测的样本值与观察到的值之间的差异的频繁使用度量值。 通过将 y_test 数据帧与模型预测的值进行比较，我们将计算结果的根本平均平方误差。 

   函数 ```mean_squared_error``` 采用两个数组，并计算它们之间的平均平方误差。 然后，采用结果的平方根。 此指标指示出租车费用预测与实际费用值的预测程度。

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
   
   平方根-正方形错误是模型预测响应的准确程度的良好度量。 在结果中，您会发现模型非常适合从数据集的功能（通常在 +-$2.00 中）预测出租车费用

3. 运行以下代码，计算平均 (MAPE) 平均百分比。 此指标以错误的百分比表示准确性。 它通过计算每个预测值和实际值之间的绝对差值，并汇总所有差异来实现此功能。 然后，它将 sum 表示为实际值总计的百分比。

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
   从两个预测准确性度量值开始，你会发现，模型从数据集的功能预测出租车费用非常好。 

4. 调整线性回归模型后，我们现在需要确定模型适合数据的程度。 为此，我们将根据预测的输出绘制实际的费用值。 此外，我们还将计算 R 平方度量值，以了解如何将数据接近拟合的回归直线。

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

   在结果中，我们可以看到，R 平方度量值科目的95% 占方差。 这也由观察到的实际辞句进行验证。 回归模型所考虑的变化越多，数据点越接近于拟合的回归直线。  

## <a name="register-model-to-azure-machine-learning"></a>将模型注册到 Azure 机器学习
验证好模型后，可将模型注册到 Azure 机器学习。 注册模型后，您可以下载或部署已注册的模型，并接收您注册的所有文件。

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
最后，您还可以通过导航到 Azure 机器学习工作区中的试验来访问迭代的结果。 在这里，可以深入了解有关运行、尝试的模型和其他模型指标的状态的其他详细信息。 

![AML 工作区的屏幕截图。](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>后续步骤
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark MLlib 教程](./apache-spark-machine-learning-mllib-notebook.md)
