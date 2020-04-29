---
title: Azure HDInsight 的 Apache HBase 加速写入
description: 概述 Azure HDInsight 加速写入功能。该功能使用高级托管磁盘来提高 Apache HBase 预写日志的性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76764578"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight 的 Apache HBase 加速写入

本文提供有关 Azure HDInsight 中 Apache HBase 的**加速写入**功能的背景信息，以及如何使用它来有效提高写入性能。 **加速写入**使用 [Azure 高级 SSD 托管磁盘](../../virtual-machines/linux/disks-types.md#premium-ssd)来提高 Apache HBase 预写日志 (WAL) 的性能。 有关 Apache HBase 的详细信息，请参阅 [HDInsight 中的 Apache HBase 是什么](apache-hbase-overview.md)。

## <a name="overview-of-hbase-architecture"></a>HBase 体系结构概述

在 HBase 中，**行**由一个或多个**列**构成，并由**行键**标识。 多个行构成了一个**表**。 列包含**单元格** - 该列中的值的带时间戳版本。 列分组成**列系列**，列系列中的所有列一起存储在名为 **HFile** 的存储文件中。

HBase 中的**区域**用于平衡数据处理负载。 HBase 最初在单个区域中存储表的行。 随着表中的数据量不断增大，行将分散到多个区域。 **区域服务器**可以处理多个区域的请求。

## <a name="write-ahead-log-for-apache-hbase"></a>Apache HBase 的预写日志

HBase 最初会将数据更新写入到一种名为“预写日志”(WAL) 的提交日志中。 更新存储到 WAL 后，将写入到内存中的 **MemStore**。 当内存中的数据达到其最大容量时，将作为 **HFile** 写入到磁盘中。

如果在刷写 MemStore 之前**区域服务器**崩溃或不可用，可以使用预写日志来重放更新。 在不使用 WAL 的情况下，如果在将更新刷写到 **HFile** 之前**区域服务器**崩溃，所有这些更新都会丢失。

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Azure HDInsight 中 Apache HBase 的加速写入功能

加速写入功能解决了使用云存储中的预写日志导致写入延迟增大的问题。  HDInsight Apache HBase 群集的加速写入功能将高级 SSD 托管磁盘附加到每个区域服务器（工作器节点）。 然后，预写日志将写入到这些高级托管磁盘中装载的 Hadoop 文件系统 (HDFS)，而不是写入到云存储。  高级托管磁盘使用固态硬盘 (SSD)，提供卓越的 I/O 性能和容错能力。  与非托管磁盘不同，如果一个存储单元出现故障，则它不会影响同一可用性集中的其他存储单元。  因此，托管磁盘可为应用程序提供较低的写入延迟和更好的复原能力。 有关 Azure 托管磁盘的详细信息，请参阅 [Azure 托管磁盘简介](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>如何启用 HDInsight 中 HBase 的加速写入

若要使用加速写入功能创建新的 HBase 群集，请执行[在 HDInsight 中设置群集](../hdinsight-hadoop-provision-linux-clusters.md)中的步骤，直到“步骤 3：存储”。  在“元存储设置”下，选中“启用 HBase 加速写入”旁边的复选框。   然后，继续执行剩余的步骤创建群集。

![启用 HDInsight Apache HBase 的加速写入选项](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>其他注意事项

若要保留数据持久性，请创建至少包含三个工作器节点的群集。 创建后，无法将群集缩减为包含三个以下的工作器节点。

在删除群集之前，请刷写或禁用 HBase 表，以免丢失预写日志数据。

```
flush 'mytable'
```

```
disable 'mytable'
```

缩减群集时按照类似的步骤操作：刷新表并禁用表以停止传入数据。 不能将群集缩减为少于三个节点。

按照这些步骤操作将确保成功缩小规模，并避免由于复制不足或临时文件而导致 namenode 进入安全模式的可能性。

如果在缩小规模后 namenode 确实进入了安全模式，请使用 hdfs 命令重新复制复制不足的块，并使 hdfs 退出安全模式。 通过此复制，可以成功地重启 HBase。

## <a name="next-steps"></a>后续步骤

* 有关[预写日志功能](https://hbase.apache.org/book.html#wal)的官方 Apache HBase 文档
* 若要升级 HDInsight Apache HBase 群集以使用加速写入，请参阅[将 Apache HBase 群集迁移到新版本](apache-hbase-migrate-new-version.md)。
