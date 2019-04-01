---
title: Azure HDInsight 增强的写入 Apache hbase （预览版）
description: 概述 Azure 增强型写入 HDInsight 功能，使用高级托管磁盘来提高性能的 Apache HBase 写入继续操作日志。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: 37e6f316a5202429396ddd2a31cb14fe61341e89
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759357"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Azure HDInsight 增强的写入 Apache hbase （预览版）

本文提供了背景**增强的写入**Azure HDInsight 中的 Apache HBase 的功能和如何它可用于有效地提高写入性能。 **增强的写入**使用[Azure 高级 SSD 托管磁盘](../../virtual-machines/linux/disks-types.md#premium-ssd)以提高性能的 Apache HBase 写入提前日志 (WAL)。 若要了解有关 Apache HBase 的详细信息，请参阅[什么是 HDInsight 中的 Apache HBase](apache-hbase-overview.md)。

## <a name="overview-of-hbase-architecture"></a>HBase 体系结构概述

在 HBase 中，**行**包含一个或多个**列**并由标识**行键**。 多个行构成**表**。 列包含**单元格**，这是该列中的值的时间戳的版本。 列分为**列系列**，和列系列中的所有列一起都存储在存储文件中名为**Hfile**。

**区域**在 HBase 用于进行数据处理负载平衡。 HBase 首先将表的行存储在单个区域和作为表增加数据量跨多个区域分布行。 **区域服务器**可以处理多个区域的请求。

## <a name="write-ahead-log-for-apache-hbase"></a>预写日志的 Apache HBase

在 HBase 中的数据更新将首先写入名为编写提前日志 (WAL) 的提交日志的类型。 更新存储在 WAL 后，它写入到内存中**MemStore**。 当内存中的数据达到其最大容量时，其写入磁盘， **HFile**。

如果**regionserver 内存**发生故障或变得不可用会刷新之前，请继续写入日志可用于重播的更新。 而无需 WAL，如果**regionserver 内存**刷新更新之前已崩溃**HFile**，所有丢失这些更新。

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Apache HBase 的 Azure HDInsight 中的增强型的写入功能

增强的写入功能解决了导致通过使用预写日志中的云存储的更高的写入的延迟问题。  对于 HDInsight Apache HBase 群集功能，增强的写入，附加高级 SSD 将托管磁盘到每个 regionserver 内存 （辅助角色节点）。 编写提前日志写入到 Hadoop 文件系统 (HDFS) 上这些高级托管磁盘而不是云存储装载。  高级托管的磁盘使用固态硬盘 (Ssd)，并提供带容错功能的极好的 I/O 性能。  与非托管磁盘，如果一个存储单元出现故障，它不会影响同一个可用性集中其他存储单元。  因此，托管的磁盘可以提供较低写入延迟和更好地为您的应用程序的复原能力。 若要详细了解 Azure 托管磁盘，请参阅[简介 Azure 托管磁盘](../../virtual-machines/windows/managed-disks-overview.md)。 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>如何为 HDInsight 中的 HBase 中启用增强的写入

若要使用增强的写入功能创建新的 HBase 群集，请按照中的步骤[在 HDInsight 中设置群集](../hdinsight-hadoop-provision-linux-clusters.md)直至到达**步骤 3 中，存储**。 下**元存储设置**，单击旁边的复选框**启用增强的写入 （预览版）**。 然后，继续创建群集的剩余步骤。

![启用增强写入 HDInsight Apache hbase 的选项](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

## <a name="other-considerations"></a>其他注意事项

若要保留的数据持久性，请至少包含三个工作节点创建群集。 创建后，不能缩减到少于三个工作节点群集。 

刷新或删除该群集之前禁用 HBase 表，不会丢失继续写入日志数据。

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>后续步骤

* 上的官方 Apache HBase 文档[继续写入日志功能](https://hbase.apache.org/book.html#wal)。