---
title: Zeppelin 笔记本和 Apache Spark 群集 - Azure HDInsight
description: 逐步说明如何在 Azure HDInsight 上的 Apache Spark 群集中使用 Zeppelin notebook。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: a692f4dd86d110f7f0a91a862a7b16ac28345de5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084522"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本

HDInsight Spark 群集包括 [Apache Zeppelin](https://zeppelin.apache.org/) 笔记本。 使用笔记本运行 Apache Spark 作业。 本文介绍如何在 HDInsight 群集中使用 Zeppelin 笔记本。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* 群集主存储的 URI 方案。 此方案适用 `wasb://` 于 Azure Blob 存储， `abfs://` 适用于 Azure Data Lake Storage Gen2 或 `adl://` Azure Data Lake Storage Gen1。 如果为 Blob 存储启用了安全传输，则 URI 将为 `wasbs://`。  有关详细信息，请参阅[在 Azure 存储中要求安全传输](../../storage/common/storage-require-secure-transfer.md)。

## <a name="launch-an-apache-zeppelin-notebook"></a>启动 Apache Zeppelin 笔记本

1. 在 Spark 群集的“概述”中，从**群集仪表板**选择“Zeppelin 笔记本”。  输入群集的管理员凭据。  

   > [!NOTE]  
   > 也可以在浏览器中打开以下 URL 来访问群集的 Zeppelin 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 创建新的笔记本。 在标题窗格中，导航到“笔记本” > “创建新笔记”。 

    ![创建新的 Zeppelin 笔记本](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "创建新的 Zeppelin 笔记本")

    输入笔记本的名称，然后选择“创建笔记”。

3. 确保笔记本标题显示“已连接”状态。 该状态由右上角的一个绿点表示。

    ![Zeppelin 笔记本状态](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 笔记本状态")

4. 将示例数据载入临时表。 在 HDInsight 中创建 Spark 群集时，系统会将示例数据文件 `hvac.csv` 复制到 `\HdiSamples\SensorSampleData\hvac` 下的关联存储帐户。

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

    按 **SHIFT + ENTER** 或为段落选择“播放”按钮以运行代码片段。 段落右上角的状态应从“就绪”逐渐变成“挂起”、“正在运行”和“已完成”。 输出会显示在同一段落的底部。 屏幕截图如下图所示：

    ![基于原始数据创建临时表](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "基于原始数据创建临时表")

    也可以为每个段落提供标题。 在段落的右侧一角，选择“设置”图标（齿轮图标），然后选择“显示标题”。   

    > [!NOTE]  
    > 所有 HDInsight 版本中的 Zeppelin 笔记本均不支持 %spark2 解释器，HDInsight 4.0 及更高版本不支持 %sh 解释器。

5. 现在可以针对 `hvac` 表运行 Spark SQL 语句。 将以下查询粘贴到新段落中。 该查询将检索建筑物 ID， 以及每栋建筑物在指定日期的目标温度与实际温度之间的差异。 按 **SHIFT + ENTER**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    开头的 **%Sql** 语句告诉笔记本要使用 Livy Scala 解释器。

6. 选择“条形图”图标以更改显示内容。  使用“设置”（选择“条形图”后显示）可以选择“键”和“值”。     以下屏幕快照显示了输出。

    ![使用 notebook1 运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用 notebook1 运行 Spark SQL 语句")

7. 还可以在查询中使用变量来运行 Spark SQL 语句。 以下代码片段演示如何在查询中使用可以用来查询的值定义 `Temp` 变量。 首次运行查询时，下拉列表中会自动填充你指定的变量值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    将此代码段粘贴到新段落，并按 **SHIFT + ENTER**。 然后，从“温度”下拉列表中选择“65”。 

8. 选择“条形图”图标以更改显示内容。  然后选择“设置”并进行以下更改：

   * **组：** 添加 **targettemp**。  
   * **值：** 1. 删除 **date**。  2. 添加 **temp_diff**。  3.  将聚合器从“SUM”更改为“AVG”。   

     以下屏幕快照显示了输出。

     ![使用 notebook2 运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用 notebook2 运行 Spark SQL 语句")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何在笔记本中使用外部包？

HDInsight 上 Apache Spark 群集中的 Zeppelin 笔记本可以使用群集中未包含的、社区提供的外部包。 在 [Maven 存储库](https://search.maven.org/)中搜索可用包的完整列表。 也可以从其他源获取可用包的列表。 例如， [Spark 包](https://spark-packages.org/)中提供了社区贡献包的完整列表。

本文将介绍如何在 Jupyter 笔记本中使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 包。

1. 打开解释器设置。 选择右上角的登录用户名，然后选择“解释器”。

    ![启动解释器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 输出")

2. 滚动到“livy2”，然后选择“编辑”。 

    ![更改解释器设置 1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "更改解释器设置 1")

3. 导航到键 `livy.spark.jars.packages`，并以 `group:id:version` 格式设置其值。 因此，如果要使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 包，必须将键的值设置为 `com.databricks:spark-csv_2.10:1.4.0`。

    ![更改解释器设置 2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "更改解释器设置 2")

    依次选择“保存”、“确定”，以重启 Livy 解释器。 

4. 要了解如何访问上面输入的键的值，请查看以下内容。

    a. 在 Maven 存储库中找出该包。 在本文中，使用了 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。

    b. 从存储库中收集 **GroupId**、**ArtifactId** 和 **Version** 的值。

    ![将外部包与 Jupyter 笔记本配合使用](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "将外部包与 Jupyter 笔记本配合使用")

    c. 串连这三个值并以冒号分隔 ( **:** )。

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin 笔记本保存在何处？

Zeppelin 笔记本保存在群集头节点。 因此，如果删除群集，笔记本也会被删除。 如果想要保留笔记本以供将来在其他群集中使用，那么必须在运行完作业之后，将笔记本导出。 若要导出笔记本，请选择下图所示的“导出”图标。

![下载笔记本](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下载笔记本")

此操作可在下载位置将笔记本另存为 JSON 文件。

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>使用 `Shiro` 在企业安全性套餐 (ESP) 群集中配置 Zeppelin 解释器的访问权限

如上所述，从 HDInsight 4.0 开始不再支持 `%sh` 解释器。 此外，由于 `%sh` 解释器会导致潜在的安全问题（例如，使用 shell 命令访问 keytabs），因此也从 HDInsight 3.6 ESP 群集中删除了该解释器。 这意味着，默认情况下，单击“创建新注释”时或位于解释器 UI 时，`%sh` 解析器不可用。

特权域用户可以使用 `Shiro.ini` 文件来控制对解释器 UI 的访问。 只有这些用户可以创建新的 `%sh` 解释器并对每个新 `%sh` 解释器设置权限。 若要使用 `shiro.ini` 文件控制访问权限，请执行以下步骤：

1. 使用现有域组名称定义新的角色。 在以下示例中，`adminGroupName` 是 AAD 中的一组特权用户。 请勿在组名称中使用特殊字符或空格。 `=` 后的字符用于为此角色提供权限。 `*` 表示组具有完全权限。

    ```
    [roles]
    adminGroupName = *
    ```

2. 添加新的角色以访问 Zeppelin 解释器。 在以下示例中，`adminGroupName` 中的所有用户都授予了 Zeppelin 解释器的访问权限，并且可以创建新的解释器。 你可以在 `roles[]` 中的括号之间放置多个角色，用逗号分隔。 然后，具有必要权限的用户可以访问 Zeppelin 解释器。

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy 会话管理

Zeppelin 笔记本中的第一个代码段会在群集中创建一个新的 Livy 会话。 此会话会在随后创建的所有 Zeppelin 笔记本中共享。 如果 Livy 会话因任何原因而被终止，则 Zeppelin 笔记本将无法运行作业。

在这种情况下，必须先执行以下步骤，然后才能开始在 Zeppelin 笔记本中运行作业。  

1. 在 Zeppelin 笔记本中重启 Livy 解释器。 为此，请选择右上角的登录用户名打开解释器设置，然后选择“解释器”。

    ![启动解释器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 输出")

2. 滚动到“livy2”，然后选择“重启”。 

    ![重启 Livy 解释器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重启 Zeppelin 解释器")

3. 在现有的 Zeppelin 笔记本中运行代码单元。 此代码会在 HDInsight 群集中创建新的 Livy 会话。

## <a name="general-information"></a>常规信息

### <a name="validate-service"></a>验证服务

若要从 Ambari 验证服务，请导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`，其中 CLUSTERNAME 是群集的名称。

若要从命令行验证服务，请通过 SSH 连接到头节点。 使用命令 `sudo su zeppelin` 将用户切换到 zeppelin。 状态命令：

|命令 |说明 |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|服务状态。|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|服务版本。|
|`ps -aux | grep zeppelin`|标识 PID。|

### <a name="log-locations"></a>日志位置

|服务 |`Path` |
|---|---|
|zeppelin-server|/usr/hdp/current/zeppelin-server/|
|服务器日志|/var/log/zeppelin|
|配置解释器、`Shiro`、site.xml、log4j|/usr/hdp/current/zeppelin-server/conf or /etc/zeppelin/conf|
|PID 目录|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>启用调试日志记录

1. 导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`，其中 CLUSTERNAME 是群集的名称。

1. 导航到“CONFIGS” > “Advanced zeppelin-log4j-properties” > “log4j_properties_content”。

1. 将 `log4j.appender.dailyfile.Threshold = INFO` 修改为 `log4j.appender.dailyfile.Threshold = DEBUG`。

1. 添加 `log4j.logger.org.apache.zeppelin.realm=DEBUG`。

1. 保存更改并重启服务。

## <a name="next-steps"></a>后续步骤

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)
