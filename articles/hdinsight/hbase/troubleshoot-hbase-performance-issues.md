---
title: 排查 Azure HDInsight 上的 Apache HBase 性能问题
description: 用于在 Azure HDInsight 上获得最佳性能的各种 Apache HBase 性能优化指导原则和技巧。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887149"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>排查 Azure HDInsight 上的 Apache HBase 性能问题

本文介绍有关在 Azure HDInsight 上获得最佳性能的各种 Apache HBase 性能优化指导原则和技巧。 其中的许多提示取决于特定的工作负荷和读/写/扫描模式。 在生产环境中应用配置更改之前，请对它们进行全面测试。

## <a name="hbase-performance-insights"></a>HBase 性能见解

大多数 HBase 工作负载中的最大瓶颈是 "预写日志（WAL）"。 这会对写入性能产生严重影响。 HDInsight HBase 包含单独的存储计算模型。 数据以远程方式存储在 Azure 存储中，即使虚拟机托管了区域服务器。 到目前为止，WAL 也已写入 Azure 存储。 在 HDInsight 中，此行为扩大了此瓶颈。 [加速写入](./apache-hbase-accelerated-writes.md)功能旨在解决此问题。 它将 WAL 写入 Azure 高级 SSD 托管磁盘。 这种极大的优点是写入性能，它有助于一些写入密集型工作负荷面临的许多问题。

若要显著提高读取操作的性能，请使用[高级块 Blob 存储帐户](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)作为远程存储。 仅当启用了 WAL 功能时，此选项才可用。

## <a name="compaction"></a>压缩

压缩是在社区中以根本上达成共识的另一个潜在瓶颈。 默认情况下，在 HDInsight HBase 群集上禁用主要压缩。 压缩功能是禁用的，因为即使是资源密集型过程，客户也可以根据工作负荷灵活地进行计划。 例如，它们可能会在非高峰时段进行计划。 而且，数据位置并不是一个问题，因为我们的存储是远程的（由 Azure 存储提供支持），而不是本地 Hadoop 分布式文件系统（HDFS）。

客户应在方便时计划主要压缩。 如果未执行此维护，压缩会对长时间运行的读取性能产生不利影响。

对于扫描操作，大于100毫秒的平均延迟应是导致问题的原因。 检查是否已准确安排重大压缩。

## <a name="apache-phoenix-workload"></a>Apache Phoenix 工作负荷

回答以下问题将有助于更好地了解 Apache Phoenix 工作负荷：

* 您是否要将 "读取" 转换为扫描？
    * 如果是这样，这些扫描的特征是什么？
    * 是否为这些扫描（包括适当的索引）优化了 Phoenix 表架构？
* 你是否已使用 `EXPLAIN` 语句来了解你的 "读取" 生成的查询计划？
* 你的写入是 "upsert" 吗？
    * 如果是这样，则它们也会进行扫描。 预计的扫描延迟大约为100毫秒，而 HBase 中的 point 的延迟时间为10毫秒。  

## <a name="test-methodology-and-metrics-monitoring"></a>测试方法和指标监视

如果使用 Yahoo！等基准 云服务基准、JMeter 或 Pherf 要测试和优化性能，请确保：

- 客户端计算机不会成为瓶颈。 为此，请检查客户端计算机上的 CPU 使用率。

- 客户端配置（如线程数）将适当调整以使客户端带宽饱和。

- 正确地记录测试结果。

如果查询突然开始比之前更糟，请检查应用程序代码中的潜在 bug。 它是否突然生成了大量无效数据？ 如果是，则可能会增加读取延迟。

## <a name="migration-issues"></a>迁移问题

如果要迁移到 Azure HDInsight，请确保迁移是系统系统完成的，并且最好通过自动化进行。 避免手动迁移。 请确保：

- 表属性可准确地迁移。 属性可以包含为压缩、布隆筛选器等。

- Phoenix 表中的加盐设置将正确映射到新的群集大小。 例如，salt 存储桶的数量应该是群集中的辅助角色节点数的倍数。 而且，您应该使用多个，它是热发现量的一个因素。

## <a name="server-side-configuration-tunings"></a>服务器端配置 tunings

在 HDInsight HBase 中，Hfile 存储在远程存储中。 如果缓存未命中，则读取成本比本地系统高，因为本地系统上的数据受本地 HDFS 的支持。 在大多数情况下，可智能地使用 HBase 缓存（块缓存和 bucket 缓存）来避免此问题。 如果问题未被规避，使用高级块 blob 帐户可能会帮助解决此问题。 Windows Azure 存储 Blob 驱动程序依赖于某些属性（如 `fs.azure.read.request.size`），根据它确定为读取模式的内容（顺序与随机）在块中提取数据，因此可能会继续存在具有读取操作的较高延迟的实例。 通过实践试验，我们发现将读取请求块大小（`fs.azure.read.request.size`）设置为 512 KB，并使 HBase 表的块大小与相同的大小进行匹配会产生最佳结果。

对于大多数大大小的节点群集，HDInsight HBase 提供 `bucketcache` 作为本地高级 SSD 上的文件，该文件连接到虚拟机，这将 `regionservers`运行。 改用堆外缓存可能会提供一些改进。 此解决方法的限制是使用可用内存，并且可能小于基于文件的缓存，因此不一定是最佳选择。

下面是我们调整的一些其他特定参数，看上去有助于变化度：

- 增加 `memstore` 大小（从默认 128 MB 到 256 MB）。 通常，建议为繁重的写入方案使用此设置。

- 增加专用于压缩的线程数，从默认设置 " **1** " 到 " **4**"。 如果我们观察到频繁的次要 compactions，则此设置是相关的。

- 由于存储限制，请避免阻止 `memstore` 刷新。 若要提供此缓冲区，请将 `Hbase.hstore.blockingStoreFiles` 设置增加到**100**。

- 若要控制刷新，请使用以下设置：

    - `Hbase.regionserver.maxlogs`： **140** （由于 WAL 限制，可避免刷新）

    - `Hbase.regionserver.global.memstore.lowerLimit`： **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`： **0.60**

- 用于线程池优化的 Phoenix 特定配置：

    - `Phoenix.query.queuesize`： **10000**

    - `Phoenix.query.threadpoolsize`： **512**

- 其他特定于 Phoenix 的配置：

    - `Phoenix.rpc.index.handler.count`： **50** （如果有很多索引查找）

    - `Phoenix.stats.updateFrequency`： **1 小时**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`： **1 小时**

    - `Phoenix.coprocessor.maxmetadatacachesize`： **50 MB**

- RPC 超时： **3 分钟**

   - RPC 超时包括 HBase RPC timeout、HBase 客户端扫描程序超时和 Phoenix 查询超时。 
   - 请确保在服务器端和客户端端将 `hbase.client.scanner.caching` 参数设置为相同的值。 如果二者不同，则此设置会导致与 `OutOfOrderScannerException`相关的客户端错误。 对于大型扫描，此设置应设置为低值。 我们将此值设置为**100**。

## <a name="other-considerations"></a>其他注意事项

下面是要考虑优化的其他参数：

- `Hbase.rs.cacheblocksonwrite` –默认情况下，此设置将设置为 " **true**"。

- 允许稍后将次压缩延迟的设置。

- 实验性设置，例如调整为读取和写入请求而保留的队列百分比。

## <a name="next-steps"></a>后续步骤

如果问题仍未解决，请访问以下通道之一以获得更多支持：

- 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

- 连接[@AzureSupport](https://twitter.com/azuresupport)。 这是用于改善客户体验的官方 Microsoft Azure 帐户。 它将 Azure 社区连接到适当的资源：答案、支持和专家。

- 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 你的 Microsoft Azure 订阅包括对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
