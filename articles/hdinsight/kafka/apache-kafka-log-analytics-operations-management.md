---
title: 适用于 Apache Kafka 的 Log Analytics - Azure HDInsight
description: 了解如何使用 Log Analytics 分析 Azure HDInsight 上 Apache Kafka 群集的日志。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 9421217383f390630687d8416512401487ad3f7e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621382"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>分析 HDInsight 上 Apache Kafka 的日志

了解如何使用 Log Analytics 分析 HDInsight 上 Apache Kafka 生成的日志。

## <a name="enable-log-analytics-for-kafka"></a>为 Kafka 启用 Log Analytics

对于所有 HDInsight 群集，启用适用于 HDInsight 的 Log Analytics 的步骤都是相同的。 通过访问以下链接来了解如何创建和配置所需的服务：

1. 创建 Log Analytics 工作区。 有关详细信息，请参阅 [Log Analytics 工作区入门](https://docs.microsoft.com/azure/log-analytics)文档。

2. 创建 Kafka on HDInsight 群集。 有关详细信息，请参阅 [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)文档。

3. 配置 Kafka 群集以使用 Log Analytics。 有关详细信息，请参阅[使用 Log Analytics 监视 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)文档。

    > [!NOTE]
    > 还可使用 `Enable-AzureRmHDInsightOperationsManagementSuite` cmdlet，将群集配置为使用 Log Analytics。 此 cmdlet 需要以下信息：
    >
    > * HDInsight 群集名称。
    > * 用于 Log Analytics 的工作区 ID。 可以在 Log Analytics 工作区中找到工作区 ID。
    > * 用于 Log Analytics 连接的主密钥。 若要查找主键，请在 Azure 门户中打开工作区，从左侧菜单中选择“高级设置”。 从“高级设置”中，选择“已连接的源”>“Linux 服务器”。


> [!IMPORTANT]
> 在数据可用于 Log Analytics 之前可能需要等待约 20 分钟。

## <a name="query-logs"></a>查询日志

1. 在 [Azure 门户](https://portal.azure.com)中，选择 Log Analytics 工作区。

2. 选择 __“日志搜索”__。 从此处可搜索从 Kafka 所收集的数据。 一下是一些示例搜索：

    * 磁盘使用情况：`Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * CPU 使用情况：`Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * 每秒传入消息数：`metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * 每秒传入字节数：`metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * 每秒传出字节数：`metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > 将查询值替换为特定于群集的信息。 例如，必须将 `ClusterName_s` 设置为群集的名称。 必须将 `HostName_s` 设置为群集中辅助角色节点的域名。

    也可输入 `*` 来搜索全部已记录的类型。 当前有以下日志可用于查询：

    | 日志类型 | Description |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |

    ![CPU 使用情况搜索图示](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>后续步骤

有关 Log Analytics 的详细信息，请参阅 [Log Analytics 工作区入门](../../log-analytics/log-analytics-get-started.md)文档。

有关使用 Kafka 的详细信息，请参阅以下文档：

 * [在 HDInsight 群集间执行 Kafka 镜像操作](apache-kafka-mirroring.md)
 * [增加 Kafka on HDInsight 的可伸缩性](apache-kafka-scalability.md)
 * [将 Spark 流式处理 (DStream) 与 Kafka 配合使用](../hdinsight-apache-spark-with-kafka.md)
 * [将 Spark 结构化流式处理与 Kafka 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
