---
title: 在 HDInsight 中将 Apache Hive 与远程桌面配合使用 - Azure
description: 学习如何通过使用远程桌面连接到 HDInsight 中的 Hadoop 群集，并通过使用 Hive 命令行界面运行 Hive 查询。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 60bed4786b61bc96b918511b63ae89daa1cba1c4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217422"
---
# <a name="use-apache-hive-with-apache-hadoop-on-hdinsight-with-remote-desktop"></a>通过远程桌面将 Apache Hive 与 HDInsight 上的 Apache Hadoop 配合使用
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

本文介绍如何使用远程桌面连接到 HDInsight 群集，以及如何使用 Hive 命令行接口 (CLI) 运行 Apache Hive 查询。

> [!IMPORTANT]  
> 远程桌面只能在使用 Windows 作为操作系统的 HDInsight 群集上使用。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。
>
> 对于 HDInsight 3.4 或更高版本，请参阅[将 Apache hive 与 HDInsight 和 Beeline 配合使用](apache-hadoop-use-hive-beeline.md)来了解有关通过命令行直接在群集上运行 Hive 查询的信息。

## <a id="prereq"></a>先决条件
要完成本文中的步骤，需要：

* 基于 Windows 的 HDInsight（HDInsight 上的 Hadoop）群集
* 运行 Windows 10、Window 8 或 Windows 7 的客户端计算机

## <a id="connect"></a>使用远程桌面进行连接
为 HDInsight 群集启用远程桌面，并根据[使用 RDP 连接到 HDInsight 群集](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)中的说明连接到该群集。

## <a id="hive"></a>使用 Hive 命令
连接到 HDInsight 群集的桌面之后，请执行以下步骤来使用 Hive：

1. 从 HDInsight 桌面启动“Hadoop 命令行”。
2. 输入以下命令启动 Hive CLI：

        %hive_home%\bin\hive

    在启动 CLI 后，会看到 Hive CLI 提示符：`hive>`。
3. 在 CLI 中输入以下语句，以使用示例数据创建名为 **log4jLogs** 的新表：

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    这些语句将执行以下操作：

   * **DROP TABLE**：删除表和数据文件（如果该表已存在）。
   * **CREATE EXTERNAL TABLE**：在 Hive 中创建一个新的“外部”表。 外部表仅在 Hive 中存储表定义；数据会保留在原始位置。

     > [!NOTE]  
     > 预期以外部源更新基础数据（例如自动化数据上传过程），或以其他 MapReduce 操作更新基础数据，但希望 Hive 查询始终使用最新数据时，必须使用外部表。
     >
     > 删除外部表**不会**删除数据，只会删除表定义。
     >
     >
   * **ROW FORMAT**：让 Hive 知道数据的格式已如何进行了设置。 在此情况下，每个日志中的字段以空格分隔。
   * **STORED AS TEXTFILE LOCATION**：让 Hive 知道数据的存储位置（example/data 目录）以及数据已存储为文本。
   * **SELECT**：选择 **t4** 列包含值 **[ERROR]** 的所有行的计数。 这应会返回值 **3**，因为有三行包含此值。
   * **INPUT__FILE__NAME LIKE '%.log'** - 告诉 Hive，我们只应返回以 .log 结尾的文件中的数据。 此项将搜索限定于包含数据的 sample.log 文件，使搜索不会返回与所定义架构不符的其他示例数据文件中的数据。
4. 使用以下语句创建名为 **errorLogs** 的新“内部”表：

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    这些语句将执行以下操作：

   * **CREATE TABLE IF NOT EXISTS**：如果表不存在，则创建表。 由于未使用 **EXTERNAL** 关键字，因此这是一个内部表，它存储在 Hive 数据仓库中并完全受 Hive 的管理。

     > [!NOTE]  
     > 与**外部**表不同，删除内部表会同时删除基础数据。
     >
     >
   * **STORED AS ORC**：以优化的行纵栏式 (ORC) 格式存储数据。 这是高度优化且有效的 Hive 数据存储格式。
   * **INSERT OVERWRITE ...SELECT**：从包含 **[ERROR]** 的 **log4jLogs** 表中选择行，然后将数据插入 **errorLogs** 表中。

     要验证是否只将其列 t4 中包含 **[ERROR]** 的行存储到了 **errorLogs** 表，请使用以下语句从 **errorLogs** 中返回所有行：

       SELECT * from errorLogs;

     应返回三行数据，所有行都包含 t4 列中的 **[ERROR]**：

## <a id="summary"></a>摘要
如你所见，使用 Hive 命令可通过简单的方法以交互方式在 HDInsight 群集上运行 Hive 查询、监视作业状态，以及检索输出。

## <a id="nextsteps"></a>后续步骤
有关 HDInsight 中的 Hive 的一般信息：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)

如果将 Tez 与 Hive 配合使用，请参阅以下文档以了解调试信息：[在基于 Linux 的 HDInsight 上使用 Apache Ambari Tez 视图](../hdinsight-debug-ambari-tez-view.md)。

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
