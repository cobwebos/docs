---
title: Apache Kafka HDInsight 群集的性能优化
description: 提供有关优化 Azure HDInsight 上的 Apache Kafka 工作负荷的技术概述。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 3f15f45e0543c582d70463fb9ddc7ac569ff57bc
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576752"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 群集的性能优化

本文提供有关在 HDInsight 中 Apache Kafka 工作负荷的性能优化的一些建议。 重点是调整生产者和 broker 配置。 有不同的方法来衡量性能，并在应用的优化将取决于你的业务需求。

## <a name="architecture-overview"></a>体系结构概述

Kafka 主题用于组织记录。 记录是由生成者、 生成和使用者使用。 生成者将记录发送到 Kafka 中转站，然后将数据存储。 HDInsight 群集中的每个辅助角色节点都是 Kafka 代理。

主题跨代理对记录进行分区。 在使用记录时，每个分区最多可使用一个使用者来实现数据并行处理。

复制用于在节点之间复制分区。 这样可以防止节点 （中转站） 服务中断。 在副本的组之间的单个分区指定分区方面处于领先地位。 生成方流量将根据由 ZooKeeper 管理的状态路由到每个节点的前导者。

## <a name="identify-your-scenario"></a>确定方案

Apache Kafka 性能具有两个主要方面 – 吞吐量和延迟。 吞吐量是可以在其中处理数据的最大速率。 通常是更高的吞吐量。 延迟是数据存储或检索所需的时间。 通常是较低的延迟。 查找适当的平衡的吞吐量、 延迟和应用程序的基础结构的成本之间极具挑战性。 你的性能要求很可能将匹配以下三种常见情况下，根据你是否需要高吞吐量、 低延迟，或这两者之一：

* 高吞吐量、 低延迟。 此方案需要较高的吞吐量和低延迟 （约 100 毫秒为单位）。 此类应用程序的示例是服务可用性监视。
* 高吞吐量、 高延迟。 此方案需要较高的吞吐量 (~1.5 GBps)，但可承受更高的延迟 （< 250 毫秒）。 此类应用程序的一个示例是为近乎实时的进程，如安全性和入侵检测应用程序的遥测数据引入。
* 较低的吞吐量、 低延迟时间。 这种情况下进行实时处理，需要低延迟 （< 10 毫秒），但可承受吞吐量较低。 此类应用程序的一个示例是联机的拼写和语法检查。

## <a name="producer-configurations"></a>生成者配置

以下部分将重点介绍一些最重要的配置属性，以优化性能的 Kafka 生成者。 所有配置属性的详细说明，请参阅[制造者配置上的 Apache Kafka 文档](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="batch-size"></a>批大小

Apache Kafka 生成者组建的消息 （称为批次） 作为一个单元发送要存储在单个存储分区中的组。 批大小意味着必须都存在才能该组传输的字节数。 增加`batch.size`参数可以提高吞吐量，因为它可以减少网络开销与 IO 请求的处理。 在负载较轻，增加的批大小可能会增加 Kafka 发送延迟，生成者等待一批才能准备就绪。 负载过大，建议增加批大小以提高吞吐量和延迟。

### <a name="producer-required-acknowledgements"></a>所需的制造者确认

所需的生成者`acks`配置确定的确认之前写入请求被视为分区领导所需的数量已完成。 此设置会影响数据的可靠性和接受值为`0`， `1`，或`-1`。 值`-1`意味着在写入完成之前必须在所有副本收到确认。 设置`acks = -1`提供更强的保证，针对数据丢失，但它还结果在更高的延迟和较低的吞吐量。 如果您的应用程序要求更高的吞吐量，请尝试设置`acks = 0`或`acks = 1`。 请记住，未确认的所有副本可减少数据可靠性。

### <a name="compression"></a>压缩

Kafka 生成者可以配置为压缩的消息发送到代理程序之前。 `compression.type`设置指定要使用的压缩编解码器。 受支持的压缩编解码器为"gzip""snappy，"和"lz4。" 压缩是有益的如果没有限制的磁盘容量，应考虑。

在两个常用的压缩编解码器`gzip`并`snappy`，`gzip`具有更高的压缩率，这会导致较低的磁盘使用情况，但代价是更高的 CPU 负载。 `snappy`编解码器提供较少 CPU 开销更少的压缩。 您可以决定要使用哪些编解码器基于 broker 磁盘或生成者 CPU 限制。 `gzip` 可以压缩数据的高五倍增长`snappy`。

使用数据压缩会增加可以存储在磁盘的记录数。 它还可以增加 CPU 开销在情况下生成者和代理正在使用的压缩格式之间存在不匹配的。 作为必须发送数据之前压缩数据，并将其然后解压缩数据，再处理。

## <a name="broker-settings"></a>代理设置

以下部分将重点介绍一些最重要的设置以优化性能的 Kafka 中转站。 有关详细说明所有的代理设置，请参阅[制造者配置上的 Apache Kafka 文档](https://kafka.apache.org/documentation/#producerconfigs)。


### <a name="number-of-disks"></a>磁盘数目

存储磁盘具有有限的 IOPS （输入/输出操作每秒） 和读/写每秒字节数。 在创建新的分区，Kafka 磁盘上具有最少的现有分区，用于平衡它们之间的可用磁盘存储每个新分区。 尽管存储策略，处理数百个分区每个磁盘上的副本时，Kafka 会轻松饱和的可用磁盘吞吐量。 此处的代价是吞吐量和成本之间。 如果你的应用程序需要更大的吞吐量，创建群集了更多托管磁盘，每个中转站。 HDInsight 目前不支持将托管的磁盘添加到正在运行的群集。 有关如何配置托管磁盘数的详细信息，请参阅[为 Apache Kafka on HDInsight 配置存储和可伸缩性](apache-kafka-scalability.md)。 了解在群集中节点的不断增加存储空间的成本影响。

### <a name="number-of-topics-and-partitions"></a>主题和分区的数量

Kafka 生成者将写入到主题。 Kafka 使用者从主题读取。 本主题是一个日志，是在磁盘上的数据结构与相关联。 Kafka 将记录从 producer(s) 主题日志的结尾追加。 主题日志都包含分散到多个文件的多个分区。 这些文件，反过来，分布在多个 Kafka 群集节点。 使用者从其节奏的 Kafka 主题读取，并且可选择主题日志中的位置 （偏移量）。

每个 Kafka 分区是在系统上，日志文件和制造者线程可以同时写入多个日志。 同样，由于每个使用者线程从一个分区中读取消息，从多个分区使用被处理并行也。

增加分区密度 （每个中转站的分区数） 将添加一项开销相关的元数据操作和每个分区领导和其关注者之间分区请求/响应。 即使在没有通过数据流时，分区副本仍从领导者，这会导致额外的处理，以便进行发送和接收通过网络请求提取数据。

Apache Kafka 群集 1.1 和上面在 HDInsight，我们建议你拥有最多 1000 个分区每个中转站，包括副本。 增加每个中转站的分区数量会降低吞吐量，并可能还会导致主题不可用。 Kafka 分区支持的详细信息，请参阅[上的受支持版本 1.1.0 中的分区数的增加的官方 Apache Kafka 博客文章](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)。 修改主题的详细信息，请参阅[Apache Kafka： 修改主题](https://kafka.apache.org/documentation/#basic_ops_modify_topic)。

### <a name="number-of-replicas"></a>副本数

更高版本的复制因子会导致分区领导和关注者之间的其他请求。 因此，更高版本的复制因子会占用更多的磁盘和 CPU 来处理其他请求增大写入延迟和降低吞吐量。

我们建议在 Azure HDInsight 中的 Kafka 使用至少为 3 x 复制。 大多数 Azure 区域有三个容错域，但在只有两个容错域的区域中，用户应使用 4 x 复制。

有关复制的详细信息，请参阅[Apache Kafka： 复制](https://kafka.apache.org/documentation/#replication)并[Apache Kafka： 增加复制因子](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)。

## <a name="next-steps"></a>后续步骤

* [Processing trillions of events per day with Apache Kafka on Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)（使用 Azure 上的 Apache kafka 每天处理数万亿个事件）
* [什么是 Apache Kafka on HDInsight？](apache-kafka-introduction.md)
