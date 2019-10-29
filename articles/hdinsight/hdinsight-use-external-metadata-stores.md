---
title: 使用外部元数据存储 - Azure HDInsight
description: 在 Azure HDInsight 群集中使用外部元数据存储，并使用最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: a876269b2746a1065cee2639cfc5804aff0b3446
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027702"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>使用外部元数据存储 - Azure HDInsight

HDInsight 中的 Apache Hive 元存储是 Apache Hadoop 体系结构的必备部分。 元存储是可供其他大数据访问工具（例如 Apache Spark、交互式查询 (LLAP)、Presto 或 Apache Pig）使用的中央架构存储库。 HDInsight 使用 Azure SQL 数据库作为 Hive 元存储。

![HDInsight Hive 元数据存储体系结构](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

可使用以下两种方式为 HDInsight 存储设置元存储：

* [默认元存储](#default-metastore)
* [自定义元存储](#custom-metastore)

## <a name="default-metastore"></a>默认元存储

默认情况下，HDInsight 为每一种群集类型创建一个元存储。 转而可指定自定义元存储。 默认元存储包括以下注意事项：

* 没有任何额外费用。 HDInsight 会为每个群集类型创建一个元存储，而不额外产生任何费用。

* 每个默认元存储都是群集生命周期的一部分。 删除群集时，会一并删除相应的元存储和元数据。

* 不能与其他群集共享默认元存储。

* 默认元存储使用基本 Azure SQL DB，它具有 5 个 DTU（数据库事务单位）的限制。
此默认元存储通常用于相对简单的工作负荷，这种工作负荷无需使用多个群集，且无需在群集生命周期结束后继续保留元数据。

## <a name="custom-metastore"></a>自定义元存储

HDInsight 还支持自定义元存储，建议对生产群集使用此项：

* 将自己的 Azure SQL 数据库指定为元存储。

* 元存储的生命周期不与群集生命周期相关联，因此你可以在不丢失元数据的情况下创建和删除群集。 即使删除和重新创建 HDInsight 群集之后，系统仍然保留 Hive 架构等元数据。

* 通过自定义元存储，可将多个群集和群集类型附加到元存储。 例如，可跨交互式查询、Hive 和 HDInsight 中的群集的 Spark 共享单个元存储。

* 根据所选的性能级别收取元存储 (Azure SQL DB) 的费用。

* 可按需增加元存储。

![HDInsight Hive 元数据存储使用案例](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>在群集创建期间选择自定义元存储

可在群集创建期间将群集指向之前所创建的 Azure SQL 数据库，还可在创建群集之后配置 SQL 数据库。 在从 Azure 门户创建新的 Hadoop、Spark 或交互式 Hive 群集时，此选项与**存储 > 元存储设置**一起指定。

![HDInsight Hive 元数据存储 Azure 门户](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

还可通过 Azure 门户或 Ambari 配置（“Hive”>“高级”）向自定义云存储添加其他群集

![HDInsight Hive 元数据存储 Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive 元存储最佳做法

下面是一些常见的 HDInsight Hive 元存储最佳做法：

* 在适用时尽量使用自定义元存储，以帮助将计算资源（运行中的群集）和元数据（保存在元存储中）分开。

* 首先使用 S2 层，它提供 50 DTU 和 250 GB 的存储空间。 如果空间不够，可扩大数据库。

* 如果你打算使用多个 HDInsight 群集来访问单独的数据，请为每个群集上的元存储使用单独的数据库。 如果在多个 HDInsight 群集之间共享元存储，则表示群集使用相同的元数据和基础用户数据文件。

* 请定期备份自定义元存储。 Azure SQL 数据库自动生成备份，但备份保留期的时间范围会有所不同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../sql-database/sql-database-automated-backups.md)。

* 将元存储和 HDInsight 群集放置在同一区域中，以获得最高的性能和最低的网络出口费用。

* 使用 Azure SQL 数据库监视工具（例如 Azure 门户或 Azure Monitor 日志）监视元存储的性能和可用性。

* 对现有的自定义元存储数据库创建更高版本的新 Azure HDInsight 时，系统会升级元存储的架构，这是不可逆转的，无法从备份还原数据库。

* 如果在多个群集之间共享元存储，请确保所有群集都采用相同的 HDInsight 版本。 不同的 Hive 版本使用不同的元存储数据库架构。 例如，不能跨 Hive 2.1 和 Hive 3.1 版本控制群集共享元存储。

* 在 HDInsight 4.0 中，Spark 和 Hive 使用独立目录来访问 SparkSQL 或 Hive 表。 Spark 创建的表驻留在 Spark 目录中。 Hive 创建的表驻留在 Hive 目录中。 这不同于 HDInsight 3.6，其中 Hive 和 Spark 共享公共目录。 HDInsight 4.0 中的 hive 和 Spark 集成依赖于 Hive 仓库连接器（HWC）。 HWC 在 Spark 和 Hive 之间起到桥梁作用。 [了解 Hive 仓库连接器](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)。

## <a name="apache-oozie-metastore"></a>Apache Oozie 元存储

Apache Oozie 是一个管理 Hadoop 作业的工作流协调系统。  Oozie 支持对 Apache MapReduce、Pig 和 Hive 等模型执行 Hadoop 作业。  Oozie 使用元存储来存储当前工作流及历史工作流的相关详细信息。 可使用 Azure SQL 数据库作为自定义元存储，提高使用 Oozie 时的性能。 删除群集后，还可通过云存储访问 Oozie 作业数据。

若要了解如何使用 Azure SQL 数据库创建 Oozie 元存储，请参阅[使用 Apache Oozie 处理工作流](hdinsight-use-oozie-linux-mac.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他组件在 HDInsight 中设置群集](./hdinsight-hadoop-provision-linux-clusters.md)
