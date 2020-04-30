---
title: 使用 Apache Spark MLlib 和 Azure Synapse Analytics 构建机器学习应用
description: 了解如何使用 Apache Spark MLlib 创建机器学习应用，以便通过逻辑回归分析使用分类的数据集。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 25d11d2cf41f8653c5a54007f121c1251bb24b1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096293"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>使用 Apache Spark MLlib 和 Azure Synapse Analytics 构建机器学习应用

本文介绍如何使用 Apache Spark [MLlib](https://spark.apache.org/mllib/)创建在 Azure 开放式数据集上执行简单预测分析的机器学习应用程序。 Spark 提供内置的机器学习库。 此示例通过逻辑回归使用*分类*。

MLlib 是一个核心 Spark 库，它提供了许多可用于机器学习任务的实用工具，包括适用于以下内容的实用程序：

- 分类
- 回归测试
- 聚类分析
- 主题建模
- 单值分解 (SVD) 和主体组件分析 (PCA)
- 假设测试和计算示例统计信息

## <a name="understand-classification-and-logistic-regression"></a>了解分类和逻辑回归

*分类*是一种常见的机器学习任务，是将输入数据按类别排序的过程。 分类算法的工作就是确定如何将*标签*分配给您提供的输入数据。 例如，你可以考虑一种机器学习算法，它接受股票信息作为输入并将股票划分为两个类别：应销售的股票和应该保留的股票。

*逻辑回归*是一种可用于分类的算法。 Spark 的逻辑回归 API 可用于 *二元分类*，或将输入数据归类到两组中的一组。 有关逻辑回归的详细信息，请参阅[维基百科](https://en.wikipedia.org/wiki/Logistic_regression)。

总之，逻辑回归的过程会产生 *逻辑函数*，可用于预测输入向量属于一个组或另一个组的概率。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC 出租车数据的预测分析示例

在此示例中，使用 Spark 对纽约的出租车行程提示数据执行一些预测分析。 数据可通过[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)获取。 此数据集的子集包含有关黄色出租车行程的信息，其中包括有关每个行程的信息、开始和结束时间、地点、成本和其他有趣的属性。

> [!IMPORTANT]
> 从存储位置拉取这些数据可能会产生额外的费用。

在下面的步骤中，您将开发一个模型来预测特定行程是否包含提示。

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>创建 Apache Spark MLlib 机器学习应用

1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅[创建笔记本](../quickstart-apache-spark-notebook.md#create-a-notebook)。
2. 导入此应用程序所需的类型。 将以下代码复制并粘贴到一个空单元格中，然后按**SHIFT + enter**，或使用代码左侧的蓝色播放图标运行该单元格。

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 当你运行第一个代码单元格时，将自动为你创建 Spark 上下文。

## <a name="construct-the-input-dataframe"></a>构造输入数据帧

由于原始数据是 Parquet 格式，因此可以使用 Spark 上下文直接将文件作为数据帧提取到内存中。 尽管下面的代码使用默认选项，但如果需要，可以强制数据类型和其他架构属性的映射。

1. 运行以下代码行，通过将代码粘贴到新单元来创建 Spark 数据帧。 第一部分将 Azure 存储访问信息分配给变量。 第二部分允许 Spark 远程从 blob 存储中读取。 最后一行代码将读取 parquet，但此时不会加载任何数据。

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. 提取所有这些数据将生成大约1500000000行。 根据 Spark 池（预览版）的大小，原始数据可能太大或需要花费太多时间来操作。 您可以将此数据筛选到较小的部分。 如果需要，请添加以下行以将数据筛选到大约2000000行，以获得更快的响应体验。 使用这些参数提取一周的数据。

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. 简单筛选的缺点是，从统计的角度来看，它可能会导致数据偏置。 另一种方法是使用 Spark 中内置的采样。 下面的代码将数据集缩小到约2000行（如果在上述代码之后应用）。 此抽样步骤可用于代替简单筛选器，也可与简单筛选器结合使用。

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. 现在，可以查看数据查看读取的内容。 通常，使用子集（而不是完整集）查看数据通常会更好，具体取决于数据集的大小。 下面的代码提供了两种查看数据的方法：前者是基本的，后者提供了更丰富的网格体验，以及以图形方式直观显示数据的功能。

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. 根据生成的数据集大小和需要多次尝试或运行笔记本的大小，可能建议在工作区本地缓存数据集。 执行显式缓存的方法有三种：

   - 将数据帧作为文件本地保存
   - 将数据帧保存为临时表或视图
   - 将数据帧保存为永久表

下面的代码示例中包含了这些方法中的前两种方法。

创建临时表或视图可提供数据的不同访问路径，但仅持续于 Spark 实例会话的持续时间。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>了解数据

通常，您会经历一段*探索性数据分析*（EDA）来开发对数据的理解。 下面的代码显示了三种不同的数据可视化效果，这些数据与导致数据状态和质量结论的提示相关。

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![直方图](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![框形绘制](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![散点图](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>准备数据

原始格式的数据通常不适合直接传递到模型。 必须对数据执行一系列操作，使其进入模型可以使用它的状态。

在下面的代码中，将执行以下四类操作：

- 通过筛选删除离群值/错误值。
- 删除不需要的列。
- 创建从原始数据派生的新列，使模型更有效地工作，有时称为特征化。
- 标记，因为您正在执行二元分类（会有一条提示，或不在给定行程中），因此需要将 tip 数量转换为0或1值。

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

然后，将对数据进行第二次传递以添加最终功能。

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>创建逻辑回归模型

最后一项任务是将标签数据转换为逻辑回归可分析的格式。 逻辑回归算法的输入需要是一组*标签功能向量对*，其中*特性向量*是表示输入点的数字向量。 因此，需要将分类列转换为数字。 `trafficTimeBins`和`weekdayString`列需要转换为整数表示形式。 有多种方法可执行转换，但在此示例中采用的方法是*OneHotEncoding*，这是一种常见的方法。

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

这会生成一个新的数据帧，其中所有列都采用正确的格式来训练模型。

## <a name="train-a-logistic-regression-model"></a>训练逻辑回归模型

第一个任务是将数据集拆分为定型集和测试集和验证集。 此处的拆分是任意的，你应该围绕不同的拆分设置来查看它们是否影响模型。

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

现在有两个 DataFrames，下一个任务就是创建模型公式并针对定型数据帧运行它，然后针对测试数据帧进行验证。 您应该试验不同版本的模型公式，以查看不同组合的影响。

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

此单元格的输出为

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>创建预测的直观表示形式

现在可以构造一个最终可视化效果，以帮助推理此测试的结果。 [ROC 曲线](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)是查看结果的一种方法。

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![逻辑回归提示模型的 ROC 曲线](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "逻辑回归提示模型的 ROC 曲线")

## <a name="shut-down-the-spark-instance"></a>关闭 Spark 实例

完成应用程序的运行后，关闭该选项卡，或从笔记本底部的 "状态" 面板中选择 "**结束会话**" 以释放资源。

## <a name="see-also"></a>另请参阅

- [概述： Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>后续步骤

- [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 官方文档](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 某些 Apache Spark 官方文档依赖于使用 Spark 控制台，但该控制台在 Azure Synapse Spark 中不可用。 请改用[笔记本](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)或 [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 体验。
