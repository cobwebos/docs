---
title: "Apache Kafka on HDInsight 简介 | Microsoft Docs"
description: "了解 Apache Kafka on HDInsight。 了解它的涵义和用途以及在何处可找到示例和入门信息。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2d744e753224e4ce98680d3228914fd89e87eba4
ms.openlocfilehash: 28d213fa23d480635fd4376e22ff54a5e6374350

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>Apache Kafka on HDInsight（预览版）简介

[Apache Kafka](https://kafka.apache.org) 是一个分布式流式处理平台，以开源方式提供，可用于构建实时流式处理数据管道和应用程序。 Kafka 还提供了类似于消息队列的消息中转站，可在其中发布和订阅命名数据流。 Kafka on HDInsight 可在 Microsoft Azure 云中提供高度可扩展和高度可用的托管服务。

## <a name="why-use-kafka-on-hdinsight"></a>为何要使用 Kafka on HDInsight？

Kafka 提供以下功能：

* 发布-订阅消息传送模式：Kafka 提供了一个 Producer API，用于将记录发布到 Kafka 主题。 订阅主题时，将使用 Consumer API。

* 流处理：Kafka 通常与 Apache Storm 或 Spark 配合使用，以实现实时流式处理。 Kafka 0.10.0.0（HDInsight 版本 3.5）引入了流式处理 API，可用于构建流式处理解决方案，而无需使用 Storm 或 Spark。

* 水平规模：Kafka 分区可对 HDInsight 群集中节点进行流式处理。 使用者进程可与单个分区关联，以便在使用记录时提供负载平衡。

* 按顺序传递：在每个分区中，记录按照被接收的顺序存储在流中。 通过为每个分区关联一个使用者进程，可保证按顺序处理记录。

* 容错：可在节点之间复制分区以提供容错。

## <a name="use-cases"></a>用例

* **消息传送**：由于 Kafka 支持发布-订阅消息模式，因此它通常用作消息中转站。

* **活动跟踪**：由于 Kafka 提供按顺序进行日志记录的记录，因此它还可用于跟踪和重新创建活动。 例如，网站上或应用程序内的用户操作。

* **聚合**：使用流处理可从不同的流中聚合信息，将信息合并和集中到操作数据中。

* **转换**：使用流处理可将多个输入主题中的数据合并到一个或多个输出主题中，丰富其内容。

## <a name="where-do-i-start"></a>从哪里开始？

若要深入了解创建 Kafka 群集和使用 Kafka 的步骤（包括使用生成者、使用者和流式处理 API 的基于 Java 的示例），请参阅 [Kafka on HDInsight 入门](hdinsight-apache-kafka-get-started.md)

## <a name="next-steps"></a>后续步骤

使用以下链接了解如何使用 Apache Kafka on HDInsight：

* [Kafka on HDInsight 入门](hdinsight-apache-kafka-get-started.md)

* [使用 MirrorMaker 创建 Kafka on HDInsight 的副本](hdinsight-apache-kafka-mirroring.md)

* [将 Apache Storm 与 Kafka on HDInsight 结合使用](hdinsight-apache-storm-with-kafka.md)

* [将 Apache Spark 与 Kafka on HDInsight 结合使用](hdinsight-apache-spark-with-kafka.md)




<!--HONumber=Nov16_HO3-->


