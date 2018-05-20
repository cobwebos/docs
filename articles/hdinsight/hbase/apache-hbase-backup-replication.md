---
title: 设置 HBase 和 Phoenix 的备份与复制 - Azure HDInsight | Microsoft Docs
description: 为 HBase 和 Phoenix 设置备份与复制。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: f43edaf16784e5ba5cc3d2b39df285582954210b
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="set-up-backup-and-replication-for-hbase-and-phoenix-on-hdinsight"></a>为 HDInsight 上的 HBase 和 Phoenix 设置备份与复制

HBase 支持通过多种方法来防范数据丢失：

* 复制 `hbase` 文件夹
* 导出再导入
* 复制表
* 快照
* 复制

> [!NOTE]
> Apache Phoenix 将其元数据存储在 HBase 表中，因此，每当备份 HBase 系统目录表时，就会备份这些元数据。

以下部分介绍上述每种方法的使用方案。

## <a name="copy-the-hbase-folder"></a>复制 hbase 文件夹

使用此方法时，将会复制所有 HBase 数据，而无法选择表或列系列的子集。 后面所述的方法提供更高的控制度。

HDInsight 中的 HBase 使用创建群集时选择的默认存储：Azure 存储 Blob 或 Azure Data Lake Store。 无论使用哪种存储，HBase 都会将其数据和元数据文件存储在以下路径：

    /hbase

* 在 Azure 存储帐户中，`hbase` 文件夹位于 Blob 容器的根目录：

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* 在 Azure Data Lake Store 中，`hbase` 文件夹位于预配群集时指定的根路径下。 此根路径通常包含一个 `clusters` 文件夹，而该文件夹包含一个与 HDInsight 群集同名的子文件夹：

    ```
    /clusters/<clusterName>/hbase
    ```

在上述任一情况下，`hbase` 文件夹都包含由 HBase 刷新到磁盘的所有数据，但可能不包含内存中数据。 只有在关闭群集之后，才能依赖此文件夹来准确表示 HBase 数据。

删除群集后，可将数据保留在原处，或将数据复制到新位置：

* 创建指向当前存储位置的新 HDInsight 实例。 新实例是使用所有现有数据创建的。

* 将 `hbase` 文件夹复制到不同的 Azure 存储 Blob 容器或 Data Lake Store 位置，然后使用该数据启动新群集。 对于 Azure 存储，可以使用 [AzCopy](../../storage/common/storage-use-azcopy.md)；对于 Data Lake Store，可以使用 [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)。

## <a name="export-then-import"></a>导出再导入

在源 HDInsight 群集上，使用“导出”实用工具（HBase 已随附）将数据从源表导出到默认的附加存储。 然后，可将导出的文件夹复制到目标存储位置，并在目标 HDInsight 群集上运行“导入”实用工具。

若要导出表，请先通过 SSH 连接到源 HDInsight 群集的头节点，然后运行以下 `hbase` 命令：

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

若要导入表，请通过 SSH 连接到目标 HDInsight 群集的头节点，然后运行以下 `hbase` 命令：

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

指定默认存储或任何附加存储选项的完整导出路径。 例如，在 Azure 存储中：

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

在 Azure Data Lake Store 中，语法为：

    adl://<accountName>.azuredatalakestore.net:443/<path>

此方法提供表级粒度。 还可以指定日期范围以包含相应的行，这样，便能以递增方式执行该过程。 每个日期从 Unix 时期开始算起，以毫秒为单位。

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

请注意，必须指定要导出的每行的版本数。 若要在日期范围中包含所有版本，请将 `<numberOfVersions>` 设置为大于可能行版本数上限的值，例如 100000。

## <a name="copy-tables"></a>复制表

CopyTable 实用工具将数据从源表逐行复制到架构与源相同的现有目标表。 目标表可以位于相同的群集中，或不同的 HBase 群集中。

若要在群集中使用 CopyTable，请通过 SSH 连接到源 HDInsight 群集的头节点，然后运行以下 `hbase` 命令：

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

若要使用 CopyTable 复制不同群集中的表，请添加 `peer` 开关和目标群集的地址：

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

目标地址由以下三个部分组成：

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` 是逗号分隔的 ZooKeeper 节点列表，例如：

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* HDInsight 上的 `<Port>` 默认为 2181，`<ZnodeParent>` 为 `/hbase-unsecure`，因此，完整的 `<destinationAddress>` 是：

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

请参阅本文中的[手动收集 ZooKeeper 仲裁列表](#manually-collect-the-zookeeper-quorum-list)，详细了解如何检索 HDInsight 群集的这些值。

CopyTable 实用工具还支持使用参数来指定要复制的行的时间范围，以及指定要复制的表中的列系列子集。 若要查看 CopyTable 支持的参数的完整列表，请运行不带任何参数的 CopyTable：

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable 将会扫描要复制到目标表的整个源表内容。 因此，在 CopyTable 执行时，这可能会降低 HBase 群集的性能。

> [!NOTE]
> 若要在表之间自动复制数据，请参阅 GitHub 上 [Azure HBase 实用工具](https://github.com/Azure/hbase-utils/tree/master/replication)存储库中的 `hdi_copy_table.sh` 脚本。

### <a name="manually-collect-the-zookeeper-quorum-list"></a>手动收集 ZooKeeper 仲裁列表

如果两个 HDInsight 群集位于同一个虚拟网络中，如前所述，内部主机名解析会自动进行。 若要对通过 VPN 网关连接的两个不同虚拟网络中的 HDInsight 群集使用 CopyTable，需要提供仲裁中 Zookeeper 节点的主机 IP 地址。

若要获取仲裁主机名，请运行以下 curl 命令：

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

该 curl 命令检索包含 HBase 配置信息的 JSON 文档，而 grep 命令只返回“hbase.zookeeper.quorum”条目，例如：

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

仲裁主机名称值为冒号右侧的整个字符串。

若要检索这些主机的 IP 地址，请针对上述列表中的每个主机使用以下 curl 命令：

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

在此 curl 命令中，`<zookeeperHostFullName>` 是 ZooKeeper 主机的完整 DNS 名称，例如 `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`。 该命令的输出包含指定主机的 IP 地址，例如：

    100    "ip" : "10.0.0.9",

收集仲裁中所有 ZooKeeper 节点的 IP 地址后，重新生成目标地址：

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

在示例中：

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>快照

使用快照可为 HBase 数据存储中的数据创建时间点备份。 快照的开销极小，并且在数秒内即可完成，因为快照操作实际上是一种元数据操作，只捕获该时刻存储中所有文件的名称。 创建快照时，不会复制实际数据。 快照依赖于 HDFS 中存储的数据不可变性质，其中的更新、删除和插入都以新数据表示。 可以在同一群集上还原（克隆）快照，或者将快照导出到另一个群集。

若要创建快照，请通过 SSH 连接到 HDInsight HBase 群集的头节点，然后启动 `hbase` shell：

    hbase shell

在 hbase shell 中，结合表和此快照的名称使用 snapshot 命令：

    snapshot '<tableName>', '<snapshotName>'

若要在 `hbase` shell 中按名称还原快照，请先禁用表，然后还原快照并重新启用表：

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

若要将快照还原到新表，请使用 clone_snapshot：

    clone_snapshot '<snapshotName>', '<newTableName>'

若要将某个快照导出到 HDFS 供另一个群集使用，请先根据前面所述创建该快照，然后使用 ExportSnapshot 实用工具。 请在与头节点建立的 SSH 会话中，而不是在 `hbase` shell 中运行此实用工具：

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>` 可以是源群集可访问的任何存储位置，应该指向目标群集所用的 hbase 文件夹。 例如，如果已将某个辅助 Azure 存储帐户附加到了源群集，并且使用该帐户可以访问目标群集的默认存储所用的容器，则可以使用以下命令：

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

导出快照后，通过 SSH 连接到目标群集的头节点，然后根据前面所述使用 restore_snapshot 命令还原快照。

快照提供执行 `snapshot` 命令时的表的完整备份。 快照不提供按时间范围执行增量快照的功能，也不允许指定要包含在快照中的列系列子集。

## <a name="replication"></a>复制

HBase 复制使用异步机制自动将事务从源群集推送到目标群集，并且只会在源群集上产生极少的开销。 在 HDInsight 中，可以在群集之间设置复制，其中：

* 源群集和目标群集位于同一虚拟网络中。
* 源群集和目标群集位于通过 VPN 网关连接的不同虚拟网络中，但两个群集位于相同的地理位置。
* 源群集和目标群集位于通过 VPN 网关连接的不同虚拟网络中，并且每个群集位于不同的地理位置。

设置复制的一般步骤如下：

1. 在源群集上，创建表并填充数据。
2. 在目标群集上，使用源表的架构创建空目标表。
3. 将目标群集注册为源群集的对等方。
4. 在所需的源表上启用复制。
5. 将源表中的现有数据复制到目标表。
6. 复制功能会自动将源表中新数据的修改内容复制到目标表。

若要在 HDInsight 上启用复制，请对运行中的源 HDInsight 群集应用脚本操作。 如需在群集中启用复制的演练，或要使用 Azure 资源管理模板对虚拟网络中创建的示例群集体验复制，请参阅[配置 HBase 复制](apache-hbase-replication.md)。 该文章还包含有关启用 Phoenix 元数据复制的说明。

## <a name="next-steps"></a>后续步骤

* [配置 HBase 复制](apache-hbase-replication.md)
