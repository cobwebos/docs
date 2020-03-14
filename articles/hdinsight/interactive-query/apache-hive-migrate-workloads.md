---
title: 将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4。0
description: 了解如何在 HDInsight 3.6 到 HDInsight 4.0 上迁移 Apache Hive 工作负荷。
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214643"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4。0

本文档介绍如何在 HDInsight 3.6 到 HDInsight 4.0 上迁移 Apache Hive 和 LLAP 工作负荷。 HDInsight 4.0 提供较新的 Hive 和 LLAP 功能，如具体化视图和查询结果缓存。 将工作负荷迁移到 HDInsight 4.0 时，可以使用 HDInsight 3.6 上不提供的众多 Hive 3 更新功能。

本文涵盖以下主题：

* 将 Hive 元数据迁移到 HDInsight 4。0
* ACID 和非 ACID 表的安全迁移
* 跨 HDInsight 版本保存 Hive 安全策略
* 从 HDInsight 3.6 到 HDInsight 4.0 的查询执行和调试

Hive 的一项优势是能够将元数据导出到外部数据库（称为 Hive 元存储）。 **Hive 元存储**负责存储表统计信息，其中包括表存储位置、列名称和表索引信息。 元存储数据库架构在 Hive 版本之间有所不同。 安全升级 Hive 元存储的建议方法是创建副本并升级副本，而不是当前的生产环境。

## <a name="copy-metastore"></a>复制元存储

HDInsight 3.6 和 HDInsight 4.0 需要不同的元存储架构，无法共享单个元存储。

### <a name="external-metastore"></a>外部元存储

创建外部元存储的新副本。 如果使用的是外部元存储，则创建元存储副本的一种安全且简单的方法是使用 SQL 数据库还原功能来[还原数据库](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore)，并使用不同的名称。  请参阅[在 Azure HDInsight 中使用外部元数据存储](../hdinsight-use-external-metadata-stores.md)了解有关将外部元存储附加到 HDInsight 群集的详细信息。

### <a name="internal-metastore"></a>内部元存储

如果使用的是内部元存储，则可以使用查询在 Hive 元存储中导出对象定义，并将其导入到新的数据库中。

此脚本完成后，假设旧群集将不再用于访问脚本中引用的任何表或数据库，。

> [!NOTE]
> 对于 ACID 表，将创建该表下的数据的新副本。

1. 使用[安全外壳（SSH）客户端](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到 HDInsight 群集。

1. 通过输入以下命令，从打开的 SSH 会话通过[Beeline 客户端](../hadoop/apache-hadoop-use-hive-beeline.md)连接到 HiveServer2：

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    此命令生成名为**alltables.sql**的文件。

1. 退出 SSH 会话。 然后输入 scp 命令以在本地下载**alltables.sql** 。

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. 将**alltables.sql**上传到*新*的 HDInsight 群集。

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 然后，使用 SSH 连接到*新*的 HDInsight 群集。 从 SSH 会话运行以下代码：

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>升级元存储

元存储**副本**完成后，在现有 HDInsight 3.6 群集上运行[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)中的架构升级脚本，将新的元存储升级到 Hive 3 架构。 这允许将数据库附加为 HDInsight 4.0 元存储。

使用下表中的值。 将 `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` 替换为**复制**的 Hive 元存储的适当值，用空格分隔。 指定 SQL server 名称时不要包含 ". database.windows.net"。

|properties | 值 |
|---|---|
|脚本类型|- Custom|
|名称|Hive 升级|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|节点类型|Head|
|parameters|SQLSERVERNAME DATABASENAME 用户名密码|

> [!Warning]  
> 无法反转将 HDInsight 3.6 元数据架构转换为 HDInsight 4.0 架构的升级。

可以通过对数据库运行以下 sql 查询来验证升级：

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>将 Hive 表迁移到 HDInsight 4。0

完成上一组步骤以将 Hive 元存储迁移到 HDInsight 4.0 后，元存储中记录的表和数据库将通过在群集内执行 `show tables` 或 `show databases` 来4.0 查看。 有关 HDInsight 4.0 群集中查询执行的信息，请参阅[在 hdinsight 版本中执行查询](#query-execution-across-hdinsight-versions)。

然而，在群集有权访问所需的存储帐户之前，不能访问表中的实际数据。 若要确保你的 HDInsight 4.0 群集可以访问与你的旧 HDInsight 3.6 群集相同的数据，请完成以下步骤：

1. 确定表或数据库的 Azure 存储帐户。

1. 如果 HDInsight 4.0 群集已在运行，请通过 Ambari 将 Azure 存储帐户附加到群集。 如果尚未创建 HDInsight 4.0 群集，请确保已将 Azure 存储帐户指定为主群集存储帐户或辅助群集存储帐户。 有关将存储帐户添加到 HDInsight 群集的详细信息，请参阅[将其他存储帐户添加到 hdinsight](../hdinsight-hadoop-add-storage.md)。

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>部署新的 HDInsight 4.0 并连接到新的元存储

架构升级完成后，部署新的 HDInsight 4.0 群集并连接升级的元存储。 如果已部署4.0，请将其设置为可从 Ambari 连接到元存储。

## <a name="run-schema-migration-script-from-hdinsight-40"></a>从 HDInsight 4.0 运行架构迁移脚本

在 HDInsight 3.6 和 HDInsight 4.0 中，表的处理方式不同。 出于此原因，不能为不同版本的群集共享同一个表。 如果要将 HDInsight 3.6 与 HDInsight 4.0 同时使用，则必须为每个版本的数据创建单独的副本。

Hive 工作负荷可能包含 ACID 和非 ACID 表的混合。 HDInsight 3.6 （Hive 2）上的 Hive 与 HDInsight 上的 Hive 4.0 （Hive 3）之间的一个主要区别是表的 ACID 遵从性。 在 HDInsight 3.6 中，启用 Hive ACID 遵从性需要额外的配置，但在 HDInsight 4.0 表中默认情况下符合 ACID。 迁移之前所需的唯一操作是对3.6 群集上的 ACID 表运行重大压缩。 从 Hive 视图或 Beeline 中，运行以下查询：

```sql
alter table myacidtable compact 'major';
```

此压缩是必需的，因为 HDInsight 3.6 和 HDInsight 4.0 ACID 表以不同的方式了解 ACID 增量。 压缩会强制执行保证一致性的干净石板。 [Hive 迁移文档](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)的第4部分包含了用于批量压缩 HDINSIGHT 3.6 ACID 表的指南。

完成元存储迁移和压缩步骤后，即可迁移实际仓库。 完成 Hive 仓库迁移后，HDInsight 4.0 仓库将具有以下属性：

|3.6 |4.0 |
|---|---|
|外部表|外部表|
|非事务性托管表|外部表|
|事务托管表|托管表|

在执行迁移之前，您可能需要调整仓库的属性。 例如，如果你预计某个表将由第三方（如 HDInsight 3.6 群集）访问，则迁移完成后，该表必须是外部表。 在 HDInsight 4.0 中，所有托管表都是事务性的。 因此，HDInsight 4.0 中的托管表只能由 HDInsight 4.0 群集访问。

正确设置表属性后，请使用 SSH shell 从某个群集头节点执行 Hive 仓库迁移工具：

1. 使用 SSH 连接到群集头节点。 有关说明，请参阅[使用 SSH 连接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 通过运行 `sudo su - hive` 以 Hive 用户身份打开登录 shell
1. 通过执行 `ls /usr/hdp`确定数据平台堆栈版本。 这会显示一个应在下一个命令中使用的版本字符串。
1. 在 shell 中执行以下命令。 将 `STACK_VERSION` 替换为上一步中的版本字符串：

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
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

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>检查兼容性并根据需要修改测试应用程序中的代码

迁移现有程序和查询等工作负荷时，请查看发行说明和文档中的更改，并在必要时应用更改。 如果你的 HDInsight 3.6 群集使用共享的 Spark 和 Hive 元存储，则需要[使用 Hive 仓库连接器进行附加配置](./apache-hive-warehouse-connector.md)。

## <a name="deploy-new-app-for-production"></a>部署用于生产的新应用

若要切换到新群集，例如，你可以安装新的客户端应用程序并将其用作新的生产环境，或者你可以升级现有的客户端应用程序并切换到 HDInsight 4.0。

## <a name="switch-hdinsight-40-to-the-production"></a>将 HDInsight 4.0 切换到生产环境

如果测试时在元存储中创建了差异，则需要在切换之前更新更改。 在这种情况下，你可以导出 & 导入元存储，然后重新升级。

## <a name="remove-the-old-production"></a>删除旧生产

确认发布已完成且完全可正常运行后，可以删除版本3.6 和上一个元存储。 请确保在删除该环境之前迁移所有内容。

## <a name="query-execution-across-hdinsight-versions"></a>跨 HDInsight 版本执行查询

可以通过两种方式在 HDInsight 3.6 群集中执行和调试 Hive/LLAP 查询。 HiveCLI 提供了命令行体验，Tez 视图/Hive 视图提供了基于 GUI 的工作流。

在 HDInsight 4.0 中，已将 HiveCLI 替换为 Beeline。 HiveCLI 是 Hiveserver 1 的 thrift 客户端，而 Beeline 是提供对 Hiveserver 2 的访问的 JDBC 客户端。 Beeline 也可用于连接到任何其他与 JDBC 兼容的数据库终结点。 Beeline 在 HDInsight 4.0 上随时可用，无需任何安装。

在 HDInsight 3.6 中，用于与 Hive 服务器交互的 GUI 客户端是 Ambari Hive 视图。 Ambari 视图不附带 HDInsight 4.0。 我们为客户提供了一种使用数据分析工作室（DAS）的方法，该方法不是核心 HDInsight 服务。 DAS 不会随 HDInsight 群集一起提供，并且不是正式支持的程序包。 但是，可以使用[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)在群集上安装 DAS，如下所示：

|properties | 值 |
|---|---|
|脚本类型|- Custom|
|名称|转移|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|节点类型|Head|

等待10到15分钟，然后使用以下 URL 启动 Data Analytics Studio： `https://CLUSTERNAME.azurehdinsight.net/das/`。

在访问 DAS 之前，可能需要刷新 Ambari UI 和/或重新启动所有 Ambari 组件。

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
