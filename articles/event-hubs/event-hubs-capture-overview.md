---
title: Azure 事件中心捕获概述 | Microsoft Docs
description: 通过事件中心捕获来捕获遥测数据
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 00eee302cc15d94ec62f5f3332e18ee2df24f5cd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
---
# <a name="azure-event-hubs-capture"></a>Azure 事件中心捕获

使用 Azure 事件中心捕获，可以按指定的时间间隔或大小差异将事件中心中的流式处理数据自动传送到所选的 [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)或 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 帐户。 设置捕获极其简单，无需管理费用即可运行它，并且可以使用事件中心[吞吐量单位](event-hubs-features.md#capacity)自动进行缩放。 事件中心捕获是在 Azure 中加载流式处理数据的最简单方法，并可让用户专注于数据处理，而不是数据捕获。

事件中心捕获可让用户在同一个流上处理实时和基于批处理的管道。 这意味着可以构建随着时间的推移随用户的需要增长的解决方案。 无论用户现在正在构建基于批处理的系统并着眼于将来进行实时处理，还是要将高效的冷路径添加到现有的实时解决方案，事件中心捕获都可以使流式处理数据处理更加简单。

## <a name="how-event-hubs-capture-works"></a>Azure 事件中心捕获的工作原理

事件中心是遥测数据入口的时间保留持久缓冲区，类似于分布式日志。 缩小事件中心的关键在于[分区使用者模式](event-hubs-features.md#partitions)。 每个分区是独立的数据段，并单独使用。 根据可配置的保留期，随着时间的推移此数据会过时。 因此，给定的事件中心永远不会装得“太满”。

使用事件中心捕获，用户可以指定自己的 Azure Blob 存储帐户和容器或 Azure Data Lake Store（用于存储已捕获数据）。 这些帐户可以与事件中心在同一区域中，也可以在另一个区域中，从而增加了事件中心捕获功能的灵活性。

已捕获数据以 [Apache Avro][Apache Avro] 格式写入；该格式是紧凑、便捷的二进制格式，并使用内联架构提供丰富的数据结构。 这种格式广泛用于 Hadoop 生态系统、流分析和 Azure 数据工厂。 在本文后面提供了有关如何使用 Avro 的详细信息。

### <a name="capture-windowing"></a>捕获窗口

使用事件中心捕获，用户可以设置用于控制捕获的窗口。 此窗口使用最小大小并具有使用“第一个获胜”策略的时间配置，这意味着遇到的第一个触发器将触发捕获操作。 如果使用 15 分钟，100 MB 的捕获窗口，且发送速度为每秒 1 MB，则大小窗口将在时间窗口之前触发。 每个分区独立捕获，并在捕获时写入已完成的块 blob，在遇到捕获间隔时针对时间进行命名。 存储命名约定如下所示：

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

请注意，日期值填充为零；示例文件名可能是：

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>缩放到吞吐量单位

事件中心流量由[吞吐量单位](event-hubs-features.md#capacity)控制。 单个吞吐量单位允许 1 MB/秒或 1000 个入口事件/秒（是出口事件量的两倍）。 标准事件中心可以配置 1 到 20 个吞吐量单位，可以使用增加配额[支持请求][support request]来购买更多吞吐量单位。 使用在超出购买的吞吐量单位时会受到限制。 事件中心捕获直接从内部事件中心存储复制数据，从而绕过吞吐量单位出口配额，为流分析或 Spark 等其他处理读取器节省了出口量。

配置后，用户发送第一个事件时，事件中心捕获会自动运行，并持续保持运行状态。 为了让下游处理更便于了解该进程正在运行，事件中心会在没有数据时写入空文件。 此进程提供了可预测的频率以及可以供给批处理处理器的标记。

## <a name="setting-up-event-hubs-capture"></a>设置事件中心捕获

可以使用 [Azure 门户](https://portal.azure.com)或使用 Azure 资源管理器模板在创建事件中心时配置捕获。 有关详细信息，请参阅以下文章：

- [通过 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)
- [使用 Azure 资源管理器模板创建包含事件中心的事件中心命名空间并启用捕获](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>浏览已捕获的文件和使用 Avro

事件中心捕获以在配置的时间窗口中指定的 Avro 格式创建文件。 可以在任何工具（例如 [Azure 存储资源管理器][Azure Storage Explorer]）中查看这些文件。 可以本地下载这些文件以进行处理。

事件中心捕获生成的文件具有以下 Avro 架构：

![][3]

浏览 Avro 文件的简单方法是使用 Apache 中的 [Avro 工具][Avro Tools] jar。 下载此 jar 后，运行以下命令即可查看特定 Avro 文件的架构：

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
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

## <a name="next-steps"></a>后续步骤

事件中心捕获是将数据加载到 Azure 最简单的方法。 使用 Azure Data Lake、Azure 数据工厂和 Azure HDInsight，可以执行批处理操作和其他分析，并且可以选择熟悉的工具和平台，以所需的任何规模执行。

访问以下链接可以了解有关事件中心的详细信息：

* [开始发送和接收事件](event-hubs-dotnet-framework-getstarted-send.md)
* [事件中心概述][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
