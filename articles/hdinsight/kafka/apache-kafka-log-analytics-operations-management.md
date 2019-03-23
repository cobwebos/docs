---
title: Apache Kafka-Azure HDInsight 的 azure Monitor 日志
description: 了解如何使用 Azure Monitor 日志来分析来自 Azure HDInsight 上 Apache Kafka 群集的日志。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 5154058bb1562b670e24f8813e979588a536f086
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359851"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>分析 HDInsight 上 Apache Kafka 的日志

了解如何使用 Azure Monitor 日志分析 HDInsight 上 Apache Kafka 生成的日志。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>为 Apache Kafka 中启用 Azure Monitor 日志

若要启用用于 HDInsight 的 Azure Monitor 日志的步骤都相同的所有 HDInsight 群集。 通过访问以下链接来了解如何创建和配置所需的服务：

1. 创建 Log Analytics 工作区。 有关详细信息，请参阅 [Log Analytics 工作区入门](https://docs.microsoft.com/azure/log-analytics)文档。

2. 创建 Kafka on HDInsight 群集。 有关详细信息，请参阅 [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)文档。

3. 配置 Kafka 群集以使用 Azure Monitor 日志。 有关详细信息，请参阅[使用 Azure Monitor 日志来监视 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)文档。

    > [!NOTE]  
    > 此外可以配置群集，以使用 Azure Monitor 日志`Enable-AzHDInsightOperationsManagementSuite`cmdlet。 此 cmdlet 需要以下信息：
    >
    > * HDInsight 群集名称。
    > * Azure Monitor 日志工作区 ID。 可以在 Log Analytics 工作区中找到工作区 ID。
    > * Log analytics 连接的主键。 若要查找主键，请在 Azure 门户中打开工作区，从左侧菜单中选择“高级设置”。 从“高级设置”中，选择“已连接的源”>“Linux 服务器”。


> [!IMPORTANT]  
> 可能需要约 20 分钟，数据可用于 Azure Monitor 日志之前。

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

     | 日志类型 | 描述 |
     | ---- | ---- |
     | log\_kafkaserver\_CL | Kafka broker server.log |
     | log\_kafkacontroller\_CL | Kafka broker controller.log |
     | metrics\_kafka\_CL | Kafka JMX metrics |

     ![CPU 使用情况搜索图示](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>后续步骤

Azure Monitor 的详细信息，请参阅[Azure Monitor 概述](../../log-analytics/log-analytics-get-started.md)文档。

有关使用 Apache Kafka 的详细信息，请参阅以下文档：

 * [在 HDInsight 群集间执行 Apache Kafka 镜像操作](apache-kafka-mirroring.md)
 * [增加 HDInsight 上的 Apache Kafka 的可伸缩性](apache-kafka-scalability.md)
 * [将 Apache Spark 流式处理 (DStream) 与 Apache Kafka 配合使用](../hdinsight-apache-spark-with-kafka.md)
 * [将 Apache Spark 结构化流式处理与 Apache Kafka 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
