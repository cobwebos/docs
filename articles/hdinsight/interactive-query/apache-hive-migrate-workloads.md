---
title: 将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0
description: 了解如何将 HDInsight 3.6 上的 Apache Hive 工作负荷迁移到 HDInsight 4.0。
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 14849dd1f68f281009808d1bd1dc1cae62927ab4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594230"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0

本文档介绍如何在 HDInsight 3.6 到 HDInsight 4.0 上迁移 Apache Hive 和 LLAP 工作负荷。 HDInsight 4.0 提供较新的 Hive 和 LLAP 功能，例如具体化视图和查询结果缓存。 将工作负荷迁移到 HDInsight 4.0 时，可以使用 HDInsight 3.6 中所不能提供的许多较新 Hive 3 功能。

本文介绍以下主题：

* 将 Hive 元数据迁移到 HDInsight 4.0
* 安全迁移 ACID 和非 ACID 表
* 在不同的 HDInsight 版本之间保留 Hive 安全策略
* 从 HDInsight 3.6 迁移到 HDInsight 4.0 之后执行查询和调试

Hive 的一项优势是能够将元数据导出到外部数据库（也称为 Hive 元存储）。 **Hive 元存储**负责存储表统计信息，包括表存储位置、列名称和表索引信息。 HDInsight 3.6 和 HDInsight 4.0 需要不同的元存储架构，并且不能共享单个元存储。 安全升级 Hive 元存储的推荐方法是在当前生产环境中升级副本而不是原始环境。 本文档要求原始群集和新群集访问相同的存储帐户。 因此，它不涉及到其他区域的数据迁移。

## <a name="migrate-from-external-metastore"></a>从外部元存储迁移

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. 在 HDInsight 3.6 中对 ACID 表运行重大压缩

HDInsight 3.6 和 HDInsight 4.0 ACID 表以不同的方式了解 ACID 增量。 迁移之前唯一需要执行的操作是对3.6 群集上的每个 ACID 表运行 "主要" 压缩。 有关压缩的详细信息，请参阅[Hive 语言手册](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact)。

### <a name="2-copy-sql-database"></a>2. 复制 SQL 数据库
创建外部元存储的新副本。 如果使用外部元存储，创建元存储副本的安全且简便方法之一是使用 SQL 数据库还原功能以不同的名称[还原数据库](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore)。  请参阅[在 Azure HDInsight 中使用外部元数据存储](../hdinsight-use-external-metadata-stores.md)，以详细了解如何将外部元存储附加到 HDInsight 群集。

### <a name="3-upgrade-metastore-schema"></a>3. 升级元存储架构
完成元存储的**复制**后，在现有 HDInsight 3.6 群集上运行[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)中的架构升级脚本，将新的元存储升级到 Hive 3 架构。 （此步骤不需要将新的元存储连接到群集。）这允许将数据库附加为 HDInsight 4.0 元存储。

接下来使用下表中的值。 替换`SQLSERVERNAME DATABASENAME USERNAME PASSWORD`为 Hive 元存储**复制**的适当值，以空格分隔。 指定 SQL 服务器名称时，请不要包含“.database.windows.net”。

|属性 | 值 |
|---|---|
|脚本类型|- Custom|
|名称|Hive 升级|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|节点类型|Head|
|参数|SQLSERVERNAME DATABASENAME USERNAME PASSWORD|

> [!Warning]  
> 将 HDInsight 3.6 元数据架构转换为 HDInsight 4.0 架构的升级过程不可逆。

可以通过对数据库运行以下 SQL 查询来验证升级：

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. 部署新的 HDInsight 4.0 群集

1. 将升级的元存储指定为新群集的 Hive 元存储。

1. 但是，只有群集有权访问所需的存储帐户之后，才能访问表中的实际数据。
请确保将 HDInsight 3.6 群集中的 Hive 表的存储帐户指定为新 HDInsight 4.0 群集的主存储帐户或辅助存储帐户。
有关将存储帐户添加到 HDInsight 群集的详细信息，请参阅[将其他存储帐户添加到 HDInsight](../hdinsight-hadoop-add-storage.md)。

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. 使用 HDInsight 4.0 中的升级后工具完成迁移

默认情况下，托管表在 HDInsight 4.0 上必须符合 ACID。 完成元存储迁移后，请运行升级后工具，使以前与 HDInsight 4.0 群集兼容的非 ACID 托管表。 此工具将应用以下转换：

|3.6 |4.0 |
|---|---|
|外部表|外部表|
|非 ACID 托管表|具有属性 "external. table" 的外部表 "true"|
|ACID 托管表|ACID 托管表|

使用 SSH shell 从 HDInsight 4.0 群集执行 Hive 升级后工具：

1. 使用 SSH 连接到群集头节点。 有关说明，请参阅[使用 SSH 连接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 以 Hive 用户的身份运行 `sudo su - hive`，以打开登录 shell
1. 从 shell 执行以下命令。

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

工具完成后，你的 Hive 仓库将准备就绪，可用于 HDInsight 4.0。

## <a name="migrate-from-internal-metastore"></a>从内部元存储迁移

如果你的 HDInsight 3.6 群集使用内部 Hive 元存储，请按照以下步骤运行脚本，该脚本将生成 Hive 查询以导出元存储中的对象定义。

HDInsight 3.6 和4.0 群集必须使用相同的存储帐户。

> [!NOTE]
>
> * 对于 ACID 表，将创建该表下的数据的新副本。
>
> * 此脚本仅支持迁移 Hive 数据库、表和分区。 需要手动复制其他元数据对象，如视图、Udf 和表约束。
>
> * 此脚本完成后，假设旧群集将不再用于访问脚本中引用的任何表或数据库，。
>
> * 所有托管表在 HDInsight 4.0 中将变为事务性的。 （可选）通过将数据导出到具有属性 "external. table" = ' true ' 的外部表，使表保持非事务性。 例如，
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. 使用[安全外壳（SSH）客户端](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到 HDInsight 3.6 群集。

1. 从打开的 SSH 会话中，下载以下脚本文件以生成名为**alltables.sql**的文件。

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * 对于常规 HDInsight 群集，不使用 ESP，只需`exporthive_hdi_3_6.sh`执行。

    * 对于带有 ESP 的群集，kinit 并修改 beeline 的参数：运行以下，为具有完全 Hive 权限的 Azure AD 用户定义用户和域。

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. 退出 SSH 会话。 然后输入 scp 命令以在本地下载**alltables.sql** 。

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. 将**alltables.sql**上传到*新*的 HDInsight 群集。

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 然后，使用 SSH 连接到*新*的 HDInsight 4.0 群集。 从与此群集的 SSH 会话运行以下代码：

    不含 ESP：

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    带有 ESP：

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

外部元存储迁移的升级后工具不适用于此处，因为 HDInsight 3.6 中的非 ACID 托管表转换为 HDInsight 4.0 中的 ACID 托管表。

> [!Important]  
> HDInsight 4.0 中的托管表（包括从 3.6 迁移的表）不应由其他服务或应用程序（包括 HDInsight 3.6 群集）访问。

## <a name="secure-hive-across-hdinsight-versions"></a>跨 HDInsight 版本保护 Hive

从 HDInsight 3.6 开始，HDInsight 将使用 HDInsight 企业安全性套餐 (ESP) 来与 Azure Active Directory 相集成。 ESP 使用 Kerberos 和 Apache Ranger 来管理群集中特定资源的权限。 可使用以下步骤，将针对 HDInsight 3.6 中的 Hive 部署的 Ranger 策略迁移到 HDInsight 4.0：

1. 在 HDInsight 3.6 群集中导航到 Ranger 服务管理器面板。
2. 导航到名为 **HIVE** 的策略，并将该策略导出到某个 JSON 文件。
3. 确保导出的策略 JSON 中引用的所有用户都存在于新群集中。 如果该策略 JSON 中引用的某个用户不存在于新群集中，请将该用户添加到新群集，或者从策略中删除引用。
4. 在 HDInsight 4.0 群集中导航到“Ranger 服务管理器”面板。****
5. 导航到名为 **HIVE** 的策略，并导入步骤 2 中导出的 Ranger 策略 JSON。

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>在测试应用中检查兼容性并根据需要修改代码

迁移工作负荷（例如现有的程序和查询）时，请查看发行说明和文档中提到的更改，并根据需要应用更改。 如果 HDInsight 3.6 群集使用共享的 Spark 和 Hive 元存储，则需要[使用 Hive 仓库连接器进行其他配置](./apache-hive-warehouse-connector.md)。

## <a name="deploy-new-app-for-production"></a>部署新应用用于生产

例如，若要切换到新群集，可以安装新的客户端应用程序并将其用作新的生产环境，或者，可以升级现有的客户端应用程序并切换到 HDInsight 4.0。

## <a name="switch-hdinsight-40-to-the-production"></a>将 HDInsight 4.0 切换到生产环境

如果在测试时元存储中产生了差异，则需要在切换之前更新更改。 在这种情况下，可以导出并导入元存储，然后再次升级。

## <a name="remove-the-old-production"></a>删除旧生产环境

确认发布已完成且完全正常运行后，可以删除版本 3.6 和以前的元存储。 在删除环境之前，请确保已迁移所有内容。

## <a name="query-execution-across-hdinsight-versions"></a>跨 HDInsight 版本执行查询

在 HDInsight 3.6 群集中可以通过两种方式执行和调试 Hive/LLAP 查询。 HiveCLI 提供命令行体验，而 Tez 视图/Hive 视图提供基于 GUI 的工作流。

在 HDInsight 4.0 中，HiveCLI 已由 Beeline 取代。 HiveCLI 是 Hiveserver 1 的 thrift 客户端，Beeline 是用于访问 Hiveserver 2 的 JDBC 客户端。 Beeline 还可用于连接 JDBC 兼容的其他任何数据库终结点。 Beeline 可以现成地在 HDInsight 4.0 上使用，而无需进行任何安装。

在 HDInsight 3.6 中，用来与 Hive 服务器交互的 GUI 客户端是 Ambari Hive 视图。 Ambari 视图不附带 HDInsight 4.0。 我们为客户提供了一种使用数据分析工作室（DAS）的方法，该方法不是核心 HDInsight 服务。 DAS 不会随 HDInsight 群集一起提供，并且不是正式支持的程序包。 但是，可以使用[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)在群集上安装 DAS，如下所示：

|属性 | 值 |
|---|---|
|脚本类型|- Custom|
|名称|转移|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|节点类型|Head|

等待10到15分钟，然后使用以下 URL 启动 Data Analytics Studio： `https://CLUSTERNAME.azurehdinsight.net/das/`。

在访问 DAS 之前，可能需要刷新 Ambari UI 和/或重新启动所有 Ambari 组件。

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
