---
title: Azure HDInsight 中的示例 Apache Storm 拓扑
description: 使用 Apache Storm on HDInsight 创建和测试的示例 Storm 拓扑列表，包括基本 C# 和 Java 拓扑，以及事件中心的用法。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: a535510ce56147f708f1cd8219cf898e6e0df36b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483793"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Apache Storm on HDInsight 的示例 Apache Storm 拓扑和组件

下面是由 Microsoft 创建和维护的、可配合 [Apache Storm](https://storm.apache.org/) on HDInsight 使用的示例的列表。 这些示例涵盖各种主题，从创建基本 C# 和 Java 拓扑，到使用 Azure 服务（例如事件中心、Cosmos DB、SQL 数据库、[Apache HBase](https://hbase.apache.org/) on HDInsight 和 Azure 存储）。 一些示例还演示了如何使用非 Azure 或甚至非 Microsoft 的技术，例如 SignalR 和 Socket.IO。

| 描述 | 演示 | 语言/框架 |
|:--- |:--- |:--- |
| [从 Apache Storm 写入 Azure Data Lake Storage](apache-storm-write-data-lake-store.md) |写入 Azure Data Lake Storage |Java |
| [事件中心 Spout 和 Bolt 源](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |事件中心 Spout 和 Bolt 的源 |Java |
| [为 Apache Storm on HDInsight 开发基于 Java 的拓扑][5797064f] |Maven |Java |
| [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑][16fce2d1] |用于 Visual Studio 的 HDInsight 工具 |C#、Java |
| [使用 Apache Storm on HDInsight 从 Azure 事件中心处理事件 (C#)][844d1d81] |事件中心 |C# 和 Java |
| [使用 Storm on HDInsight 从 Azure 事件中心处理事件 (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |事件中心 |Java |
| [使用 Apache Storm on HDInsight 处理事件中心的车辆传感器数据][246ee964] |事件中心、Cosmos DB、Azure 存储 Blob (WASB) |C#、Java |
| [提取、 转换和加载 (ETL) 从 Azure 事件中心的 Apache HBase，使用 Apache Storm on HDInsight][b4b68194] |事件中心、HBase |C# |
| [模板C#Storm 拓扑项目来处理从 Apache Storm on HDInsight 的 Azure 服务][ce0c02a2] |事件中心、Cosmos DB、SQL 数据库、HBase、SignalR |C#、Java |
| [从使用 Apache Storm on HDInsight 的 Azure 事件中心读取的伸缩性基准][d6c540e3] |消息吞吐量、事件中心、SQL 数据库 |C#、Java |
| [将 Python 与 Apache Storm on HDInsight 配合使用](apache-storm-develop-python-topology.md) |具有 Flux 拓扑的 Python 组件 |Python |
| [将 Apache Kafka 与 Apache Storm on HDInsight 配合使用](../hdinsight-apache-storm-with-kafka.md) | Apache Storm 读取和写入 Apache Kafka | Java |

> [!WARNING]  
> 此列表中的 C# 示例最初通过基于 Windows 的 HDInsight 创建和测试，可能无法在基于 Linux 的 HDInsight 群集中正常运行。 基于 Linux 的群集使用 Mono 运行 .NET 代码，可能与示例中使用的框架和包有兼容性问题。
>
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。

### <a name="next-steps"></a>后续步骤

* [创建和监视 Azure HDInsight 中的 Apache Storm 拓扑](./apache-storm-quickstart.md)
* [了解如何部署和管理 Apache Storm 拓扑使用 Apache Storm on HDInsight][6eb0d3b8]


[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "了解如何使用基于 Web 的 Apache Storm 仪表板和 Storm UI 或 Visual Studio 的 HDInsight 工具来部署和管理拓扑。"
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "了解如何使用 Visual Studio 的 HDInsight 工具创建 C# Storm 拓扑。"
[5797064f]:apache-storm-develop-java-topology.md "了解如何通过创建一个基本的单词计数拓扑，使用 Maven 以 Java 语言创建 Storm 拓扑。"
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "了解如何使用 Storm on HDInsight 从 Azure 事件中心读取和写入数据。"
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "了解如何使用 Storm 拓扑从 Azure 事件中心读取消息，从 Azure Cosmos DB 读取数据参考文档，并将数据保存到 Azure 存储。"
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "用于演示使用 Apache Storm on HDInsight 从 Azure 事件中心读取数据并将数据存储到 SQL 数据库时的吞吐量的多个拓扑。"
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "了解如何从 Azure 事件中心读取数据，聚合并转换数据，然后将数据存储到 HBase on HDInsight。"
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "此项目包含用来与各种 Azure 服务（例如事件中心、Cosmos DB 和 SQL 数据库）进行交互的 Spout、Bolt 和拓扑的模板。"

