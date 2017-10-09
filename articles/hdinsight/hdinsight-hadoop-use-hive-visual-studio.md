---
title: "将 Hive 与用于 Visual Studio 的 Data Lake (Hadoop) 工具配合使用 — Azure HDInsight | Microsoft Docs"
description: "了解如何使用用于 Visual Studio 的 Data Lake 工具，通过 Azure HDInsight 上的 Apache Hadoop 运行 Apache Hive 查询。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: b9a8f857f42f16ec7735a79ec29e77e3c99c8ba7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/29/2017

---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具运行 Hive 查询

了解如何使用针对 Visual Studio 的 Data Lake 工具查询 Apache Hive。 使用 Data Lake 工具，可以轻松创建 Hive 查询，将其提交到 Azure HDInsight 上的 Hadoop 并进行监视。

## <a id="prereq"></a>先决条件

* Azure HDInsight（HDInsight 上的 Hadoop）群集

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* Visual Studio（以下版本之一）：

    * 包含 Update 4 的 Visual Studio 2013 Community/Professional/Premium/Ultimate

    * Visual Studio 2015（任何版本）

    * Visual Studio 2017（任何版本）

* Visual Studio 的 HDInsight 工具或 Visual Studio 的 Azure Data Lake 工具。 请参阅 [Get started using Visual Studio Hadoop tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md)（开始使用 Visual Studio Hadoop tools for HDInsight），了解如何安装和配置这些工具。

## <a id="run"></a>使用 Visual Studio 运行 Hive 查询

1. 打开“Visual Studio”，选择“新建” > “项目” > “Azure Data Lake” > “HIVE” > “Hive 应用程序”。 提供此项目的名称。

2. 打开使用此项目创建的 **Script.hql** 文件，并在其中粘贴以下 HiveQL 语句：

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

   * `ROW FORMAT`：告知 Hive 如何设置数据的格式。 在此情况下，每个日志中的字段以空格分隔。

   * `STORED AS TEXTFILE LOCATION`：告知 Hive 数据已以文本形式存储在 example/data 目录中。

   * `SELECT`：选择 `t4` 列包含值 `[ERROR]` 的所有行计数。 此语句返回值 `3`，因为有三行包含此值。

   * `INPUT__FILE__NAME LIKE '%.log'` - 告诉 Hive，我们只应返回以 .log 结尾的文件中的数据。 此子句将搜索限定为包含数据的 sample.log 文件。

3. 在工具栏中，选择需要用于此查询的“HDInsight 群集”。 选择“提交”，让语句以 Hive 作业的形式运行。

   ![“提交”栏](./media/hdinsight-hadoop-use-hive-visual-studio/toolbar.png)

4. “Hive 作业摘要”会出现并显示有关正在运行的作业的信息。 在“作业状态”更改为“已完成”之前，使用“刷新”链接刷新作业信息。

   ![作业摘要，显示已完成的作业](./media/hdinsight-hadoop-use-hive-visual-studio/jobsummary.png)

5. 使用“作业输出”链接查看此作业的输出。 它显示 `[ERROR] 3`，这是此查询返回的值。

6. 也可以运行 Hive 查询，而无需创建项目。 使用“服务器资源管理器”，展开“Azure” > “HDInsight”，右键单击 HDInsight 服务器，并选择“编写 Hive 查询”。

7. 在出现的 **temp.hql** 文档中，添加以下 HiveQL 语句：

   ```hiveql
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

8. 从工具栏中，选择“提交”以运行该作业。 使用“作业状态”确定作业是否已成功完成。

9. 若要验证作业是否已创建表，请使用“服务器资源管理器”，然后展开“Azure” > “HDInsight”> 用户的 HDInsight 群集 >“Hive 数据库” > “默认值”。 此时会列出 **errorLogs** 表和 **log4jLogs** 表。

## <a id="nextsteps"></a>后续步骤

可以看到，适用于 Visual Studio 的 HDInsight 工具可以轻松地在 HDInsight 上处理 Hive 查询。

有关 HDInsight 中的 Hive 的一般信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)

* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)

有关适用于 Visual Studio 的 HDInsight 工具的详细信息：

* [Visual Studio 的 HDInsight 工具入门](hdinsight-hadoop-visual-studio-tools-get-started.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

