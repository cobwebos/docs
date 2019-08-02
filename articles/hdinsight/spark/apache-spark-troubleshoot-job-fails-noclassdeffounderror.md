---
title: 从 Apache Kafka 群集读取数据的 Apache Spark 流式处理作业失败, 并出现 Azure HDInsight 中的 NoClassDefFoundError
description: 从 Apache Kafka 群集读取数据的 Apache Spark 流式处理作业失败, 并出现 Azure HDInsight 中的 NoClassDefFoundError
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 986b1dd2e749a0968c744f861feb0ac0bf2376e8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700478"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>方案:从 Apache Kafka 群集读取数据的 Apache Spark 流式处理作业失败, 并出现 Azure HDInsight 中的 NoClassDefFoundError

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache Spark 群集运行一个 Spark 流式处理作业, 该作业从 Apache Kafka 群集读取数据。 如果启用了 Kafka 流压缩, 则 Spark 流式处理作业将会失败。 在这种情况下, 由于出现错误, Spark 流式处理 Yarn 应用 application_1525986016285_0193 失败:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>原因

此错误的原因可能是, 指定`spark-streaming-kafka` jar 文件的版本不同于正在运行的 Kafka 群集的版本。

例如, 如果你运行的是 Kafka 群集版本 0.10.1, 则以下命令将导致错误:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>解决

使用带有`–packages`选项的 spark-submit 命令, 并确保 Spark kafka jar 文件的版本与你正在运行的 kafka 群集的版本相同。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
