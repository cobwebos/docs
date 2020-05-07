---
title: Azure HDInsight Apache Spark 准则
description: 了解在 Azure HDInsight 中使用 Apache Spark 的准则。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 01cdc121abded954c2443599c5d69689acd69b62
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562332"
---
# <a name="apache-spark-guidelines"></a>Apache Spark 准则

本文提供了有关在 Azure HDInsight 上使用 Apache Spark 的各种指导原则。

## <a name="how-do-i-run-or-submit-spark-jobs"></a>如何运行或提交 Spark 作业？

| 选项 | 文档 |
|---|---|
| VSCode | [使用适用于 Visual Studio Code 的 Spark 和 Hive 工具](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [教程：在 Azure HDInsight 中的 Apache Spark 群集上加载数据并运行查询](./apache-spark-load-data-run-query.md) |
| IntelliJ | [教程：使用 Azure Toolkit for IntelliJ 为 HDInsight 群集创建 Apache Spark 应用程序](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [教程：使用 IntelliJ 在 HDInsight 中创建适用于 Apache Spark 的 Scala Maven 应用程序](./apache-spark-create-standalone-application.md) |
| Zeppelin 笔记本 | [在 Azure HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](./apache-spark-zeppelin-notebook.md) |
| 使用 Livy 进行远程作业提交 | [使用 Apache Spark REST API 将远程作业提交到 HDInsight Spark 群集](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie 是一个管理 Hadoop 作业的工作流和协调系统。|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|可以使用 Livy 运行交互式 Spark shell，或提交要在 Spark 上运行的批处理作业。|
|[适用于 Apache Spark 的 Azure 数据工厂](../../data-factory/transform-data-using-spark.md)|数据工厂管道中的 Spark 活动在自己的或 [按需 HDInsight 群集上执行 Spark 程序。|
|[适用于 Apache Hive 的 Azure 数据工厂](../../data-factory/transform-data-using-hadoop-hive.md)|数据工厂管道中的 HDInsight Hive 活动会在自己的或按需 HDInsight 群集上执行 Hive 查询。|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>如何监视和调试 Spark 作业？

| 选项 | 文档 |
|---|---|
| Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ 进行失败 Spark 作业调试（预览）](apache-spark-intellij-tool-failure-debug.md) |
| 通过 SSH 使用的 Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ 通过 SSH 在本地或远程调试 HDInsight 群集上的 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| 通过 VPN 使用的 Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ 通过 VPN 在 HDInsight 中远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Apache Spark History Server 上的作业图形 | [使用扩展的 Apache Spark History Server 调试和诊断 Apache Spark 应用程序](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>如何使 Spark 作业更高效地运行？

| 选项 | 文档 |
|---|---|
| IO 缓存 | [使用 Azure HDInsight IO 缓存提高 Apache Spark 工作负载的性能（预览版）](./apache-spark-improve-performance-iocache.md) |
| 配置选项 | [优化 Apache Spark 作业](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>如何连接到其他 Azure 服务？

| 选项 | 文档 |
|---|---|
| HDInsight 上的 Apache Hive | [将 Apache Spark 和 Apache Hive 与 Hive Warehouse Connector 集成](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase on HDInsight | [使用 Apache Spark 读取和写入 Apache HBase 数据](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka on HDInsight | [教程：将 Apache Spark 结构化流式处理与 Apache Kafka on HDInsight 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB：在 Azure 平台上实现 lambda 体系结构](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>可以使用哪些存储选项？

| 选项 | 文档 |
|---|---|
| Data Lake Storage Gen2 | [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [将 Data Lake Storage Gen1 与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob 存储 | [将 Azure 存储与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>后续步骤

* [配置 Apache Spark 设置](apache-spark-settings.md)
* [在 HDInsight 中优化 Apache Spark 作业](apache-spark-perf.md)
