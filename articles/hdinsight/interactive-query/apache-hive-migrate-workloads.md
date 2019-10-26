---
title: 将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4。0
description: 了解如何在 HDInsight 3.6 到 HDInsight 4.0 上迁移 Apache Hive 工作负荷。
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 1b270663a83461ecd777599fead9d717e93482c0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930893"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4。0

本文档介绍如何在 HDInsight 3.6 到 HDInsight 4.0 上迁移 Apache Hive 和 LLAP 工作负荷。 HDInsight 4.0 提供较新的 Hive 和 LLAP 功能，如具体化视图和查询结果缓存。 将工作负荷迁移到 HDInsight 4.0 时，可以使用 HDInsight 3.6 上不提供的众多 Hive 3 更新功能。

本文涵盖以下主题：

* 将 Hive 元数据迁移到 HDInsight 4。0
* ACID 和非 ACID 表的安全迁移
* 跨 HDInsight 版本保存 Hive 安全策略
* 从 HDInsight 3.6 到 HDInsight 4.0 的查询执行和调试

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>将 Apache Hive 元数据迁移到 HDInsight 4。0

Hive 的一项优势是能够将元数据导出到外部数据库（称为 Hive 元存储）。 **Hive 元存储**负责存储表统计信息，其中包括表存储位置、列名称和表索引信息。 元存储数据库架构在 Hive 版本之间有所不同。 执行以下操作以升级 HDInsight 3.6 Hive 元存储，使其与 HDInsight 4.0 兼容。

1. 创建外部元存储的新副本。 HDInsight 3.6 和 HDInsight 4.0 需要不同的元存储架构，无法共享单个元存储。 请参阅[在 Azure HDInsight 中使用外部元数据存储](../hdinsight-use-external-metadata-stores.md)了解有关将外部元存储附加到 HDInsight 群集的详细信息。 
2. 针对 HDI 3.6 群集启动脚本操作，并使用 "头节点" 作为执行的节点类型。 将以下 URI 粘贴到标记为 "Bash 脚本 URI" 的文本框中： https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh 。在标有 "参数" 的文本框中，输入要**复制**的 Hive 元存储的 servername、数据库、用户名和密码，以空格分隔。 指定 servername 时不包括 "database.windows.net"。

> [!Warning]
> 无法反转将 HDInsight 3.6 元数据架构转换为 HDInsight 4.0 架构的升级。

## <a name="migrate-hive-tables-to-hdinsight-40"></a>将 Hive 表迁移到 HDInsight 4。0

完成上一组步骤以将 Hive 元存储迁移到 HDInsight 4.0 后，元存储中记录的表和数据库将通过在群集内执行 `show tables` 或 `show databases` 来4.0 查看。 有关 HDInsight 4.0 群集中查询执行的信息，请参阅[在 hdinsight 版本中执行查询](#query-execution-across-hdinsight-versions)。

然而，在群集有权访问所需的存储帐户之前，不能访问表中的实际数据。 若要确保你的 HDInsight 4.0 群集可以访问与你的旧 HDInsight 3.6 群集相同的数据，请完成以下步骤：

1. 使用 "描述格式" 确定表或数据库的 Azure 存储帐户。
2. 如果 HDInsight 4.0 群集已在运行，请通过 Ambari 将 Azure 存储帐户附加到群集。 如果尚未创建 HDInsight 4.0 群集，请确保已将 Azure 存储帐户指定为主群集存储帐户或辅助群集存储帐户。 有关将存储帐户添加到 HDInsight 群集的详细信息，请参阅[将其他存储帐户添加到 hdinsight](../hdinsight-hadoop-add-storage.md)。

> [!Note]
> 在 HDInsight 3.6 和 HDInsight 4.0 中，表的处理方式不同。 出于此原因，不能为不同版本的群集共享同一个表。 如果要将 HDInsight 3.6 与 HDInsight 4.0 同时使用，则必须为每个版本的数据创建单独的副本。

Hive 工作负荷可能包含 ACID 和非 ACID 表的混合。 HDInsight 3.6 （Hive 2）上的 Hive 与 HDInsight 上的 Hive 4.0 （Hive 3）之间的一个主要区别是表的 ACID 遵从性。 在 HDInsight 3.6 中，启用 Hive ACID 遵从性需要额外的配置，但在 HDInsight 4.0 表中默认情况下符合 ACID。 迁移之前所需的唯一操作是对3.6 群集上的 ACID 表运行重大压缩。 从 Hive 视图或 Beeline 中，运行以下查询：

```bash
alter table myacidtable compact 'major';
```

此压缩是必需的，因为 HDInsight 3.6 和 HDInsight 4.0 ACID 表以不同的方式了解 ACID 增量。 压缩会强制执行保证一致性的干净石板。 [Hive 迁移文档](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)的第4部分包含了用于批量压缩 HDINSIGHT 3.6 ACID 表的指南。

完成元存储迁移和压缩步骤后，即可迁移实际仓库。 完成 Hive 仓库迁移后，HDInsight 4.0 仓库将具有以下属性：

* HDInsight 3.6 中的外部表将是 HDInsight 4.0 中的外部表
* HDInsight 3.6 中的非事务性托管表将是 HDInsight 4.0 中的外部表
* HDInsight 3.6 中的事务托管表将是 HDInsight 4.0 中的托管表

在执行迁移之前，您可能需要调整仓库的属性。 例如，如果你预计某个表将由第三方（如 HDInsight 3.6 群集）访问，则迁移完成后，该表必须是外部表。 在 HDInsight 4.0 中，所有托管表都是事务性的。 因此，HDInsight 4.0 中的托管表只能由 HDInsight 4.0 群集访问。

正确设置表属性后，请使用 SSH shell 从某个群集头节点执行 Hive 仓库迁移工具：

1. 使用 SSH 连接到群集头节点。 有关说明，请参阅[使用 SSH 连接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 通过运行 `sudo su - hive` 以 Hive 用户身份打开登录 shell
1. 通过执行 `ls /usr/hdp`确定数据平台堆栈版本。 这会显示一个应在下一个命令中使用的版本字符串。
1. 在 shell 中执行以下命令。 将 `${{STACK_VERSION}}` 替换为上一步中的版本字符串：

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

迁移工具完成后，你的 Hive 仓库将准备就绪，可用于 HDInsight 4.0。 

> [!Important]
> HDInsight 4.0 （包括从3.6 迁移的表）的托管表不应由其他服务或应用程序（包括 HDInsight 3.6 群集）访问。

## <a name="secure-hive-across-hdinsight-versions"></a>跨 HDInsight 版本保护 Hive

由于 HDInsight 3.6，HDInsight 与 Azure Active Directory 使用 HDInsight 企业安全性套餐（ESP）集成。 ESP 使用 Kerberos 和 Apache Ranger 管理群集中特定资源的权限。 对于 HDInsight 3.6 中的 Hive 部署的 Ranger 策略，可将其迁移到 HDInsight 4.0，步骤如下：

1. 导航到 HDInsight 3.6 群集中的 Ranger Service Manager 面板。
2. 导航到名为**HIVE**的策略，然后将策略导出到 json 文件。
3. 请确保新群集中存在导出的策略 json 中所引用的所有用户。 如果用户在策略 json 中被引用，但在新群集中不存在，请将该用户添加到新群集，或者从策略中删除该引用。
4. 导航到 HDInsight 4.0 群集中的**Ranger Service Manager**面板。
5. 导航到名为**HIVE**的策略，并导入步骤2中的 ranger 策略 json。

## <a name="query-execution-across-hdinsight-versions"></a>跨 HDInsight 版本执行查询

可以通过两种方式在 HDInsight 3.6 群集中执行和调试 Hive/LLAP 查询。 HiveCLI 提供了命令行体验，Tez 视图/Hive 视图提供了基于 GUI 的工作流。

在 HDInsight 4.0 中，已将 HiveCLI 替换为 Beeline。 HiveCLI 是 Hiveserver 1 的 thrift 客户端，而 Beeline 是提供对 Hiveserver 2 的访问的 JDBC 客户端。 Beeline 也可用于连接到任何其他与 JDBC 兼容的数据库终结点。 Beeline 在 HDInsight 4.0 上随时可用，无需任何安装。

在 HDInsight 3.6 中，用于与 Hive 服务器交互的 GUI 客户端是 Ambari Hive 视图。 HDInsight 4.0 将 Hive 视图替换为 Hortonworks Data Analytics Studio （DAS）。 DAS 不会随 HDInsight 群集一起提供，并且不是正式支持的程序包。 但是，可以在群集上安装 DAS，如下所示：

针对群集启动脚本操作，并使用 "头节点" 作为执行的节点类型。 将以下 URI 粘贴到标记为 "Bash 脚本 URI" 的文本框中： https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

等待5到10分钟，然后使用以下 URL 启动 Data Analytics Studio： https://\<> clustername/das/

安装 DAS 后，如果在查询查看器中看不到已运行的查询，请执行以下步骤：

1. 设置 Hive、Tez 和 DAS 的配置，如本指南中所述，[用于排查 DAS 安装问题](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)。
2. 请确保以下 Azure 存储目录配置为页 blob，并在 `fs.azure.page.blob.dirs`下列出它们：
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在两个头节点上重新启动 HDFS、Hive、Tez 和 DAS。

## <a name="next-steps"></a>后续步骤

* [HDInsight 4.0 公告](../hdinsight-version-release.md)
* [HDInsight 4.0 深入探讨](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
