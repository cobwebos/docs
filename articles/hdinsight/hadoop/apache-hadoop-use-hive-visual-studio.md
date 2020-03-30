---
title: Apache Hive &可视化工作室的数据湖工具 - Azure HDInsight
description: 了解如何使用用于 Visual Studio 的 Data Lake 工具，通过 Azure HDInsight 上的 Apache Hadoop 运行 Apache Hive 查询。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687805"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具运行 Apache Hive 查询

了解如何使用针对 Visual Studio 的 Data Lake 工具查询 Apache Hive。 使用 Data Lake 工具，可以轻松创建 Hive 查询，将其提交到 Azure HDInsight 上的 Apache Hadoop 并进行监视。

## <a name="prerequisites"></a>先决条件

* HDInsight 中的 Apache Hadoop 群集。 有关创建此项的信息，请参阅[使用资源管理器模板在 Azure HDInsight 中创建 Apache Hadoop 群集](./apache-hadoop-linux-tutorial-get-started.md)。

* [视觉工作室](https://visualstudio.microsoft.com/vs/)。 本文中的步骤使用 Visual Studio 2019。

* Visual Studio 的 HDInsight 工具或 Visual Studio 的 Azure Data Lake 工具。 有关安装和配置工具的信息，请参阅[安装适用于 Visual Studio 的 Data Lake 工具](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)。

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>使用 Visual Studio 运行 Apache Hive 查询

可以使用两个选项来创建并运行 Hive 查询：

* 创建临时查询。
* 创建 Hive 应用程序。

### <a name="create-an-ad-hoc-hive-query"></a>创建临时 Hive 查询。

即席查询可以**批处理**或**交互式**模式执行。

1. 启动 **Visual Studio** 并选择“继续但无需代码”****。

2. 在服务器资源管理器中右键单击“Azure”并选择“连接到 Microsoft Azure 订阅...”，然后完成登录过程。************

3. 展开“HDInsight”，右键单击要运行查询的群集，然后选择“编写 Hive 查询”********。

4. 输入以下 Hive 查询：

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. 选择“执行”****。 执行模式默认为“交互式”。****

    ![执行交互式 Hive 查询，Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 若要以**批处理**模式下运行同一查询，请将下拉列表从“交互式”**** 切换到“批处理”****。 执行按钮将从“执行”**** 更改为“提交”****。

    ![提交批处理 Hive 查询，Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive 编辑器支持 IntelliSense。 用于 Visual Studio 的 Data Lake 工具支持在编辑 Hive 脚本时加载远程元数据。 例如，如果键入 `SELECT * FROM`，则 IntelliSense 会列出所有建议的表名称。 在指定表名称后，IntelliSense 会列出列名称。 这些工具支持大多数 Hive DML 语句、子查询和内置 UDF。 IntelliSense 只建议 HDInsight 工具栏中所选群集的元数据。

7. 在查询工具栏（查询选项卡下面以及查询文本上面的区域）中，选择“提交”，或者选择“提交”旁边的下拉箭头并从下拉列表中选择“高级”。************ 如果选择后一个选项，

8. 即选择高级提交选项，请在“提交脚本”对话框中配置“作业名称”、“参数”、“其他配置”和“状态目录”。******************** 然后选择 **“提交”**。

    ![“提交脚本”对话框，HDInsight Hadoop Hive 查询](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>创建 Hive 应用程序

若要通过创建 Hive 应用程序来运行 Hive 查询，请执行以下步骤：

1. 打开**视觉工作室**。

2. 在“开始”窗口中，选择“创建新项目”********。

3. 在“创建新项目”窗口中的“搜索模板”框内，输入 *Hive*。******** 然后依次选择“Hive 应用程序”、“下一步”。********

4. 在“配置新项目”窗口中输入一个**项目名称**，选择或创建新项目的**位置**，然后选择“创建”。********

5. 打开使用此项目创建的 **Script.hql** 文件，并在其中粘贴以下 HiveQL 语句：

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    这些语句执行以下操作：

    * `DROP TABLE`：如果表存在，则将其删除。

    * `CREATE EXTERNAL TABLE`：在 Hive 中创建一个新的“外部”表。 外部表只会在 Hive 中存储表定义。 （数据保留在原始位置。）

        > [!NOTE]  
        > 如果你预期基础数据会由外部源（例如 MapReduce 作业或 Azure 服务）更新，则应使用外部表。
        >
        > 删除外部表**不会**删除数据，只会删除表定义。

    * `ROW FORMAT`：告知 Hive 如何设置数据的格式。 在此情况下，每个日志中的字段以空格分隔。

    * `STORED AS TEXTFILE LOCATION`：告诉 Hive 数据存储在*示例/数据*目录中，并且以文本形式存储。

    * `SELECT`： 选择列`t4`包含值`[ERROR]`的所有行的计数。 此语句会返回值 `3`，因为有三个行包含此值。

    * `INPUT__FILE__NAME LIKE '%.log'`：告诉 Hive 仅从 以 .log 结尾的文件返回数据。 此子句将搜索限制为包含数据的*sample.log*文件。

6. 在查询文件工具栏（其外观与临时查询工具栏类似）中，选择要用于此查询的 HDInsight 群集。 然后，将“交互式”更改为“批处理”（如果需要），并选择“提交”以 Hive 作业形式运行语句。************

   “Hive 作业摘要”**** 会出现并显示有关正在运行的作业的信息。 在“作业状态”**** 更改为“已完成”**** 之前，使用“刷新”**** 链接刷新作业信息。

   ![已完成 Hive 作业摘要，Hive 应用程序，Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. 选择“作业输出”查看此作业的输出。**** 它显示 `[ERROR] 3`，这是此查询返回的值。

### <a name="additional-example"></a>其他示例

以下示例依赖于在前一过程[创建 Hive 应用程序](#create-a-hive-application)中创建的 `log4jLogs` 表。

1. 在“服务器资源管理器”**** 中，右键单击群集，然后选择“编写 Hive 查询”****。

2. 输入以下 Hive 查询：

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    这些语句执行以下操作：

    * `CREATE TABLE IF NOT EXISTS`：如果表不存在，则创建表。 由于未使用 `EXTERNAL` 关键字，因此此语句会创建内部表。 内部表存储在 Hive 数据仓库中，由 Hive 管理。

        > [!NOTE]  
        > 与 `EXTERNAL` 表不同，删除内部表会同时删除基础数据。

    * `STORED AS ORC`：以*优化的行列*（ORC） 格式存储数据。 ORC 是高度优化且有效的 Hive 数据存储格式。

    * `INSERT OVERWRITE ... SELECT`：从包含 `[ERROR]` 的 `log4jLogs` 表中选择行，然后将数据插入 `errorLogs` 表中。

3. 根据需要将“交互式”更改为“批处理”，然后选择“提交”。************

4. 若要验证该作业是否已创建表，请转到“服务器资源管理器”并展开“Azure” > “HDInsight”。************ 展开 HDInsight 群集，然后展开**Hive 数据库** > **默认值**。 此时会列出 **errorLogs** 表和 **log4jLogs** 表。

## <a name="next-steps"></a>后续步骤

可以看到，适用于 Visual Studio 的 HDInsight 工具可以轻松地在 HDInsight 上处理 Hive 查询。

* 有关 HDInsight 中的 Hive 的一般信息，请参阅[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什么？](hdinsight-use-hive.md)

* 有关在 HDInsight 上使用 Hadoop 的其他方法的信息，请参阅[在 HDInsight 上使用 Apache Hadoop 中的地图减少](hdinsight-use-mapreduce.md)

* 有关 Visual Studio 的 HDInsight 工具的详细信息，请参阅[使用可视化工作室的数据湖工具连接到 Azure HDInsight 并运行 Apache Hive 查询](apache-hadoop-visual-studio-tools-get-started.md)
