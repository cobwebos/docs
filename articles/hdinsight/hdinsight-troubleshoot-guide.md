---
title: Azure HDInsight 故障排除指南
description: 使用 Azure HDInsight 对 Apache Hadoop 工作负荷进行故障排除。 此分步文档说明如何使用 HDInsight 解决 Apache Hive、Apache Spark、Apache YARN、Apache HBase、HDFS 和 Apache Storm 的常见问题。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 4460c24bb742bff72af47954363b1051f5d9b43d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895275"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>使用 Azure HDInsight 进行故障排除

| Apache 工作负荷 | 最常见的问题 |
|---|---|
|![hdinsight apache HBase 图标图标](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Apache HBase 故障排除](hbase/apache-troubleshoot-hbase.md)|<br>[未分配区域](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Azure HDInsight 中的“hbase hbck”命令超时](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Azure HDInsight 中的 Apache Phoenix 连接问题](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[哪些因素会导致主服务器无法启动？](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException-地址已在使用中](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![hdinsight apache hdfs 图标图标](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Apache Hadoop HDFS 故障排除](hdinsight-troubleshoot-hdfs.md)|<br>[如何从群集内访问本地 HDFS？](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[本地 HDFS 在 Azure HDInsight 群集上的安全模式下停滞](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![hdinsight apache Hive 图标图标](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[排查 Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[如何导出 Hive 元存储并将其导入另一个群集？](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[如何在群集上查找 Apache Hive 日志？](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[如何在群集上使用特定配置启动 Apache Hive shell？](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[如何在群集关键路径上分析 Apache Tez DAG 数据？](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[如何从群集下载 Apache Tez DAG 数据？](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![hdinsight apache Spark 图标图标](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Apache Spark 故障排除](hdinsight-troubleshoot-SPARK.md)|<br>[如何在群集上使用 Apache Ambari 配置 Apache Spark 应用程序？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[如何在群集上使用 Jupyter Notebook 配置 Apache Spark 应用程序？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[如何在群集上使用 Apache Livy 配置 Apache Spark 应用程序？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[如何在群集上使用 spark-submit 配置 Apache Spark 应用程序？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[如何使用 IntelliJ 配置 Apache Spark 应用程序？](spark/apache-spark-intellij-tool-plugin.md)<br><br>[如何使用 Eclipse 配置 Apache Spark 应用程序？](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[如何使用 VSCode 配置 Apache Spark 应用程序？](hdinsight-for-vscode.md)<br><br>[Apache Spark 的 OutOfMemoryError 异常](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![hdinsight apache 风暴图标图标](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Apache Storm 故障排除](hdinsight-troubleshoot-STORM.md)|<br>[如何在群集上访问 Apache Storm UI？](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[如何将 Apache Storm 事件中心 Spout 检查点信息从一个拓扑传输到另一个拓扑？](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[如何在群集上查找 Storm 二进制文件？](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[如何确定 Storm 群集的部署拓扑？](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[如何查找用于开发的 Apache Storm 事件中心 Spout 二进制文件？](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![hdinsight apache YARN 图标图标](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Apache Hadoop YARN 故障排除](hdinsight-troubleshoot-YARN.md)|<br>[如何在群集上创建新的 Apache Hadoop YARN 队列？](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[如何从群集下载 Apache Hadoop YARN 日志？](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight 故障排除资源

| 有关以下方面的信息 | 请参阅以下文章： |
| --- | --- |
| HDInsight on Linux 和优化 | - [有关在 Linux 上使用 HDInsight 的信息](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop 内存和性能故障排除](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive 查询性能](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| 日志和转储 | - [访问 Linux 上的 Apache Hadoop YARN 应用程序日志](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [为 Linux 上的 Apache Hadoop 服务启用堆转储](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [分析 HDInsight 日志](hdinsight-debug-jobs.md)|
| 错误 | - [了解和解决 WebHCat 错误](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [用于解决 OutofMemory 错误的 Apache Hive 设置](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| 工具 | - [优化 Apache Hive 查询](hdinsight-hadoop-optimize-hive-query.md)<br>- [HDInsight IntelliJ 工具](./spark/apache-spark-intellij-tool-plugin.md)<br>- [HDInsight Eclipse 工具](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [HDInsight VSCode 工具](hdinsight-for-vscode.md)<br>- [HDInsight Visual Studio 工具](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。