---
title: 捕获流式处理事件 - Azure 事件中心 | Microsoft Docs
description: 本文概述了捕获功能，该功能可以捕获通过 Azure 事件中心流式处理的事件。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 9b69feef7c6587f7356648e6a6828277ba500aea
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77460069"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件
使用 azure 事件中心，可以在[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)中自动捕获事件中心中的流数据，也可以 Azure Data Lake Storage 所选的第[1 代或第2代](https://azure.microsoft.com/services/data-lake-store/)帐户中进行选择，同时增加了指定时间或大小间隔的灵活性。 设置捕获极其简单，无需管理费用即可运行它，并且可以使用事件中心[吞吐量单位](event-hubs-scalability.md#throughput-units)自动进行缩放。 事件中心捕获是在 Azure 中加载流式处理数据的最简单方法，并可让用户专注于数据处理，而不是数据捕获。

事件中心捕获可让用户在同一个流上处理实时和基于批处理的管道。 这意味着可以构建随着时间的推移随用户的需要增长的解决方案。 无论用户现在正在构建基于批处理的系统并着眼于将来进行实时处理，还是要将高效的冷路径添加到现有的实时解决方案，事件中心捕获都可以使流式处理数据处理更加简单。


## <a name="how-event-hubs-capture-works"></a>Azure 事件中心捕获的工作原理

事件中心是遥测数据入口的时间保留持久缓冲区，类似于分布式日志。 缩小事件中心的关键在于[分区使用者模式](event-hubs-scalability.md#partitions)。 每个分区是独立的数据段，并单独使用。 根据可配置的保留期，随着时间的推移此数据会过时。 因此，给定的事件中心永远不会装得“太满”。

事件中心捕获使你可以指定自己的 Azure Blob 存储帐户和容器，或 Azure Data Lake Storage 帐户，用于存储捕获的数据。 这些帐户可以与事件中心在同一区域中，也可以在另一个区域中，从而增加了事件中心捕获功能的灵活性。

捕获的数据是以[Apache Avro][Apache Avro]格式编写的，它是一种简洁、快速、简单的二进制格式，它使用内联架构提供丰富的数据结构。 这种格式广泛用于 Hadoop 生态系统、流分析和 Azure 数据工厂。 在本文后面提供了有关如何使用 Avro 的详细信息。

### <a name="capture-windowing"></a>捕获窗口

使用事件中心捕获，用户可以设置用于控制捕获的窗口。 此窗口使用最小大小并具有使用“第一个获胜”策略的时间配置，这意味着遇到的第一个触发器将触发捕获操作。 如果使用 15 分钟，100 MB 的捕获窗口，且发送速度为每秒 1 MB，则大小窗口将在时间窗口之前触发。 每个分区独立捕获，并在捕获时写入已完成的块 blob，在遇到捕获间隔时针对时间进行命名。 存储命名约定如下所示：

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

请注意，日期值填充为零；示例文件名可能是：

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

如果你的 Azure 存储 blob 暂时不可用，事件中心捕获将保留你的数据，以便在你的事件中心配置数据保留期，并在存储帐户再次可用之后，重新填充数据。

### <a name="scaling-to-throughput-units"></a>缩放到吞吐量单位

事件中心流量由[吞吐量单位](event-hubs-scalability.md#throughput-units)控制。 单个吞吐量单位允许 1 MB/秒或 1000 个入口事件/秒（是出口事件量的两倍）。 标准事件中心可以配置1-20 个吞吐量单位，可以使用配额增加[支持请求][support request]来购买更多。 使用在超出购买的吞吐量单位时会受到限制。 事件中心捕获直接从内部事件中心存储复制数据，从而绕过吞吐量单位出口配额，为流分析或 Spark 等其他处理读取器节省了出口量。

配置后，用户发送第一个事件时，事件中心捕获会自动运行，并持续保持运行状态。 为了让下游处理更便于了解该进程正在运行，事件中心会在没有数据时写入空文件。 此进程提供了可预测的频率以及可以供给批处理处理器的标记。

## <a name="setting-up-event-hubs-capture"></a>设置事件中心捕获

可以使用 [Azure 门户](https://portal.azure.com)或使用 Azure 资源管理器模板在创建事件中心时配置捕获。 有关详细信息，请参阅以下文章：

- [通过 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)
- [使用 Azure 资源管理器模板创建包含事件中心的事件中心命名空间并启用捕获](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>浏览已捕获的文件和使用 Avro

事件中心捕获以在配置的时间窗口中指定的 Avro 格式创建文件。 可以在任何工具（例如 [Azure 存储资源管理器][Azure Storage Explorer]）中查看这些文件。 可以本地下载这些文件以进行处理。

事件中心捕获生成的文件具有以下 Avro 架构：

![Avro 架构][3]

浏览 Avro 文件的简单方法是使用 Apache 中的 [Avro 工具][Avro Tools] jar。 你还可以使用[Apache 钻取][Apache Drill]轻型 SQL 驱动的体验或[Apache Spark][Apache Spark]来对引入数据执行复杂的分布式处理。 

### <a name="use-apache-drill"></a>使用 Apache Drill

[Apache 钻取][Apache Drill]"用于大数据浏览的开源 SQL 查询引擎"，可以在任何位置查询结构化和半结构化数据。 该引擎可以作为独立节点或作为巨型群集运行以实现优异性能。

它原生支持 Azure Blob 存储，这使得查询 Avro 文件中的数据非常轻松，如以下文档中所述：

[Apache 钻取： Azure Blob 存储插件][Apache Drill: Azure Blob Storage Plugin]

若要轻松查询捕获的文件，可以通过容器在启用了 Apache Drill 的情况下创建和执行 VM 来访问 Azure Blob 存储：

https://github.com/yorek/apache-drill-azure-blob

“大规模流式处理”存储库中提供了完整的端到端示例：

[大规模流式传输：事件中心捕获]

### <a name="use-apache-spark"></a>使用 Apache Spark

[Apache Spark][Apache Spark]是一种用于大规模数据处理的统一分析引擎。 它支持不同的语言，包括 SQL，并且可以轻松地访问 Azure Blob 存储。 有两个选项可用来在 Azure 中运行 Apache Spark，这两个选项都可以轻松访问 Azure Blob 存储：

- [HDInsight： Azure 存储中的地址文件][HDInsight: Address files in Azure storage]
- [Azure Databricks： Azure Blob 存储][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>使用 Avro 工具

[Avro 工具][Avro Tools]作为 jar 包提供。 下载此 jar 文件后，可以运行以下命令来查看特定 Avro 文件的架构：

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

此命令返回

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

还可以使用 Avro 工具将文件转换为 JSON 格式以及执行其他处理。

若要执行更高级的处理，请下载并安装适用于所选平台的 Avro。 在撰写本文时，有可用于 C、C++、C\#、Java、NodeJS、Perl、PHP、Python 和 Ruby 的实现。

Apache Avro 针对 [Java][Java] 和 [Python][Python] 提供了完整的快速入门指南。 还可以参阅[事件中心捕获入门](event-hubs-capture-python.md)一文。

## <a name="how-event-hubs-capture-is-charged"></a>Azure 事件中心捕获的收费方式

事件中心捕获的计量方式与吞吐量单位类似：按小时收费。 费用直接与为命名空间购买的吞吐量单位数成正比。 随着吞吐量单位增加和减少，事件中心捕获计量也相应地增加和减少以提供匹配的性能。 相继进行计量。 有关定价的详细信息，请参见[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。 

请注意，捕获不会使用出口配额，因为它是单独计费的。 

## <a name="integration-with-event-grid"></a>事件网格集成 

可以创建 Azure 事件网格订阅，其中事件中心命名空间作为其源。 以下教程介绍如何创建事件网格订阅（其中事件中心作为源，Azure Functions 应用作为接收器）：[使用事件网格和 Azure Functions 处理捕获的事件中心数据并将其迁移到 SQL 数据仓库](store-captured-data-data-warehouse.md)。

## <a name="next-steps"></a>后续步骤
事件中心捕获是将数据加载到 Azure 最简单的方法。 使用 Azure Data Lake、Azure 数据工厂和 Azure HDInsight，可以执行批处理操作和其他分析，并且可以选择熟悉的工具和平台，以所需的任何规模执行。

了解如何使用 Azure 门户和 Azure 资源管理器模板启用此功能：

- [使用 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)
- [使用 Azure 资源管理器模板启用事件中心捕获](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[大规模流式传输：事件中心捕获]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
