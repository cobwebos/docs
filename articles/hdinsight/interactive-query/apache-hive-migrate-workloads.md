---
title: Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0
description: 了解如何将 Apache Hive 在 HDInsight 3.6 上的工作负荷迁移到 HDInsight 4.0。
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/24/2019
ms.openlocfilehash: b39279e560cb1738ff9b33ec587562efd2ed4e8d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800954"
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

1. 创建新的外部元存储副本。 HDInsight 3.6 和 HDInsight 4.0 需要不同的元存储架构，并且不能共享单个元存储。 请参阅[使用 Azure HDInsight 中的外部元数据存储](../hdinsight-use-external-metadata-stores.md)若要了解有关将外部元存储附加到 HDInsight 群集的详细信息。 
2. 执行的节点类型以启动对 HDI 3.6 群集，使用"头节点"脚本操作。 粘贴到文本框下面的 URI 标记为"Bash 脚本 URI": https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh。在标记为"参数"文本框中，输入服务器名称、 数据库、 用户名和密码**复制**Hive 元存储，由空格分隔。 不包括"。 database.windows.net"指定服务器名称时。

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

需要这种压缩，因为 HDInsight 3.6 和 HDInsight 4.0 ACID 表了解 ACID 增量数据以不同的方式。 压缩强制实施可保证一致性在干净状态。 第 4 部分[Hive 迁移文档](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)包含用于在 HDInsight 3.6 ACID 表的大容量压缩指南。

完成元存储迁移和压缩步骤后，你可以迁移实际仓库。 完成配置单元仓库迁移后，HDInsight 4.0 仓库将具有以下属性：

* 在 HDInsight 3.6 中的外部表将为 HDInsight 4.0 中的外部表
* 在 HDInsight 3.6 中的非事务性托管的表将为 HDInsight 4.0 中的外部表
* 在 HDInsight 3.6 中的事务托管的表将为 HDInsight 4.0 中的托管的表

您可能需要执行迁移之前调整仓库的属性。 例如，如果您希望第三方 （例如 HDInsight 3.6 群集） 将访问某些表，该表必须是外部的迁移完成后。 在 HDInsight 4.0 中，所有托管的表是事务性的。 因此，仅应由 HDInsight 4.0 群集访问 HDInsight 4.0 中的托管的表。

表属性设置正确后, 从一个群集头节点使用 SSH 命令行程序执行配置单元仓库迁移工具：

1. 连接到使用 SSH 在群集头节点。 有关说明，请参阅[连接到 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 通过运行 Hive 用户以打开登录 shell `sudo su - hive`
1. 通过执行确定 Hortonworks 数据平台堆栈版本`ls /usr/hdp`。 这将显示在下一个命令应使用的版本字符串。
1. 从 shell 中执行以下命令。 替换为`${{STACK_VERSION}}`与上一步骤中的版本字符串：

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

迁移工具完成后，将配置单元仓库将可供 HDInsight 4.0。 

> [!Important]
> 不应由其他服务或应用程序，包括 HDInsight 3.6 群集访问 （包括表迁移从 3.6） 的 HDInsight 4.0 中的托管的表。

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

执行的节点类型以启动对群集，使用"头节点"脚本操作。 将以下 URI 粘贴到标记为"Bash 脚本 URI"文本框中： https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

数据分析 Studio 可以使用 URL 启动应用： https://<clustername>.azurehdinsight.net/das/



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
