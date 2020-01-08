---
title: Apache Kafka HDInsight 群集的性能优化
description: 概述在 Azure HDInsight 上优化 Apache Kafka 工作负荷的方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75494925"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 群集的性能优化

本文提供了一些建议，用于在 HDInsight 中优化 Apache Kafka 工作负荷的性能。 重点是调整制造者和 broker 配置。 可以通过不同的方式衡量性能，而应用的优化将取决于您的业务需求。

## <a name="architecture-overview"></a>体系结构概述

Kafka 主题用于组织记录。 记录由生成者生成，由使用者使用。 发生器将记录发送到 Kafka 代理，然后存储数据。 HDInsight 群集中的每个辅助角色节点都是 Kafka 代理。

主题跨代理对记录进行分区。 在使用记录时，每个分区最多可使用一个使用者来实现数据并行处理。

复制用于跨节点复制分区。 这可以防止节点（broker）中断。 副本组中的单个分区被指定为分区领导。 生成方流量将根据由 ZooKeeper 管理的状态路由到每个节点的前导者。

## <a name="identify-your-scenario"></a>确定方案

Apache Kafka 性能有两个主要方面–吞吐量和延迟。 吞吐量是指可以处理数据的最大速率。 更高的吞吐量通常更好。 滞后时间是存储或检索数据所用的时间。 通常，延迟越低越好。 在吞吐量、延迟和应用程序的基础结构成本之间找出适当的平衡可能会很困难。 根据你是需要高吞吐量、低延迟还是同时满足这两个条件，你的性能要求可能会匹配以下三种常见情况之一：

* 高吞吐量、低延迟。 此方案需要高吞吐量和低延迟（约100毫秒）。 此类应用程序的一个示例是服务可用性监视。
* 高吞吐量、高延迟。 此方案需要高吞吐量（约 1.5 GBps），但可容忍更高的延迟（< 250 ms）。 此类应用程序的一个示例是针对近乎实时的过程（如安全和入侵检测应用程序）的遥测数据引入。
* 低吞吐量、低延迟。 此方案需要较低的延迟（< 10 ms）来进行实时处理，但会降低吞吐量。 此类应用程序的一个示例是联机拼写和语法检查。

## <a name="producer-configurations"></a>生成者配置

以下部分将重点介绍一些最重要的配置属性，以优化 Kafka 创建者的性能。 有关所有配置属性的详细说明，请参阅[有关生成方配置的 Apache Kafka 文档](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="batch-size"></a>批大小

Apache Kafka 发生器汇集消息组（称为批处理），这些消息将作为要存储在单个存储分区中的单元发送。 批大小表示在传输该组之前必须存在的字节数。 增加 `batch.size` 参数可能会增加吞吐量，因为这样可以减少网络和 IO 请求的处理开销。 在轻型负载下，增加的批大小可能会增加 Kafka 发送延迟，因为发生器会等待批处理准备就绪。 在重负载下，建议增加批大小，以提高吞吐量和延迟时间。

### <a name="producer-required-acknowledgments"></a>制造者要求的确认

`acks` 配置所需的制造者确定在写入请求被视为已完成之前分区前导符所需的确认次数。 此设置会影响数据的可靠性，并采用 `0`、`1`或 `-1`的值。 `-1` 的值表示在写入完成之前必须从所有副本接收确认。 设置 `acks = -1` 提供更强的保证来防止数据丢失，但它也会导致更高的延迟和更低的吞吐量。 如果你的应用程序要求更高的吞吐量，请尝试设置 `acks = 0` 或 `acks = 1`。 请记住，未确认所有副本可能会降低数据的可靠性。

### <a name="compression"></a>压缩

Kafka 生成者可以配置为在将消息发送到代理之前对消息进行压缩。 `compression.type` 设置指定要使用的压缩编解码器。 支持的压缩编解码器为 "gzip"、"snappy" 和 "lz4"。 压缩非常有用，如果磁盘容量有限制，则应考虑压缩。

在两个常用的压缩编解码器（`gzip` 和 `snappy`）中，`gzip` 具有更高的压缩率，这将导致较低的磁盘使用率，同时降低 CPU 负载。 `snappy` 编解码器降低了降低 CPU 开销的压缩。 可以根据 broker 磁盘或制造者 CPU 限制决定要使用的编解码器。 `gzip` 可以按比 `snappy`高5倍的速率压缩数据。

使用数据压缩将增加可以存储在磁盘上的记录数。 在制造者和 broker 使用的压缩格式不匹配的情况下，它还可能会增加 CPU 开销。 因为在发送之前必须压缩数据，然后在处理之前解压缩数据。

## <a name="broker-settings"></a>代理设置

以下部分将重点介绍一些最重要的设置，以优化 Kafka 代理的性能。 有关所有代理设置的详细说明，请参阅[有关生成方配置的 Apache Kafka 文档](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="number-of-disks"></a>磁盘数

存储磁盘的 IOPS 有限（每秒输入/输出操作数）和每秒读取/写入字节数。 创建新分区时，Kafka 会将每个新分区存储在具有较少现有分区的磁盘上，以便在可用磁盘之间进行平衡。 尽管存储策略，但在每个磁盘上处理数百个分区副本时，Kafka 可以轻松地将可用磁盘吞吐量饱和。 此处的缺点是吞吐量和成本。 如果你的应用程序需要更高的吞吐量，请使用每个 broker 创建更多托管磁盘的群集。 HDInsight 目前不支持将托管磁盘添加到正在运行的群集。 有关如何配置托管磁盘数量的详细信息，请参阅[为 HDInsight 上的 Apache Kafka 配置存储和可伸缩性](apache-kafka-scalability.md)。 了解增加群集中节点的存储空间的成本含义。

### <a name="number-of-topics-and-partitions"></a>主题和分区数

Kafka 发生器写入主题。 Kafka 使用者从主题中读取。 主题与日志关联，后者是磁盘上的数据结构。 Kafka 将来自一个制造者的记录追加到主题日志的末尾。 主题日志包含多个分布在多个文件上的分区。 这些文件进而分散到多个 Kafka 群集节点。 使用者在其步调上从 Kafka 主题读取，并可以在主题日志中选取其位置（偏移量）。

每个 Kafka 分区都是系统上的一个日志文件，而制造者线程可以同时写入多个日志。 同样，由于每个使用者线程从一个分区读取消息，因此也会并行处理多个分区中的消息。

增加分区密度（每个 broker 的分区数）将在分区领导者及其关注者之间增加与元数据操作和每个分区请求/响应相关的开销。 即使在没有数据流动的情况下，分区副本仍会从前导引线获取数据，这会导致通过网络执行发送和接收请求的额外处理。

对于 HDInsight 中的 Apache Kafka 群集1.1 及更高版本，建议每个代理最多包含1000个分区，包括副本。 增加每个 broker 的分区数会降低吞吐量，并且还可能导致主题不可用。 有关 Kafka 分区支持的详细信息，请参阅[官方 Apache Kafka 博客文章1.1.0 版本中受支持的分区数量的增加](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)。 有关修改主题的详细信息，请参阅[Apache Kafka：修改主题](https://kafka.apache.org/documentation/#basic_ops_modify_topic)。

### <a name="number-of-replicas"></a>副本数

更高的复制因素导致在分区领导和关注者之间进行附加请求。 因此，更高的复制系数会消耗更多的磁盘和 CPU 来处理其他请求，从而提高写入延迟并降低吞吐量。

建议在 Azure HDInsight 中为 Kafka 使用至少3到3个复制。 大多数 Azure 区域都有三个容错域，但在仅有两个容错域的区域中，用户应使用4x 复制。

有关复制的详细信息，请参阅[Apache Kafka：复制](https://kafka.apache.org/documentation/#replication)和[Apache Kafka：增加复制因子](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)。

## <a name="next-steps"></a>后续步骤

* [Processing trillions of events per day with Apache Kafka on Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)（使用 Azure 上的 Apache kafka 每天处理数万亿个事件）
* [什么是 Apache Kafka on HDInsight？](apache-kafka-introduction.md)
