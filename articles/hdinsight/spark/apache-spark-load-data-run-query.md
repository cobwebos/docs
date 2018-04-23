---
title: 在 Azure HDInsight Spark 群集上运行交互式查询 | Microsoft Docs
description: 此 HDInsight Spark 快速入门介绍如何在 HDInsight 中创建 Apache Spark 群集。
keywords: spark 快速入门,交互式 spark,交互式查询,hdinsight spark,azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 177fb47c72e9abbafcda69416643fbd3848373bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>在 HDInsight 中的 Spark 群集上运行交互式查询

了解如何使用 Jupyter Notebook 针对 Spark 群集运行交互式 Spark SQL 查询。 

[Jupyter Notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) 是一个基于浏览器的应用程序，它将基于控制台的交互式体验扩展到 Web。 Spark on HDInsight 还包括 [Zeppelin Notebook](apache-spark-zeppelin-notebook.md)。 本教程使用 Jupyter Notebook。

HDInsight 群集上的 Jupyter Notebook 支持三种内核：**PySpark**、**PySpark3** 和 **Spark**。 本教程使用 **PySpark** 内核。 有关内核的详细信息以及使用 **PySpark** 的好处，请参阅[将 Jupyter Notebook 内核与 HDInsight 中的 Apache Spark 群集配合使用](apache-spark-jupyter-notebook-kernels.md)。 若要使用 Zeppelin Notebook，请参阅[在 Azure HDInsight 上的 Apache Spark 群集中使用 Zeppelin Notebook](./apache-spark-zeppelin-notebook.md)。

在本教程中，我们要查询某个 csv 文件中的数据。 必须首先将该数据作为数据帧加载到 Spark 中。 然后可以使用 Jupyter Notebook 针对数据帧运行查询。 

## <a name="prerequisites"></a>先决条件

* **一个 Azure HDInsight Spark 群集**。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* **使用 PySpark 的 Jupyter Notebook**。 有关说明，请参阅[创建 Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)。

## <a name="create-a-dataframe-from-a-csv-file"></a>从 csv 文件创建数据帧

使用 SQLContext，应用程序可从现有 RDD、Hive 表或数据源创建数据帧。 

**从 csv 文件创建数据帧**

1. 使用 PySpark 创建一个 Jupyter Notebook（如果没有）。 有关说明，请参阅[创建 Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)。

2. 在 Notebook 的空单元格中粘贴以下代码，然后按 **SHIFT + ENTER** 运行这些代码。 这些代码会导入此方案所需的类型：

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    如果使用 PySpark 内核创建 Notebook，在运行第一个代码单元格时，系统会自动创建 Spark 和 Hive 上下文。 不需要显式创建任何上下文。

    在 Jupyter 中运行交互式查询时，Web 浏览器窗口或选项卡标题中会显示“(繁忙)”状态和 Notebook 标题。 右上角“PySpark”文本的旁边还会出现一个实心圆。 作业完成后，实心圆将变成空心圆。

    ![交互式 Spark SQL 查询的状态](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "交互式 Spark SQL 查询的状态")

3. 运行以下代码创建数据帧和临时表 (**hvac**)：该代码不会提取 CSV 文件中的所有列。 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    以下屏幕截图显示 HVAC.csv 文件的快照。 所有 HDInsigt Spark 群集都随附了该 csv 文件。 该数据捕获了某栋建筑物的温度变化。

    ![交互式 Spark SQL 查询的数据快照](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "交互式 Spark SQL 查询的数据快照")

## <a name="run-queries-on-the-dataframe"></a>对数据帧运行查询

创建表后，可以针对数据运行交互式查询。

**运行查询**

1. 在 Notebook 的空单元格中运行以下代码：

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   由于在 Notebook 中使用了 PySpark 内核，因此现在可直接对使用 `%%sql` magic 创建的临时表 **hvac** 运行交互式 SQL 查询。 有关 `%%sql` magic 以及可在 PySpark 内核中使用的其他 magic 的详细信息，请参阅[包含 Spark HDInsight 群集的 Jupyter Notebook 上可用的内核](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

   默认会显示以下表格输出。

     ![交互式 Spark 查询结果的表输出](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "交互式 Spark 查询结果的表输出")

3. 也可以在其他视觉效果中查看结果。 若要查看相同输出的面积图，请选择“面积”，然后设置其他值，如下所示。

    ![交互式 Spark 查询结果的面积图](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "交互式 Spark 查询结果的面积图")

10. 在笔记本的“文件”菜单中，单击“保存和检查点”。 

11. 如果现在开始[下一教程](apache-spark-use-bi-tools.md)，请使笔记本保持打开状态。 否则，关闭笔记本以释放群集资源：在笔记本的“文件”菜单中，单击“关闭并停止”。

## <a name="next-step"></a>后续步骤

本文已介绍如何使用 Jupyter Notebook 在 Spark 中运行交互式查询。 请前进到下一文章来了解如何将在 Spark 中注册的数据拉取到诸如 Power BI 和 Tableau 的 BI 分析工具中。 

> [!div class="nextstepaction"]
>[使用 Azure HDInsight 上的数据可视化工具的 Spark BI](apache-spark-use-bi-tools.md)




