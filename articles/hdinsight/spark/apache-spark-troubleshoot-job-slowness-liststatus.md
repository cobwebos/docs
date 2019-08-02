---
title: 在 Azure HDInsight 中处理 Apache Spark 流式处理作业所需的时间比平时长
description: 在 Azure HDInsight 中处理 Apache Spark 流式处理作业所需的时间比平时长
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679427"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>方案:在 Azure HDInsight 中处理 Apache Spark 流式处理作业所需的时间比平时长

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

你观察到, 某些 Apache Spark 流式处理作业速度较慢, 或者处理速度比平常长。 对于 Spark 流式处理应用程序, 每批消息对应于提交到 Spark 的一个作业。 如果作业通常需要 X 秒来处理, 则可能偶尔需要2-3 分钟以上。

## <a name="cause"></a>原因

一个可能的原因是 Apache Kafka 制造者需要2分钟以上的时间才能完成写出 Kafka 群集。 若要进一步调试 Kafka 问题, 可以向代码添加一些日志记录, 使用 Kafka 制造者发送消息, 并将其与 Kafka 群集中的日志相关联。

另一个可能的原因是频繁读取和写入 WASB 可能会导致后续的微批处理延迟。 `Filesystem.listStatus` 由于`O(n!)`算法删除重复项, 的 WASB 实现非常缓慢。 由于从`BlobListItem`到`FileMetadata`到`FileStatus`的额外转换, 它会占用太多内存。 例如, 算法需要超过30分钟的时间来列出700000文件。 因此, `ListBlobs`如果通过 SparkSQL 的每个微批处理进行主动调用, 则会导致后续的微批处理延迟, 从而导致在计划延迟较高的情况下出现这种情况。 [此修补](https://issues.apache.org/jira/browse/HADOOP-15547)程序可解决此问题, 但如果你的环境中缺少`ListBlobs`此修补程序, 将会经历高延迟。 而且, 即使您每小时删除文件, 后端中的列表也必须循环访问所有行 (包括删除的行), 因为垃圾回收过程尚未完成。 虽然修补程序可能会解决某些问题, 但垃圾回收问题可能仍会导致批处理的流处理延迟。

## <a name="resolution"></a>解决

应用[HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547)修补程序。 如果无法做到这一点, 则可以使用 HDFS 作为检查点位置。 设置`checkpointDirectory`为类似于: `hdfs://mycluster/checkpoint`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
