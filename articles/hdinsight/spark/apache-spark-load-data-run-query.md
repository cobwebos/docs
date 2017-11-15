---
title: "在 Azure HDInsight Spark 群集上运行交互式查询 | Microsoft Docs"
description: "此 HDInsight Spark 快速入门介绍如何在 HDInsight 中创建 Apache Spark 群集。"
keywords: "spark 快速入门,交互式 spark,交互式查询,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: 0d93e261121f11d2a1082b9672e6d979955d3bee
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集上运行交互式查询

在本文中，将使用 Jupyter notebook 针对 Spark 群集运行交互式 Spark SQL 查询。 Jupyter notebook 是一个基于浏览器的应用程序，它将基于控制台的交互式体验扩展到了 Web。 有关详细信息，请参阅 [Jupyter notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html)。

本教程将使用 Jupyter notebook 中的 **PySpark** 内核运行交互式 Spark SQL 查询。 HDInsight 群集上的 Jupyter notebook 还支持两个其他内核：**PySpark3** 和 **Spark**。 有关内核的详细信息以及使用 **PySpark** 的好处，请参阅[将 Jupyter Notebook 内核与 HDInsight 中的 Apache Spark 群集配合使用](apache-spark-jupyter-notebook-kernels.md)。

## <a name="prerequisites"></a>先决条件

* **一个 Azure HDInsight Spark 群集**。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>创建 Jupyter notebook 来运行交互式查询

为了运行查询，我们使用默认情况下在与群集关联的存储中提供的示例数据。 但是，必须首先将该数据作为数据帧加载到 Spark 中。 在获得数据帧后，可以使用 Jupyter notebook 对其运行查询。 在本文中，你将了解如何：

* 将示例数据集注册为 Spark 数据帧。
* 对数据帧运行查询。

让我们开始吧。

1. 打开 [Azure 门户](https://portal.azure.com/)。 如果已选择将群集固定到仪表板，请单击仪表板中的群集磁贴，启动群集边栏选项卡。

    如果未将群集固定到仪表板，则请单击左窗格中的“HDInsight 群集”，并单击已创建的群集。

3. 在“快速链接”中，单击“群集仪表板”，并单击“Jupyter 笔记本”。 出现提示时，请输入群集的管理员凭据。

   ![打开 Jupyter notebook 来运行交互式 Spark SQL 查询](./media/apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "打开 Jupyter notebook 来运行交互式 Spark SQL 查询")

   > [!NOTE]
   > 也可以在浏览器中打开以下 URL 来访问群集的 Jupyter notebook。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. 创建笔记本。 单击“新建”，并单击“PySpark”。

   ![创建 Jupyter notebook 来运行交互式 Spark SQL 查询](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "创建 Jupyter notebook 来运行交互式 Spark SQL 查询")

   新笔记本随即已创建，并以 Untitled(Untitled.pynb) 名称打开。

4. 在顶部单击笔记本名称，并根据需要输入友好名称。

    ![为要从中运行交互式 Spark 查询的 Jupter notebook 提供一个名称](./media/apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "为要从中运行交互式 Spark 查询的 Jupter notebook 提供一个名称")

5. 将以下代码粘贴到一个空单元格中，并按 **SHIFT + ENTER** 来运行这些代码。 这些代码会导入此方案所需的类型：

        from pyspark.sql import *
        from pyspark.sql.types import *

    使用笔记本是使用 PySpark 内核创建的，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 和 Hive 上下文。

    ![交互式 Spark SQL 查询的状态](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "交互式 Spark SQL 查询的状态")

    每次在 Jupyter 中运行交互式查询时，Web 浏览器窗口标题中都会显示“(繁忙)”状态和 Notebook 标题。 右上角“PySpark”文本的旁边还会出现一个实心圆。 作业完成后，实心圆将变成空心圆。

6. 在将数据加载到 Spark 群集中之前，让我们看一下它的快照。 本教程中使用的示例数据在所有 HDInsight Spark 群集上都作为 CSV 文件提供，它位于 **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**。 该数据捕获了某栋建筑物的温度变化。 下面是前几行数据。

    ![交互式 Spark SQL 查询的数据快照](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "交互式 Spark SQL 查询的数据快照")

6. 通过运行以下代码，创建一个数据帧和一个临时表 (**hvac**)。 对于本教程，我们没有在 CSV 文件中创建所有可用的列。 

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

7. 在创建表后，使用以下代码对数据运行交互式查询。

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   由于使用的是 PySpark 内核，因此现在可直接对使用 `%%sql` magic 创建的临时表 **hvac** 运行交互式 SQL 查询。 有关 `%%sql` magic 以及可在 PySpark 内核中使用的其他 magic 的详细信息，请参阅[包含 Spark HDInsight 群集的 Jupyter Notebook 上可用的内核](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

   默认会显示以下表格输出。

     ![交互式 Spark 查询结果的表输出](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "交互式 Spark 查询结果的表输出")

9. 也可以在其他视觉效果中查看结果。 若要查看相同输出的面积图，请选择“面积”，然后设置其他值，如下所示。

    ![交互式 Spark 查询结果的面积图](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "交互式 Spark 查询结果的面积图")

10. 在笔记本的“文件”菜单中，单击“保存和检查点”。 

11. 如果现在开始[下一教程](apache-spark-use-bi-tools.md)，请使笔记本保持打开状态。 否则，关闭笔记本以释放群集资源：在笔记本的“文件”菜单中，单击“关闭并停止”。

## <a name="next-step"></a>后续步骤

在本文中，你已学习了如何使用 Jupyter notebook 在 Spark 中运行交互式查询。 请前进到下一文章来了解如何将在 Spark 中注册的数据拉取到诸如 Power BI 和 Tableau 的 BI 分析工具中。 

> [!div class="nextstepaction"]
>[使用 Azure HDInsight 上的数据可视化工具的 Spark BI](apache-spark-use-bi-tools.md)




