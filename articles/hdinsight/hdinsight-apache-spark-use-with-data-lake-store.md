---
title: "使用 Apache Spark 分析 Azure Data Lake Store 中的数据 | Microsoft Docs"
description: "运行 Spark 作业分析 Azure Data Lake Store 中存储的数据"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a3bdeb6fea306babc9358134c37044843b9bdd1c
ms.openlocfilehash: e9780d487043a86df5a627b92579b67154c59279


---

# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>使用 HDInsight Spark 群集分析 Data Lake Store 中的数据

本教程使用适用于 HDInsight Spark 群集的 Jupyter Notebook 运行作业，从 Data Lake Store 帐户中读取数据。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* Azure Data Lake Store 帐户。 遵循[使用 Azure 门户，实现 Azure Data Lake Store 入门](../data-lake-store/data-lake-store-get-started-portal.md)中的说明。

* 包含 Data Lake Store（作为存储）的 Azure HDInsight Spark 群集。 按照[使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)中的说明进行操作。

## <a name="prepare-the-data"></a>准备数据

如果已创建将 Data Lake Store 作为默认存储的 HDInsight 群集，则无需执行此步骤，因为群集创建过程会在创建群集时指定的 Data Lake Store 帐户中添加一些示例数据。

如果创建了将 Data Lake Store 作为附加存储并将 Azure 存储 Blob 作为默认存储的 HDInsight 群集，则应先将一些示例数据复制到 Data Lake Store 帐户。 可以使用与 HDInsight 群集关联的 Azure 存储 Blob 中的示例数据。 为此，可使用 [ADLCopy 工具](http://aka.ms/downloadadlcopy)。 从此链接下载并安装该工具。

1. 打开命令提示符，然后导航到 AdlCopy 的安装目录（通常是 `%HOMEPATH%\Documents\adlcopy`）。

2. 运行以下命令从源容器复制特定 blob 到 Data Lake Store：

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    将 **/HdiSamples/HdiSamples/SensorSampleData/hvac/** 中的 **HVAC.csv** 示例数据文件复制到 Azure Data Lake Store 帐户。 代码段应如下所示：

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > 请确保文件和路径名称采用合适的大小写。
   >
   >
3. 系统会提示输入 Azure 订阅（其下提供 Data Lake Store 帐户）的凭据。 会显示与下面类似的输出：

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    会将数据文件 (**HVAC.csv**) 复制到 Data Lake Store 帐户中的 **/hvac** 文件夹下。

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>使用包含 Data Lake Store 的 HDInsight Spark 群集

1. 在 [Azure 门户](https://portal.azure.com/)上的启动板中，单击 Spark 群集的磁贴（如果已将它固定到启动板）。 也可以单击“全部浏览” > “HDInsight 群集”导航到群集。

2. 在 Spark 群集边栏选项卡中单击“快速链接”，然后在“群集仪表板”边栏选项卡中单击“Jupyter 笔记本”。 出现提示时，请输入群集的管理员凭据。

   > [!NOTE]
   > 也可以在浏览器中打开以下 URL 来访问群集的 Jupyter 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. 创建新的笔记本。 单击“新建”，然后单击“PySpark”。

    ![创建新的 Jupyter 笔记本](./media/hdinsight-apache-spark-use-with-data-lake-store/hdispark.note.jupyter.createnotebook.png "创建新的 Jupyter 笔记本")

4. 使用笔记本是使用 PySpark 内核创建的，因此不需要显式创建任何上下文。 当你运行第一个代码单元格时，系统将自动为你创建 Spark 和 Hive 上下文。 首先可以导入此方案所需的类型。 为此，请将以下代码片段粘贴到某个单元中，然后按 **SHIFT + ENTER**。

        from pyspark.sql.types import *

    每次在 Jupyter 中运行作业时，Web 浏览器窗口标题中都会显示“(繁忙)”状态和笔记本标题。 右上角“PySpark”文本的旁边还会出现一个实心圆。 作业完成后，实心圆将变成空心圆。

     ![Jupyter 笔记本作业的状态](./media/hdinsight-apache-spark-use-with-data-lake-store/hdispark.jupyter.job.status.png "Jupyter 笔记本作业的状态")

5. 使用已复制到 Data Lake Store 帐户的 **HVAC.csv** 文件将示例数据上传到临时表。 可使用以下 URL 模式访问 Data Lake Store 帐户中的数据。

    如果将 Data Lake Store 作为默认存储，则 HVAC.csv 位于类似于以下 URL 的路径中：

         adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    如果将 Data Lake Store 作为附加存储，则 HVAC.csv 位于复制它的位置，如：

        adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     在空白单元格中，粘贴以下代码示例，将 **MYDATALAKESTORE** 替换为自己的 Data Lake Store 帐户名称，然后按 **Shift + Enter**。 此代码示例会将数据注册到名为 **hvac**的临时表中。

         # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
         hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

         # Create the schema
         hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

         # Parse the data in hvacText
         hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

         # Create a data frame
         hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

         # Register the data fram as a table to run queries against
         hvacdf.registerTempTable("hvac")

6. 由于使用的是 PySpark 内核，因此现在可直接在刚才使用 `%%sql` magic 创建的临时表 **hvac** 上运行 SQL 查询。 有关 `%%sql` magic 以及可在 PySpark 内核中使用的其他 magic 的详细信息，请参阅 [Kernels available on Jupyter notebooks with Spark HDInsight clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#choose-between-the-kernels)（包含 Spark HDInsight 群集的 Jupyter 笔记本上可用的内核）。

         %%sql
         SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. 作业成功完成后，默认情况下会显示以下表格输出。

      ![查询结果的表输出](./media/hdinsight-apache-spark-use-with-data-lake-store/tabular.output.png "查询结果的表输出")

     你也可以在其他视觉效果中查看结果。 例如，同一输出的分区图看起来如下所示。

     ![查询结果分区图](./media/hdinsight-apache-spark-use-with-data-lake-store/area.output.png "查询结果分区图")

8. 完成运行应用程序之后，应该要关闭笔记本以释放资源。 为此，请在笔记本的“文件”菜单中，单击“关闭并停止”。 这将会关闭笔记本。


## <a name="next-steps"></a>后续步骤

* [创建要在 Apache Spark 群集上运行的独立 Scala 应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具为 HDInsight Spark Linux 群集创建 Spark 应用程序](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具为 HDInsight Spark Linux 群集创建 Spark 应用程序](hdinsight-apache-spark-eclipse-tool-plugin.md)



<!--HONumber=Feb17_HO1-->


