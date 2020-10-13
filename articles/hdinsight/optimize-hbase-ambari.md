---
title: 在 Azure HDInsight 中使用 Apache Ambari 优化 Apache HBase
description: 使用 Apache Ambari Web UI 来配置和优化 Apache HBase。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: b262e07bd07320e4b10b12a2f2cf07b97e58c61e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91821706"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 Apache Ambari 优化 Apache HBase

Apache Ambari 是用于管理和监视 HDInsight 群集的 Web 界面。 有关 Ambari Web UI 的简介，请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

可以通过“HBase 配置”**** 选项卡修改 Apache HBase 配置。以下部分介绍了一些影响 HBase 性能的重要配置设置。

## <a name="set-hbase_heapsize"></a>设置 HBASE_HEAPSIZE

HBase 堆大小指定区域服务器和主服务器要使用的最大堆数量（以 MB 为单位）。** ** 默认值为 1,000 MB。 应该为群集工作负荷优化此值。

1. 若要修改，请导航到 HBase“配置”选项卡中的“高级 HBase-env”窗格，然后找到 `HBASE_HEAPSIZE` 设置。**** ****

1. 将默认值更改为 5,000 MB。

    ![“Apache Ambari HBase 内存堆大小”](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>优化读取密集型工作负荷

以下配置对于提高读取密集型工作负荷的性能非常重要。

### <a name="block-cache-size"></a>块缓存大小

块缓存是读取缓存。 其大小由 `hfile.block.cache.size` 参数控制。 默认值为 0.4，即总区域服务器内存的 40%。 块缓存大小越大，随机读取的速度越快。

1. 若要修改此参数，请导航到 HBase“配置”选项卡中的“设置”选项卡，然后找到“分配到读取缓冲区的 RegionServer 内存百分比”。**** **** ****

    ![“Apache HBase 内存块缓存大小”](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. 若要更改此值，请选择“编辑”图标。****

### <a name="memstore-size"></a>Memstore 大小

所有编辑内容都存储在称作 *Memstore* 的内存缓冲区中。 此缓冲区增加了可以在单个操作中写入磁盘的数据总量。 它还加快了对最近编辑内容的访问速度。 Memstore 大小由以下两个参数定义：

* `hbase.regionserver.global.memstore.UpperLimit`：定义 Memstore 总共可以使用的最大区域服务器百分比。

* `hbase.regionserver.global.memstore.LowerLimit`：定义 Memstore 总共可以使用的最小区域服务器百分比。

若要优化随机读取，可以减小 Memstore 的上限和下限。

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>从磁盘扫描时提取的行数

`hbase.client.scanner.caching` 设置定义在扫描程序中调用 `next` 方法时，要从磁盘读取的行数。  默认值为 100。 该数字越大，从客户端向区域服务器发出的远程调用数就越少，因而扫描速度也就越快。 但是，此设置也会增加客户端上的内存压力。

![Apache HBase 提取的行数](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> 设置此值时，请不要使扫描程序中的下一次方法调用间隔时间大于扫描程序的超时时间。 扫描程序超时期限由 `hbase.regionserver.lease.period` 属性定义。

## <a name="optimize-write-heavy-workloads"></a>优化写入密集型工作负荷

以下配置对于提高写入密集型工作负荷的性能非常重要。

### <a name="maximum-region-file-size"></a>最大区域文件大小

HBase 使用称作 *HFile* 的内部文件格式存储数据。 属性 `hbase.hregion.max.filesize` 定义区域的单个 HFile 的大小。  如果区域中的 HFiles 总数大于此设置，则会将该区域拆分为两个区域。

![“Apache HBase HRegion 最大文件大小”](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

区域文件大小越大，拆分数目越小。 可以增大文件大小，以确定可以最大程度地提高写入性能的值。

### <a name="avoid-update-blocking"></a>避免阻止更新

* 属性 `hbase.hregion.memstore.flush.size` 定义 Memstore 刷新到磁盘的增量大小。 默认大小为 128 MB。

* Hbase 区域块乘数由 `hbase.hregion.memstore.block.multiplier` 定义。 默认值为 4。 允许的最大值为 8。

* 如果 Memstore 为 (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) 字节，则 HBase 会阻止更新。

    使用刷新大小和块乘数的默认值时，如果 Memstore 大小为 128 * 4 = 512 MB，则会阻止更新。 若要减少更新阻止计数，请增大 `hbase.hregion.memstore.block.multiplier` 的值。

![Apache HBase 区域块乘数](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>定义 Memstore 大小

Memstore 大小由 `hbase.regionserver.global.memstore.upperLimit` 和 `hbase.regionserver.global.memstore.lowerLimit` 参数定义。 将这些值设置为相等可以减少写入期间的暂停次数（同时提高刷新频率），并可以提高写入性能。

## <a name="set-memstore-local-allocation-buffer"></a>设置 Memstore 本地分配缓冲区

Memstore 本地分配缓冲区使用率由 `hbase.hregion.memstore.mslab.enabled` 属性确定。 如果已启用 (true)，则此设置可以防止在执行写入密集型操作期间出现堆碎片。 默认值为 true。

![hbase.hregion.memstore.mslab.enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>后续步骤

* [使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [优化群集](./hdinsight-changing-configs-via-ambari.md)
* [优化 Apache Hive](./optimize-hive-ambari.md)
* [优化 Apache Pig](./optimize-pig-ambari.md)
