---
title: 阿帕奇卡夫卡的 Azure 监视器日志 - Azure HDInsight
description: 了解如何使用 Azure 监视器日志分析 Azure HDInsight 上的 Apache Kafka 群集的日志。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471174"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>分析 HDInsight 上 Apache Kafka 的日志

了解如何使用 Azure 监视器日志来分析 Apache Kafka 在 HDInsight 上生成的日志。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>日志位置

群集中的 Apache Kafka 日志位于`/var/log/kafka`。 无论是否使用托管磁盘，Kafka 日志不会跨群集生命周期保存或持久化。 下表显示了可用的日志。

|日志 |描述 |
|---|---|
|卡夫卡·奥特|卡夫卡过程的粗壮和稳高。 您将在此文件中找到 Kafka 启动和关闭日志。|
|服务器.日志|主卡夫卡服务器日志。 所有卡夫卡经纪人日志都在这里结束。|
|控制器.日志|如果代理充当控制器，则控制器将记录。|
|状态更改.log|所有状态更改事件到代理都记录在此文件中。|
|卡夫卡-gc.log|卡夫卡垃圾收集统计。|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>为阿帕奇卡夫卡启用 Azure 监视器日志

为所有 HDInsight 群集启用 Azure 监视器日志的步骤相同。 通过访问以下链接来了解如何创建和配置所需的服务：

1. 创建 Log Analytics 工作区。 有关详细信息，请参阅 Azure[监视器文档中的日志](../../azure-monitor/platform/data-platform-logs.md)。

2. 创建 Kafka on HDInsight 群集。 有关详细信息，请参阅 [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)文档。

3. 将 Kafka 群集配置为使用 Azure 监视器日志。 有关详细信息，请参阅[使用 Azure 监视器日志来监视 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)文档。

> [!IMPORTANT]  
> 可能需要大约 20 分钟才能为 Azure 监视器日志提供数据。

## <a name="query-logs"></a>查询日志

1. 从[Azure 门户](https://portal.azure.com)中选择日志分析工作区。

2. 从左侧菜单中，在 **"常规"** 下，选择 **"日志**"。 从此处可搜索从 Kafka 所收集的数据。 在查询窗口中输入查询，然后选择 **"运行**"。 一下是一些示例搜索：

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

* 每秒传入消息：（替换为`your_kafka_cluster_name`群集名称。

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 每秒传入字节：（替换为`wn0-kafka`辅助节点主机名。

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 每秒传出字节：（替换为`your_kafka_cluster_name`群集名称。

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    也可输入 `*` 来搜索全部已记录的类型。 当前有以下日志可用于查询：

    | 日志类型 | 描述 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |

    ![阿帕奇卡夫卡日志分析cpu使用](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>后续步骤

有关 Azure 监视器的详细信息，请参阅[Azure 监视器概述](../../log-analytics/log-analytics-get-started.md)和[查询 Azure 监视器日志以监视 HDInsight 群集](../hdinsight-hadoop-oms-log-analytics-use-queries.md)。

有关使用 Apache Kafka 的详细信息，请参阅以下文档：

* [在 HDInsight 群集间执行 Apache Kafka 镜像操作](apache-kafka-mirroring.md)
* [扩大HDInsight上的阿帕奇卡夫卡的规模](apache-kafka-scalability.md)
* [将 Apache Spark 流式处理 (DStream) 与 Apache Kafka 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Spark 结构化流式处理与 Apache Kafka 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
