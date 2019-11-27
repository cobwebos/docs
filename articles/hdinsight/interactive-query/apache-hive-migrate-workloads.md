---
title: 将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0
description: 了解如何将 HDInsight 3.6 上的 Apache Hive 工作负荷迁移到 HDInsight 4.0。
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 3d55e0e7ecbd52b6d96c657e333c5557388f2721
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406505"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0

本文档介绍如何将 HDInsight 3.6 上的 Apache Hive 和 LLAP 工作负荷迁移到 HDInsight 4.0。 HDInsight 4.0 提供较新的 Hive 和 LLAP 功能，例如具体化视图和查询结果缓存。 将工作负荷迁移到 HDInsight 4.0 时，可以使用 HDInsight 3.6 中所不能提供的许多较新 Hive 3 功能。

本文介绍以下主题：

* 将 Hive 元数据迁移到 HDInsight 4.0
* 安全迁移 ACID 和非 ACID 表
* 在不同的 HDInsight 版本之间保留 Hive 安全策略
* 从 HDInsight 3.6 迁移到 HDInsight 4.0 之后执行查询和调试

Hive 的一项优势是能够将元数据导出到外部数据库（也称为 Hive 元存储）。 **Hive 元存储**负责存储表统计信息，包括表存储位置、列名称和表索引信息。 元存储数据库架构根据 Hive 版本的不同而异。 安全升级 Hive 元存储的建议方法是创建副本并升级副本，而不是当前的生产环境。

## <a name="copy-metastore"></a>复制元存储

HDInsight 3.6 和 HDInsight 4.0 需要不同的元存储架构，并且不能共享单个元存储。

### <a name="external-metastore"></a>外部元存储

创建外部元存储的新副本。 如果使用的是外部元存储，则创建元存储副本的一种安全且简单的方法是使用 SQL 数据库还原功能来[还原数据库](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore)，并使用不同的名称。  请参阅[在 Azure HDInsight 中使用外部元数据存储](../hdinsight-use-external-metadata-stores.md)，以详细了解如何将外部元存储附加到 HDInsight 群集。

### <a name="internal-metastore"></a>内部元存储

如果使用的是内部元存储，则可以使用查询在 Hive 元存储中导出对象定义，并将其导入到新的数据库中。

1. 使用[安全外壳（SSH）客户端](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到 HDInsight 群集。

1. 通过输入以下命令，从打开的 SSH 会话通过[Beeline 客户端](../hadoop/apache-hadoop-use-hive-beeline.md)连接到 HiveServer2：

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    此命令生成名为**alltables.sql**的文件。 由于无法删除/重新创建默认数据库，请删除**alltables.sql**中的 `create database default;` 语句。

1. 退出 SSH 会话。 然后输入 scp 命令以在本地下载**alltables.sql** 。

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. 将**alltables.sql**上传到*新*的 HDInsight 群集。

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 然后，使用 SSH 连接到*新*的 HDInsight 群集。 从 SSH 会话运行以下代码：

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>升级元存储

元存储**副本**完成后，在现有 HDInsight 3.6 群集上运行[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)中的架构升级脚本，将新的元存储升级到 Hive 3 架构。 这允许将数据库附加为 HDInsight 4.0 元存储。

使用下表中的值。 将 `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` 替换为**复制**的 Hive 元存储的适当值，用空格分隔。 指定 SQL server 名称时不要包含 ". database.windows.net"。

|属性 | 值 |
|---|---|
|脚本类型|- Custom|
|名称|Hive 升级|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|节点类型|标头|
|参数|SQLSERVERNAME DATABASENAME 用户名密码|

> [!Warning]  
> 将 HDInsight 3.6 元数据架构转换为 HDInsight 4.0 架构的升级过程不可逆。

可以通过对数据库运行以下 sql 查询来验证升级：

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>将 Hive 表迁移到 HDInsight 4.0

完成上述步骤将 Hive 元存储迁移到 HDInsight 4.0 之后，可以通过在 HDInsight 4.0 群集中执行 `show tables` 或 `show databases`，从群集内部查看元存储中记录的表和数据库。 有关 HDInsight 4.0 群集中的查询执行的信息，请参阅[跨 HDInsight 版本执行查询](#query-execution-across-hdinsight-versions)。

但是，只有群集有权访问所需的存储帐户之后，才能访问表中的实际数据。 为了确保 HDInsight 4.0 群集能够访问与旧的 HDInsight 3.6 群集相同的数据，请完成以下步骤：

1. 确定表或数据库的 Azure 存储帐户。

1. 如果 HDInsight 4.0 群集已运行，请通过 Ambari 将 Azure 存储帐户附加到群集。 如果尚未创建 HDInsight 4.0 群集，请确保将 Azure 存储帐户指定为主要或辅助群集存储帐户。 有关将存储帐户添加到 HDInsight 群集的详细信息，请参阅[将其他存储帐户添加到 HDInsight](../hdinsight-hadoop-add-storage.md)。

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>部署新的 HDInsight 4.0 并连接到新的元存储

架构升级完成后，部署新的 HDInsight 4.0 群集并连接升级的元存储。 如果已部署4.0，请将其设置为可从 Ambari 连接到元存储。

## <a name="run-schema-migration-script-from-hdinsight-40"></a>从 HDInsight 4.0 运行架构迁移脚本

HDInsight 3.6 和 HDInsight 4.0 中的表将以不同的方式进行处理。 出于此原因，不能为不同版本的群集共享同一个表。 若要同时使用 HDInsight 3.6 和 HDInsight 4.0，必须为每个版本单独创建数据副本。

Hive 工作负荷可以包含 ACID 和非 ACID 表的混合形式。 HDInsight 3.6 上的 Hive (Hive 2) 与 HDInsight 4.0 上的 Hive (Hive 3) 之间的一个主要差别在于表的 ACID 合规性。 在 HDInsight 3.6 中，启用 Hive ACID 合规性需要进行额外的配置，但 HDInsight 4.0 表默认已符合 ACID 规范。 在迁移之前唯一需要执行的操作是针对 3.6 版群集上的 ACID 表运行主要压缩。 在 Hive 视图或 Beeline 中运行以下查询：

```sql
alter table myacidtable compact 'major';
```

之所以需要运行这种压缩，是因为 HDInsight 3.6 和 HDInsight 4.0 ACID 表以不同的方式理解 ACID 增量数据。 压缩可强制实施某种干净状态，以保证一致性。 [Hive 迁移文档](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)的第 4 部分包含了有关批量压缩 HDInsight 3.6 ACID 表的指导。

完成元存储迁移和压缩步骤后，即可迁移实际仓库。 完成 Hive 仓库迁移后，HDInsight 4.0 仓库将具有以下属性：

|3.6 |4.0 |
|---|---|
|外部表|外部表|
|非事务性托管表|外部表|
|事务托管表|托管表|

在执行迁移之前，可能需要调整仓库的属性。 例如，如果预期某个表会由第三方（例如 HDInsight 3.6 群集）访问，则迁移完成后，该表必须是外部表。 在 HDInsight 4.0 中，所有托管表都是事务性的。 因此，HDInsight 4.0 中的托管表只能由 HDInsight 4.0 群集访问。

正确设置表属性后，使用 SSH shell 从某个群集头节点执行 Hive 仓库迁移工具：

1. 使用 SSH 连接到群集头节点。 有关说明，请参阅[使用 SSH 连接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 以 Hive 用户的身份运行 `sudo su - hive`，以打开登录 shell
1. 通过执行 `ls /usr/hdp`确定数据平台堆栈版本。 此命令会显示要在下一条命令中使用的版本字符串。
1. 从 shell 执行以下命令。 请将 `STACK_VERSION` 替换为在上一步骤中获取的版本字符串：

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

执行完迁移工具后，Hive 仓库可供 HDInsight 4.0 使用。

> [!Important]  
> HDInsight 4.0 中的托管表（包括从 3.6 迁移的表）不应由其他服务或应用程序（包括 HDInsight 3.6 群集）访问。

## <a name="secure-hive-across-hdinsight-versions"></a>跨 HDInsight 版本保护 Hive

从 HDInsight 3.6 开始，HDInsight 将使用 HDInsight 企业安全性套餐 (ESP) 来与 Azure Active Directory 相集成。 ESP 使用 Kerberos 和 Apache Ranger 来管理群集中特定资源的权限。 可使用以下步骤，将针对 HDInsight 3.6 中的 Hive 部署的 Ranger 策略迁移到 HDInsight 4.0：

1. 在 HDInsight 3.6 群集中导航到 Ranger 服务管理器面板。
2. 导航到名为 **HIVE** 的策略，并将该策略导出到某个 JSON 文件。
3. 确保导出的策略 JSON 中引用的所有用户都存在于新群集中。 如果该策略 JSON 中引用的某个用户不存在于新群集中，请将该用户添加到新群集，或者从策略中删除引用。
4. 在 HDInsight 4.0 群集中导航到“Ranger 服务管理器”面板。
5. 导航到名为 **HIVE** 的策略，并导入步骤 2 中导出的 Ranger 策略 JSON。

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>检查兼容性并根据需要修改测试应用程序中的代码

迁移现有程序和查询等工作负荷时，请查看发行说明和文档中的更改，并在必要时应用更改。 如果你的 HDInsight 3.6 群集使用共享的 Spark 和 Hive 元存储，则需要[使用 Hive 仓库连接器进行附加配置](./apache-hive-warehouse-connector.md)。

## <a name="deploy-new-app-for-production"></a>部署用于生产的新应用

若要切换到新群集，例如，你可以安装新的客户端应用程序并将其用作新的生产环境，或者你可以升级现有的客户端应用程序并切换到 HDInsight 4.0。

## <a name="switch-hdinsight-40-to-the-production"></a>将 HDInsight 4.0 切换到生产环境

如果测试时在元存储中创建了差异，则需要在切换之前更新更改。 在这种情况下，你可以导出 & 导入元存储，然后重新升级。

## <a name="remove-the-old-production"></a>删除旧生产

确认发布已完成且完全可正常运行后，可以删除版本3.6 和上一个元存储。 请确保在删除该环境之前迁移所有内容。

## <a name="query-execution-across-hdinsight-versions"></a>跨 HDInsight 版本执行查询

在 HDInsight 3.6 群集中可以通过两种方式执行和调试 Hive/LLAP 查询。 HiveCLI 提供命令行体验，而 Tez 视图/Hive 视图提供基于 GUI 的工作流。

在 HDInsight 4.0 中，HiveCLI 已由 Beeline 取代。 HiveCLI 是 Hiveserver 1 的 thrift 客户端，Beeline 是用于访问 Hiveserver 2 的 JDBC 客户端。 Beeline 还可用于连接 JDBC 兼容的其他任何数据库终结点。 Beeline 可以现成地在 HDInsight 4.0 上使用，而无需进行任何安装。

在 HDInsight 3.6 中，用来与 Hive 服务器交互的 GUI 客户端是 Ambari Hive 视图。 HDInsight 4.0 使用 Hortonworks Data Analytics Studio (DAS) 取代了 Hive 视图。 DAS 不会随 HDInsight 群集一起提供，并且不是正式支持的程序包。 但是，可以使用[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)在群集上安装 DAS，如下所示：

|属性 | 值 |
|---|---|
|脚本类型|- Custom|
|名称|转移|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|节点类型|标头|

等待5到10分钟，然后使用以下 URL 启动 Data Analytics Studio： `https://CLUSTERNAME.azurehdinsight.net/das/`。

安装 DAS 后，如果看不到在查询查看器中运行的查询，请执行以下步骤：

1. 根据[此 DAS 安装故障排除指南](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)中所述，设置 Hive、Tez 和 DAS 的配置。
2. 确保以下 Azure 存储目录配置为页 Blob，并且它们列在 `fs.azure.page.blob.dirs` 之下：
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在两个头节点上重启 HDFS、Hive、Tez 和 DAS。

## <a name="next-steps"></a>后续步骤

* [HDInsight 4.0 通告](../hdinsight-version-release.md)
* [HDInsight 4.0 深入探讨](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
