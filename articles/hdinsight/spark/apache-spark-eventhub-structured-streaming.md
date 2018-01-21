---
title: "在 Azure HDInsight 中将 Apache Spark 结构化流与事件中心配合使用 | Microsoft Docs"
description: "构建一个 Apache Spark 流式处理示例，用于演示如何向 Azure 事件中心发送数据流，并使用 scala 应用程序在 HDInsight Spark 群集中接收这些事件。"
keywords: "apache spark 流式处理,spark 流式处理,spark 示例,apache spark 流式处理示例,事件中心 azure 示例,spark 示例"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: e0486d2c5f78da1d1e4a12703f120eccef43c305
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>使用 HDInsight 中的 Apache Spark 结构化流处理事件中心的事件

本文介绍如何使用 Spark 结构化流处理实时遥测数据。 为此，请执行以下概要步骤：

1. 编译并在本地工作站上运行一个示例事件生成者应用程序，用于生成要发送到事件中心的事件。
2. 使用 [Spark Shell](apache-spark-shell.md) 定义并运行一个简单的 Spark 结构化流应用程序。

## <a name="prerequisites"></a>系统必备

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

* Azure 事件中心命名空间。 有关详细信息，请参阅[创建 Azure 事件中心命名空间](apache-spark-eventhub-streaming.md#create-an-azure-event-hub)。

* Oracle Java 开发工具包。 可从[此处](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)进行安装。

* Apache Maven。 可以从[此处](https://maven.apache.org/download.cgi)下载它。 [此处](https://maven.apache.org/install.html)提供安装 Maven 的说明。

## <a name="build-configure-and-run-the-event-producer"></a>生成、配置和运行事件生成者
此任务克隆一个可创建随机事件并将其发送到配置的事件中心的示例应用程序。 GitHub 中提供了此示例应用程序，网址为：[https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)。

1. 确保已安装 git 工具。 可以从 [GIT 下载](http://www.git-scm.com/downloads)页下载。 
2. 打开命令提示符或终端 shell，从所选的目录运行以下命令以克隆项目。
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. 这会创建名为 eventhubs-client-sample 的新文件夹。 在 shell 或命令提示符中，导航到此文件夹。
4. 运行 Maven，使用以下命令生成该应用程序：

          mvn package

5. 在 shell 或命令提示符中，导航到所创建的、包含文件 ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar`` 的目标目录。
6. 接下来，需要生成命令行来针对事件中心运行事件生成者。 为此，请按如下所示替换命令中的值：

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. 例如，完整的命令如下所示：

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. 该命令将会启动，如果配置正确，则在几分钟后，可以看到该命令发送到事件中心的事件相关输出，如下所示：

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. 让事件生成者保持运行，同时继续执行后续步骤。

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>在 HDInsight 群集上运行 Spark Shell
在此任务中，需通过 SSH 连接到 HDInsight 群集的头节点，启动 Spark Shell，并运行一个可检索和处理事件中心的事件的 Spark 流应用程序。 

此时，HDInsight 群集应已准备就绪。 如果未就绪，则需要等到其完成预配。 就绪后，请继续执行以下步骤：

1. 使用 SSH 连接到 HDInsight 群集。 有关说明，请参阅[使用 SSH 连接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)。

5. 生成的应用程序需要 Spark 流事件中心包。 若要运行 Spark Shell 以便自动从 [Maven 中心](https://search.maven.org)检索此依赖项，请务必按如下所示提供 packages 开关和 Maven 坐标：

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.5"

6. Spark Shell 完成加载后，应会看到：

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.1.2.6.2.3-1
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_151)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. 将以下代码片段复制到文本编辑器并对其进行修改，以便适当地为事件中心设置策略密钥和命名空间。

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )
            
8. 如果查看以下格式的与事件中心兼容的终结点，则显示 `iothub-xxxxxxxxxx` 的部分是与事件中心兼容的命名空间名称并且可用于 `eventhubs.namespace`。 字段 `SharedAccessKeyName` 可用于 `eventhubs.policyname`，而 `SharedAccessKey` 可用于 `eventhubs.policykey`： 

        Endpoint=sb://iothub-xxxxxxxxxx.servicebus.windows.net/;SharedAccessKeyName=xxxxx;SharedAccessKey=xxxxxxxxxx 

9. 将修改后的代码片段粘贴到等待中的 scala> 提示符，然后按回车键。 应看到如下输出：

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

10. 接下来，通过指定源来创作 Spark 结构化流查询。 将以下内容粘贴到 Spark Shell，然后按回车键。

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

11. 应看到如下输出：

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

12. 接下来创作查询，以便将其输出写入控制台。 为此，请将以下内容粘贴到 Spark Shell 并按回车键。

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

13. 此时，应会看到一些以如下所示输出开头的批

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

14. 此内容后接处理每个事件微批后生成的输出结果。 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

15. 当事件生成者发送的新事件抵达时，此结构化流查询会处理这些事件。
16. 完成运行此示例后，请务必删除 HDInsight 群集。



## <a name="see-also"></a>另请参阅

* [Spark 流概述](apache-spark-streaming-overview.md)













