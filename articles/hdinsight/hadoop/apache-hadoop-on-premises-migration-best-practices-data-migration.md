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
ms.openlocfilehash: 6b06b8eb8d5e18acd3107ec5cccac79fc7be7edc
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418171"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 数据迁移最佳做法

本文提供有关将数据迁移到 Azure HDInsight 的建议。 本文是帮助用户将本地 Apache Hadoop 系统迁移到 Azure HDInsight 的最佳做法系列教程中的其中一篇。

## <a name="migrate-data-from-on-premises-to-azure"></a>将数据从本地迁移到 Azure

有两个主要选项可将数据从本地迁移到 Azure 环境：

1.  使用 TLS 通过网络传输数据
    1.  通过 Internet
    2.  Express Route
2.  传送数据
    1.  导入/导出服务
        - 仅限内部 SATA HDD 或 SSD
        - 静态加密（AES-128/AES-256）
        - 导入作业最多可以包括 10 个磁盘
        - 已在所有公共区域和正式版中推出
    1.  Data Box
        - 每个 Data Box 最多包含 80 TB 数据
        - 静态加密 (AES-256)
        - 使用 NAS 协议，并支持常见的数据复制工具
        - 加固的硬件
        - 仅在美国和公共预览版中推出

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

可以使用 Azure 的本机工具（例如 DistCp、Azure 数据工厂和 AzureCp）通过网络传输数据。 也可以使用第三方工具 WANDisco 实现相同的目的。 使用 Kafka Mirrormaker 和 Sqoop 可以持续将数据从本地传输到 Azure 存储系统。

## <a name="performance-considerations-when-using-apache-distcp"></a>使用 Apache DistCp 时的性能注意事项

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

### <a name="hive"></a>Hive

可以使用脚本或数据库复制来迁移 Hive 元存储。

#### <a name="hive-metastore-migration-using-scripts"></a>使用脚本迁移 Hive 元存储

1. 从本地 Hive 元存储生成 Hive DDL。 可以使用 [包装器 bash 脚本](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md) 完成此步骤
1. 编辑生成的 DDL，将 HDFS URL 替换为 WASB/ADLS/ABFS URL
1. 针对 HDInsight 群集中的元存储运行更新的 DDL
1. 确保本地与云之间的 Hive 元存储版本兼容

#### <a name="hive-metastore-migration-using-db-replication"></a>使用数据库复制迁移 Hive 元存储

- 在本地 Hive 元存储数据库与 HDInsight 元存储数据库之间设置数据库复制
- 使用“Hive MetaTool”将 HDFS URL 替换为 WASB/ADLS/ABFS URL，例如：

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="ranger"></a>Ranger

- 将本地 Ranger 策略导出到 XML 文件。
- 使用 XSLT 等工具将基于 HDFS 的本地特定路径转换为 WASB/ADLS。
- 将策略导入到 HDInsight 上运行的 Ranger。

## <a name="next-steps"></a>后续步骤

阅读本系列教程的下一篇文章：

- [有关从本地迁移到 Azure HDInsight Hadoop 的安全和 DevOps 最佳做法](apache-hadoop-on-premises-migration-best-practices-security-devops.md)