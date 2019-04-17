---
title: Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0
description: 了解如何将 Apache Hive 在 HDInsight 3.6 上的工作负荷迁移到 HDInsight 4.0。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 708df64802ace17fa77b4e0a695c9f1c3bd18a77
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610191"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0

本文档演示如何在 HDInsight 3.6 上的 Apache Hive 和 LLAP 工作负荷迁移到 HDInsight 4.0。 HDInsight 4.0 提供了更高版本 Hive 和 LLAP 功能，例如具体化的视图和查询结果缓存。 当将工作负荷迁移到 HDInsight 4.0 时，可以使用许多较新功能 Hive 3 HDInsight 3.6 上未提供的。

本文涵盖以下主题：

* 为 HDInsight 4.0 的 Hive 元数据的迁移
* ACID 和非 ACID 表安全迁移
* 保留的 Hive 在 HDInsight 版本之间的安全策略
* 查询执行和从 HDInsight 3.6 HDInsight 4.0 到调试

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>将 Apache Hive 元数据迁移到 HDInsight 4.0

配置单元的一个优点是能够将元数据导出到外部数据库 （也称为 Hive 元存储）。 **Hive 元存储**负责存储表统计信息，包括表存储位置、 列名称和表索引信息。 元存储数据库架构之间的 Hive 版本不同。 执行以下操作来升级 HDInsight 3.6 Hive 元存储，以便它与 HDInsight 4.0 都兼容。

1. 创建新的外部元存储副本。 HDInsight 3.6 和 HDInsight 4.0 需要不同的元存储架构，并且不能共享单个元存储。
1. 将元存储的新副本附加到 a） 与现有的 HDInsight 4.0 群集或要创建第一次 b） 的群集。 请参阅[使用 Azure HDInsight 中的外部元数据存储](../hdinsight-use-external-metadata-stores.md)若要了解有关将外部元存储附加到 HDInsight 群集的详细信息。 元存储附加后，它将自动转换为 4.0 兼容元存储。

> [!Warning]
> 升级将 HDInsight 3.6 元数据架构转换为 HDInsight 4.0 架构中，将无法撤消。

## <a name="migrate-hive-tables-to-hdinsight-40"></a>将 Hive 表迁移到 HDInsight 4.0

完成上一组步骤以将 Hive 元存储迁移到 HDInsight 4.0 之后, 的表和记录在元存储数据库将会看到从 HDInsight 4.0 群集内执行`show tables`或`show databases`从群集中. 请参阅[HDInsight 版本中进行查询执行](#query-execution-across-hdinsight-versions)HDInsight 4.0 群集中的查询执行的信息。

实际数据从表中，但是，无法访问，直到群集具有必要的存储帐户的访问权限。 若要确保你的 HDInsight 4.0 群集可以访问旧的 HDInsight 3.6 群集相同的数据，请完成以下步骤：

1. 确定您的表的 Azure 存储帐户或数据库使用描述格式。
2. 如果已在运行 HDInsight 4.0 群集，附加到通过 Ambari 在群集的 Azure 存储帐户。 如果你尚未创建 HDInsight 4.0 群集，请确保 Azure 存储帐户指定为主要或辅助群集存储帐户。 有关将存储帐户添加到 HDInsight 群集的详细信息，请参阅[将其他存储帐户添加到 HDInsight](../hdinsight-hadoop-add-storage.md)。

> [!Note]
> 在 HDInsight 3.6 和 HDInsight 4.0 中，表的处理方式不同。 出于此原因，不能共享相同的表的不同版本的群集。 如果你想要使用 HDInsight 3.6 HDInsight 4.0 时，必须具有每个版本的数据的单独的副本。

Hive 工作负荷可能包括多个 ACID 和非 ACID 表。 在 HDInsight 3.6 (Hive 2) 上的 Hive 和 HDInsight 4.0 (Hive 3) 上的 Hive 的一个主要区别是表的 ACID 合规性。 在 HDInsight 3.6 中启用 Hive ACID 合规性需要额外的配置，但在 HDInsight 4.0 表是 ACID 合规默认情况下。 唯一需要的操作之前迁移是对 ACID 表 3.6 版群集上运行主要压缩。 Hive 视图中或从 Beeline，运行以下查询：

```bash
alter table myacidtable compact 'major';
```

需要这种压缩，因为 HDInsight 3.6 和 HDInsight 4.0 ACID 表了解不同的 ACID 增量。 压缩强制实施可保证表一致性在干净状态。 压缩完成后，元存储和表的迁移的上一步骤将不足以在 HDInsight 4.0 中使用的任何 HDInsight 3.6 ACID 表。

## <a name="secure-hive-across-hdinsight-versions"></a>在 HDInsight 版本之间的安全配置单元

HDInsight 3.6 自 HDInsight 与 Azure Active Directory 使用 HDInsight 企业安全包 (ESP) 集成。 ESP 使用 Kerberos 和 Apache Ranger 管理群集中的特定资源的权限。 针对 Hive 在 HDInsight 3.6 中部署的 ranger 策略可以将迁移到 HDInsight 4.0 通过以下步骤：

1. 导航到 Ranger 服务管理器面板在 HDInsight 3.6 群集。
2. 导航到名为的策略**HIVE**并将策略导出到 json 文件。
3. 请确保在新群集中存在的导出的策略 json 中引用的所有用户。 如果用户在策略 json 中称为，但在新群集中不存在，将用户添加到新群集，或从策略中删除该引用。
4. 导航到**Ranger Service Manager** HDInsight 4.0 群集中的面板。
5. 导航到名为的策略**HIVE**和 ranger 策略将 json 导入步骤 2 中。

## <a name="query-execution-across-hdinsight-versions"></a>在 HDInsight 版本之间的查询执行

有两种方法来执行和调试 Hive/LLAP 的 HDInsight 3.6 群集中的查询。 HiveCLI 提供命令行体验，并在 Tez 视图/Hive 视图提供基于 GUI 的工作流。

在 HDInsight 4.0 中，HiveCLI 已替换为 Beeline。 HiveCLI 是 Hiveserver 1 的 thrift 客户端和 Beeline 是 JDBC 客户端提供对 Hiveserver 2 访问权限。 Beeline 还可用于连接到任何其他 JDBC 兼容数据库终结点。 Beeline 是可用的现成可用 HDInsight 4.0 上无需进行任何所需的安装。

在 HDInsight 3.6 中使用 Hive 服务器交互的 GUI 客户端是 Ambari Hive 视图。 HDInsight 4.0 替换 Hive 视图与 Hortonworks 数据分析 Studio (DAS)。 DAS 没有随附 HDInsight 群集的现成可用，并不是受支持的包。 但是，DAS 可以在群集上安装，如下所示：

1. 下载[DAS 包安装脚本](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-mpack.sh)并在这两个群集头节点上运行。 不执行此脚本作为脚本操作。
2. 下载[DAS 服务安装脚本](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-component.sh)并将其作为脚本操作运行。 选择**头节点**作为脚本操作接口中的所选的节点类型。
3. 脚本操作完成后，导航到 Ambari，并选择**数据分析 Studio**从服务列表。 停止所有 DAS 服务。 在右上角中，选择**操作**并**启动**。 现在可以执行和调试查询直连存储。

DAS 安装后，如果看不到已在查询查看器中运行的查询，请执行以下步骤操作：

1. 将 Hive 和 Tez，DAS 的配置设置中所述[本指南进行故障排除 DAS 安装](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)。
2. 请确保以下 Azure 存储目录配置是页 blob，并且它们将列在`fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在这两个头节点上重新启动 HDFS、 Hive、 Tez、 和 DAS。

## <a name="next-steps"></a>后续步骤

* [HDInsight 4.0 公告](../hdinsight-version-release.md)
* [HDInsight 4.0 的深入探讨](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)