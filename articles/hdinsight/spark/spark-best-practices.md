---
title: Azure HDInsight 上的 Apache Spark 最佳实践
description: 了解在 Azure HDInsight 中使用 Apache Spark 的最佳实践。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106120"
---
# <a name="apache-spark-best-practices"></a>Apache Spark 最佳实践

本文提供了有关在 Azure HDInsight 上使用 Apache Spark 的各种最佳实践。

## <a name="how-do-i-run-or-submit-spark-jobs"></a>如何实现运行或提交 Spark 作业吗？

| 选项 | 文档 |
|---|---|
| VSCode | [为 Visual Studio Code 使用 Spark & Hive 工具](../hdinsight-for-vscode.md) |
| Jupyter 笔记本 | [教程：在 Azure HDInsight 中的 Apache Spark 群集上加载数据和运行查询](./apache-spark-load-data-run-query.md) |
| IntelliJ | [教程：使用 Azure Toolkit for IntelliJ 为 HDInsight 群集创建 Apache Spark 应用程序](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [教程：使用 IntelliJ 为 HDInsight 中的 Apache Spark 创建 Scala Maven 应用程序](./apache-spark-create-standalone-application.md) |
| Zeppelin 笔记本 | [在 Azure HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](./apache-spark-zeppelin-notebook.md) |
| 通过 Livy 的远程作业提交 | [使用 Apache Spark REST API 将远程作业提交到 HDInsight Spark 群集](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>如何实现监视和调试 Spark 作业？

| 选项 | 文档 |
|---|---|
| Azure Toolkit for IntelliJ | [用 Azure Toolkit for IntelliJ 进行的 spark 作业调试失败（预览版）](apache-spark-intellij-tool-failure-debug.md) |
| 通过 SSH Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ 通过 SSH 在本地或远程调试 HDInsight 群集上的 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| 通过 VPN Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ 通过 VPN 在 HDInsight 中远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Apache Spark History Server 上的作业关系图 | [使用扩展的 Apache Spark History Server 调试和诊断 Apache Spark 应用程序](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>如何实现使 Spark 作业更有效地运行？

| 选项 | 文档 |
|---|---|
| IO 缓存 | [使用 Azure HDInsight IO 缓存（预览版）改善 Apache Spark 工作负荷的性能](./apache-spark-improve-performance-iocache.md) |
| 配置选项 | [优化 Apache Spark 作业](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>如何实现连接到其他 Azure 服务？

| 选项 | 文档 |
|---|---|
| HDInsight 上的 Apache Hive | [将 Apache Spark 和 Apache Hive 与 Hive Warehouse Connector 集成](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase on HDInsight | [使用 Apache Spark 读取和写入 Apache HBase 数据](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka on HDInsight | [教程：在 HDInsight 上使用 Apache Kafka Apache Spark 结构化流式处理](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB：在 Azure 平台上实现 lambda 体系结构](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>我的存储选项有哪些？

| 选项 | 文档 |
|---|---|
| Data Lake Storage Gen2 | [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [将 Data Lake Storage Gen1 与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob 存储 | [将 Azure 存储与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>后续步骤

* [配置 Apache Spark 设置](apache-spark-settings.md)
* [优化 HDInsight 中的 Apache Spark 作业](apache-spark-perf.md)
