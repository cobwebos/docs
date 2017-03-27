---
title: "在 Azure 上使用 Scala 和 Spark 展开数据科学 | Microsoft Docs"
description: "如何在 Azure HDInsight Spark 群集上通过 Spark 可缩放 MLlib 和 Spark ML 包使用 Scala 进行监管式的机器学习任务。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bradsev;deguhath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 99f12dc1ea65d4b9be3249ea5d5c7452f5a8d72e


---
# <a name="data-science-using-scala-and-spark-on-azure"></a>在 Azure 上使用 Scala 和 Spark 展开数据科研
本文介绍如何在 Azure HDInsight Spark 群集上通过 Spark 可缩放 MLlib 和 Spark ML 包使用 Scala 进行监管式的机器学习任务。 它将指导完成[数据科学过程](http://aka.ms/datascienceprocess)所需的任务：数据引入和浏览、可视化、特征工程、建模和模型使用。 本文中的模型包括逻辑和线性回归、随机林和梯度提升树 (GBT)，以及两个常见的监管式机器学习任务：

* 回归问题：预测某个出租车行程的小费金额 ($)
* 二元分类：预测某个出租车行程是否支付小费 (1/0)

建模过程需要对测试数据集和相关准确性度量值进行定型和评估。 在本文中，可以了解如何在 Azure Blob 存储中存储这些模型，以及如何对其预测性能进行评分和评估。 本文还介绍了如何通过使用交叉验证和超参数扫描优化模型的更高级主题。 所使用的数据是 GitHub 上提供的 2013 年 NYC 出租车行程和费用数据集样本。

[Scala](http://www.scala-lang.org/) 是一种基于 Java 虚拟机的语言，其集成面向对象和功能语言概念。 这是一种可扩展的语言，非常适合云中的分布式处理，且在 Azure Spark 群集上运行。

[Spark](http://spark.apache.org/) 是一种开放源代码并行处理框架，支持内存中处理，以提升大数据分析应用程序的性能。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中分布式计算功能使其成为机器学习和图形计算中的迭代算法的最佳选择。 [Spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) 包提供了一组统一的高级 API，它们构建在数据帧之上，可以帮助创建和优化实际机器学习管道。 [MLlib](http://spark.apache.org/mllib/) 是 Spark 的可扩展机器学习库，为此分布式环境提供建模功能。

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) 是 Azure 托管的开放源代码 Spark 产品。 它还包括对 Spark 群集上 Jupyter Scala 笔记本的支持，并且可运行 Spark SQL 交互式查询以便对存储在 Azure Blob 存储中的数据进行转换、筛选和可视化。 本文中的 Scala 代码片段提供解决方案，并显示相关图表，以可视化安装在 Spark 群集上的 Jupyter 笔记本中运行的数据。 这些主题中的建模步骤具有代码，显示每种类型模型的定型、评估、保存和使用方式。

本文中的设置步骤和代码适用于 Azure HDInsight 3.4 Spark 1.6。 但是，本文和 [Scala Jupyter Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb)中的代码是通用的，应该可以在任何 Spark 群集上使用。 如果不使用 HDInsight Spark，群集设置和管理步骤可能与本文中显示的稍有不同。

> [!NOTE]
> 有关演示如何使用 Python 而非 Scala 完成端到端数据科学过程任务的主题，请参阅[在 Azure HDInsight 上使用 Spark 展开数据科学](machine-learning-data-science-spark-overview.md)。
> 
> 

## <a name="prerequisites"></a>先决条件
* 必须拥有 Azure 订阅。 如果还没有 Azure 订阅，请[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* 需要 Azure HDInsight 3.4 Spark 1.6 群集来完成以下过程。 若要创建群集，请参阅[入门：在 Azure HDInsight 上创建 Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)中的说明。 在“选择群集类型”菜单上设置群集类型和版本。

![HDInsight 群集类型配置](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

有关 NYC 出租车行程数据说明以及如何从 Spark 群集上的 Jupyter notebook 执行代码的说明，请参阅[在 Azure HDInsight 上使用 Spark 展开数据科学的概述](machine-learning-data-science-spark-overview.md)中的相关部分。  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>从 Spark 群集上的 Jupyter notebook 执行 Scala 代码
可以从 Azure 门户启动 Jupyter notebook。 在仪表板上找到 Spark 群集，然后单击进入群集管理页面。 接下来，单击“群集仪表板”，再单击“Jupyter Notebook”打开与 Spark 群集关联的笔记本。

![群集仪表板和 Jupyter notebook](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

还可以在 https://&lt;clustername&gt;.azurehdinsight.net/jupyter 访问 Jupyter notebook。 将 *clustername* 替换为群集名称。 需要使用管理员帐户密码访问 Jupyter notebook。

![使用群集名称可转到 Jupyter notebook](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

选择“Scala”，查看包含数个使用 PySpark API 的预打包笔记本示例的目录。 浏览建模和评分可在 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala) 上获得，它使用 Scala.ipynb 笔记本，笔记本中包含此套 Spark 主题的代码示例。

可以将笔记本直接从 GitHub 上传到 Spark 群集上的 Jupyter Notebook 服务器。 在 Jupyter 主页上，单击“上传”按钮。 在文件资源管理器中，粘贴 Scala notebook 的 GitHub（原始内容）URL，然后单击“打开”。 可通过以下 URL 获取 Scala notebook:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>设置：预设 Spark 和 Hive 上下文、Spark magic 和 Spark 库
### <a name="preset-spark-and-hive-contexts"></a>预设的 Spark 和 Hive 上下文
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


与 Jupyter notebook 一起提供的 Spark 内核具有预设上下文。 在开始处理正在开发的应用程序前，无需显式设置 Spark 或 Hive 上下文。 预设上下文是：

* SparkContext 的 `sc`
* HiveContext 的 `sqlContext`

### <a name="spark-magics"></a>Spark magic
Spark 内核提供一些预定义的“magic”，这是可以结合 `%%` 调用的特殊命令。 以下代码示例中使用了其中两个命令。

* `%%local` 后续行中的代码将在本地执行。 代码必须是有效的 Scala 代码。
* `%%sql -o <variable name>` 针对 `sqlContext` 执行 Hive 查询。 如果传递了 `-o` 参数，则查询的结果将以 Spark 数据帧的形式保存在 `%%local` Scala 上下文中。

若要详细了解 Jupyter notebook 内核及调用的其 `%%`（如 `%%local`）预定义“magic”，请参阅[适用于 HDInsight 上具有 HDInsight Spark Linux 群集的 Jupyter notebook 的内核](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md)。

### <a name="import-libraries"></a>导入库
使用以下代码导入 Spark、MLlib 和其他需要的库。

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>数据引入
数据科学过程的第一步是引入要分析的数据。 将数据从外部源或其所在的系统引入数据浏览和建模环境中。 本文中，引入的数据是 0.1％ 出租车行程和费用文件（以 .tsv 文件的形式存储）示例的结合。 数据浏览和建模环境是 Spark。 本部分包含用于完成以下一系列任务的代码：

1. 设置数据和模型存储的目录路径。
2. 读取输入数据集（以 .tsv 文件的形式存储）。
3. 定义数据架构，并清理数据。
4. 创建清理数据帧并将其缓存在内存中。
5. 将数据注册为 SQLContext 中的临时表。
6. 查询表，并将结果导入数据帧。

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>在 Azure Blob 存储中为存储位置设置目录路径
Spark 可以读取和写入到 Azure Blob 存储。 可以使用 Spark 处理任何现有数据，然后将结果再次存储在 Blob 存储中。

若要在 Blob 存储中保存模型或文件，需要指定正确路径。 可使用以 `wasb:///` 开头的路径引用附加到 Spark 群集的默认容器。 使用 `wasb://` 引用其他位置。

以下代码示例指定要读取的输入数据位置以及到 Blob 存储的路径，该存储附加到将要保存模型的 Spark 群集。

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>导入数据，创建 RDD，并根据架构定义数据帧
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**输出：**

运行该单元格的时间：8 秒。

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>查询表，并将结果导入数据帧
接下来，查询表格中的费用、乘客和小费数据；筛选出期限外和范围外的数据；并打印数行。

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**输出：**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>数据浏览和可视化
将数据引入到 Spark 中后，数据科学过程的下一步是通过浏览和可视化更深入地了解数据。 在此部分中，可以使用 SQL 查询检查出租车数据。 然后，将结果导入到数据帧中，使用 Jupyter 的自动可视化功能绘制目标变量和预期功能，以进行目测。

### <a name="use-local-and-sql-magic-to-plot-data"></a>使用本地和 SQL magic 绘制数据
默认情况下，从 Jupyter notebook 运行的任何代码片段的输出，在保留于辅助角色节点上的会话上下文中可用。 如果要将行程保存到辅助角色节点，以便每次计算，并且计算所需的所有数据都可以在 Jupyter 服务器节点（这是头节点）上本地可用，则可以使用 `%%local` magic 在 Jupyter 服务器上运行代码片段。

* **SQL magic** (`%%sql`). HDInsight Spark 内核支持针对 SQLContext 的简单内联 HiveQL 查询。 （`-o VARIABLE_NAME`）参数在 Jupyter 服务器上将 SQL 查询的输出保留为 Pandas 数据帧。 这意味着它在本地模式下可用。
* `%%local` **magic**。 `%%local`magic 在 Jupyter 服务器上本地运行代码，该服务器是 HDInsight 群集的头节点。 通常，将 `%%local` magic 与 `%%sql` magic 和 `-o` 参数结合使用。 `-o` 参数将本地保留 SQL 查询的输出，然后 `%%local` 将触发下一组代码片段，针对本地保留的 SQL 查询输出本地运行。

### <a name="query-the-data-by-using-sql"></a>使用 SQL 查询数据
此查询按费用金额、乘客数量和小费金额检索出租车行程。

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

在以下代码中，`%%local` magic 创建本地数据帧 sqlResults。 可以使用 sqlResults 通过 matplotlib 进行绘图。

> [!TIP]
> 本文中多次使用本地 magic。 如果数据集较大，请采样创建本地内存可容纳的数据帧。
> 
> 

### <a name="plot-the-data"></a>绘制数据
可以在数据帧处于本地上下文之后，使用 Python 代码作为 Pandas 数据帧进行绘制。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 运行代码后，Spark 内核自动可视化 SQL (HiveQL) 查询的输出。 可以在以下几种类型的可视化之间进行选择：

* 表
* 饼图
* 折线图
* 区域
* 条形图​​

以下是用于绘制数据的代码：

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**输出：**

![小费金额直方图](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![按乘客数的小费金额](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![按费用金额的小费金额](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>创建特征和转换特征，然后准备输入到建模函数中的数据
对于 Spark ML 和 MLlib 中基于树的建模函数，必须使用各种技术（如装箱、索引、独热编码和矢量化）准备目标和特征。 以下是本部分中要遵循的步骤：

1. 通过将小时**装箱**到交通时间存储桶来创建新特征。
2. 将**索引和独热编码**应用于分类特征。
3. **将数据集采样和拆分**为定型和测试分数。
4. **指定定型变量和特征**，然后创建索引或独热编码定型和测试输入标记点弹性分布式数据集 (RDD) 或数据帧。
5. 自动**对特征和目标进行分类和矢量化**，以用作机器学习模型的输入。

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>通过将小时装箱到交通时间存储桶来创建新特征
此代码显示如何通过将小时装入交通时间存储桶创建新特征，以及如何在内存中缓存生成的数据帧。 重复使用 RDD 和数据帧时，缓存会改善执行时间。 相应地，会在以下过程中的多个节点缓存 RDD 和数据帧。

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>分类特征的索引和独热编码
MLlib 的建模和预测函数需要在使用前，对带有分类输入数据的特征进行索引或独热编码。 本部分介绍如何对分类特征进行索引和独热编码，输入到建模函数中。

根据模型，需要以不同方式为其编制索引或进行独热编码。 例如，逻辑和线性回归模型需进行独热编码。 例如，具有三个类别的特性可以扩展为三个特征列。 根据观察类别，每个列将包含 0 或 1 个类别。 MLlib 为独热编码提供 [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder)。 此编码器将标签索引列映射到二元向量列，该列最多只有单个值。 使用此编码，可将预期数值特征的算法（如逻辑回归）应用到分类特征。

此处，只需转换四个字符串变量来显示示例。 还可以将由数值表示的其他变量（例如工作日）编制索引为类别变量。

对于索引，请使用 `StringIndexer()`，对于独热编码，请使用 MLlib 中的 `OneHotEncoder()` 函数。 以下是用于对分类特征进行索引编制和编码的代码：

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**输出：**

运行该单元格时间：4 秒。

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>将数据集采样和拆分为定型和测试分数
此代码创建数据的随机采样（本示例中为 25%）。 尽管由于数据集的大小限制，本示例不需要进行采样，但本文介绍了如何采样，以便了解如何在需要时针对自己的问题使用它。 若样本很大，此操作可在定型模型时节省大量时间。 接下来将样本拆分为定型部分（本示例中为 75%）和测试部分（本示例中为 25%），用于分类和回归建模。

在每一行（“rand”列）中添加一个随机数（0 和 1 之间），可用于在定型期间选择交叉验证折叠。

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**输出：**

运行该单元格时间：2 秒。

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>指定定型变量和特征，然后创建索引或独热编码定型和测试输入标记点 RDD 或数据帧
本部分包含的代码演示了如何将分类文本数据编制索引为标签点数据类型，并对其编码，以便其可用于定型和测试 MLlib 逻辑回归和其他分类模型。 标签点对象是 RDD，格式为 MLlib 中的大多数机器学习算法所需的输入数据。 [标签点](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)是本地向量，可能密集，也可能稀疏，与标签/响应相关联。

在此代码中，指定用于定型模型的目标（依赖）变量和特征。 然后创建索引或独热编码定型和测试输入标记点 RDD 或数据帧。

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**输出：**

运行该单元格时间：4 秒。

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>自动对特征和目标进行分类和矢量化，以用作机器学习模型的输入
使用 Spark ML 对用于基于树的建模函数的目标和特征进行分类。 代码完成两个任务：

* 通过使用阈值 0.5 为 0 到 1 之间的每个数据点分配值 0 或 1，创建用于分类的二元目标。
* 自动对特征进行分类。 如果任何特征的不同数值的数量小于 32，则对该特征进行分类。

下面是这两个任务的代码。

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>二元分类模型：预测是否应支付小费
在本部分中，创建三种类型的二元分类模型，用以预测是否应支付小费：

* 使用 Spark ML `LogisticRegression()` 函数创建**逻辑回归模型**
* 使用 Spark ML `RandomForestClassifier()` 函数创建**随机林分类模型**
* 使用 MLlib `GradientBoostedTrees()` 函数创建**梯度提升树分类模型**

### <a name="create-a-logistic-regression-model"></a>创建逻辑回归模型
下一步，使用 Spark ML `LogisticRegression()` 函数创建逻辑回归模型。 通过一系列步骤创建模型构建代码：

1. 使用一个参数集**定型模型**数据。
2. 使用度量值**评估测试数据集上的模型**。
3. 在 Blob 存储中**保存模型**以供将来使用。
4. 根据测试数据**对模型进行评分**。
5. 使用接收者操作特性 (ROC) 曲线**绘制结果**。

下面是这些过程的代码：

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

加载、评分和保存结果。

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**输出：**

测试数据的 ROC = 0.9827381497557599

在本地 Pandas 数据帧上使用 Python 绘制 ROC 曲线。

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**输出：**

![有无小费 ROC 曲线](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>创建随机林分类模型
下一步，使用 Spark ML `RandomForestClassifier()` 函数创建随机林分类模型，然后根据测试数据评估模型。

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**输出：**

测试数据的 ROC = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>创建 GBT 分类模型
下一步，使用 MLlib 的 `GradientBoostedTrees()` 函数创建 GBT 分类模型，然后根据测试数据评估模型。

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**输出：**

ROC 曲线为 0.9846895479241554 下的面积

## <a name="regression-model-predict-tip-amount"></a>回归模型：预测小费金额
在本部分中，创建两种类型的回归模型，预测小费金额：

* 使用 Spark ML `LinearRegression()` 函数创建**正则化线性回归模型**。 保存此模型，并根据测试数据评估模型。
* 使用 Spark ML `GBTRegressor()` 函数创建**梯度提升树回归模型**。

### <a name="create-a-regularized-linear-regression-model"></a>创建正则化线性回归模型
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**输出：**

运行该单元格时间：13 秒。

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**输出：**

测试数据的 R-sqr = 0.5960320470835743

下一步，将测试结果作为数据帧进行查询，并使用 AutoVizWidget 和 matplotlib 将其可视化。

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

该代码从查询输出创建本地数据帧，并绘制数据。 `%%local`magic 创建本地数据帧`sqlResults`，可用于使用 matplotlib 进行绘制。

> [!NOTE]
> 本文中多次使用此 Spark magic。 如果数据量很大，应采样创建本地内存可容纳的数据帧。
> 
> 

使用 Python matplotlib 创建绘图。

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**输出：**

![小费金额：实际与预测](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>创建 GBT 回归模型
使用 Spark ML `GBTRegressor()` 函数创建 GBT 回归模型，然后根据测试数据评估模型。

[梯度提升树](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) 是决策树的整体。 GBT 以迭代方式定型决策树以最大程度减少损失函数。 可使用 GBT 进行回归和分类。 其可处理分类特征，不需要特征缩放，并且能够捕获非线性和特征交互。 它们还可以在多类分类设置中使用。

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**输出：**

测试 R sqr 为：0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>用于优化的高级建模实用程序
本部分中，使用开发人员经常用于模型优化的机器学习实用程序。 具体而言，可以通过使用参数扫描和交叉验证，以三种不同的方式优化机器学习模型：

* 将数据拆分成定型和验证数据集，通过对定型数据集使用超参数扫描优化模型，并且对验证数据集进行评价（线性回归）
* 使用 Spark ML 的 CrossValidator 函数，通过交叉验证和超参数扫描优化模型（二元分类）
* 通过自定义交叉验证和参数扫描代码，使用任何机器学习函数和参数集优化模型（线性回归）

**交叉验证**是一种技术，用于评估在已知数据集上定型的模型的概括性，利用概括性来预测未对其进行定型的数据集的特征。 此技术背后的一般理念是模型在已知数据的数据集上定型，然后参照独立数据集测试其预测的准确性。 此处使用的常见实现是将数据集划分为 *K* 折叠，然后以轮询机制方式在所有折叠上定型模型（其中一个折叠除外）。

**超参数优化**是为学习算法选择一组超参数的问题，通常目标是优化算法在独立数据集上的性能度量值。 超参数是必须在模型定型过程外指定的值。 关于超参数值的假设可能影响模型的灵活性和准确性。 例如，决策树具有超参数，如所需的深度和树中的树叶数量。 必须为支持向量机 (SVM) 设置错误分类惩罚项。

执行超参数优化的常用方法是使用网格搜索，也称为**参数扫描**。 在网格搜索中，为学习算法在这些值（超参数空间的指定子集）中执行详尽搜索。 交叉验证可提供性能指标，用于为网格搜索算法生成的最佳结果排序。 如果使用交叉验证超参数扫描，有助于限制模型过度拟合以定型数据等问题。 如此，模型可保留应用于从中提取定型数据的一般数据集的容量。

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>使用超参数扫描优化线性回归模型
下一步，将数据拆分成定型和验证数据集，对定型数据集使用超参数扫描，并且对验证数据集进行评价（线性回归）

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**输出：**

测试 R sqr 为：0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>使用交叉验证和超参数扫描优化二元分类模型
本部分介绍如何使用交叉验证和超参数扫描优化二元分类模型。 这使用 Spark ML `CrossValidator` 函数。

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**输出：**

运行该单元格时间：33 秒。

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>使用自定义交叉验证和参数扫描代码优化线性回归模型
接下来，通过使用自定义代码优化模型，并通过使用最高准确性条件标识最佳模型参数。 然后，创建最终模型，根据测试数据评估模型，并在 Blob 存储中保存此模型。 最后，加载模型、测试数据评分并评估准确性。

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**输出：**

运行该单元格时间：61 秒。

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>通过 Scala 自动使用 Spark 构建的机器学习模型
获取主题概述，了解包含在 Azure 中的数据科学过程的任务，请参阅[团队数据科学过程](http://aka.ms/datascienceprocess)。

[Team Data Science Process 演练](data-science-process-walkthroughs.md)针对特定方案，介绍了其他端到端演练，演示 Team Data Science Process 中的步骤。 该演练还展示了如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。

[为 Spark 构建的机器学习模型评分](machine-learning-data-science-spark-model-consumption.md)展示如何使用 Scala 代码通过内置在 Spark 中且保存在 Azure Blob 存储中的机器学习模型自动加载数据并对新数据评分。 可以按照文中提供的说明进行操作，只需使用本文中的 Scala 代码替换 Python 代码，便可自动使用。




<!--HONumber=Nov16_HO3-->


