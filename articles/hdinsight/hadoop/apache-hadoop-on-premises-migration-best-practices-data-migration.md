---
title: 将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 数据迁移最佳做法
description: 了解有关将本地 Hadoop 群集迁移到 Azure HDInsight 的数据迁移最佳做法。
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 5d0259726a45346f1e9b891cb235531d6c24d4a2
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433417"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 数据迁移最佳做法

本文提供有关将数据迁移到 Azure HDInsight 的建议。 本文是帮助用户将本地 Apache Hadoop 系统迁移到 Azure HDInsight 的最佳做法系列教程中的其中一篇。

## <a name="migrate-on-premises-data-to-azure"></a>将本地数据迁移到 Azure

有两个主要选项可将数据从本地迁移到 Azure 环境：

1.  使用 TLS 通过网络传输数据
    1. 通过 Internet - 可以使用以下多个工具中的任意一个将数据通过常规 Internet 连接传输到 Azure 存储：Azure 存储资源管理器、AzCopy、Azure Powershell 和 Azure CLI。  有关详细信息，请参阅[将数据移入和移出 Azure 存储](../../storage/common/storage-moving-data.md)。
    2. Express Route - ExpressRoute 是一项 Azure 服务，允许在 Microsoft 数据中心与本地环境或共同租用设施中的基础结构之间创建专用连接。 ExpressRoute 连接不通过公共 Internet，与通过 Internet 的典型连接相比，提供更高的安全性、可靠性、速度和更低的延迟。 有关详细信息，请参阅[创建和修改 ExpressRoute 线路](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    1. Data Box 联机数据传输 - Data Box Edge 和 Data Box Gateway 是联机数据传输产品，它们用作网络存储网关来管理站点和 Azure 之间的数据。 Data Box Edge 是一种本地网络设备，可将数据传入和传出 Azure，并使用支持人工智能 (AI) 的边缘计算来处理数据。 Data Box Gateway 是具有存储网关功能的虚拟设备。 有关详细信息，请参阅 [Azure Data Box 文档 - 联机传输](https://docs.microsoft.com/azure/databox-online/)。
1.  脱机寄送数据
    1. 导入 / 导出服务 - 你可以将物理磁盘发送到 Azure，他们将为你上传数据。 有关详细信息，请转到[什么是 Azure 导入/导出服务？](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。
    1. Data Box 脱机数据传输 - Data Box、Data Box Disk 和 Data Box Heavy 设备可在网络不可用时将大量数据传输到 Azure。 这些脱机数据传输设备在组织和 Azure 数据中心之间往返运输。 它们使用 AES 加密来帮助保护传输中的数据，还在上传后执行一个清理过程，从设备中删除你的数据。 有关详细信息，请参阅 [Azure Data Box 文档 - 脱机传输](https://docs.microsoft.com/azure/databox/)。

下表根据数据量和网络带宽列出了大致的数据传输持续时间。 如果数据迁移预计需要花费三周以上，请使用 Data Box。

|**数据量**|**网络带宽**|||
|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**
|1 TB|2 天|1 天| 2 小时|14 分钟|
|10 TB|22 天|10 天|1 天|2 小时|
|35 TB|76 天|34 天|3 天|8 小时|
|80 TB|173 天|78 天|8 天|19 小时|
|100 TB|216 天|97 天|10 天|1 天|
|200 TB|1 年|194 天|19 天|2 天|
|500 TB|3 年|1 年|49 天|5 天|
|1 PB|6 年|3 年|97 天|10 天|
|2 PB|12 年|5 年|194 天|19 天|

可以使用 Azure 的本机工具（例如 Apache Hadoop DistCp、Azure 数据工厂和 AzureCp）通过网络传输数据。 也可以使用第三方工具 WANDisco 实现相同的目的。 使用 Apache Kafka Mirrormaker 和 Apache Sqoop 可以持续将数据从本地传输到 Azure 存储系统。


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>使用 Apache Hadoop DistCp 时的性能注意事项


DistCp 是一个 Apache 项目，它使用 MapReduce 映射作业来传输数据、处理错误以及从这些错误中恢复。 它将源文件列表分配到每个映射任务。 然后，映射任务将其所有已分配的文件复制到目标。 可通过多种方法来提高 DistCp 的性能。

### <a name="increase-the-number-of-mappers"></a>增加映射器数目

DistCp 会尝试创建映射任务，使每个副本的字节数大致相同。 默认情况下，DistCp 作业使用 20 个映射器。 对 Distcp 使用更多的映射器（在命令行中包含“m”参数）可在数据传输过程中提高并行度，减少数据传输的时长。 但是，增加映射器时需要注意两点：

1. DistCp 的最低粒度是一个文件。 指定超过源文件数目的映射器数目没有任何帮助，而且会浪费可用的群集资源。
1. 确定映射器数目时，请考虑群集上的可用 Yarn 内存。 每个映射任务作为 Yarn 容器启动。 假设群集上没有其他繁重的工作负荷在运行，可通过以下公式确定映射器数目：m = (工作节点数 \* 每个工作节点的 YARN 内存) / YARN 容器大小。 但是，如果其他应用程序正在使用内存，请选择仅将一部分 YARN 内存用于 DistCp 作业。

### <a name="use-more-than-one-distcp-job"></a>使用多个 DistCp 作业

当要移动的数据集大小超过 1 TB 时，请使用多个 DistCp 作业。 使用多个作业可以限制故障造成的影响。 如果任一作业失败，你只需重启该特定作业，而无需重启所有作业。

### <a name="consider-splitting-files"></a>考虑拆分文件

如果大型文件较少，请考虑将它们拆分为 256-MB 的文件块，以通过更多的映射器获得更高的潜在并发性。

### <a name="use-the-strategy-command-line-parameter"></a>使用“strategy”命令行参数

考虑在命令行中使用 `strategy = dynamic` 参数。 `strategy` 参数的默认值为 `uniform size`，在这种情况下，每个映射副本的字节数大致相同。 如果将此参数更改为 `dynamic`，则列表文件将拆分为多个“块文件”。 块文件的数目是映射数的倍数。 为每个映射任务分配一个块文件。 处理块中的所有路径后，将删除当前块，并获取新块。 进程将会继续，直到没有其他可用的块。 这种“动态”方法使快速映射任务能够使用比慢速映射任务更多的路径，从而加快 DistCp 作业的总体速度。

### <a name="increase-the-number-of-threads"></a>增加线程数目

看看增大 `-numListstatusThreads` 参数是否能够提高性能。 此参数控制用于生成文件列表的线程数，最大值为 40。

### <a name="use-the-output-committer-algorithm"></a>使用输出提交器算法

看看传递参数 `-Dmapreduce.fileoutputcommitter.algorithm.version=2` 是否能够提高 DistCp 的性能。 此输出提交器算法在将输出文件写入到目标方面做了优化。 以下示例命令演示了不同参数的用法：

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>元数据迁移

### <a name="apache-hive"></a>Apache Hive

可以使用脚本或数据库复制来迁移 Hive 元存储。

#### <a name="hive-metastore-migration-using-scripts"></a>使用脚本迁移 Hive 元存储

1. 从本地 Hive 元存储生成 Hive DDL。 可以使用[包装器 bash 脚本](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)完成此步骤。
1. 编辑生成的 DDL，将 HDFS URL 替换为 WASB/ADLS/ABFS URL。
1. 针对 HDInsight 群集中的元存储运行更新的 DDL。
1. 确保本地与云之间的 Hive 元存储版本兼容。

#### <a name="hive-metastore-migration-using-db-replication"></a>使用数据库复制迁移 Hive 元存储

- 在本地 Hive 元存储 DB 与 HDInsight 元存储 DB 之间设置数据库复制。
- 使用“Hive MetaTool”将 HDFS URL 替换为 WASB/ADLS/ABFS URL，例如：

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- 将本地 Ranger 策略导出到 XML 文件。
- 使用 XSLT 等工具将基于 HDFS 的本地特定路径转换为 WASB/ADLS。
- 将策略导入到 HDInsight 上运行的 Ranger。

## <a name="next-steps"></a>后续步骤

阅读本系列教程的下一篇文章：

- [有关从本地迁移到 Azure HDInsight Hadoop 的安全和 DevOps 最佳做法](apache-hadoop-on-premises-migration-best-practices-security-devops.md)