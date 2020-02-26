---
title: Zeppelin 笔记本 & Apache Spark 群集-Azure HDInsight
description: 逐步说明如何在 Azure HDInsight 上的 Apache Spark 群集中使用 Zeppelin 笔记本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598266"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本

HDInsight Spark 群集包括可用于运行 [Apache Spark](https://zeppelin.apache.org/) 作业的 [Apache Zeppelin](https://spark.apache.org/) 笔记本。 本文介绍如何在 HDInsight 群集中使用 Zeppelin 笔记本。

## <a name="prerequisites"></a>必备条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* 群集主存储的 URI 方案。 这会 `wasb://` Azure Blob 存储、`abfs://` Azure Data Lake Storage Gen2 或 `adl://` 用于 Azure Data Lake Storage Gen1。 如果为 Blob 存储启用安全传输，则将 `wasbs://`URI。  有关详细信息，请参阅[在 Azure 存储中要求安全传输](../../storage/common/storage-require-secure-transfer.md)。

## <a name="launch-an-apache-zeppelin-notebook"></a>启动 Apache Zeppelin 笔记本

1. 从 Spark 群集**概述**的 "**群集仪表板**" 中选择 " **Zeppelin 笔记本**"。 输入群集的管理员凭据。  

   > [!NOTE]  
   > 也可以在浏览器中打开以下 URL 来访问群集的 Zeppelin 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 创建新的笔记本。 从 "标题" 窗格中，导航到 "**笔记本** > **创建新便笺**"。

    ![创建新的 Zeppelin 笔记本](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "创建新的 Zeppelin 笔记本")

    输入笔记本的名称，并选择 "**创建便笺**"。

3. 确保笔记本标头显示连接状态。 其右上角有一个绿色圆点表示。

    ![Zeppelin 笔记本状态](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 笔记本状态")

4. 将示例数据载入临时表。 在 HDInsight 中创建 Spark 群集时，`hvac.csv`的示例数据文件将复制到 `\HdiSamples\SensorSampleData\hvac`下的关联存储帐户。

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

    按**SHIFT + enter**或选择段落的 "**播放**" 按钮以运行代码段。 段落右上角的状态应从“就绪”逐渐变成“挂起”、“正在运行”和“已完成”。 输出会显示在同一段落的底部。 屏幕截图如下所示：

    ![基于原始数据创建临时表](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "基于原始数据创建临时表")

    也可以为每个段落提供标题。 在段落的右上角，选择 "**设置**" 图标（sprocket），然后选择 "**显示标题**"。  

    > [!NOTE]  
    > 所有 HDInsight 版本中的 Zeppelin 笔记本均不支持 %spark2 解释器，HDInsight 4.0 及更高版本不支持 %sh 解释器。

5. 你现在可以在 `hvac` 表上运行 Spark SQL 语句。 将以下查询粘贴到新段落中。 该查询将检索建筑物 ID，以及在给定日期当天每栋建筑物的目标温度与实际温度之间的差异。 按 **Shift + Enter**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    开头的 **%Sql** 语句告诉笔记本要使用 Livy Scala 解释程序。

6. 选择 "**条形图**" 图标以更改显示。  选择 "**条形图**" 后显示的**设置**允许您选择**键**和**值**。  以下屏幕快照显示了输出。

    ![使用 notebook1 运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用 notebook1 运行 Spark SQL 语句")

7. 还可以在查询中使用变量来运行 Spark SQL 语句。 下一个代码片段演示了如何在查询中使用想要查询的可能值来定义变量 `Temp`。 首次运行查询时，下拉列表中会自动填充你指定的变量值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    将此代码段粘贴到新段落，然后按 **Shift + Enter**。 然后从**Temp**下拉列表中选择**65** 。

8. 选择 "**条形图**" 图标以更改显示。  然后选择 "**设置**" 并进行以下更改：

   * **组：** 添加**targettemp**。  
   * **值：** 2. 删除**日期**。  2. 添加**temp_diff**。  3.  将聚合器从**SUM**更改为**AVG**。  

     以下屏幕快照显示了输出。

     ![使用 notebook2 运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用 notebook2 运行 Spark SQL 语句")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何将外部包与笔记本配合使用？

可以在 HDInsight 上 Apache Spark 群集中配置 Zeppelin 笔记本，以使用未现成包含在群集中的、由社区提供的外部包。 可以搜索 [Maven 存储库](https://search.maven.org/)获取可用包的完整列表。 也可以从其他源获取可用包的列表。 例如，[Spark 包](https://spark-packages.org/)中提供了社区贡献包的完整列表。

在本文中，你将了解如何将[spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)包与 Jupyter 笔记本配合使用。

1. 打开解释程序设置。 在右上角，选择 "登录用户名"，然后选择 "**解释**器"。

    ![启动解释器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 输出")

2. 滚动到**livy2**，然后选择 "**编辑**"。

    ![更改解释器 settings1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "更改解释器 settings1")

3. 导航到 "key `livy.spark.jars.packages`"，然后将其值设置为 "`group:id:version`格式。 因此，如果要使用 [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 包，必须将密钥的值设置为 `com.databricks:spark-csv_2.10:1.4.0`。

    ![更改解释器 settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "更改解释器 settings2")

    选择 "**保存**"，然后单击 **"确定"** 以重新启动 Livy 解释器。

4. 要了解如何访问上面输入的键的值，请查看以下内容。

    a. 在 Maven 存储库中找出该包。 对于本文，我们使用了[spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。

    b. 从存储库中收集 **GroupId**、**ArtifactId** 和 **Version** 的值。

    ![将外部包与 Jupyter 笔记本配合使用](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "将外部包与 Jupyter 笔记本配合使用")

    c. 串连这三个值并以冒号分隔 ( **:** )。

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin 笔记本保存在何处？

Zeppelin 笔记本的保存位置为群集头节点。 因此，如果删除该群集，笔记本也会被删除。 要将笔记本保存到其他群集以供将来使用，则必须在完成作业运行后将其导出。 若要导出笔记本，请选择**导出**图标，如下图所示。

![下载笔记本](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下载笔记本")

这会将笔记本作为 JSON 文件保存在下载位置。

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>使用 Shiro 配置对企业安全性套餐（ESP）群集中 Zeppelin 解释器的访问权限
如上所述，从 HDInsight 4.0 开始不支持 `%sh` 解释器。 此外，由于 `%sh` 解释器引入了潜在的安全问题，例如使用 shell 命令访问 keytabs，因此还从 HDInsight 3.6 ESP 群集中删除了该问题。 这意味着，在默认情况下单击 "**创建新注释**" 或在解释器 UI 中，`%sh` 解释器不可用。 

特权域用户可以利用 `Shiro.ini` 文件来控制对解释器 UI 的访问。 因此，只有这些用户可以创建新的 `%sh` 解释器并对每个新 `%sh` 解释器设置权限。 若要使用 `shiro.ini` 文件控制访问权限，请执行以下步骤：

1. 使用现有的域组名称定义新的角色。 在下面的示例中，`adminGroupName` 是 AAD 中的一组特权用户。 不要在组名称中使用特殊字符或空格。 `=` 为此角色提供权限后的字符。 `*` 表示该组具有完全权限。

    ```
    [roles]
    adminGroupName = *
    ```

2. 添加新的角色以访问 Zeppelin 解释器。 在下面的示例中，`adminGroupName` 中的所有用户都被授予 Zeppelin 解释器的访问权限，并且可以创建新的解释器。 可以在 `roles[]`中的括号之间放置多个角色，用逗号分隔。 然后，具有必要权限的用户可以访问 Zeppelin 解释器。

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy 会话管理

在 Zeppelin 笔记本中运行第一个代码段时，会在 HDInsight Spark 群集中创建一个新的 Livy 会话。 此会话会在随后创建的所有 Zeppelin 笔记本之间共享。 如果由于某种原因，Livy 会话已终止（群集重新启动等），你将无法从 Zeppelin 笔记本中运行作业。

在这种情况下，必须执行以下步骤，才能从 Zeppelin 笔记本运行作业。  

1. 在 Zeppelin 笔记本中，重新启动 Livy 解释程序。 为此，请从右上角选择登录的用户名，然后选择 "**解释**器" 来打开解释器设置。

    ![启动解释器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 输出")

2. 滚动到**livy2**，然后选择 "**重新启动**"。

    ![重新启动 Livy 解释器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新启动 Zeppelin 解释器")

3. 从现有的 Zeppelin 笔记本运行代码单元格。 这会在 HDInsight 群集中创建新的 Livy 会话。

## <a name="general-information"></a>常规信息

### <a name="validate-service"></a>验证服务

若要从 Ambari 验证服务，请导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`，其中 CLUSTERNAME 是群集的名称。

若要从命令行验证服务，请通过 SSH 连接到头节点。 使用命令 `sudo su zeppelin`将用户切换到 zeppelin。 状态命令：

|Command |说明 |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|服务状态。|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|服务版本。|
|`ps -aux | grep zeppelin`|标识 PID。|

### <a name="log-locations"></a>日志位置

|服务 |路径 |
|---|---|
|zeppelin-服务器|/usr/hdp/current/zeppelin-server/|
|服务器日志|/var/log/zeppelin|
|配置解释器、Shiro、log4j|/usr/hdp/current/zeppelin-server/conf 或/etc/zeppelin/conf|
|PID 目录|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>启用调试日志记录

1. 导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`，其中 CLUSTERNAME 是群集的名称。

1. 导航到 "配置 **" > "** **Advanced zeppelin-log4j"-属性** > **log4j_properties_content**"。

1. 修改 `log4j.appender.dailyfile.Threshold = INFO` 以 `log4j.appender.dailyfile.Threshold = DEBUG`。

1. 添加 `log4j.logger.org.apache.zeppelin.realm=DEBUG`。

1. 保存更改并重新启动服务。

## <a name="next-steps"></a>后续步骤

[概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案

* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
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
