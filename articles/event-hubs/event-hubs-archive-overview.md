---
title: "Azure 事件中心存档 | Microsoft Docs"
description: "Azure 事件中心存档功能概述。"
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c21d4fbcfef17c204d74850f740fd8ee18d65856


---
# <a name="azure-event-hubs-archive"></a>Azure 事件中心存档
使用 Azure 事件中心存档，可以更灵活地将事件中心中的流数据自动传送到所选的 Blob 存储帐户，可以指定所选的时间或大小间隔。 设置存档是快速操作，无需管理费用即可运行它，并且可以使用事件中心[吞吐量单位](event-hubs-overview.md#capacity-and-security)自动对其进行缩放。 事件中心存档是在 Azure 中加载流数据的最简单方法，并可让用户专注于数据处理，而不是数据捕获。

Azure 事件中心存档可让用户处理同一个流中的实时和基于 Batch 的管道。 这使用户能够构建随着时间的推移随用户的需要增长的解决方案。 无论用户现在正在构建基于 Batch 的系统并着眼于将来进行实时处理，还是要将高效的冷路径添加到现有的实时解决方案，事件中心存档都可以使流数据处理更加简单。

## <a name="how-event-hubs-archive-works"></a>事件中心存档的工作原理
事件中心是遥测数据入口的时间保留持久缓冲区，类似于分布式日志。 缩小事件中心的关键在于[分区使用者模式](event-hubs-overview.md#partition-key)。 每个分区是独立的数据段，并单独使用。 根据可配置的保留期，随着时间的推移此数据会过时。 因此，给定的事件中心永远不会装得“太满”。

事件中心存档可让用户指定自己的 Azure Blob 存储帐户和容器（将用于存储已存档数据）。 此帐户可以与事件中心在同一区域中，也可以在另一个区域中，从而为事件中心存档功能添加了灵活性。

已存档数据以 [Apache Avro][Apache Avro] 格式写入；该格式是紧凑、便捷的二进制格式，并使用内联架构提供丰富的数据结构。 这种格式广泛用于 Hadoop 生态系统，以及由流分析和 Azure 数据工厂使用。 在本文后面提供了有关如何使用 Avro 的详细信息。

### <a name="archive-windowing"></a>存档窗口化
事件中心存档可让用户设置用于控制存档的窗口。 此窗口最小并具有使用“第一个获胜”策略的时间配置，意味着遇到的第一个触发器将触发存档操作。 如果使用 15 分钟/100 MB 的存档窗口，且发送速度为 1 MB/秒，则大小窗口将在时间窗口之前触发。 每个分区独立存档，并在存档时写入已完成的块 blob，在遇到存档间隔时针对时间进行命名。 命名约定如下所示：

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>缩放到吞吐量单位
事件中心流量由[吞吐量单位](event-hubs-overview.md#capacity-and-security)控制。 单个吞吐量单位允许 1 MB/秒或 1000 个入口事件/秒（是出口事件量的两倍）。 标准事件中心可以配置 1 到 20 个吞吐量单位，更多吞吐量单位可以通过发出增加配额[支持请求][支持请求]来购买。 超出购买的吞吐量单位的使用将受到限制。 事件中心存档直接从内部事件中心存储复制数据，从而绕过吞吐量单位出口配额，为流分析或 Spark 等其他处理读取器节省了出口量。

事件中心存档配置后，在用户发送第一个事件时，就立即自动运行。 它始终继续运行。 为了让下游处理更便于了解该进程正在运行，事件中心在没有数据时写入空文件。 这提供了可预测的频率以及可以供给 Batch 处理器的标记。

## <a name="setting-up-event-hubs-archive"></a>设置事件中心存档
可以通过门户或 Azure Resource Manager 在创建事件中心时配置事件中心存档。 只需单击“启用”按钮即可启用存档。 单击边栏选项卡的“容器”部分可配置存储帐户和容器。 由于事件中心存档对存储使用服务到服务身份验证，因此无需指定存储连接字符串。 资源选取器自动为存储帐户选择资源 URI。 如果使用 Azure Resource Manager，必须以字符串形式显式提供此 URI。

默认时间窗口为 5 分钟。 最小值为 1，最大值为 15。 **大小**窗口的范围为 10-500 MB。

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>将存档添加到现有的事件中心
可以在事件中心命名空间中的现有事件中心上配置存档。 较早的消息类型或混合类型命名空间中未提供此功能。 若要对现有的事件中心启用“存档”，或者要更改存档设置，请单击命名空间以加载“概要”边栏选项卡，然后单击要启用或更改存档设置的事件中心。 最后，单击打开的边栏选项卡的“属性”部分，如下图中所示。

![][2]

还可以通过 Azure Resource Manager 模板配置事件中心存档。 有关详细信息，请参阅[此文章](event-hubs-resource-manager-namespace-event-hub-enable-archive.md)。

## <a name="exploring-the-archive-and-working-with-avro"></a>浏览存档和使用 Avro
配置后，事件中心存档在配置的时间窗口中提供的 Azure 存储帐户和容器中创建文件。 可以在任何工具（例如 [Azure 存储资源管理器][Azure 存储资源管理器]）中查看这些文件。 可以本地下载这些文件以进行处理。

事件中心存档生成的文件具有以下 Avro 架构：

![][3]

浏览 Avro 文件的简单方法是使用 Apache 中的 [Avro 工具][Avro 工具] jar。 下载此 jar 后，运行以下命令即可查看特定 Avro 文件的架构：

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

此命令返回

```
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

Apache Avro 针对 [Java][Java] 和 [Python][Python] 提供了完整的快速入门指南。 还可以阅读[事件中心存档入门](event-hubs-archive-python.md)一文。

## <a name="how-event-hubs-archive-is-charged"></a>事件中心存档如何收费
事件中心存档的计量方式与吞吐量单位类似，并按小时付费。 费用直接与为命名空间购买的吞吐量单位数成正比。 随着吞吐量单位增加和减少，事件中心存档也相应地增加和减少以提供匹配的性能。 计量器一前一后地发生。 事件中心存档的费用为每小时每个吞吐量单位 0.10 美元，在预览期间以 50%的折扣提供。

事件中心存档真正是将数据导入 Azure 的最简单方法。 使用 Azure Data Lake、Azure 数据工厂和 Azure HDInsight，可以执行 Batch 操作和其他所选的分析，请使用熟悉的工具和平台，以所需的任何规模执行。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* 完整的[使用事件中心的完整示例应用程序][使用事件中心的完整示例应用程序]。
* [使用事件中心扩大事件处理][使用事件中心扩大事件处理]示例。
* [事件中心概述][事件中心概述]

[Apache Avro]: http://avro.apache.org/
[支持请求]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure 存储资源管理器]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro 工具]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[事件中心概述]: event-hubs-overview.md
[使用事件中心的完整示例应用程序]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[使用事件中心扩大事件处理]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


