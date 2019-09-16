---
title: Apache Kafka Azure HDInsight Azure Monitor 日志
description: 了解如何使用 Azure Monitor 日志来分析 Azure HDInsight 上 Apache Kafka 群集中的日志。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 44eea1bc6390e743aff104550e5b6d7e97c45929
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960110"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>分析 HDInsight 上 Apache Kafka 的日志

了解如何使用 Azure Monitor 日志来分析 HDInsight 上 Apache Kafka 生成的日志。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>为 Apache Kafka 启用 Azure Monitor 日志

为 HDInsight 启用 Azure Monitor 日志的步骤对于所有 HDInsight 群集都是相同的。 通过访问以下链接来了解如何创建和配置所需的服务：

1. 创建 Log Analytics 工作区。 有关详细信息，请参阅[中的日志 Azure Monitor](../../azure-monitor/platform/data-platform-logs.md)文档。

2. 创建 Kafka on HDInsight 群集。 有关详细信息，请参阅 [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)文档。

3. 配置 Kafka 群集以使用 Azure Monitor 日志。 有关详细信息，请参阅[使用 Azure Monitor 日志监视 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)文档。

> [!IMPORTANT]  
> 在数据可用于 Azure Monitor 日志之前，可能需要大约20分钟的时间。

## <a name="query-logs"></a>查询日志

1. 在 [Azure 门户](https://portal.azure.com)中，选择 Log Analytics 工作区。

2. 在左侧菜单中的 "**常规**" 下，选择 "**日志**"。 从此处可搜索从 Kafka 所收集的数据。 在查询窗口中输入查询，然后选择 "**运行**"。 一下是一些示例搜索：

* 磁盘使用情况：

    ```kusto
    Perf 
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU 使用率：

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* 每秒传入消息数：

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 每秒传入的字节数：

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 每秒传出字节数：

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > 将查询值替换为特定于群集的信息。 例如，必须将 `ClusterName_s` 设置为群集的名称。 必须将 `HostName_s` 设置为群集中辅助角色节点的域名。
    
    也可输入 `*` 来搜索全部已记录的类型。 当前有以下日志可用于查询：
    
    | 日志类型 | 描述 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |
    
    ![CPU 使用情况搜索图示](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)
 
## <a name="next-steps"></a>后续步骤

有关 Azure Monitor 的详细信息，请参阅[Azure Monitor 概述](../../log-analytics/log-analytics-get-started.md)和[查询 Azure Monitor 日志来监视 HDInsight 群集](../hdinsight-hadoop-oms-log-analytics-use-queries.md)。

有关使用 Apache Kafka 的详细信息，请参阅以下文档：

* [在 HDInsight 群集间执行 Apache Kafka 镜像操作](apache-kafka-mirroring.md)
* [增加 HDInsight 上的 Apache Kafka 的可伸缩性](apache-kafka-scalability.md)
* [将 Apache Spark 流式处理 (DStream) 与 Apache Kafka 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Spark 结构化流式处理与 Apache Kafka 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
