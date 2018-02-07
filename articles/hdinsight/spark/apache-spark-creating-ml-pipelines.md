---
title: "创建 Apache Spark 机器学习管道 - Azure HDInsight | Microsoft Docs"
description: "使用 Apache Spark 机器学习库创建数据管道。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: maxluk
ms.openlocfilehash: 238ab5f940fbea836b75e20b015ae16f22eef3e9
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-spark-machine-learning-pipeline"></a>创建 Spark 机器学习管道

Apache Spark 的可缩放机器学习库 (MLlib) 向分布式环境引入了建模功能。 Spark 包 [`spark.ml`](http://spark.apache.org/docs/latest/ml-pipeline.html) 是一套基于数据帧的高级 API。 借助这些 API，可创建和调整实际的机器学习管道。  Spark 机器学习引用此基于 MLlib 数据帧的 API，而不是旧的基于 RDD 的管道 API。

机器学习 (ML) 管道是结合了多种机器学习算法的完整工作流。 处理和了解数据需要许多步骤，需要一系列算法。 管道定义机器学习过程的阶段和顺序。 在 MLlib 中，管道的阶段由特定的 PipelineStages 序列表示，其中转换器和估算器各自执行任务。

转换器是一种算法，它通过使用 `transform()` 方法将一个数据帧转换为另一个数据帧。 例如，功能转换器可读取数据帧的一个列，将其映射到另一个列，并输出追加有映射列的新数据帧。

估算器是一种抽象的学习算法，负责调整或培训数据集，以生成转换器。 估算器实现一种名为 `fit()` 的方法，该方法接受数据帧并生成数据帧，即为转换器。

转换器或估算器的每个无状态实例都具有其自己的、指定参数时使用的唯一标识符。 两者都使用统一的 API 来指定这些参数。

## <a name="pipeline-example"></a>管道示例

为了演示 ML 管道的实际用途，此示例使用预加载在 HDInsight 群集默认存储（Azure 存储或 Data Lake Store）上的示例 `HVAC.csv` 数据文件。 若要查看文件的内容，导航到 `/HdiSamples/HdiSamples/SensorSampleData/hvac` 目录。 `HVAC.csv` 包含一组时间和各种建筑物中 HVAC（供暖、通风和空调）系统的目标温度和实际温度。 其目标是使用数据来训练模型，并生成给定建筑物的预测温度。

以下代码：

1. 定义 `LabeledDocument`，用于存储 `BuildingID`、`SystemInfo`（系统的标识符和年龄），以及 `label`（如果建筑物温度过高，则为 1.0，否则为 0.0）。
2. 创建自定义分析器函数 `parseDocument`，该函数接收一行数据，并通过比较目标温度与实际温度确定建筑物是否为“高温”。
3. 提取源数据时应用分析器。
4. 创建培训数据。

```python
# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0        

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)

# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

此示例管道包含三个阶段：`Tokenizer`、`HashingTF`（两者均为转换器）和 `Logistic Regression`（估算器）。  `training` 数据帧中提取和分析的数据在调用 `pipeline.fit(training)` 时将流经管道。

1. 第一个阶段 `Tokenizer` 将 `SystemInfo` 输入列（由系统标识符和年龄值组成）拆分为 `words` 输出列。 此新 `words` 列被添加到数据帧。 
2. 第二个阶段 `HashingTF` 将新 `words` 列转换为功能矢量。 此新 `features` 列被添加到数据帧。 这前两个阶段为转换器。 
3. 第三个阶段 `LogisticRegression` 为估算器，因此，管道调用 `LogisticRegression.fit()` 方法以生成 `LogisticRegressionModel`。 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

若要查看由 `Tokenizer` 和 `HashingTF` 转换器添加的新的 `words` 和 `features` 列以及 `LogisticRegression` 估算器示例，在原始数据帧上运行 `PipelineModel.transform()` 方法。 在生产代码中，下一步为传入测试数据帧，以验证培训。

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

现在可以使用 `model` 对象来进行预测。 有关此机器学习应用程序的完整示例以及运行此应用程序的分步说明，请参阅[在 Azure HDInsight 上生成 Apache Spark 机器学习应用程序](apache-spark-ipython-notebook-machine-learning.md)。

## <a name="see-also"></a>另请参阅

* [在 Azure 上使用 Scala 和 Spark 展开数据科研](../../machine-learning/team-data-science-process/scala-walkthrough.md)
