---
title: 在 Azure HDInsight 上的 Apache Spark 群集中使用 Zeppelin 笔记本
description: 逐步说明如何在 Azure HDInsight 上的 Apache Spark 群集中使用 Zeppelin 笔记本。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 18660b41f4413d3ae1cd820cd645ddb540397906
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095887"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本

HDInsight Spark 群集包括可用于运行 [Apache Spark](https://spark.apache.org/) 作业的 [Apache Zeppelin](https://zeppelin.apache.org/) 笔记本。 本文介绍如何在 HDInsight 群集中使用 Zeppelin 笔记本。

**先决条件：**

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* 群集主存储 URI 方案。 这将是`wasb://`Azure Blob 存储`abfs://`有关 Azure 数据湖存储第 2 代或`adl://`的 Azure 数据湖存储 Gen1。 如果为 Blob 存储或数据湖存储第 2 代启用了安全传输，则 URI 将为`wasbs://`或`abfss://`分别。  此外，请参阅[需要在 Azure 存储中的安全传输](../../storage/common/storage-require-secure-transfer.md)有关详细信息。

## <a name="launch-an-apache-zeppelin-notebook"></a>启动 Apache Zeppelin 笔记本

1. 从 Spark 群集**概述**，选择**Zeppelin 笔记本**从**群集仪表板**。 输入群集管理员凭据。  

   > [!NOTE]  
   > 也可以在浏览器中打开以下 URL 来访问群集的 Zeppelin 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 创建新的笔记本。 在标题窗格中，导航到**Notebook** > **创建新便笺**。

    ![创建新的 Zeppelin 笔记本](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "创建新的 Zeppelin 笔记本")

    输入笔记本的名称，然后选择**创建笔记**。

3. 请确保笔记本标题显示连接的状态。 右上角的绿点则表示“已连接”状态。

    ![Zeppelin 笔记本状态](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 笔记本状态")

4. 将示例数据载入临时表。 在 HDInsight，示例数据文件中创建 Spark 群集`hvac.csv`，复制到关联的存储帐户下`\HdiSamples\SensorSampleData\hvac`。

    将以下代码段粘贴到新笔记本中默认创建的空白段落处。

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    按 **Shift + Enter** 或单击“播放”按钮，使段落运行代码段。 段落右上角的状态应从“就绪”逐渐变成“挂起”、“正在运行”和“已完成”。 输出会显示在同一段落的底部。 屏幕截图如下所示：

    ![基于原始数据创建临时表](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "基于原始数据创建临时表")

    也可以为每个段落提供标题。 在段落的右下角，选择**设置**图标 (sprocket)，然后选择**显示标题**。  

    > [!NOTE]  
    > 所有 HDInsight 版本中的 Zeppelin 笔记本均不支持 %spark2 解释器，HDInsight 4.0 及更高版本不支持 %sh 解释器。

5. 你现在可以运行 Spark SQL 语句`hvac`表。 将以下查询粘贴到新段落中。 该查询将检索建筑物 ID，以及在给定日期当天每栋建筑物的目标温度与实际温度之间的差异。 按 **Shift + Enter**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    开头的 **%Sql** 语句告诉笔记本要使用 Livy Scala 解释程序。

6. 选择**条形图**图标来更改显示。  **设置**，将显示此选择后**条形图**，允许你选择**密钥**，并且**值**。  以下屏幕快照显示了输出。

    ![使用笔记本运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用笔记本运行 Spark SQL 语句")

7. 还可以在查询中使用变量来运行 Spark SQL 语句。 下一个代码段演示如何定义一个变量， `Temp`，可能的值与你想要使用查询在查询中。 首次运行查询时，下拉列表中会自动填充你指定的变量值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    将此代码段粘贴到新段落，然后按 **Shift + Enter**。 然后选择**65**从**Temp**下拉 ist。 

8. 选择**条形图**图标来更改显示。  然后选择**设置**并进行以下更改：

   * **组：** 添加**targettemp**。  
   * **值：** 1. 删除**日期**。  2. 添加**temp_diff**。  3.  更改从聚合器**总和**到**AVG**。  

     以下屏幕快照显示了输出。

     ![使用笔记本运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用笔记本运行 Spark SQL 语句")

9. 重新启动 Livy 解释程序以退出应用程序。 为此，请通过选择已登录用户名称，从右上角中，打开解释程序设置，然后选择**解释器**。  

    ![启动解释程序](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 输出")

10. 滚动到**livy**，然后选择**重新启动**。  选择**确定**在提示符处。

    ![重启 Livy 解释程序](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重启 Zeppelin 解释程序")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何将外部包与笔记本配合使用？
可以使用、 社区贡献的外部包不包括--现成的群集中的 HDInsight 上的 Apache Spark 群集中配置 Zeppelin 笔记本。 可以搜索 [Maven 存储库](https://search.maven.org/)获取可用包的完整列表。 也可以从其他源获取可用包的列表。 例如，[Spark 包](https://spark-packages.org/)中提供了社区贡献包的完整列表。

本文介绍如何在 Jupyter 笔记本中使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 包。

1. 打开解释程序设置。 在右上角选择登录用户名，然后选择**解释器**。

    ![启动解释程序](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 输出")

2. 滚动到**livy**，然后选择**编辑**。

    ![更改解释程序设置](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "更改解释程序设置")

3. 添加名为的新项`livy.spark.jars.packages`，并将其值设置格式`group:id:version`。 因此，如果要使用 [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 包，必须将密钥的值设置为 `com.databricks:spark-csv_2.10:1.4.0`。

    ![更改解释程序设置](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "更改解释程序设置")

    选择**保存**，然后重新启动 Livy 解释程序。

4. 要了解如何访问上面输入的键的值，请查看以下内容。
   
    a. 在 Maven 存储库中找出该包。 在本教程中，使用了 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。
   
    b. 从存储库中收集 **GroupId**、**ArtifactId** 和 **Version** 的值。
   
    ![将外部包与 Jupyter 笔记本配合使用](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "将外部包与 Jupyter 笔记本配合使用")
   
    c. 串连这三个值并以冒号分隔 (**:**)。
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin 笔记本保存在何处？
Zeppelin 笔记本的保存位置为群集头节点。 因此，如果删除该群集，笔记本也会被删除。 要将笔记本保存到其他群集以供将来使用，则必须在完成作业运行后将其导出。 若要导出笔记本，请选择**导出**图标，如下图中所示。

![下载笔记本](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下载笔记本")

这会将笔记本作为 JSON 文件保存在下载位置。

## <a name="livy-session-management"></a>Livy 会话管理
在 Zeppelin 笔记本中运行第一个代码段时，在 HDInsight Spark 群集中创建了新的 Livy 会话。 此会话在随后创建的所有 Zeppelin 笔记本中共享。 如果由于某种原因（群集重新启动等）导致 Livy 会话终止，则无法从 Zeppelin 笔记本运行作业。

在这种情况下，必须执行以下步骤，才能从 Zeppelin 笔记本运行作业。  

1. 在 Zeppelin 笔记本中，重新启动 Livy 解释程序。 若要执行此操作，打开解释程序设置中从右上角的用户名称选择已登录，然后选择**解释器**。

    ![启动解释程序](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 输出")

2. 滚动到**livy**，然后选择**重新启动**。

    ![重启 Livy 解释程序](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重启 Zeppelin 解释程序")

3. 在现有的 Zeppelin 笔记本中运行代码单元。 此操作可在 HDInsight 群集中创建新的 Livy 会话。

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Apache Spark 与 BI：将 HDInsight 中的 Spark 与 BI 工具配合使用来执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Apache Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
