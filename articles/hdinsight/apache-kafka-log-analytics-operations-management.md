---
title: "适用于 Apache Kafka 的 Log analytics - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Operations Management Suite 分析 Azure HDInsight 上 Apache Kafka 集群的日志。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/11/2017
ms.author: larryfr
ms.openlocfilehash: be549c8fd072663ae9bbb9441a654a2bf1912198
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-logs-for-apache-kafka-preview-on-hdinsight"></a>HDInsight 上的 Apache Kafka（预览版）分析日志

了解如何使用 Microsoft Operations Management Suite 分析 Apache Kafka on HDInsight 所生成的日志。

## <a name="enable-oms-for-kafka"></a>为 Kafka 启用 OMS

对于所有 HDInsight 群集，启用适用于 HDInsight 的 Log Analytics 的步骤都是相同的。 通过访问以下链接来了解如何创建和配置所需的服务：

1. 创建 Log Analytics 工作区。 有关详细信息，请参阅 [Log Analytics 工作区入门](../log-analytics/log-analytics-get-started.md)文档。

2. 创建 Kafka on HDInsight 群集。 有关详细信息，请参阅 [Apache Kafka on HDInsight 入门](hdinsight-apache-kafka-get-started.md)文档。

3. 配置 Kafka 群集以使用日志分析。 有关详细信息，请参阅[使用 Log Analytics 监视 HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md)文档。

    > [!NOTE]
    > 还可使用 `Enable-AzureRmHDInsightOperationsManagementSuite` cmdlet，通过配置群集来使用 Log Analytics。 此 cmdlet 需要以下信息：
    >
    > * HDInsight 群集名称。
    > * 用于 Log Analytics 的工作区 ID。 可在 OMS 工作区中找到 Log Analytics 工作区的工作区 ID。
    > * 用于 OMS 连接的主键。 要查找主键，请选择 Log Analytics 实例，然后选择 __OMS 门户__。 在 OMS 门户中，依次选择__“设置”__、__“已连接的源”__和__“Linux 服务器”__。


> [!IMPORTANT]
> 在数据可用于 Log Analytics 之前可能需要等待约 20 分钟。

## <a name="query-logs"></a>查询日志

1. 在 [Azure 门户](https://portal.azure.com)中，选择 Log Analytics 工作区。

2. 选择__“日志搜索”__。 从此处可搜索从 Kafka 所收集的数据。 一下是一些示例搜索：

    * 磁盘使用情况：`Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * CPU 使用情况：`Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * 每秒传入消息数：`Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * 每秒传入字节数：`Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * 每秒传出字节数：`Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > 将查询值替换为特定于群集的信息。 例如，必须将 `ClusterName_s` 设置为群集的名称。 必须将 `HostName_s` 设置为群集中辅助角色节点的域名。

    也可输入 `*` 来搜索全部已记录的类型。 当前有以下日志可用于查询：

    | 日志类型 | 说明 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |

    ![CPU 使用情况搜索图示](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>后续步骤

有关 Log Analytics 的详细信息，请参阅 [Log Analytics 工作区入门](../log-analytics/log-analytics-get-started.md)文档。

有关使用 Kafka 的详细信息，请参阅以下文档：

 * [在 HDInsight 群集间执行 Kafka 镜像操作](hdinsight-apache-kafka-mirroring.md)
 * [增加 Kafka on HDInsight 的可伸缩性](hdinsight-apache-kafka-scalability.md)
 * [将 Spark 流式处理 (DStream) 与 Kafka 配合使用](hdinsight-apache-spark-with-kafka.md)
 * [将 Spark 结构化流式处理与 Kafka 配合使用](hdinsight-apache-kafka-spark-structured-streaming.md)