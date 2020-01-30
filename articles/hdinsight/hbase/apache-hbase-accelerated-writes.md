---
title: 适用于 Apache HBase 的 Azure HDInsight 加速写入
description: 概述 Azure HDInsight 加速写入功能，该功能使用高级托管磁盘来改善 Apache HBase 预写入日志的性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764578"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>适用于 Apache HBase 的 Azure HDInsight 加速写入

本文提供了有关 Azure HDInsight 中 Apache HBase 的**加速写入**功能的背景知识，以及如何有效地使用它来提高写入性能。 **加速写入**使用[Azure 高级 SSD 托管磁盘](../../virtual-machines/linux/disks-types.md#premium-ssd)来改善 Apache HBase 写入日志的性能（WAL）。 若要了解有关 Apache HBase 的详细信息，请参阅[什么是 HDInsight 中的 Apache HBase](apache-hbase-overview.md)。

## <a name="overview-of-hbase-architecture"></a>HBase 体系结构概述

在 HBase 中，**一行**由一个或多个**列**组成，由**行键**标识。 多行构成一个**表**。 列包含**单元**，它们是该列中值的有时间戳的版本。 列按**列系列**分组，列系列中的所有列一起存储在名为**hfile**的存储文件中。

HBase 中的**区域**用于平衡数据处理负载。 HBase 首先将表中的行存储在单个区域中。 当表中的数据量增加时，这些行将分散到多个区域。 **区域服务器**可以处理多个区域的请求。

## <a name="write-ahead-log-for-apache-hbase"></a>为 Apache HBase 编写提前日志

HBase 首先将数据更新写入一种称为 "提前写入日志" （WAL）的提交日志。 更新存储在 WAL 中后，会写入内存中**MemStore**。 当内存中的数据达到其最大容量时，将以**HFile**的形式写入磁盘。

如果在刷新 MemStore 之前， **RegionServer**崩溃或变得不可用，则可以使用 "提前写" 日志来重播更新。 如果不使用 WAL， **RegionServer**在刷新对**HFile**的更新之前崩溃，则所有更新都将丢失。

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>适用于 Apache HBase 的 Azure HDInsight 中的加速写入功能

加速写入功能可解决由于使用云存储中的预写日志而导致更高写入延迟的问题。  适用于 HDInsight Apache HBase 群集的加速写入功能将高级 SSD 托管磁盘附加到每个 RegionServer （辅助节点）。 然后将写入的日志写入到在这些高级托管磁盘（而不是云存储）上装载的 Hadoop 文件系统（HDFS）。  高级托管磁盘使用固态磁盘（Ssd），并提供具有容错能力的出色 i/o 性能。  与非托管磁盘不同，如果一个存储单元出现故障，则它不会影响同一可用性集中的其他存储单元。  因此，托管磁盘为应用程序提供低写入延迟和更好的复原能力。 若要详细了解 Azure 托管磁盘，请参阅[azure 托管磁盘简介](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>如何在 HDInsight 中启用 HBase 的加速写入

若要使用加速写入功能创建新的 HBase 群集，请按照在[HDInsight 中设置群集中](../hdinsight-hadoop-provision-linux-clusters.md)的步骤进行操作 **，直到达到步骤3的存储**。 在 "**元存储设置**" 下，选中 "**启用 HBase 加速写入**" 旁边的复选框。 然后，继续执行群集创建的剩余步骤。

![为 HDInsight Apache HBase 启用加速写入选项](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>其他注意事项

若要保持数据持久性，请创建至少包含三个辅助角色节点的群集。 创建后，不能将群集向下扩展到小于三个工作节点。

请在删除群集之前刷新或禁用 HBase 表，以便不会丢失写入日志数据。

```
flush 'mytable'
```

```
disable 'mytable'
```

按比例缩小群集时执行类似步骤：刷新表并禁用表以停止传入的数据。 不能将群集缩小到少于三个节点。

按照这些步骤操作，可以确保向下滚动，并避免 namenode 进入安全模式，这是由复制或临时文件引起的。

如果 namenode 在缩小后进入安全模式，请使用 hdfs 命令重新复制未复制的块，并从安全模式获取 hdfs。 此重新复制将允许你成功重新启动 HBase。

## <a name="next-steps"></a>后续步骤

* 有关[预写日志功能](https://hbase.apache.org/book.html#wal)的官方 Apache HBase 文档
* 若要升级 HDInsight Apache HBase 群集以使用加速写入，请参阅将[Apache hbase 群集迁移到新版本](apache-hbase-migrate-new-version.md)。
