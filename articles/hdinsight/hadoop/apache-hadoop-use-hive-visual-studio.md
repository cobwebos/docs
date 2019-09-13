---
title: 适用于 Visual Studio 的 Data Lake 工具 Apache Hive-Azure HDInsight
description: 了解如何使用用于 Visual Studio 的 Data Lake 工具，通过 Azure HDInsight 上的 Apache Hadoop 运行 Apache Hive 查询。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 129f200bc9f61d70f4403b1154978d57e09fee26
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917487"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具运行 Apache Hive 查询

了解如何使用针对 Visual Studio 的 Data Lake 工具查询 Apache Hive。 使用 Data Lake 工具，可以轻松创建 Hive 查询，将其提交到 Azure HDInsight 上的 Apache Hadoop 并进行监视。

## <a id="prereq"></a>先决条件

* HDInsight 中的 Apache Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* Visual Studio（以下版本之一）：

    * Visual Studio 2015、2017（任何版本）
    * 包含 Update 4 的 Visual Studio 2013 Community/Professional/Premium/Ultimate

* Visual Studio 的 HDInsight 工具或 Visual Studio 的 Azure Data Lake 工具。 请参阅 [Get started using Visual Studio Hadoop tools for HDInsight](apache-hadoop-visual-studio-tools-get-started.md)（开始使用 Visual Studio Hadoop tools for HDInsight），了解如何安装和配置这些工具。

## <a id="run"></a> 使用 Visual Studio 运行 Apache Hive 查询

可以使用两个选项来创建并运行 Hive 查询：

* 创建即席查询
* 创建 Hive 应用程序

### <a name="ad-hoc"></a>即席

即席查询可以**批处理**或**交互式**模式执行。

1. 打开 **Visual Studio**。

2. 在“服务器资源管理器”中，导航到“Azure” > “HDInsight”。

3. 展开“HDInsight”，右键单击要运行查询的群集，然后选择“编写 Hive 查询”。

4. 输入以下 Hive 查询：

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. 选择“执行”。 请注意，执行模式默认设置为“交互式”。

    ![执行交互式 Hive 查询的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 若要以**批处理**模式下运行同一查询，请将下拉列表从“交互式”切换到“批处理”。 请注意，执行按钮将从“执行”更改为“提交”。

    ![提交 Hive 查询的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive 编辑器支持 IntelliSense。 用于 Visual Studio 的 Data Lake 工具支持在编辑 Hive 脚本时加载远程元数据。 例如，如果键入 `SELECT * FROM`，则 IntelliSense 会列出所有建议的表名称。 在指定表名称后，IntelliSense 会列出列名称。 这些工具支持大多数 Hive DML 语句、子查询和内置 UDF。 IntelliSense 只建议 HDInsight 工具栏中所选群集的元数据。

    ![HDInsight Visual Studio Tools IntelliSense 示例 1 的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools IntelliSense 示例 2 的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. 选择“提交”或“提交(高级)”。

   如果选择高级提交选项，请为脚本配置“作业名称”、“参数”、“其他配置”和“状态目录”：

    ![HDInsight Hadoop Hive 查询的屏幕截图](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "提交查询")

### <a name="hive-application"></a>Hive 应用程序

1. 打开 **Visual Studio**。

2. 在菜单栏中，导航到“文件” > “新建” > “项目”。

3. 在“新建项目”窗口中，导航至“模板” > “Azure Data Lake” > “HIVE (HDInsight)” > “Hive 应用程序”。 

4. 提供此项目的名称，然后选择“确定”。

5. 打开使用此项目创建的 **Script.hql** 文件，并在其中粘贴以下 HiveQL 语句：

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    这些语句将执行以下操作：

   * `DROP TABLE`：如果表存在，此语句会将其删除。

   * `CREATE EXTERNAL TABLE`：在 Hive 中创建一个新的“外部”表。 外部表仅在 Hive 中存储表定义；数据会保留在原始位置。

     > [!NOTE]  
     > 如果希望通过外部源更新基础数据，应使用外部表。 例如，MapReduce 作业或 Azure 服务。
     >
     > 删除外部表**不会**删除数据，只会删除表定义。

   * `ROW FORMAT`：让 Hive 知道数据的格式已如何进行了设置。 在此情况下，每个日志中的字段以空格分隔。

   * `STORED AS TEXTFILE LOCATION`：告知 Hive 数据已以文本形式存储在 example/data 目录中。

   * `SELECT`：选择 `t4` 列包含值 `[ERROR]` 的所有行计数。 此语句返回值 `3`，因为有三行包含此值。

   * `INPUT__FILE__NAME LIKE '%.log'` - 告诉 Hive，我们只应返回以 .log 结尾的文件中的数据。 此子句将搜索限定为包含数据的 sample.log 文件。

6. 在工具栏中，选择需要用于此查询的“HDInsight 群集”。 选择“提交”，让语句以 Hive 作业的形式运行。

   ![“提交”栏](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. “Hive 作业摘要”将会出现并显示有关正在运行的作业的信息。 在“作业状态”更改为“已完成”之前，使用“刷新”链接刷新作业信息。

   ![作业摘要，显示已完成的作业](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. 使用“作业输出”链接查看此作业的输出。 它显示 `[ERROR] 3`，这是此查询返回的值。

### <a name="additional-example"></a>其他示例

此示例依赖于前一步骤中创建的 `log4jLogs` 表。

1. 在“服务器资源管理器”中，右键单击群集，然后选择“编写 Hive 查询”。

2. 输入以下 Hive 查询：

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    这些语句将执行以下操作：

    * `CREATE TABLE IF NOT EXISTS`：如果表不存在，则创建表。 因为未使用 `EXTERNAL` 关键字，此语句创建内部表。 内部表存储在 Hive 数据仓库中，由 Hive 管理。
    
    > [!NOTE]  
    > 与 `EXTERNAL` 表不同，删除内部表会同时删除基础数据。

    * `STORED AS ORC`：以优化的行纵栏式 (ORC) 格式存储数据。 ORC 是高度优化且有效的 Hive 数据存储格式。
    
    * `INSERT OVERWRITE ... SELECT`：从包含 `[ERROR]` 的 `log4jLogs` 表中选择行，然后将数据插入 `errorLogs` 表中。

3. 以**批处理**模式执行查询。

4. 若要验证作业是否已创建表，请使用“服务器资源管理器”，然后展开“Azure” > “HDInsight”> 用户的 HDInsight 群集 >“Hive 数据库” > “默认值”。 此时会列出 **errorLogs** 表和 **log4jLogs** 表。

## <a id="nextsteps"></a>后续步骤

可以看到，适用于 Visual Studio 的 HDInsight 工具可以轻松地在 HDInsight 上处理 Hive 查询。

有关 HDInsight 中的 Hive 的一般信息：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)

* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)

有关适用于 Visual Studio 的 HDInsight 工具的详细信息：

* [用于 Visual Studio 的 HDInsight 工具入门](apache-hadoop-visual-studio-tools-get-started.md)