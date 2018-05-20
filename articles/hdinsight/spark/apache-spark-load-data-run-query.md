---
title: 教程：在 Azure HDInsight 中的 Apache Spark 群集上加载数据并运行查询 | Microsoft Docs
description: 了解如何在 Azure HDInsight 中的 Spark 群集上加载数据并运行交互式查询。
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.author: jgao
ms.date: 05/07/2018
ms.openlocfilehash: 63a876dc148129cd2a3eb93ed7ab6baf06a07c62
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>教程：在 Azure HDInsight 中的 Apache Spark 群集上加载数据并运行查询

本教程介绍如何使用从 csv 文件创建数据帧，以及如何针对 Azure HDInsight 中的 Apache Spark 群集运行交互式 Spark SQL 查询。 在 Spark 中，数据帧是已组织成命名列的分布式数据集合。 数据帧在概念上相当于关系型数据库中的表，或 R/Python 中的数据帧。
 
本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 从 csv 文件创建数据帧
> * 对数据帧运行查询

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* 完成[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

## <a name="create-a-dataframe-from-a-csv-file"></a>从 csv 文件创建数据帧

应用程序可使用 SQLContext 对象从现有弹性分布式数据集 (RDD)、Hive 表或数据源创建数据帧。 以下屏幕截图显示本教程中所用 HVAC.csv 文件的快照。 所有 HDInsight Spark 群集都随附了该 csv 文件。 该数据捕获了一些建筑物的温度变化。
    
![交互式 Spark SQL 查询的数据快照](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "交互式 Spark SQL 查询的数据快照")


1. 打开在先决条件部分中创建的 Jupyter 笔记本。
2. 在 Notebook 的空单元格中粘贴以下代码，然后按 **SHIFT + ENTER** 运行这些代码。 这些代码会导入此方案所需的类型：

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    在 Jupyter 中运行交互式查询时，Web 浏览器窗口或选项卡标题中会显示“(繁忙)”状态和 Notebook 标题。 右上角“PySpark”文本的旁边还会出现一个实心圆。 作业完成后，实心圆将变成空心圆。

    ![交互式 Spark SQL 查询的状态](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "交互式 Spark SQL 查询的状态")

3. 运行以下代码，创建数据帧和临时表 (hvac)。 该代码不会提取 CSV 文件中的所有可用列。 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for the data
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

    > [!NOTE]
    > 通过使用 PySpark 内核创建 Notebook，在运行第一个代码单元格时，系统会自动创建 SQL 上下文。 不需要显式创建任何上下文。


## <a name="run-queries-on-the-dataframe"></a>对数据帧运行查询

创建表后，可以针对数据运行交互式查询。

1. 在 Notebook 的空单元格中运行以下代码：

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   由于在笔记本中使用了 PySpark 内核，因此现在可直接对临时表 hvac 运行交互式 SQL 查询。

   以下表格输出随即显示。

     ![交互式 Spark 查询结果的表输出](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "交互式 Spark 查询结果的表输出")

3. 也可以在其他视觉效果中查看结果。 若要查看相同输出的面积图，请选择“面积”，然后设置其他值，如下所示。

    ![交互式 Spark 查询结果的面积图](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "交互式 Spark 查询结果的面积图")

10. 在笔记本的“文件”菜单中，选择“保存和检查点”。 

11. 如果现在开始[下一教程](apache-spark-use-bi-tools.md)，请使笔记本保持打开状态。 否则，关闭笔记本，释放群集资源：在笔记本的“文件”菜单中，选择“关闭并停止”。

## <a name="clean-up-resources"></a>清理资源

有了 HDInsight，便可以将数据存储在 Azure 存储或 Azure Data Lake Store 中，以便在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。 如果打算立即开始学习下一教程，可能需要保留该群集。

在 Azure 门户中打开群集，然后选择“删除”。

![删除 HDInsight 群集](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "删除 HDInsight 群集")

还可以选择资源组名称，打开“资源组”页，然后选择“删除资源组”。 通过删除资源组，可以删除 HDInsight Spark 群集和默认存储帐户。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 创建 Spark 数据帧。
* 针对数据帧运行 Spark SQL。

请前进到下一篇文章，了解如何将在 Spark 中注册的数据拉取到 Power BI 等 BI 分析工具中。 
> [!div class="nextstepaction"]
> [使用 BI 工具分析数据](apache-spark-use-bi-tools.md)

