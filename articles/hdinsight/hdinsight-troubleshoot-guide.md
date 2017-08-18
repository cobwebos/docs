---
title: "Azure HDInsight 文档 - 故障排除指南 | Microsoft Docs"
description: "对 HDInsight 上的 Hadoop 工作负荷进行故障排除。分步说明文档介绍了如何解决 HDInsight 上的 Hive、Spark、HBase、Storm 和 Kafka 常见问题。"
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 9313f0713818f80bae99c32a438d97ba544a7a6b
ms.contentlocale: zh-cn
ms.lasthandoff: 08/17/2017

---


# <a name="azure-hdinsight-troubleshooting"></a>Azure HDInsight 故障排除

| Apache 工作负荷 | 最常见的问题 |
|---|---|
|![HBASE](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[HBASE 故障排除](hdinsight-troubleshoot-hbase.md)|<br>[如何对多个未分配区域运行 hbck 命令报告](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[如何纠正针对区域分配的 hbck 命令的超时问题](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[如何在群集中强制禁用 HDFS 安全模式](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-an-cluster)<br><br>[如何纠正 Apache Phoenix 的 JDBC 或 sqlline 连接问题](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[导致主服务器无法启动的原因](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[导致在区域服务器上重启失败的原因](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[HDFS 故障排除](hdinsight-troubleshoot-hdfs.md)|<br>[如何从群集内访问本地 HDFS](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[如何在群集中强制禁用 HDFS 安全模式](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![HIVE](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[HIVE 故障排除](hdinsight-troubleshoot-hive.md)|<br>[如何导出 Hive 元存储并将其导入另一个群集](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[如何在群集上查找 Hive 日志](hdinsight-troubleshoot-hive.md#how-do-locate-hive-logs-on-a-cluster)<br><br>[如何在群集上使用特定的配置启动 Hive shell](hdinsight-troubleshoot-hive.md#how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[如何在群集关键路径上分析 Tez DAG 数据](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[如何从群集下载 Tez DAG 数据](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![SPARK](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Spark 故障排除](hdinsight-troubleshoot-SPARK.md)|<br>[如何在群集上通过 Ambari 配置 Spark 应用程序](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-ambari-on-clusters)<br><br>[如何在群集上通过 Jupyter Notebook 配置 Spark 应用程序](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters)<br><br>[如何在群集上通过 LIVY 配置 Spark 应用程序](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-livy-on-clusters)<br><br>[如何在群集上通过 spark-submit 配置 Spark 应用程序](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-spark-submit-on-clusters)<br><br>[哪些因素会导致 Spark 应用程序出现 OutOfMemoryError 异常](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[STORM 故障排除](hdinsight-troubleshoot-STORM.md)|<br>[如何在群集上访问 Storm UI](hdinsight-troubleshoot-storm.md#how-do-i-access-storm-ui-on-a-cluster)<br><br>[如何将 Storm 事件中心 Spout 检查点信息从一个拓扑传输到另一个拓扑](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[如何在群集上查找 Storm 二进制文件](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[如何确定 Storm 群集的部署拓扑](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[如何查找用于开发的 Storm 事件中心 Spout 二进制文件](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-eventhub-spout-binaries-for-development)<br><br>[如何在群集上查找 Storm Log4J 配置文件](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[YARN 故障排除](hdinsight-troubleshoot-YARN.md)|<br>[如何在群集上创建新的 Yarn 队列](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[如何从群集下载 Yarn 日志](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight 故障排除资源
请参阅以下文章，获取更多的故障排除帮助。

| 有关以下方面的信息 | 请参阅以下资源 |
| --- | --- |
| HDInsight on Linux 和优化 | [有关在 Linux 上使用 HDInsight 的信息](hdinsight-hadoop-linux-information.md)<br>[Hadoop 内存和性能](hdinsight-hadoop-stack-trace-error-messages.md)<br>[Hive 查询性能](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| 日志和转储 | [在 Linux 上访问 Hadoop YARN 应用程序日志](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>[为 Hadoop 服务启用堆转储](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| 错误 | [了解和解决 WebHCat 错误](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>[通过 Hive 设置解决内存不足错误](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| 工具 | [使用 Ambari 视图调试 Tez 作业](hdinsight-debug-ambari-tez-view.md)<br>[优化 Hive 查询](hdinsight-hadoop-optimize-hive-query.md) |



