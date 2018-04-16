---
title: 从 Azure 流分析中的事件流的数据流输入
description: 了解如何在 Azure 流分析中设置数据连接。 输入包括来自事件的数据流，也包括引用数据。
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/11/2017
ms.openlocfilehash: 0a90e97779416db7b7244cce9d6bdad740161051
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>数据连接：了解从事件到流分析的数据流输入
与流分析作业的数据连接是数据源提供的事件流，这称为“输入”。 流分析与包括 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)、[Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)和 [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)在内的 Azure 数据流源具有一流的集成。 这些输入源可以来自与分析作业相同的 Azure 订阅，也可以来自其他订阅。

## <a name="data-input-types-data-stream-and-reference-data"></a>数据输入类型：数据流和引用数据
将数据推送到数据源后，流分析作业就可使用该数据并对其进行实时处理。 输入分为两种类型：数据流输入和引用数据输入。

### <a name="data-stream-inputs"></a>数据流输入
数据流是一段时间内不受限制的事件序列。 流分析作业必须至少包含一个数据流输入。 事件中心、IoT 中心和 Blob 存储均可作为数据流输入源。 事件中心用于从多个设备和服务收集事件流。 这些流可能包括社交媒体活动源、股票交易信息或传感器数据。 IoT 中心经过优化从物联网 (IoT) 方案中连接的设备收集数据。  Blob 存储可用作按流的形式引入大容量数据（如日志文件）的输入源。  

### <a name="reference-data"></a>引用数据
流分析还支持称为“引用数据”的输入。 此类数据为辅助数据，处于静态或者缓慢变化状态。 通常用于执行关联性操作和查找操作。 例如，可以将数据流输入中的数据联接到引用数据中的数据，就像执行 SQL 联接以查找静态值一样。 目前只支持使用 Azure Blob 存储作为引用数据的输入源。 引用数据源 Blob 的大小限制为 100 MB。

若要了解如何创建引用数据输入，请参阅[使用引用数据](stream-analytics-use-reference-data.md)。  

## <a name="compression"></a>压缩

Azure 流分析支持跨所有数据流输入源（事件中心、IoT 中心和 Blob 存储）的压缩功能。 此功能将新的下拉选项添加到 Azure 门户中的“新建输入”边栏选项卡，用户可以根据需要选择压缩数据流。 当前支持的引用类型为 None、GZip、和 Deflate 压缩。 对压缩的支持不可用于参考数据。

不需要通过 Avro 序列化指定压缩类型。 如果输入 Avro 数据已压缩，它会以透明方式进行处理。 

## <a name="create-data-stream-input-from-event-hubs"></a>从事件中心创建数据流输入

Azure 事件中心提供高度可缩放的发布-订阅事件引入器。 事件中心每秒可收集数百万个事件，使用户能够处理和分析互连设备与应用程序生成的海量数据。 事件中心与流分析一起为用户提供端到端解决方案，以进行实时分析。事件中心允许用户将事件实时传送到 Azure，流分析作业可实时处理这些事件。 例如，用户可以将 Web 点击操作、传感器读数或联机日志事件发送到事件中心。 然后可以创建流分析作业，将事件中心用作输入数据流，以便进行实时筛选、聚合和关联操作。

来自流分析中事件中心的事件默认时间戳是事件到达事件中心的时间戳，即 `EventEnqueuedUtcTime`。 若要在事件负载中使用时间戳以流方式处理数据，则必须使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 关键字。

### <a name="consumer-groups"></a>使用者组
应对每个流分析事件中心输入进行配置，使之拥有自己的使用者组。 如果作业包含自联接或具有多个输入，则某些输入可能会由下游的多个读取器读取。 这种情况会影响单个使用者组中的读取器数量。 为了避免超出针对事件中心设置的每个分区每个使用者组 5 个读取器的限制，最好是为每个流分析作业指定一个使用者组。 此外，还有一项限制，即每个事件中心只能有 20 个使用者组。 有关详细信息，请参阅[事件中心编程指南](../event-hubs/event-hubs-programming-guide.md)。

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>将事件中心配置为数据流输入
下表介绍了将事件中心配置为输入时，Azure 门户中“新输入”边栏选项卡中的每个属性。

| 属性 | 说明 |
| --- | --- |
| **输入别名** |在作业查询中用于引用此输入的友好名称。 |
| **服务总线命名空间** |Azure 服务总线命名空间是包含一组消息传递实体的容器。 创建新的事件中心后，同时还创建了服务总线命名空间。 |
| **事件中心名称** |要用作输入的事件中心的名称。 |
| **事件中心策略名称** |提供对事件中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 |
| **事件中心使用者组**（可选） |用于从事件中心引入数据的使用者组。 如果未指定任何使用者组，流分析作业将使用默认使用者组。 建议对每个流分析作业使用不同的使用者组。 |
| **事件序列化格式** |传入数据流的序列化格式（JSON、CSV 或 Avro）。 |
| **编码** | 目前只支持 UTF-8 这种编码格式。 |
| **压缩**（可选） | 传入数据流的压缩类型（None、GZip 或 Deflate）。 |

如果数据来自事件中心，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **EventProcessedUtcTime** |流分析处理事件的日期和时间。 |
| **EventEnqueuedUtcTime** |事件中心收到事件的日期和时间。 |
| **PartitionId** |输入适配器的从零开始的分区 ID。 |

例如，可以使用这些字段编写如以下示例所示的查询：

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> 当使用事件中心作为 IoT 中心路由的终结点时，可通过 [GetMetadataPropertyValue 函数](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx)访问 IoT 中心元数据。
> 

## <a name="create-data-stream-input-from-iot-hub"></a>从 IoT 中心创建数据流输入
Azure Iot 中心是已针对 IoT 进行优化，具有高度伸缩性的发布-订阅事件引入器。

来自流分析中 IoT 中心的事件默认时间戳是事件到达 IoT 中心的时间戳，即 `EventEnqueuedUtcTime`。 若要在事件负载中使用时间戳以流方式处理数据，则必须使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 关键字。

> [!NOTE]
> 只能处理使用 `DeviceClient` 属性发送的消息。
> 
> 

### <a name="consumer-groups"></a>使用者组
应对每个流分析 IoT 中心输入进行配置，使之拥有自己的使用者组。 如果作业包含自联接或具有多个输入，则某些输入可能会由下游的多个读取器读取。 这种情况会影响单个使用者组中的读取器数量。 为了避免超出针对 Azure IoT 中心设置的每个分区每个使用者组 5 个读取器的限制，最好是为每个流分析作业指定一个使用者组。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>将 IoT 中心配置为数据流输入
下表介绍了将 IoT 中心配置为输入时，Azure 门户中“新输入”边栏选项卡中的每个属性。

| 属性 | 说明 |
| --- | --- |
| **输入别名** |在作业查询中用于引用此输入的友好名称。|
| **IoT 中心** |要用作输入的 IoT 中心的名称。 |
| **终结点** |IoT 中心终结点。|
| **共享访问策略名称** |提供对 IoT 中心访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 |
| **共享访问策略密钥** |用于授予对 IoT 中心访问权限的共享访问密钥。 |
| **使用者组**（可选） |用于从 IoT 中心引入数据的使用者组。 如果未指定任何使用者组，流分析作业将使用默认使用者组。 建议对每个流分析作业使用不同的使用者组。 |
| **事件序列化格式** |传入数据流的序列化格式（JSON、CSV 或 Avro）。 |
| **编码** |目前只支持 UTF-8 这种编码格式。 |
| **压缩**（可选） | 传入数据流的压缩类型（None、GZip 或 Deflate）。 |

如果数据来自 IoT 中心，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **EventProcessedUtcTime** |处理事件的日期和时间。 |
| **EventEnqueuedUtcTime** |IoT 中心收到事件的日期和时间。 |
| **PartitionId** |输入适配器的从零开始的分区 ID。 |
| **IoTHub.MessageId** | 用于关联 IoT 中心内的双向通信的 ID。 |
| **IoTHub.CorrelationId** |用于 IoT 中心内的消息响应和反馈的 ID。 |
| **IoTHub.ConnectionDeviceId** |用于发送此消息的身份验证 ID。 此值由 IoT 中心在服务绑定的消息上加盖标记。 |
| **IoTHub.ConnectionDeviceGenerationId** |用于发送此消息的经过身份验证设备的生成 ID。 此值由 IoT 中心在服务绑定的消息上加盖标记。 |
| **IoTHub.EnqueuedTime** |IoT 中心收到消息的时间。 |
| **IoTHub.StreamId** |由发送方设备添加的自定义事件属性。 |


## <a name="create-data-stream-input-from-blob-storage"></a>从 Blob 存储中创建数据流输入
对于需要将大量非结构化数据存储在云中的情况，Azure Blob 存储提供了一种经济高效且可伸缩的解决方案。 Blob 存储中的数据通常被视为静态数据。 但这些数据可以作为数据流由流分析进行处理。 流分析使用 Blob 存储输入的典型情况是进行日志处理。 在这种情况下，首先从某个系统捕获遥测数据，然后根据需要对这些数据进行分析和处理以提取有意义的数据。

流分析中 Blob 存储事件的默认时间戳是上次修改 Blob 的时间戳，即 `BlobLastModifiedUtcTime`。 若要在事件负载中使用时间戳以流方式处理数据，则必须使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 关键字。

CSV 格式的输入需要标头行，以便为数据集定义字段。 此外，所有标头行字段都必须唯一。

> [!NOTE]
> 流分析不支持将内容添加到现有 blob 文件。 流分析将仅查看每个文件一次，并且在作业读取数据后对文件所做的任何更改都不会得到处理。 最佳做法是立即上传 blob 文件的全部数据，然后将其他较新的事件添加到其他全新的 blob 文件中。
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>将 Blob 存储配置为数据流输入

下表介绍了将 Blob 存储配置为输入时，Azure 门户中“新输入”边栏选项卡中的每个属性。

| 属性 | 说明 |
| --- | --- |
| **输入别名** | 在作业查询中用于引用此输入的友好名称。 |
| **存储帐户** | 存储 Blob 文件所在的存储帐户的名称。 |
| **存储帐户密钥** | 与存储帐户关联的密钥。 |
| **容器** | 用于 Blob 输入的容器。 容器对存储在 Microsoft Azure Blob 服务中的 blob 进行逻辑分组。 将 Blob 上传到 Azure Blob 存储服务时，必须为该 Blob 指定一个容器。 |
| **路径模式**（可选） | 用于定位指定容器中的 blob 的文件路径。 在路径中，可以指定以下 3 个变量的一个或多个实例：`{date}`、`{time}` 或 `{partition}`<br/><br/>示例 1：`cluster1/logs/{date}/{time}/{partition}`<br/><br/>示例 2：`cluster1/logs/{date}`<br/><br/>`*` 字符不是路径前缀允许使用的值。 仅允许使用有效的 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob 字符</a>。 |
| **日期格式**（可选） | 如果在路径中使用日期变量，则为组织文件的日期格式。 示例：`YYYY/MM/DD` |
| **时间格式**（可选） |  如果在路径中使用时间变量，则为组织文件的时间格式。 目前唯一支持的值是 `HH`。 |
| **事件序列化格式** | 传入数据流的序列化格式（JSON、CSV 或 Avro）。 |
| **编码** | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| **压缩**（可选） | 传入数据流的压缩类型（None、GZip 或 Deflate）。 |

如果数据来自 Blob 存储源，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **BlobName** |提供事件的输入 blob 的名称。 |
| **EventProcessedUtcTime** |流分析处理事件的日期和时间。 |
| **BlobLastModifiedUtcTime** |上次修改 blob 的日期和时间。 |
| **PartitionId** |输入适配器的从零开始的分区 ID。 |

例如，可以使用这些字段编写如以下示例所示的查询：

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
已经了解了 Azure 中针对流分析作业的数据连接选项。 若要了解流分析的更多内容，请参阅：

* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
