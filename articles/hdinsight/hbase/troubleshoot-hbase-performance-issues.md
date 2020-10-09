---
title: 在 Azure HDInsight 上排查 Apache HBase 性能问题
description: 有关在 Azure HDInsight 上获得最佳性能的各种 Apache HBase 性能优化指导和提示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887149"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>在 Azure HDInsight 上排查 Apache HBase 性能问题

本文介绍有关在 Azure HDInsight 上获得最佳性能的各种 Apache HBase 性能优化指导和提示。 其中的许多提示取决于特定的工作负荷和读/写/扫描模式。 在生产环境中应用配置更改之前，请对其进行全面的测试。

## <a name="hbase-performance-insights"></a>HBase 性能见解

大多数 HBase 工作负荷的最大瓶颈是“预写日志”(WAL)。 它会严重影响写入性能。 HDInsight HBase 具有独立的存储计算模型。 即使虚拟机托管区域服务器，数据也会远程存储在 Azure 存储中。 从最近开始，WAL 也会写入到 Azure 存储。 在 HDInsight 中，此行为严重化了此瓶颈。 [加速写入](./apache-hbase-accelerated-writes.md)功能旨在解决此问题。 它将 WAL 写入到 Azure 高级 SSD 托管磁盘。 此功能大大提高了写入性能，并有助于解决某些写入密集型工作负荷面临的许多问题。

若要显著改善读取性能，可使用[高级块 Blob 存储帐户](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)作为远程存储。 仅当启用了 WAL 功能时，此选项才可用。

## <a name="compaction"></a>压缩

压缩是社区中在根本上达成了共识的另一个潜在瓶颈。 HDInsight HBase 群集上默认已禁用主要压缩。 之所以禁用压缩，是因为即使对于资源密集型进程，客户也可以根据其工作负荷灵活进行安排。 例如，他们可将此进程安排在非高峰时段运行。 此外，数据位置并不是一个问题，因为我们的存储是远程的（由 Azure 存储提供支持），而不是本地的 Hadoop 分布式文件系统 (HDFS)。

客户应将压缩安排在方便的时间。 如果他们不执行这种维护，则从长期来看，压缩会对读取性能产生不利影响。

对于扫描操作，应该关注平均延迟超过 100 毫秒的情况。 检查是否准确安排了主要压缩。

## <a name="apache-phoenix-workload"></a>Apache Phoenix 工作负荷

回答以下问题有助于更好地了解 Apache Phoenix 工作负荷：

* 是否将所有“读取”转换为扫描？
    * 如果是，这些扫描的特征是什么？
    * 是否优化了这些扫描的 Phoenix 表架构（包括适当的索引）？
* 是否已使用 `EXPLAIN` 语句来了解“读取”生成的查询计划？
* 写入是否为“upsert-select”？
    * 如果是，则它们也会执行扫描。 预期的扫描平均延迟大约为 100 毫秒，而 HBase 中的延迟为 10 毫秒。  

## <a name="test-methodology-and-metrics-monitoring"></a>测试方法和指标监视

如果使用 Yahoo! Cloud Serving Benchmark、JMeter 或 Pherf 等基准来测试和优化性能，请确保：

- 客户端计算机不会成为瓶颈。 为此，请检查客户端计算机上的 CPU 使用率。

- 客户端配置（例如线程数）将会相应地优化以使客户端带宽饱和。

- 系统性地准确记录测试结果。

如果查询性能突然比以前要差得多，请检查应用程序代码中的潜在 bug。 代码是否突然生成大量的无效数据？ 如果是，则可能会增大读取延迟。

## <a name="migration-issues"></a>迁移问题

如果迁移到 Azure HDInsight，请确保系统性地准确执行迁移，最好是通过自动化方式执行。 避免手动迁移。 请确保：

- 准确迁移表属性。 压缩的属性可以包含布隆筛选器等。

- Phoenix 表中的加盐设置相应地映射到新的群集大小。 例如，盐桶数应是群集中工作器节点数的倍数。 使用的倍数应是热点数量的系数。

## <a name="server-side-configuration-tunings"></a>服务器端配置优化

在 HDInsight HBase 中，Hfile 存储在远程存储上。 如果发生缓存未命中，则读取成本将会高于本地系统，因为本地系统上的数据由本地 HDFS 提供支持。 对于大多数方案，HBase 缓存（块缓存和桶缓存）的智能用法旨在规避此问题。 如果未规避此问题，使用高级块 Blob 帐户可能有助于解决此问题。 Windows Azure 存储 Blob 驱动程序依赖于使用 `fs.azure.read.request.size` 等属性，根据确定为读取模式的对象（顺序与随机）在块中提取数据，因此仍然存在读取延迟增大的情况。 我们通过实证试验发现，在实践中将读取请求块大小 (`fs.azure.read.request.size`) 设置为 512 KB 并使用相同的 HBase 表块大小可产生最佳效果。

对于大部分大节点群集，HDInsight HBase 在附加到运行 `regionservers` 的虚拟机的本地高级 SSD 上以文件的形式提供 `bucketcache`。 改用堆外缓存可能会带来一定的改进。 此解决方法限制为使用可用内存，并且可能小于基于文件的缓存，因此不一定是最佳选择。

下面是我们优化的其他一些特定参数，它们似乎可在不同的程度上提供帮助：

- 将 `memstore` 大小从默认的 128 MB 增大至 256 MB。 通常建议对繁重写入方案使用此设置。

- 将专门用于压缩的线程数从默认设置 **1** 增大至 **4**。 如果观察到频繁的次要压缩，则此设置是相关的。

- 由于存储限制，请避免阻止 `memstore` 刷新。 若要提供此缓冲区，请将 `Hbase.hstore.blockingStoreFiles` 设置增大至 **100**。

- 若要控制刷新，请使用以下设置：

    - `Hbase.regionserver.maxlogs`：**140**（避免因 WAL 限制而导致的刷新）

    - `Hbase.regionserver.global.memstore.lowerLimit`：**0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`：**0.60**

- 用于线程池优化的 Phoenix 特定配置：

    - `Phoenix.query.queuesize`：**10000**

    - `Phoenix.query.threadpoolsize`：**512**

- 其他 Phoenix 特定配置：

    - `Phoenix.rpc.index.handler.count`：**50**（如果存在大型或众多的索引查找）

    - `Phoenix.stats.updateFrequency`：**1 小时**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`：**1 小时**

    - `Phoenix.coprocessor.maxmetadatacachesize`：**50 MB**

- RPC 超时：**3 分钟**

   - RPC 超时包括 HBase RPC 超时、HBase 客户端扫描程序超时和 Phoenix 查询超时。 
   - 请确保在服务器端和客户端端将 `hbase.client.scanner.caching` 参数设置为相同的值。 如果两者不同，此设置会导致 `OutOfOrderScannerException`相关的客户端错误。 对于大型扫描，应将此设置指定为较小值。 我们将此值设置为 **100**。

## <a name="other-considerations"></a>其他注意事项

下面是要考虑优化的其他参数：

- `Hbase.rs.cacheblocksonwrite` – 在 HDI 上，此设置默认指定为 **true**。

- 用于推迟次要压缩的设置。

- 试验性设置，例如，调整保留给读取和写入请求使用的队列百分比。

## <a name="next-steps"></a>后续步骤

如果问题仍未解决，请访问以下通道之一以获得更多支持：

- 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

- 联系 [@AzureSupport](https://twitter.com/azuresupport)。 这是用于改善客户体验的官方 Microsoft Azure 帐户。 它将 Azure 社区连接到适当的资源：答案、支持和专家。

- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 你的 Microsoft Azure 订阅包括对订阅管理和计费支持的访问权限，并且通过一个 [Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
