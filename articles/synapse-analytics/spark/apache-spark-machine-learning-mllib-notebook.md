---
title: 使用 Apache Spark MLlib 和 Azure 突触分析构建机器学习应用
description: 了解如何使用 Apache Spark MLlib 创建机器学习应用，该应用程序使用逻辑回归的分类分析数据集。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430001"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>使用 Apache Spark MLlib 和 Azure 突触分析构建机器学习应用

在本文中，您将了解如何使用 Apache Spark [MLlib](https://spark.apache.org/mllib/)创建机器学习应用程序，该应用程序在 Azure 打开的数据集上执行简单的预测分析。 Spark 提供内置机器学习库。 此示例使用逻辑回归*分类*。

MLlib 是一个核心 Spark 库，它提供了许多可用于机器学习任务的实用程序，包括适用于以下功能的实用程序：

- 分类
- 回归
- 群集
- 主题建模
- 单值分解 (SVD) 和主体组件分析 (PCA)
- 假设测试和计算示例统计信息

## <a name="understand-classification-and-logistic-regression"></a>了解分类和逻辑回归

*分类*是一种常见的机器学习任务，是将输入数据按类别排序的过程。 分类算法的工作是找出如何为提供的输入数据分配*标签*。 例如，您可以想到机器学习算法，该算法接受股票信息作为输入，并将股票分为两类：您应该出售的股票和应保留的股票。

*逻辑回归*是可用于分类的算法。 Spark 的逻辑回归 API 可用于 *二元分类*，或将输入数据归类到两组中的一组。 有关逻辑回归的详细信息，请参阅[维基百科](https://en.wikipedia.org/wiki/Logistic_regression)。

总之，逻辑回归的过程会产生 *逻辑函数*，可用于预测输入向量属于一个组或另一个组的概率。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>纽约市出租车数据预测分析示例

在此示例中，您可以使用 Spark 对来自纽约的出租车行程提示数据执行一些预测性分析。 数据可通过 Azure[开放数据集获得](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)。 数据集的此子集包含有关黄色出租车行程的信息，包括有关每次行程、开始和结束时间和地点、成本和其他有趣属性的信息。

> [!IMPORTANT]
> 从存储位置提取此数据可能会收取额外费用。

在以下步骤中，您将开发一个模型来预测特定行程是否包含提示。

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>创建 Apache Spark MLlib 机器学习应用

1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅[创建笔记本](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook)。
2. 导入此应用程序所需的类型。 复制以下代码并将其粘贴到空单元格中，然后按**SHIFT + ENTER**，或使用代码左侧的蓝色播放图标运行单元格。

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

    由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 当您运行第一个代码单元时，将自动为您创建 Spark 上下文。

## <a name="construct-the-input-dataframe"></a>构造输入数据帧

由于原始数据采用 Parquet 格式，因此可以使用 Spark 上下文将文件直接作为数据帧放入内存。 虽然下面的代码使用默认选项，但如果需要，可以强制映射数据类型和其他架构属性。

1. 通过将代码粘贴到新单元格中，运行以下行以创建 Spark 数据帧。 第一节将 Azure 存储访问信息分配给变量。 第二部分允许 Spark 从 Blob 存储远程读取。 最后一行代码读取镶木地板，但此时未加载任何数据。

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

2. 拉动所有这些数据会产生大约 15 亿行。 根据 Spark 池（预览）的大小，原始数据可能太大或操作时间过长。 您可以将此数据筛选为更小的数据。 如果需要，添加以下行以将数据筛选到大约 200 万行，以便获得响应更灵敏的体验。 使用这些参数提取一周的数据。

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. 简单筛选的缺点是，从统计角度来看，它可能会引入数据偏差。 另一种方法是使用 Spark 中内置的采样。 如果在上面的代码之后应用，以下代码可将数据集减少到大约 2000 行。 此采样步骤可以代替简单筛选器使用，也可以与简单筛选器结合使用。

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. 现在可以查看数据以查看已读取的内容。 通常最好使用子集而不是完整集查看数据，具体取决于数据集的大小。 以下代码提供了两种查看数据的方法：前者是基本数据，后者是基本代码，后者提供了更丰富的网格体验，以及以图形方式可视化数据的能力。

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. 根据生成的数据集大小的大小以及多次试验或运行笔记本的需要，最好在工作区中本地缓存数据集。 执行显式缓存有三种方法：

   - 将数据框本地保存为文件
   - 将数据框另存为临时表或视图
   - 将数据框另存为永久表

这些方法的前 2 个包含在以下代码示例中。

创建临时表或视图提供对数据的不同访问路径，但仅在 Spark 实例会话的持续期间持续。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>了解数据

通常，此时您将经历*一个探索性数据分析*（EDA） 阶段，以了解数据。 以下代码显示了与提示相关的三种不同的数据可视化效果，这些提示导致对数据的状态和质量下结论。

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
![框 Whisker](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![绘图散射图](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>准备数据

原始形式的数据通常不适合直接传递到模型。 必须对数据执行一系列操作，才能使其进入模型可以使用它的状态。

在下面的代码中，将执行四类操作：

- 通过筛选删除异常值/不正确的值。
- 删除不需要的列。
- 创建从原始数据派生的新列，使模型更有效地工作，有时称为"壮举"。
- 标签，因为您正在进行二进制分类（是否会在给定行程中有提示或没有），因此需要将小费金额转换为 0 或 1 值。

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

然后通过数据进行第二次传递以添加最终要素。

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

最后一项任务是将标签数据转换为逻辑回归可分析的格式。 逻辑回归算法的输入需要是一组*标签特征矢量对*，其中*特征向量*是表示输入点的数字矢量。 因此，我们需要将分类列转换为数字。 和`trafficTimeBins``weekdayString`列需要转换为整数表示形式。 执行转换的方法有多种，但本示例中采用的方法是*OneHotEncode，* 这是一种常见的方法。

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

这将导致一个新的数据框，所有列都采用正确的格式来训练模型。

## <a name="train-a-logistic-regression-model"></a>训练逻辑回归模型

第一个任务是将数据集拆分为训练集和测试或验证集。 此处的拆分是任意的，您应该使用不同的拆分设置来四处播放，以查看它们是否影响模型。

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

现在有两个 DataFrame，下一个任务是创建模型公式，并针对训练数据帧运行它，然后根据测试 DataFrame 进行验证。 您应该尝试模型公式的不同版本，以查看不同组合的影响。

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

来自此单元格的输出是

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>创建预测的直观表示形式

现在可以构造一个最终可视化效果，以帮助推理此测试的结果。 [ROC 曲线](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)是查看结果的一种方式。

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

运行完应用程序后，关闭笔记本以释放资源，关闭选项卡或从笔记本底部的状态面板中选择 **"结束会话**"。

## <a name="see-also"></a>另请参阅

- [概述：Azure 突触分析上的 Apache 火花](apache-spark-overview.md)

## <a name="next-steps"></a>后续步骤

- [阿帕奇火花文档的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [阿帕奇火花官方文件](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 某些官方的 Apache Spark 文档依赖于使用 Spark 控制台，这在 Azure 同步 Spark 上不可用。 改用[笔记本](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)或[IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)体验。
