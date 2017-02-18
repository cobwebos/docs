---
title: "为 Spark 构建的机器学习模型评分 | Microsoft Docs"
description: "如何为已在 Azure Blob 存储 (WASB) 中存储的学习模型评分。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 626305a2-0abf-4642-afb0-dad0f6bd24e9
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: deguhath;bradsev;gokuma
translationtype: Human Translation
ms.sourcegitcommit: 304323601a7fb2c9b46cf0e1eea9429cf099a111
ms.openlocfilehash: debc2a1671ed8fc2507408a887a7604ee02625f3


---
# <a name="score-spark-built-machine-learning-models"></a>为 Spark 构建的机器学习模型评分
[!INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

本主题介绍如何加载使用 Spark MLlib 生成并存储在 Azure Blob 存储 (WASB) 中的机器学习 (ML) 模型，以及如何使用同样存储在 WASB 中的数据集为它们评分。 它介绍如何预处理输入数据、使用 MLlib 工具包中的索引和编码函数转换特征，以及如何创建可用作 ML 模型评分的输入的标签点数据对象。 用于评分的模型包括线性回归、逻辑回归、随机林模型和梯度提升树模型。

## <a name="prerequisites"></a>先决条件
1. 你需要一个 Azure 帐户和一个 Spark 1.6 或 Spark 2.0 HDInsight 群集来完成本演练。 有关如何满足这些要求的说明，请参阅[在 Azure HDInsight 上使用 Spark 的数据科学的概述](machine-learning-data-science-spark-overview.md)。 该主题还包含此处使用的 NYC 2013 出租车数据的说明以及有关如何在 Spark 群集上执行来自 Jupyter 笔记本的代码的说明。 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) 中提供包含本主题中的代码示例的 **pySpark-machine-learning-data-science-spark-model-consumption.ipynb** 笔记本。
2. 还必须在此处通过演练[使用 Spark 进行数据探索和建模](machine-learning-data-science-spark-data-exploration-modeling.md)主题创建要评分的机器学习模型。   

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>设置：存储位置、库和预设 Spark 上下文
Spark 能够读取和写入 Azure 存储 Blob (WASB)。 因此，存储在该处的任何现有数据都可以使用 Spark 处理，并将结果再次存储在 WASB 中。

若要在 WASB 中保存模型或文件，需要正确指定路径。 可使用以以下内容开头的路径引用附加到 Spark 群集的默认容器：*"wasb//"*。 以下代码示例指定要读取的数据的位置和模型输出要保存到的模型存储目录的路径。 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>在 WASB 中为存储位置设置目录路径
模型保存在：“wasb:///user/remoteuser/NYCTaxi/Models”。 如果未正确设置此路径，则不加载模型用于评分。

评分结果已保存在：“wasb:///user/remoteuser/NYCTaxi/ScoredResults”。 如果文件夹路径不正确，则结果不保存在该文件夹中。   

> [!NOTE]
> 可从 **machine-learning-data-science-spark-data-exploration-modeling.ipynb** 笔记本的最后一个单元格的输出中将文件路径位置复制并粘贴到此代码中的占位符中。   
> 
> 

下面是设置目录路径的代码： 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**输出：**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>导入库
设置 Spark 上下文并使用以下代码导入必要的库

    #IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>预设 Spark 上下文和 PySpark magic
与 Jupyter 笔记本一起提供的 PySpark 内核具有预设上下文。 因此在开始处理正在开发的应用程序前无需显式设置 Spark 或 Hive 上下文。 这些上下文默认可用。 这些上下文包括：

* sc - 用于 Spark 
* sqlContext - 用于 Hive

PySpark 内核提供一些预定义的“magic”，这是可以结合 %% 调用的特殊命令。 在这些代码示例中使用的此类命令有两个。

* **%%local** 已指定后续行中的代码在本地执行。 代码必须是有效的 Python 代码。
* **%%sql -o <variable name>** 
* 针对 sqlContext 执行 Hive 查询。 如果传递了 -o 参数，则查询的结果将以 Pandas 数据帧的形式保存在 %%local Python 上下文中。

有关 Jupyter 笔记本内核和它们提供的预定义“magic”的详细信息，请参阅[适用于装有 HDInsight 上的 HDInsight Spark Linux 群集的 Jupyter 笔记本的内核](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md)。

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>引入数据并创建已清理的数据帧
本部分包含引入要评分的数据所需的一系列任务的代码。 读入出租车行程和车费文件（存储为 .tsv 文件）的已加入的 0.1% 样本，然后创建干净的数据帧。

基于以下主题中提供的过程加入了出租车行程和车费文件：[运行中的 Team Data Science Process：使用 HDInsight Hadoop 群集](machine-learning-data-science-process-hive-walkthrough.md)主题。

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出：**

执行以上单元格所花的时间：46.37 秒

## <a name="prepare-data-for-scoring-in-spark"></a>为 Spark 中的评分准备数据
本部分介绍如何为分类数据编制索引、编码和缩放，以使它们准备好在分类和回归的 MLlib 监督学习算法中使用。

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>特征转换：为分类特征编制索引并编码以输入到模型中进行评分
本部分介绍如何使用 `StringIndexer` 为分类数据编制索引，并使用 `OneHotEncoder` 为特征编码以输入到模型中。

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) 将标签的字符串列编码为标签索引列。 索引按标签频率排序。 

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 将标签索引列映射到二元向量列，该列最多只有单个值。 此编码允许将预期连续值特征的算法（如逻辑回归）应用到分类特征。

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出：**

执行以上单元格所花的时间：5.37 秒

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>使用特征数组创建 RDD 对象以输入到模型中
本部分包含的代码显示如何将分类文本数据编制索引为标签点数据类型，并对其进行独热编码，以便它可用于训练和测试 MLlib 逻辑回归和基于树的模型。 索引数据存储在[弹性分布式数据集 (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) 对象中。 这些是 Spark 中的基本抽象。 RDD 对象表示可与 Spark 并行处理的不可变、已分区的元素集合。

它还包含显示如何使用 MLlib 提供的 `StandardScalar` 缩放数据的代码，用于使用随机梯度下降 (SGD) 的线性回归，随机梯度下降是一种用于训练范围广泛的机器学习模型的流行算法。 [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) 用于将特征缩放到单位方差。 特征缩放（也称为数据规范化）确保具有广泛分散的值的特征不在目标函数中得到过多权重。 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出：**

执行以上单元格所花的时间：11.72 秒

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>使用逻辑回归模型评分并将输出保存到 blob
本部分中的代码显示如何加载已存储在 Azure Blob 存储中的逻辑回归模型，并使用它预测是否在某个出租车行程中支付小费、使用标准分类指标为其评分，然后将结果保存并绘制到 Blob 存储。 评分结果存储在 RDD 对象中。 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**输出：**

执行以上单元格所花的时间：19.22 秒

## <a name="score-a-linear-regression-model"></a>为线性回归模型评分
我们已使用 [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) 通过随机梯度下降 (SGD) 训练线性回归模型进行优化，以预测支付的小费金额。 

本部分中的代码显示如何从 Azure Blob 存储加载线性回归模型、使用缩放变量评分，然后将结果保存回 blob。

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出：**

执行以上单元格所花的时间：16.63 秒

## <a name="score-classification-and-regression-random-forest-models"></a>为分类和回归随机林模型评分
本部分中的代码显示如何加载已保存在 Azure Blob 存储中的分类和回归随机林模型、使用标准分类器和回归测量为其性能评分，然后将结果保存回 Blob 存储。

[随机林](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests)是决策树的整体。  它们组合了许多决策树以降低过度拟合的风险。 随机林可处理分类特征、扩展到多类分类设置、不需要功能缩放，并且能够捕获非线性和特征交互。 随机林是用于分类和回归的最成功的机器学习模型之一。

[spark.mllib](http://spark.apache.org/mllib/) 支持将随机林用于使用连续和分类特征的二元和多类分类以及回归。 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**输出：**

执行以上单元格所花的时间：31.07 秒

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>为分类和回归梯度提升树模型评分
本部分中的代码显示如何从 Azure Blob 存储加载分类和回归梯度提升树模型、使用标准分类器和回归测量为其性能评分，然后将结果保存回 Blob 存储。 

**spark.mllib** 支持将 GBT 用于使用连续和分类特征的二元分类和回归。 

[梯度提升树](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) 是决策树的整体。 GBT 以迭代方式训练决策树以最大程度减少损失函数。 GBT 可处理分类特征、不需要特征缩放，并且能够捕获非线性和特征交互。 它们还可以在多类分类设置中使用。

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出：**

执行以上单元格所花的时间：14.6 秒

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>从内存中清除对象并打印评分文件位置
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**输出：**

logisticRegFileLoc：LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc：LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc：RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc：RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc：GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc：GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>通过 Web 界面使用 Spark 模型
Spark 提供使用名为 Livy 的组件通过 REST 界面远程提交批处理作业或交互式查询的机制。 Livy 在 HDInsight Spark 群集上默认处于启用状态。 有关 Livy 的详细信息，请参阅：[使用 Livy 远程提交 Spark 作业](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md)。 

可使用 Livy 远程提交一个作业，该作业批处理评分存储在 Azure Blob 中的文件，然后将结果写入另一个 blob。 若要执行此操作，将 Python 脚本从  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) 上传到 Spark 群集的 blob。 可使用 **Microsoft Azure 存储资源管理器**或 **AzCopy** 将脚本复制到群集 blob。 在本例中，我们将脚本上传到了 ***wasb:///example/python/ConsumeGBNYCReg.py***。   

> [!NOTE]
> 可在与 Spark 群集相关联的存储帐户的门户上找到所需的访问密钥。 
> 
> 

上传到此位置后，此脚本在分布式上下文中在 Spark 内运行。 它加载模型，并基于模型对输入文件运行预测。  

可通过在 Livy 上发出简单 HTTPS/REST 请求来远程调用此脚本。  下面是用于构造远程调用 Python 脚本的 HTTP 请求的 curl 命令。 将 CLUSTERLOGIN、CLUSTERPASSWORD、CLUSTERNAME 替换为 Spark 群集的相应值。

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

通过使用基本身份验证发出简单的 HTTPS 调用，可通过 Livy 在远程系统上使用任意语言调用 Spark 作业。   

> [!NOTE]
> 进行此 HTTP 调用时使用 Python 请求库将会很方便，但它当前未默认安装在 Azure Functions 中。 因此改为使用较早的 HTTP 库。   
> 
> 

下面是 HTTP 调用的 Python 代码：

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


还可以将此 Python 代码添加到 [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) 以触发一个 Spark 作业提交，该作业提交基于各种事件（如计时器、创建或 blob 更新）为 blob 评分。 

如果首选无代码客户端体验，请使用 [Azure 逻辑应用](https://azure.microsoft.com/documentation/services/app-service/logic/)通过在**逻辑应用设计器**上定义一个 HTTP 操作并设置其参数来调用 Spark 批处理评分。 

* 从 Azure 门户，通过依次选择“+新建” -> “Web + 移动” -> “逻辑应用”创建新的逻辑应用。 
* 若要弹出“逻辑应用设计器”，请输入逻辑应用名称和应用服务计划。
* 选择某个 HTTP 操作并输入下图中显示的参数：

![逻辑应用设计器](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>后续步骤
**交叉验证和超参数扫描**：请参阅[使用 Spark 进行高级数据探索和建模](machine-learning-data-science-spark-advanced-data-exploration-modeling.md)了解如何使用交叉验证和超参数扫描训练模型。




<!--HONumber=Feb17_HO2-->


