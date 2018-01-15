---
title: "HDInsight 上的示例 Apache Storm 拓扑 | Microsoft Docs"
description: "使用 Apache Storm on HDInsight 创建和测试的示例 Storm 拓扑列表，包括基本 C# 和 Java 拓扑，以及事件中心的用法。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: c6c1a1483191e654c245eb3f05ee9e8406510b08
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Apache Storm on HDInsight 的示例 Storm 拓扑和组件

以下是 Microsoft 创建和维护的、可配合 Apache Storm on HDInsight 使用的示例列表。 这些示例涵盖各种主题，从创建基本 C# 和 Java 拓扑，到使用 Azure 服务（例如事件中心、Cosmos DB、SQL 数据库、HBase on HDInsight 和 Azure 存储）。 一些示例还演示了如何使用非 Azure 或甚至非 Microsoft 的技术，例如 SignalR 和 Socket.IO。

| 说明 | 演示 | 语言/框架 |
|:--- |:--- |:--- |
| [从 Apache Storm 写入 Azure Data Lake Store](apache-storm-write-data-lake-store.md) |写入 Azure Data Lake 存储 |Java |
| [事件中心 Spout 和 Bolt 源](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |事件中心 Spout 和 Bolt 的源 |Java |
| [为 Apache Storm on HDInsight 开发基于 Java 的拓扑][5797064f] |Maven |Java |
| [使用 Visual Studio 为 Apache Storm on HDInsight 开发 C# 拓扑][16fce2d1] |用于 Visual Studio 的 HDInsight 工具 |C#、Java |
| [使用 Storm on HDInsight 处理 Azure 事件中心的事件 (C#)][844d1d81] |事件中心 |C# 和 Java |
| [使用 Storm on HDInsight 从 Azure 事件中心处理事件 (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |事件中心 |Java |
| [使用 Storm on HDInsight 处理事件中心的车辆传感器数据][246ee964] |事件中心、Cosmos DB、Azure 存储 Blob (WASB) |C#、Java |
| [使用 Storm on HDInsight 从 Azure 事件中心提取、转换及加载 (ETL) 到 HBase][b4b68194] |事件中心、HBase |C# |
| [通过 Storm on HDInsight 使用模板 C# Storm 拓扑项目来处理 Azure 服务][ce0c02a2] |事件中心、Cosmos DB、SQL 数据库、HBase、SignalR |C#、Java |
| [使用 Storm on HDInsight 建立从 Azure 事件中心读取数据的伸缩性基准][d6c540e3] |消息吞吐量、事件中心、SQL 数据库 |C#、Java |
| [将 HDInsight 上的 Python 与 Storm 配合使用](apache-storm-develop-python-topology.md) |具有 Flux 拓扑的 Python 组件 |Python |
| [将 HDInsight 上的 Kafka 与 Storm 配合使用](../hdinsight-apache-storm-with-kafka.md) | Apache Storm 读取和写入 Apache Kafka | Java |

### <a name="next-steps"></a>后续步骤

* [HDInsight 上的 Apache Storm 入门][2b8c3488]
* [了解如何使用 Storm on HDInsight 部署和管理 Storm 拓扑][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "了解如何创建 Storm on HDInsight 群集，以及如何使用 Storm 仪表板部署示例拓扑。"
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "了解如何使用基于 Web 的 Storm 仪表板和 Storm UI 或 Visual Studio 的 HDInsight 工具来部署和管理拓扑。"
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "了解如何使用 Visual Studio 的 HDInsight 工具创建 C# Storm 拓扑。"
[5797064f]:apache-storm-develop-java-topology.md "了解如何通过创建一个基本的单词计数拓扑，使用 Maven 以 Java 语言创建 Storm 拓扑。"
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "了解如何使用 Storm on HDInsight 从 Azure 事件中心读取和写入数据。"
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "了解如何使用 Storm 拓扑从 Azure 事件中心读取消息，从 Azure Cosmos DB 读取数据参考文档，并将数据保存到 Azure 存储。"
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "用于演示使用 Apache Storm on HDInsight 从 Azure 事件中心读取数据并将数据存储到 SQL 数据库时的吞吐量的多个拓扑。"
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "了解如何从 Azure 事件中心读取数据，聚合并转换数据，然后将数据存储到 HBase on HDInsight。"
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "此项目包含用来与各种 Azure 服务（例如事件中心、Cosmos DB 和 SQL 数据库）进行交互的 Spout、Bolt 和拓扑的模板。"

