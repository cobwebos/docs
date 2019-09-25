---
title: 排查 Azure HDInsight 上的 Apache HBase 性能问题
description: 用于在 Azure HDInsight 上获得最佳性能的各种 Apache HBase 性能优化指导原则和技巧。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266648"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>排查 Azure HDInsight 上的 Apache HBase 性能问题

本文档介绍用于在 Azure HDInsight 上获取最佳性能的各种 Apache HBase 性能优化指导原则和技巧。 其中的许多提示取决于特定的工作负荷和读/写/扫描模式。 在生产环境中应用之前，请全面测试配置更改。

## <a name="hdinsight-hbase-performance-insights"></a>HDInsight HBase 性能见解

大多数 HBase 工作负载中的最大瓶颈是 "预写日志（WAL）"。 这会对写入性能产生严重影响。 HDInsight HBase 有一个单独的存储计算模型，即数据以远程方式存储在 Azure 存储中，而区域服务器托管在 Vm 上。 到目前为止，预写日志还写入到 Azure 存储中，因此在 HDInsight 中放大这种瓶颈。 [加速写入](./apache-hbase-accelerated-writes.md)功能旨在解决此问题，方法是将 "提前写入" 日志写入 AZURE 高级 SSD 托管磁盘。 这种优势极大地提高了写入性能，并有助于一些写入密集型工作负荷面临许多问题。

使用[高级块 Blob 存储帐户](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)作为远程存储，以显著提高读取操作的性能。 仅当启用了 "提前写入日志" 功能时，此选项才可用。

## <a name="compaction"></a>压缩

压缩是在社区中以根本上达成共识的另一个潜在瓶颈。  默认情况下，在 HDInsight HBase 群集上禁用主要压缩。 这是因为，因为这是一项耗费大量资源的过程，因此我们希望允许客户根据工作负荷特征（即在非高峰时段）对其进行最大程度的灵活性。 另外，我们的存储是远程的（由 Azure 存储提供支持），这与本地 HDFS 不同，后者在大多数本地实例上很常见，数据位置并不是重要的，这是主要压缩的主要目标之一。

假设客户需要根据自己的便利来计划重大压缩。 如果未执行此维护，则压缩会显著影响长时间运行的读取性能。

特别是对于扫描操作而言，平均延迟大于 100 ms 应是导致问题的原因。 检查是否已准确安排重大压缩。

## <a name="know-your-apache-phoenix-workload"></a>了解 Apache Phoenix 工作负荷

回答以下问题将有助于更好地了解 Apache Phoenix 工作负荷：

* 您是否要将 "读取" 转换为扫描？
    * 如果是这样，这些扫描的特征是什么？
    * 是否为这些扫描（包括适当的索引）优化了 phoenix 表架构？
* 你使用了`EXPLAIN`语句来了解你的 "读取" 生成的查询计划。
* 你的写入是 "upsert" 吗？
    * 如果是这样，则它们也会进行扫描。 预计的扫描延迟时间平均为100毫秒，而 HBase 中点获取的时间是10毫秒。  

## <a name="test-methodology-and-metrics-monitoring"></a>测试方法和指标监视

如果使用 YCSB、JMeter 或 Pherf 等基准来测试和优化性能，请确保以下各项：

1. 客户端计算机不会成为瓶颈（请检查客户端计算机上的 CPU 使用率）。

1. 客户端配置（如线程数等）进行了适当调整以使客户端带宽饱和。

1. 正确地记录测试结果。

如果查询突然开始比之前更糟，请检查应用程序代码中是否存在潜在的 bug –是否突然生成了大量无效数据，从而自然增加了读取延迟？

## <a name="migration-issues"></a>迁移问题

如果迁移到 Azure HDInsight，请确保迁移是系统系统完成的，并且最好通过自动化进行。 避免手动迁移。 请确保以下各项:

1. 应准确迁移表属性（如压缩、布隆筛选器等）。

1. 对于 Phoenix 表，应将加盐设置正确映射到新的群集大小。 例如，建议将 salt 存储桶的数量作为群集中的辅助角色节点数的倍数–特定的多个是观察到的发现的数量。  

## <a name="server-side-config-tunings"></a>服务器端配置 Tunings

在 HDInsight HBase 中，Hfile 存储在远程存储上，因此，如果缓存未命中，则读取的开销肯定比本地系统更高，因为这会导致网络延迟。 在大多数情况下，可智能地使用 HBase 缓存（块缓存和 bucket 缓存）来避免此问题。 但是，在某些情况下，这可能是客户的问题。 使用高级块 blob 帐户对此进行了进一步的帮助。 但是，在 WASB （Windows Azure 存储驱动程序） blob 依赖`fs.azure.read.request.size`于某些属性（例如）时，根据它确定为读取模式的数据块（顺序与随机）来提取数据，我们可能会继续查看具有读取操作的较高延迟的实例。 我们已通过实践试验，将读取请求块大小（`fs.azure.read.request.size`）设置为 512 KB，并使 HBase 表的块大小与相同。

HDInsight HBase，适用于大多数大型节点群集，在`bucketcache`附加到 VM 的本地 SSD 上提供文件，该文件`regionservers`运行。 有时，使用 off 堆缓存可以提高性能。 这就限制了使用可用内存，并且可能会有较小的大小，而不是基于文件的缓存，因此这并非总是最佳选择。

我们已经优化了一些其他特定参数，这些参数似乎已帮助您以如下方式区分度数：

1. 从`memstore`默认大小 128 mb 增加到 256 MB –此设置通常建议用于高写入方案。

1. 增加专用于压缩的线程数–从默认值1到4。 如果我们观察到频繁的次要 compactions，则此设置是相关的。

1. 由于存储`memstore`限制，请避免阻止刷新。 `Hbase.hstore.blockingStoreFiles`可以增加到100以提供此缓冲区。

1. 对于控制刷新，可以按如下所示对默认值进行寻址：

    1. `Hbase.regionserver.maxlogs`可以从 32 upped 到140（避免由于 WAL 限制而刷新）。

    1. `Hbase.regionserver.global.memstore.lowerLimit`= 0.55。

    1. `Hbase.regionserver.global.memstore.upperLimit`= 0.60。

1. 用于线程池优化的 Phoenix 特定配置：

    1. `Phoenix.query.queuesize`可以增加到10000。

    1. `Phoenix.query.threadpoolsize`可以增加到512。

1. 其他 phoenix 特定配置：

    1. `Phoenix.rpc.index.handler.count`如果有大型索引查找或多个索引查找，则可以设置为50。

    1. `Phoenix.stats.updateFrequency`–可以从默认15分钟 upped 到1小时。

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems`–可以从30分钟 upped 到1小时。

    1. `Phoenix.coprocessor.maxmetadatacachesize`–可以从 20 MB upped 到 50 MB。

1. RPC 超时– HBase rpc timeout、HBase 客户端扫描程序超时和 Phoenix 查询超时值可增加到3分钟。 请注意，此处的`hbase.client.scanner.caching`参数设置为与服务器端和客户端端匹配的值。 否则，此设置将导致客户端`OutOfOrderScannerException`的相关错误。 对于大型扫描，此设置应设置为低值。 我们将此值设置为100。

## <a name="other-considerations"></a>其他注意事项

要考虑优化的其他一些参数：

1. `Hbase.rs.cacheblocksonwrite`–默认情况下，此设置在 HDI 上设置为 true。

1. 允许稍后将次压缩延迟的设置。

1. 试验性设置，例如调整为读取和写入请求预留的队列百分比。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

- [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
