---
title: 分析 HDInsight Apache Spark Azure Data Lake Storage Gen1
description: 运行 Apache Spark 作业来分析存储在 Azure Data Lake Storage Gen1 中的数据
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818168"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>使用 HDInsight Spark 群集分析 Data Lake Storage Gen1 中的数据

本文介绍如何使用 HDInsight Spark 群集可用[Jupyter Notebook](https://jupyter.org/)来运行从 Data Lake Storage 帐户读取数据的作业。

## <a name="prerequisites"></a>先决条件

* Azure Data Lake Storage Gen1 帐户。 请遵循[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-get-started-portal.md) 中的说明进行操作。

* 包含 Data Lake Storage Gen1 作为存储的 Azure HDInsight Spark 群集。 按照[快速入门：在 HDInsight 中设置群集](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)中的说明进行操作。

## <a name="prepare-the-data"></a>准备数据

> [!NOTE]  
> 如果已创建将 Data Lake Storage 作为默认存储的 HDInsight 群集，则无需执行此步骤。 群集创建过程在创建群集时指定的 Data Lake Storage 帐户中添加一些示例数据。 跳转到“配合使用 HDInsight Spark 群集与 Data Lake Storage”一节。

如果创建了将 Data Lake Storage 作为附加存储并将 Azure 存储 Blob 作为默认存储的 HDInsight 群集，则应先将一些示例数据复制到 Data Lake Store 帐户。 可以使用与 HDInsight 群集关联的 Azure 存储 Blob 中的示例数据。 为此，可使用 [ADLCopy 工具](https://www.microsoft.com/download/details.aspx?id=50358)。 从此链接下载并安装该工具。

1. 打开命令提示符，并导航到 AdlCopy 的安装目录（通常是 `%HOMEPATH%\Documents\adlcopy`）。

2. 运行以下命令，将特定的 Blob 从源容器复制到 Data Lake Storage：

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    将 /HdiSamples/HdiSamples/SensorSampleData/hvac/ 中的 HVAC.csv 示例数据文件复制到 Azure Data Lake Storage 帐户。 代码段应如下所示：

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > 请确保文件和路径名称使用正确的大小写。

3. 系统会提示输入 Azure 订阅（其下提供 Data Lake Storage 帐户）的凭据。 会显示类似于以下代码片段的输出：

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    会将数据文件 (HVAC.csv) 复制到 Data Lake Storage 帐户中的 /hvac 文件夹下。

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>使用包含 Data Lake Store Gen1 的 HDInsight Spark 群集

1. 在[Azure 门户](https://portal.azure.com/)的启动板中，单击 Apache Spark 群集的磁贴（如果已将它固定到启动板）。 也可以单击“全部浏览” > “HDInsight 群集”导航到群集。

2. 在 Spark 群集边栏选项卡中单击“快速链接”，并在“群集仪表板”边栏选项卡中单击“Jupyter 笔记本”。 出现提示时，请输入群集的管理员凭据。

   > [!NOTE]  
   > 也可以在浏览器中打开以下 URL 来访问群集的 Jupyter 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. 创建新的笔记本。 单击“新建”，并单击“PySpark”。

    ![创建新的 Jupyter 笔记本](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "创建新的 Jupyter 笔记本")

4. 使用笔记本是使用 PySpark 内核创建的，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 和 Hive 上下文。 首先可以导入此方案所需的类型。 为此，请将以下代码片段粘贴到某个单元中，然后按 **SHIFT + ENTER**。

        from pyspark.sql.types import *

    每次在 Jupyter 中运行作业时，Web 浏览器窗口标题中都会显示“(繁忙)”状态和笔记本标题。 右上角“PySpark”文本的旁边还会出现一个实心圆。 作业完成后，实心圆将变成空心圆。

     ![Jupyter 笔记本作业的状态](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Jupyter 笔记本作业的状态")

5. 使用已复制到 Data Lake Storage Gen1 帐户的 **HVAC.csv** 文件将示例数据加载到临时表。 可使用以下 URL 模式访问 Data Lake Storage 帐户中的数据。

   * 如果将 Data Lake Storage Gen1 作为默认存储，则 HVAC.csv 位于类似以下 URL 的路径中：

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       也可使用如下所示的缩写格式：

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * 如果将 Data Lake Storage 作为附加存储，则 HVAC.csv 位于复制它的位置，如：

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     在空白单元格中，粘贴以下代码示例，将 MYDATALAKESTORE 替换为自己的 Data Lake Storage 帐户名称，然后按 Shift + Enter。 此代码示例会将数据注册到名为 **hvac**的临时表中。

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. 由于使用的是 PySpark 内核，因此现在可直接在刚才使用 **magic 创建的临时表**hvac`%%sql` 上运行 SQL 查询。 有关 `%%sql` magic 以及可在 PySpark 内核中使用的其他 magic 的详细信息，请参阅 [Kernels available on Jupyter notebooks with Apache Spark HDInsight clusters](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)（包含 Apache Spark HDInsight 群集的 Jupyter notebook 上可用的内核）。

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. 作业成功完成后，默认情况下会显示以下表格输出。

      ![查询结果的表输出](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "查询结果的表输出")

     也可以在其他视觉效果中查看结果。 例如，同一输出的分区图看起来如下所示。

     ![查询结果的面积图](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "查询结果分区图")

8. 完成运行应用程序之后，应该要关闭笔记本以释放资源。 为此，请在笔记本的“文件”菜单中，单击“关闭并停止”。 这会关闭笔记本。


## <a name="next-steps"></a>后续步骤

* [创建要在 Apache Spark 群集上运行的独立 Scala 应用程序](apache-spark-create-standalone-application.md)
* [使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具为 HDInsight Spark Linux 群集创建 Apache Spark 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具为 HDInsight Spark Linux 群集创建 Apache Spark 应用程序](apache-spark-eclipse-tool-plugin.md)
* [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
