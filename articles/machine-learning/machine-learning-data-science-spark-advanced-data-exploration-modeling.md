---
title: "使用 Spark 进行高级数据探索和建模 | Microsoft Docs"
description: "使用 HDInsight Spark 进行数据探索，并使用交叉验证和超参数优化训练二元分类和回归模型。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: f90d9a80-4eaf-437b-a914-23514390cd60
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: deguhath;bradsev;gokuma
translationtype: Human Translation
ms.sourcegitcommit: 304323601a7fb2c9b46cf0e1eea9429cf099a111
ms.openlocfilehash: 853f3c9b2a4781b683b03b5c7ec93f0197c78cdc


---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>使用 Spark 进行高级数据探索和建模
[!INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

此演练对 NYC 出租车行程和车费 2013 数据的样本使用 HDInsight Spark 进行数据探索，并使用交叉验证和超参数优化训练二元分类和回归模型。 它端到端演练[数据科学过程](http://aka.ms/datascienceprocess)的步骤，使用 HDInsight Spark 群集进行处理并使用 Azure Blob 存储数据和模型。 此过程探索并可视化从 Azure 存储 Blob 引入的数据，然后使数据为生成预测模型做好准备。 已使用 Python 编写解决方案并显示相关绘图。 这些模型使用 Spark MLlib 工具包生成，用于执行二元分类和回归建模任务。 

* **二元分类**任务用于预测某个行程是否会支付小费。 
* **回归**任务用于根据其他小费特征预测小费的金额。 

建模步骤还包含显示如何训练、评估和保存每个模型类型的代码。 此主题涵盖某些与[使用 Spark 进行数据探索和建模](machine-learning-data-science-spark-data-exploration-modeling.md)主题相同的基础。 但是它更“高级”，因为它还将交叉验证与超参数扫描结合使用以训练最准确的分类和回归模型。 

**交叉验证 (CV)** 是评估在已知数据集上训练的模型对预测未经训练的数据集特征的一般化程度的技术。  此处使用的常见实现是将数据集划分为 K 折，然后以轮询机制方式在除一折以外的所有折上训练模型。 模型的准确预测能力在针对此不用来训练模型的折叠中的独立数据集进行测试时受到评估。

**超参数优化**是为学习算法选择一组超参数的问题，通常目标是优化算法在独立数据集上的性能度量值。 **超参数**是必须在模型训练过程之外指定的值。 关于这些值的假设可能影响模型的灵活性和准确性。 例如，决策树具有超参数，如所需的深度和树中的树叶数量。 支持向量机 (SVM) 需要设置错误分类惩罚项。 

执行此处使用的超参数优化的常用方法是网格搜索或**参数扫描**。 这包括为学习算法在这些值（超参数空间的指定子集）中执行详尽搜索。 交叉验证可提供性能指标，用于为网格搜索算法生成的最佳结果排序。 与超参数扫描一起使用的 CV 有助于解决极限问题（如使模型过渡拟合训练数据），从而使模型保留适用于一般数据集（从中提取了训练数据）的能力。

我们使用的模型包括逻辑和线性回归、随机林和梯度提升树：

* [使用 SGD 的线性回归](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD)是使用随机梯度下降 (SGD) 方法进行优化和特征缩放的线性回归模型，以预测支付的小费金额。 
* [使用 LBFGS 的逻辑回归](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS)或“logit”回归，是可在因变量分类时用于执行数据分类的回归模型。 LBFGS 是拟牛顿优化算法，近似于使用有限计算机内存量的 Broyden–Fletcher–Goldfarb–Shanno (BFGS) 算法，在机器学习中广泛使用。
* [随机林](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests)是决策树的整体。  它们组合了许多决策树以降低过度拟合的风险。 随机林用于回归和分类，并且可处理分类特征，也可扩展到多类分类设置。 它们不需要特征缩放，并且能够捕获非线性和特征交互。 随机林是用于分类和回归的最成功的机器学习模型之一。
* [梯度提升树](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) 是决策树的整体。 GBT 以迭代方式训练决策树以最大程度减少损失函数。 GBT 用于回归和分类，并且可处理分类特征，不需要功能缩放，并且能够捕获非线性和特征交互。 它们还可以在多类分类设置中使用。

为二元分类问题显示了使用 CV 和超参数扫描的建模示例。 回归任务的主要主题中显示了更简单的示例（不使用参数扫描）。 但是在附录中，还显示了为线性回归使用弹性网络的验证和为随机林回归使用参数扫描的 CV。 **弹性网络**是用于拟合线性回归模型的正则化回归方法，该方法将 L1 和 L2 指标组合起来，作为 [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) 和 [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) 方法的惩罚。   

> [!NOTE]
> 尽管 Spark MLlib 工具包设计用于处理大型数据集，但为方便起见，此处使用相对较小的样本（约 30 Mb，使用 170K 行，大约原始 NYC 数据集的 0.1%）。 此处提供的练习在带有 2 个辅助节点的 HDInsight 群集中高效运行（在大约 10 分钟内）。 相同的代码（带有少量修改）可用于处理更大的数据集，前提是进行相应的修改以在内存中缓存数据和更改群集大小。
> 
> 

## <a name="prerequisites"></a>先决条件
你需要一个 Azure 帐户和一个 Spark 1.6 或 Spark 2.0 HDInsight 群集来完成本演练。 有关如何满足这些要求的说明，请参阅[在 Azure HDInsight 上使用 Spark 的数据科学的概述](machine-learning-data-science-spark-overview.md)。 该主题还包含此处使用的 NYC 2013 出租车数据的说明以及有关如何在 Spark 群集上执行来自 Jupyter 笔记本的代码的说明。 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) 中提供包含本主题中的代码示例的 **pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** 笔记本。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>设置：存储位置、库和预设 Spark 上下文
Spark 能够读取和写入 Azure 存储 Blob（也称为 WASB）。 因此，存储在该处的任何现有数据都可以使用 Spark 处理，并将结果再次存储在 WASB 中。

若要在 WASB 中保存模型或文件，需要正确指定路径。 可使用开头为以下内容的路径，引用附加到 Spark 群集的默认容器：“wasb:///”。 其他位置通过“wasb://”引用。

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>在 WASB 中为存储位置设置目录路径
以下代码示例指定要读取的数据位置，以及用于保存模型输出的模型存储目录的路径：

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**输出**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>导入库
使用以下代码导入必要的库：

    # LOAD PYSPARK LIBRARIES
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

* **%%local** 指定本地执行后续行中的代码。 代码必须是有效的 Python 代码。
* **%%sql -o <variable name>** 针对 sqlContext 执行 Hive 查询。 如果传递了 -o 参数，则查询的结果将以 Pandas 数据帧的形式保存在 %%local Python 上下文中。

有关 Jupyter 笔记本内核和它们提供的预定义“magic”的详细信息，请参阅[适用于装有 HDInsight 上的 HDInsight Spark Linux 群集的 Jupyter 笔记本的内核](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md)。

## <a name="data-ingestion-from-public-blob"></a>来自公共 blob 的数据引入：
数据科学过程的第一步是从源引入数据以供分析，使其驻留在数据探索和建模环境中。 此环境在本演练中为 Spark。 本部分包含要完成一系列任务的代码：

* 引入数据样本以供建模
* 读取输入数据集（以 .tsv 文件的形式存储）
* 格式化并清理数据
* 在内存中创建并缓存对象（RDD 或数据帧）
* 在 SQL 上下文中将其注册为临时表。

下面是用于数据引入的代码。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
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

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

执行以上单元格所花的时间：276.62 秒

## <a name="data-exploration--visualization"></a>数据探索和可视化
将数据引入到 Spark 中后，数据科学过程的下一步是通过探索和可视化更深入地了解数据。 在本部分中，我们使用 SQL 查询检查出租车数据，并绘制目标变量和预期特征以供视觉检查。 具体而言，我们绘制出租车行程中乘客数的频率、小费金额的频率以及小费如何随支付金额和类型而变化。

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>绘制出租车小费样本中乘客数频率的直方图
此代码和后续代码段使用 SQL magic 查询样本，使用本地 magic 绘制数据。

* **SQL magic (`%%sql`)** HDInsight PySpark 内核支持针对 sqlContext 的轻松内联 HiveQL 查询。 (-o VARIABLE_NAME) 参数在 Jupyter 服务器上将 SQL 查询的输出保留为 Pandas 数据帧。 这意味着它在本地模式下可用。
* **`%%local` magic** 用于在 Jupyter 服务器上本地运行代码，该服务器是 HDInsight 群集的头节点。 通常，在将 `%%sql -o` magic 用于运行查询后，使用 `%%local` magic。 -o 参数会在本地保留 SQL 查询的输出。 然后，`%%local` magic 触发下一组代码片段在本地针对已保留在本地的 SQL 查询输出运行。 该输出在运行代码后自动可视化。

此查询通过乘客数检索行程。 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


此代码从查询输出创建本地数据帧，并绘制数据。 `%%local` magic 创建本地数据帧 `sqlResults`，可用于使用 matplotlib 进行绘制。 

> [!NOTE]
> 此 PySpark magic 在本演练中多次使用。 如果数据量很大，应采样以创建本地内存可容纳的数据帧。
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

下面是用于按乘客数绘制行程的代码

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**输出**

![按乘客数的行程频率](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

可通过使用笔记本中的“类型”菜单按钮从多个不同类型的可视化（表、饼图、行、区域或栏）中选择。 此处显示了条形图。

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>绘制小费金额以及小费金额如何随乘客数和车费金额变化的直方图。
使用 SQL 查询为数据采样。

    # SQL SQUERY
    %%sql -q -o sqlResults
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train 
        WHERE passenger_count > 0 
        AND passenger_count < 7
        AND fare_amount > 0 
        AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 
        AND tip_amount < 25


此代码单元格使用 SQL 查询创建三个数据图。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**输出：** 

![小费金额分布](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![按乘客数的小费金额](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![按车费金额的小费金额](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>用于建模的特征工程、转换和数据准备
本部分介绍并提供使数据准备好在 ML 建模中使用的过程的代码。 它介绍如何执行以下任务：

* 通过将小时划分到交通时间箱来创建新特征
* 为分类特征编制索引并进行独热编码
* 创建标签点对象用于输入到 ML 函数中
* 创建数据的随机子采样，并将其拆分为训练集和测试集
* 特征缩放
* 在内存中缓存对象

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>通过将交通时间划分到箱来创建新特征
此代码显示如何通过将交通时间划分到箱来创建新特征，以及如何在内存中缓存生成的数据帧。 当重复使用弹性分布式数据集 (RDD) 和数据帧时，缓存导致执行时间改善。 因此，我们在本演练中的多个阶段缓存 RDD 和数据帧。

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**输出**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>为分类特征编制索引并进行独热编码
本部分介绍如何为分类特征编制索引或进行编码以输入到建模函数中。 MLlib 的建模和预测函数需要在使用前，对带有分类输入数据的特征进行索引或编码。 

根据模型，需要以不同方式为它们编制索引或进行独热编码。 例如，逻辑和线性回归模型需要独热编码，举例来说，具有三个类别的特征可扩展为三个特征列，每个根据观察的类别包含 0 或 1。 MLlib 提供 [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 函数用于执行独热编码。 此编码器将标签索引列映射到二元向量列，该列最多只有单个值。 此编码允许将预期数值特征的算法（如逻辑回归）应用到分类特征。

下面是用于为分类特征编制索引和编码的代码：

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
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

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

执行以上单元格所花的时间：3.14 秒

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>创建标签点对象用于输入到 ML 函数中
本节包含的代码演示如何将分类文本数据索引为标签点数据类型以及如何对其进行编码。 这将准备将其用于训练和测试 MLlib 逻辑回归和其他分类模型。 标签点对象是弹性分布式数据集 (RDD)，格式化为 MLlib 中的大多数 ML 算法所需的输入数据。 [标签点](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)是本地向量，可能密集，也可能稀疏，与标签/响应相关联。

下面是用于针对二元分类为文本特征编制索引和编码的代码。

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


下面是用于针对线性回归分析为分类文本特征编码和编制索引的代码。

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>创建数据的随机子采样，并将其拆分为训练集和测试集
此代码创建数据的随机采样（此处使用&25;%）。 尽管由于数据集的大小，本示例不需要此操作，但我们将演示如何在此处对数据采样。 然后，你就会知道如何在需要时针对自己的问题使用它。 当样本很大时，这可以在训练模型时节省大量时间。 接下来我们将样本拆分为训练部分（此处为&75;%）和测试部分（此处为&25;%），用于分类和回归建模。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出**

执行以上单元格所花的时间：0.31 秒

### <a name="feature-scaling"></a>特征缩放
特征缩放（也称为数据规范化）确保具有广泛分散的值的特征不在目标函数中得到过多权重。 用于特征缩放的代码使用 [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) 将特征缩放为单位方差。 它由 MLlib 提供，用于使用随机梯度下降 (SGD) 的线性回归。 SGD 是一种用于训练范围广泛的其他机器学习模型（如正则化回归或支持向量机 (SVM)）的流行算法。   

> [!TIP]
> 我们发现，LinearRegressionWithSGD 算法对特征缩放很敏感。   
> 
> 

下面是用于缩放变量以用于正则化线性 SGD 算法的代码。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出**

执行以上单元格所花的时间：11.67 秒

### <a name="cache-objects-in-memory"></a>在内存中缓存对象
可通过缓存用于分类、回归和缩放特征的输入数据帧对象来缩短训练和测试 ML 算法所花的时间。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出** 

执行以上单元格所花的时间：0.13 秒

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>通过二元分类模型预测是否支付小费
本部分介绍如何将二元分类任务的三个模型用于预测是否为某个出租车行程支付小费。 显示的模型包括：

* 逻辑回归 
* 随机林
* 梯度提升树

每个模型生成代码部分拆分为多个步骤： 

1. 带有一个参数集的**模型训练**数据
2. 测试数据集上的使用指标的**模型评估**
3. 在 blob 中**保存模型**以供将来使用

我们介绍如何通过两种方法使用参数扫描进行交叉验证 (CV)：

1. 使用**泛型**自定义代码，此代码可应用到 MLlib 中的任何算法以及算法中的任何参数集。 
2. 使用 **pySpark CrossValidator 管道函数**。 请注意，CrossValidator 对于 Spark 1.5.0 有几个限制： 
   
   * 无法保存/保留管道模型以供将来使用。
   * 无法用于模型中的每个参数。
   * 无法用于每个 MLlib 算法。

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>与二元分类的逻辑回归算法一起使用的泛型交叉验证和超参数扫描
本部分中的代码显示如何使用 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) 训练、评估和保存逻辑回归模型，该模型在 NYC 出租车行程和车费数据集中预测某个行程是否支付小费。 使用交叉验证 (CV) 和通过自定义代码（可应用到 MLlib 中的任何学习算法）实现的超参数扫描训练模型。   

> [!NOTE]
> 此自定义 CV 代码的执行可能需要几分钟。
> 
> 

**使用 CV 和超参数扫描训练逻辑回归模型**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

系数：[0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

截距：-0.0111216486893

执行以上单元格所花的时间：14.43 秒

**通过标准指标评估二元分类模型**

本部分中的代码显示如何针对测试数据集评估逻辑回归模型，包括 ROC 曲线图。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

PR = 0.985336538462 下的面积

ROC = 0.983383274312 下的面积

摘要统计信息

精度 = 0.984174341679

召回率 = 0.984174341679

F1 分数 = 0.984174341679

执行以上单元格所花的时间：2.67 秒

**绘制 ROC 曲线。**

在上一个单元格中，*predictionAndLabelsDF* 注册为表 *tmp_results*。 *tmp_results* 可用于执行查询并将结果输出到 sqlResults 数据帧中用于绘图。 代码如下。

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


下面是进行预测并绘制 ROC 曲线的代码。

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**输出**

![泛型方法的逻辑回归 ROC 曲线](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**在 blob 中保留模型以供将来使用**

本部分中的代码显示如何保存逻辑回归模型以供使用。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**输出**

执行以上单元格所花的时间：34.57 秒

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>将 MLlib 的 CrossValidator 管道函数与逻辑回归（弹性回归）模型一起使用
本部分中的代码显示如何使用 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) 训练、评估和保存逻辑回归模型，该模型在 NYC 出租车行程和车费数据集中预测某个行程是否支付小费。 使用交叉验证 (CV) 和通过 MLlib CrossValidator 管道函数（用于使用参数扫描的 CV）实现的超参数扫描训练模型。   

> [!NOTE]
> 此 MLlib CV 代码的执行可能需要几分钟。
> 
> 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**输出**

执行以上单元格所花的时间：107.98 秒

**绘制 ROC 曲线。**

在上一个单元格中，*predictionAndLabelsDF* 注册为表 *tmp_results*。 *tmp_results* 可用于执行查询并将结果输出到 sqlResults 数据帧中用于绘图。 代码如下。

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

下面是用于绘制 ROC 曲线的代码。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    #PLOT
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**输出**

![使用 MLlib 的 CrossValidator 的逻辑回归 ROC 曲线](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>随机林分类
本部分中的代码显示如何训练、评估和保存随机林回归，该模型在 NYC 出租车行程和车费数据集中预测某个行程是否支付小费。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

ROC = 0.985336538462 下的面积

执行以上单元格所花的时间：26.72 秒

### <a name="gradient-boosting-trees-classification"></a>梯度提升树分类
本部分中的代码显示如何训练、评估和保存渐变提升树模型，该模型在 NYC 出租车行程和车费数据集中预测某个行程是否支付小费。

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出**

ROC = 0.985336538462 下的面积

执行以上单元格所花的时间：28.13 秒

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>通过回归模型预测小费金额（不使用 CV）
本部分介绍如何针对以下回归任务使用三个模型：基于其他小费特征预测为某个出租车行程支付的小费金额。 显示的模型包括：

* 正则化线性回归
* 随机林
* 梯度提升树

简介中介绍了这些模型。 每个模型生成代码部分拆分为多个步骤： 

1. 带有一个参数集的**模型训练**数据
2. 测试数据集上的使用指标的**模型评估**
3. 在 blob 中**保存模型**以供将来使用   

> AZURE 备注：交叉验证在本部分中不与三个回归模型一起使用，因为已针对逻辑回归模型详细显示此操作。 本主题的附录中提供了显示如何将 CV 和弹性网络一起用于线性回归的示例。
> 
> AZURE 备注：在我们的经验中，LinearRegressionWithSGD 模型的收敛可能出现问题，需要仔细更改/优化参数以获取有效的模型。 变量的缩放对收敛帮助很大。 还可使用本主题的附录中显示的弹性网络回归代替 LinearRegressionWithSGD。
> 
> 

### <a name="linear-regression-with-sgd"></a>使用 SGD 的线性回归
本部分中的代码显示如何使用缩放特征训练使用随机梯度下降线性回归 (SGD) 进行优化的线性回归，以及如何在 Azure Blob 存储 (WASB) 中评分、评估和保存模型。

> [!TIP]
> 根据我们的经验，LinearRegressionWithSGD 模型的收敛可能出现问题，需要仔细更改/优化参数以获取有效的模型。 变量的缩放对收敛帮助很大。
> 
> 

    # LINEAR REGRESSION WITH SGD 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出**

系数：[0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

截距：0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

执行以上单元格所花的时间：38.62 秒

### <a name="random-forest-regression"></a>随机林回归
本部分中的代码显示如何训练、评估和保存随机林模型，该模型预测 NYC 出租车行程数据的小费金额。   

> [!NOTE]
> 附录中提供通过使用自定义代码的超参数扫描的交叉验证。
> 
> 

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**输出**

RMSE = 0.931981967875

R-sqr = 0.733445485802

执行以上单元格所花的时间：25.98 秒

### <a name="gradient-boosting-trees-regression"></a>梯度提升树回归
本部分中的代码显示如何训练、评估和保存梯度提升树模型，该模型预测 NYC 出租车行程数据的小费金额。

**训练和评估**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

RMSE = 0.928172197114

R-sqr = 0.732680354389

执行以上单元格所花的时间：20.9 秒

**图**

*tmp_results* 在上一个单元格中注册为 Hive 表。 表中的结果输出到 *sqlResults* 数据帧中用于绘图。 代码如下

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


下面是用于使用 Jupyter 服务器绘制数据的代码。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>附录：使用交叉验证和参数扫描的其他回归任务
本附录包含的代码显示如何为线性回归使用弹性网络执行 CV 以及如何为随机林回归使用自定义代码执行使用参数扫描的 CV。

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>用于线性回归的使用弹性网络的交叉验证
本部分中的代码显示如何为线性回归使用弹性网络执行交叉验证以及如何参照测试数据评估模型。

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

执行以上单元格所花的时间：161.21 秒

**使用 R-SQR 指标评估**

*tmp_results* 在上一个单元格中注册为 Hive 表。 表中的结果输出到 *sqlResults* 数据帧中用于绘图。 代码如下

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


下面是用于计算 R-sqr 的代码。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**输出**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>用于随机林回归的通过使用自定义代码的参数扫描的交叉验证
本部分中的代码显示如何为线性回归通过使用自定义代码的参数扫描执行交叉验证以及如何参照测试数据评估模型。

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**输出**

RMSE = 0.906972198262

R-sqr = 0.740751197012

执行以上单元格所花的时间：69.17 秒

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>从内存中清除对象并打印模型位置
使用 `unpersist()` 删除内存中缓存的对象。

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


**输出**

PythonRDD[122] at RDD at PythonRDD.scala: 43

**打印输出要在使用笔记本中使用的模型文件的路径。** 若要使用和评分独立数据集，需要在“使用笔记本”中复制并粘贴这些文件名。

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**输出**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>后续步骤
现在已使用 Spark MlLib 创建了回归和分类模型，可了解如何评分和评估这些模型。

**模型使用：**若要了解如何评分和评估在本主题中创建的分类和回归模型，请参阅[评分和评估 Spark 构建的机器学习模型](machine-learning-data-science-spark-model-consumption.md)。




<!--HONumber=Feb17_HO2-->


