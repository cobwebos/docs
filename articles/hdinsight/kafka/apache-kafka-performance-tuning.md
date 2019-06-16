---
title: Apache Kafka HDInsight 群集的性能优化
description: 提供 Azure HDInsight 上的 Apache Kafka 工作负荷的优化方法概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 8226d1f49b8ba73870dba009e97ff2718a0eee27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689354"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 群集的性能优化

本文提供有关优化 HDInsight 中 Apache Kafka 工作负荷的性能的一些建议， 并重点介绍如何调整生成者和代理配置。 可通过不同的方法来衡量性能，运用的优化方法取决于业务需求。

## <a name="architecture-overview"></a>体系结构概述

Kafka 主题用于对记录进行组织。 记录由生成者生成，由使用者使用。 生成者将记录发送到 Kafka 代理，后者存储数据。 HDInsight 群集中的每个辅助角色节点都是 Kafka 代理。

主题跨代理对记录进行分区。 在使用记录时，每个分区最多可使用一个使用者来实现数据并行处理。

复制用于在节点之间复制分区。 这可以防止节点（代理）发生服务中断。 将副本组之间的单个分区指定为分区领先者。 生成方流量将根据由 ZooKeeper 管理的状态路由到每个节点的前导者。

## <a name="identify-your-scenario"></a>确定方案

Apache Kafka 性能体现在两个主要方面 – 吞吐量和延迟。 吞吐量是指数据的最大处理速率。 通常，吞吐量越高越好。 延迟是指存储或检索数据所花费的时间。 通常，延迟越低越好。 在吞吐量、延迟和应用基础结构的开销方面找到适当的平衡可能会有难度。 根据追求的是高吞吐量、低延迟还是此两者，性能要求可能符合以下三种常见情况中的一种：

* 高吞吐量，低延迟。 此方案要求同时满足高吞吐量和低延迟（大约 100 毫秒）。 服务可用性监视就是这种应用场景的一个例子。
* 高吞吐量，高延迟。 此方案要求满足高吞吐量（大约 1.5 GBps），但可以容许较高的延迟（小于 250 毫秒）。 这种应用场景的一个例子是引入遥测数据进行近实时的处理，例如安全与入侵检测应用程序。
* 低吞吐量，低延迟。 此方案要求提供低延迟（小于 10 毫秒）以完成实时处理，但可以容许较低的吞吐量。 在线拼写和语法检查就是这种应用场景的一个例子。

## <a name="producer-configurations"></a>生成者配置

以下部分重点介绍一些用于优化 Kafka 生成者性能的最重要配置属性。 有关所有配置属性的详细说明，请参阅[有关生成者配置的 Apache Kafka 文档](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="batch-size"></a>批大小

Apache Kafka 生成者将作为一个单元发送的消息组（称为批）汇编到一起，以将其存储在单个存储分区中。 批大小表示在传输该组之前必须达到的字节数。 增大 `batch.size` 参数可以提高吞吐量，因为这可以降低网络和 IO 请求的处理开销。 负载较轻时，增大批大小可能会增大 Kafka 发送延迟，因为生成者需要等待某个批准备就绪。 负载较重时，建议增大批大小以改善吞吐量和延迟。

### <a name="producer-required-acknowledgements"></a>生成者所需的确认

生成者所需的 `acks` 配置确定在将某个写入请求视为已完成之前，分区领先者所需的确认数目。 此设置会影响数据可靠性，其值为 `0`、`1` 或 `-1`。 值 `-1` 表示必须收到所有副本的确认，才能将写入请求视为已完成。 设置 `acks = -1` 能够更可靠地保证数据不会丢失，但同时也会导致延迟增大，吞吐量降低。 如果应用场景要求提供较高的吞吐量，请尝试设置 `acks = 0` 或 `acks = 1`。 请记住，不确认所有副本可能会降低数据可靠性。

### <a name="compression"></a>压缩

可将 Kafka 生成者配置为先压缩消息，然后再将消息发送到代理。 `compression.type` 设置指定要使用的压缩编解码器。 支持的压缩编解码器为“gzip”、“snappy”和“lz4”。 如果磁盘容量存在限制，则压缩是有利的做法，应予以考虑。

在 `gzip` 和 `snappy` 这两个常用的压缩编解码器中，`gzip` 的压缩率更高，它可以降低磁盘用量，但代价是使 CPU 负载升高。 `snappy` 编解码器的压缩率更低，但造成的 CPU 开销更低。 可以根据代理磁盘或生成者的 CPU 限制来决定使用哪个编解码器。 `gzip` 可以压缩数据的高五倍增长`snappy`。

使用数据压缩会增加磁盘中可存储的记录数。 如果生成者与代理使用的压缩格式不匹配，则数据压缩也会增大 CPU 开销。 因为数据在发送之前必须经过压缩，并在处理之前经过解压缩。

## <a name="broker-settings"></a>代理设置

以下部分重点介绍一些用于优化 Kafka 代理性能的最重要设置。 有关所有代理设置的详细说明，请参阅[有关生成者配置的 Apache Kafka 文档](https://kafka.apache.org/documentation/#producerconfigs)。


### <a name="number-of-disks"></a>磁盘数目

存储磁盘的 IOPS（每秒输入/输出操作次数）和每秒读/写字节数有限制。 创建新分区时，Kafka 会将每个新分区存储在现有分区最少的磁盘上，以便在可用磁盘之间平衡分区的数目。 尽管有存储策略进行调节，但在处理每个磁盘上的数百个分区副本时，Kafka 很容易就会使可用磁盘吞吐量达到饱和。 此时，需要在吞吐量与成本之间进行取舍。 如果应用场景需要更大的吞吐量，请创建一个可为每个代理提供更多托管磁盘的群集。 HDInsight 目前不支持将托管磁盘添加到正在运行的群集。 有关如何配置托管磁盘数目的详细信息，请参阅[为 HDInsight 上的 Apache Kafka 配置存储和可伸缩性](apache-kafka-scalability.md)。 了解为群集中的节点增加存储空间所造成的成本影响。

### <a name="number-of-topics-and-partitions"></a>主题和分区的数目

Kafka 生成者将写入主题。 Kafka 使用者读取主题。 主题与日志相关联，该日志是磁盘上的数据结构。 Kafka 将生成者中的记录追加到主题日志的末尾。 主题日志包括分散在多个文件之间的多个分区。 而这些文件又分散在多个 Kafka 群集节点之间。 使用者从其节奏的 Kafka 主题读取，并且可选择主题日志中的位置 （偏移量）。

每个 Kafka 分区是在系统上的一个日志文件，生成者线程可以同时写入到多个日志。 同样，由于每个使用者线程从一个分区读取消息，因此也能并行处理从多个分区使用消息的操作。

提高分区密度（每个代理的分区数）会增大与元数据操作以及每个分区领先者及其后继者之间的分区请求/响应相关的开销。 即使不存在流动的数据，分区副本也仍会从领先者提取数据，导致需要通过网络额外处理发送和接收请求。

对于 HDInsight 中的 Apache Kafka 群集 1.1 和更高版本，我们建议最多为每个代理提供 1000 个分区（包括副本）。 增加每个代理的分区数会降低吞吐量，并可能导致主题不可用。 有关 Kafka 分区支持的详细信息，请参阅[有关在版本 1.1.0 中增加受支持分区数目的官方 Apache Kafka 博客文章](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)。 有关修改主题的详细信息，请参阅 [Apache Kafka：修改主题](https://kafka.apache.org/documentation/#basic_ops_modify_topic)。

### <a name="number-of-replicas"></a>副本数

较高的复制因子会导致分区领先者与后继者之间的请求数增加。 因而，较高的复制因子会消耗更多的磁盘和 CPU 来处理额外的请求，并增大写入延迟，降低吞吐量。

我们建议对 Azure HDInsight 中的 Kafka 至少使用 3 倍的复制因子。 大部分 Azure 区域有三个容错域。在只有两个容错域的区域中，用户应使用 4 倍复制因子。

有关复制的详细信息，请参阅 [Apache Kafka：复制](https://kafka.apache.org/documentation/#replication)和 [Apache Kafka：增大复制因子](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)。

## <a name="next-steps"></a>后续步骤

* [Processing trillions of events per day with Apache Kafka on Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)（使用 Azure 上的 Apache kafka 每天处理数万亿个事件）
* [什么是 Apache Kafka on HDInsight？](apache-kafka-introduction.md)
