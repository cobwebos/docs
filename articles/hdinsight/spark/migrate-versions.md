---
title: 将 Apache Spark 2.1 或 2.2 工作负载迁移到 2.3 或 2.4 - Azure HDInsight
description: 了解如何将 Apache Spark 2.1 和 2.2 迁移到 2.3 或 2.4。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1ce9b0faa6636d1318871cc9ef66cfbe47908265
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504972"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>将 Apache Spark 2.1 和2.2 工作负载迁移到 2.3 和 2.4

本文档介绍如何将 Spark 2.1 和 2.2 上的 Apache Spark 工作负载迁移到 2.3 或 2.4。

如[发行说明](../hdinsight-release-notes.md#upcoming-changes)中所述，从 2020 年 7 月 1 日开始，将不支持以下群集配置，并且客户将无法创建具有这些配置的新群集：
 - HDInsight 3.6 Spark 群集中的 Spark 2.1 和 2.2
 - HDInsight 4.0 Spark 群集中的 Spark 2.3

这些配置中的现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前将 HDInsight 3.6 上的 Spark 2.1 或 2.2 转移到 HDInsight 3.6 上的 Spark 2.3，以避免潜在的系统/支持中断。 请考虑在 2020 年 6 月 30 日之前将 HDInsight 4.0 上的 Spark 2.3 转移到 HDInsight 4.0 上的 Spark 2.4，以避免潜在的系统/支持中断。

有关将 HDInsight 群集从 3.6 迁移到 4.0 的一般信息，请参阅[将 HDInsight 群集迁移到较新版本](../hdinsight-upgrade-cluster.md)。 有关迁移到较新版 Apache Spark 的一般信息，请参阅 [Apache Spark：版本控制策略](https://spark.apache.org/versioning-policy.html)。

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>有关 HDInsight 上 Spark 版本升级的指南

| 升级方案 | 机制 | 注意事项 | Spark/Hive 集成 |
|------------------|-----------|--------------------|------------------------|
|从 HDInsight 3.6 Spark 2.1 升级到 HDInsight 3.6 Spark 2.3| 使用 HDInsight Spark 2.3 重新创建群集 | 请参阅以下文章： <br> [Apache Spark：从 Spark SQL 2.2 升级到 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark：从 Spark SQL 2.1 升级到 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | 无更改 |
|从 HDInsight 3.6 Spark 2.2 升级到 HDInsight 3.6 Spark 2.3 | 使用 HDInsight Spark 2.3 重新创建群集 | 请参阅以下文章： <br> [Apache Spark：从 Spark SQL 2.2 升级到 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | 无更改 |
| 从 HDInsight 3.6 Spark 2.1 升级到 HDInsight 4.0 Spark 2.4 | 使用 HDInsight 4.0 Spark 2.4 重新创建群集 | 请参阅以下文章： <br> [Apache Spark：从 Spark SQL 2.3 升级到 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark：从 Spark SQL 2.2 升级到 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark：从 Spark SQL 2.1 升级到 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Spark 和 Hive 集成在 HDInsight 4.0 中已更改。 <br><br> 在 HDInsight 4.0 中，Spark 和 Hive 使用独立目录来访问 SparkSQL 或 Hive 表。 Spark 创建的表位于 Spark 目录中。 Hive 创建的表位于 Hive 目录中。 这与 HDInsight 3.6 不同，在 HDInsight 3.6 中，Hive 和 Spark 共享公共目录。 HDInsight 4.0 中的 Hive 和 Spark 集成依赖于 Hive Warehouse Connector (HWC)。 HWC 充当 Spark 和 Hive 之间的桥梁。 了解 Hive Warehouse Connector。 <br> 在 HDInsight 4.0 中，如果要在 Hive 和 Spark 之间共享元存储，可以通过在 Spark 群集中将 metastore.catalog.default 属性更改为 hive。 可在 Ambari Advanced spark2-hive-site-override 中找到此属性。 必须了解，元存储的共享仅适用于外部 hive 表，如果有内部/托管 hive 表或 ACID 表，这将不起作用。 <br><br>有关详细信息，请参阅[将 Azure HDInsight 3.6 Hive 工作负载迁移到 HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)。<br><br> |
| 从 HDInsight 3.6 Spark 2.2 升级到 HDInsight 4.0 Spark 2.4 | 使用 HDInsight 4.0 Spark 2.4 重新创建群集 | 请参阅以下文章： <br> [Apache Spark：从 Spark SQL 2.3 升级到 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark：从 Spark SQL 2.2 升级到 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Spark 和 Hive 集成在 HDInsight 4.0 中已更改。 <br><br> 在 HDInsight 4.0 中，Spark 和 Hive 使用独立目录来访问 SparkSQL 或 Hive 表。 Spark 创建的表位于 Spark 目录中。 Hive 创建的表位于 Hive 目录中。 这与 HDInsight 3.6 不同，在 HDInsight 3.6 中，Hive 和 Spark 共享公共目录。 HDInsight 4.0 中的 Hive 和 Spark 集成依赖于 Hive Warehouse Connector (HWC)。 HWC 充当 Spark 和 Hive 之间的桥梁。 了解 Hive Warehouse Connector。 <br> 在 HDInsight 4.0 中，如果要在 Hive 和 Spark 之间共享元存储，可以通过在 Spark 群集中将 metastore.catalog.default 属性更改为 hive。 可在 Ambari Advanced spark2-hive-site-override 中找到此属性。 必须了解，元存储的共享仅适用于外部 hive 表，如果有内部/托管 hive 表或 ACID 表，这将不起作用。 <br><br>有关详细信息，请参阅[将 Azure HDInsight 3.6 Hive 工作负载迁移到 HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)。|

## <a name="next-steps"></a>后续步骤

* [将 HDInsight 群集迁移到较新版本](../hdinsight-upgrade-cluster.md)
* [将 Azure HDInsight 3.6 Hive 工作负载迁移到 HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)。
