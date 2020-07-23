---
title: 使用外部元数据存储 - Azure HDInsight
description: 在 Azure HDInsight 群集中使用外部元数据存储。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/30/2020
ms.openlocfilehash: 2d6ebcd720a5cea8d41bf3c05f753f2e9d4775d1
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085899"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>使用外部元数据存储 - Azure HDInsight

HDInsight 允许通过外部数据存储来控制数据和元数据。 此功能可用于 [Apache Hive 元存储](#custom-metastore)、[Apache Oozie 元存储](#apache-oozie-metastore)和 [Apache Ambari 数据库](#custom-ambari-db)。

HDInsight 中的 Apache Hive 元存储是 Apache Hadoop 体系结构的必备部分。 元存储是中心架构存储库。 其他大型数据访问工具（例如 Apache Spark、Interactive Query [LLAP]、Presto 或 Apache Pig）使用元存储。 HDInsight 使用 Azure SQL 数据库作为 Hive 元存储。

![HDInsight Hive 元数据存储体系结构](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

可使用以下两种方式为 HDInsight 存储设置元存储：

* [默认元存储](#default-metastore)
* [自定义元存储](#custom-metastore)

## <a name="default-metastore"></a>默认元存储

默认情况下，HDInsight 为每一种群集类型创建一个元存储。 转而可指定自定义元存储。 默认元存储包括以下注意事项：

* 没有任何额外费用。 HDInsight 会为每个群集类型创建一个元存储，而不额外产生任何费用。

* 每个默认元存储都是群集生命周期的一部分。 删除群集时，也会删除相应的该元存储和元数据。

* 不可与其他群集共享默认元存储。

* 默认的元存储使用基本的 Azure SQL 数据库，该数据库具有5个 DTU （数据库事务单位）限制。
此默认元存储通常用于相对简单的工作负荷。 即不需要多个群集且不需要在群集生命周期之外保留的元数据的工作负荷。

* 对于生产工作负荷，我们建议迁移到外部元存储。 请参阅以下部分了解更多详细信息。

## <a name="custom-metastore"></a>自定义元存储

HDInsight 还支持自定义元存储，建议对生产群集使用此项：

* 将自己的 Azure SQL 数据库指定为元存储。

* 元存储的生命周期不与群集生命周期相关联，因此你可以在不丢失元数据的情况下创建和删除群集。 即使删除和重新创建 HDInsight 群集之后，系统仍然保留 Hive 架构等元数据。

* 通过自定义元存储，可将多个群集和群集类型附加到元存储。 例如，可跨交互式查询、Hive 和 HDInsight 中的群集的 Spark 共享单个元存储。

* 根据所选的性能级别支付元存储（Azure SQL 数据库）的成本。

* 可按需增加元存储。

* 群集和外部元存储必须托管在同一区域中。

![HDInsight Hive 元数据存储使用案例](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>针对自定义元存储创建并配置 Azure SQL 数据库

在为 HDInsight 群集设置自定义 Hive 元存储之前，需创建 Azure SQL 数据库或有一个现有的 Azure SQL 数据库。  有关详细信息，请参阅[快速入门：在 AZURE SQL 数据库中创建单个数据库](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)。

创建群集时，HDInsight 服务需要连接到外部元存储并验证你的凭据。 配置 Azure SQL 数据库防火墙规则以允许 Azure 服务和资源访问服务器。 通过选择“设置服务器防火墙”**** 来在 Azure 门户中启用此选项。 然后选择**No** "**拒绝公共网络访问**" 下面的 **"是"** ，在 "**允许 azure 服务和资源" 访问此服务器**以获取 azure SQL 数据库。 有关详细信息，请参阅[创建和管理 IP 防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

不支持使用 SQL 存储的专用终结点。

![“设置服务器防火墙”按钮](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![允许 Azure 服务访问](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>在群集创建期间选择自定义元存储

可以随时将群集指向之前创建的 Azure SQL 数据库。 若要通过门户创建群集，请从“存储”>“元存储设置”**** 指定该选项。

![HDInsight Hive 元数据存储 Azure 门户](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Hive 元存储指南

* 尽可能使用自定义元存储来帮助分离计算资源（正在运行的群集）和元数据（存储在元存储中）。

* 首先使用 S2 层，它提供 50 DTU 和 250 GB 的存储空间。 如果空间不够，可扩大数据库。

* 如果你希望多个 HDInsight 群集访问单独的数据，请对每个群集上的元存储使用单独的数据库。 如果在多个 HDInsight 群集之间共享元存储，则意味着这些群集将使用相同的元数据和底层用户数据文件。

* 请定期备份自定义元存储。 Azure SQL 数据库会自动生成备份，但备份保留时间范围会有所不同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../azure-sql/database/automated-backups-overview.md)。

* 将元存储和 HDInsight 群集放在同一区域。 此配置将提供最高的性能和最低的网络流出费用。

* 使用 Azure SQL 数据库监视工具或 Azure Monitor 日志监视元存储库的性能和可用性。

* 针对现有的自定义元存储数据库创建更高版本的新 Azure HDInsight 时，系统将升级元存储的架构。 如果不从备份还原数据库，则升级不可逆。

* 如果在多个群集之间共享元存储，请确保所有群集都具有相同的 HDInsight 版本。 不同的 Hive 版本使用不同的元存储数据库架构。 例如，不能在具有 Hive 版本 2.1 的群集和具有 Hive 版本 3.1 的群集之间共享元存储。

* 在 HDInsight 4.0 中，Spark 和 Hive 使用独立目录来访问 SparkSQL 或 Hive 表。 Spark 创建的表位于 Spark 目录中。 Hive 创建的表位于 Hive 目录中。 这与 HDInsight 3.6 不同，在 HDInsight 3.6 中，Hive 和 Spark 共享公共目录。 HDInsight 4.0 中的 Hive 和 Spark 集成依赖于 Hive 仓库连接器 (HWC)。 HWC 在 Spark 和 Hive 之间起到桥梁作用。 [了解 Hive 仓库连接器](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)。

* 在 HDInsight 4.0 中，如果想在 Hive 和 Spark 之间共享元存储，可以通过在 Spark 群集中将 metastore.catalog.default 属性更改为 hive 来实现。 可在 Ambari Advanced spark2-hive-site-override 中找到此属性。 请务必了解，共享元存储仅适用于外部 Hive 表，如果具有内部/托管的 Hive 表或 ACID 表，则无法共享元存储。  

## <a name="apache-oozie-metastore"></a>Apache Oozie 元存储

Apache Oozie 是一个管理 Hadoop 作业的工作流协调系统。 Oozie 支持对 Apache MapReduce、Pig 和 Hive 等模型执行 Hadoop 作业。  Oozie 使用元存储来存储有关工作流的详细信息。 可使用 Azure SQL 数据库作为自定义元存储，提高使用 Oozie 时的性能。 删除群集后，可通过元存储访问 Oozie 作业数据。

若要了解如何使用 Azure SQL 数据库创建 Oozie 元存储，请参阅[使用 Apache Oozie 处理工作流](hdinsight-use-oozie-linux-mac.md)。

## <a name="custom-ambari-db"></a>自定义 Ambari DB

若要在 Apache Ambari on HDInsight 上使用自己的外部数据库，请参阅[自定义 Apache Ambari 数据库](hdinsight-custom-ambari-db.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他组件在 HDInsight 中设置群集](./hdinsight-hadoop-provision-linux-clusters.md)
