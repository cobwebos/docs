---
title: 将数据作为 Azure 流分析的输入进行流式传输
description: 了解如何在 Azure 流分析中设置数据连接。 输入包括来自事件的数据流，也包括引用数据。
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/19/2018
ms.openlocfilehash: 5ebf2d1025c8f9469a83a408cb79e3d944a601bc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>将数据作为流分析的输入进行流式传输

流分析接受的数据来自多种事件源。 作为流分析作业的输入提供的数据连接称为作业的“输入”。 

流分析与下述三种资源提供的作为输入的 Azure 数据流进行一流的集成：
- [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/) 

这些输入资源与流分析作业可以属于同一 Azure 订阅，也可以属于不同的订阅。

## <a name="compare-stream-and-reference-inputs"></a>比较流输入和引用输入
将数据推送到数据源后，流分析作业就可使用该数据并对其进行实时处理。 输入分为两种类型：数据流输入和引用数据输入。

### <a name="data-stream-input"></a>数据流输入
数据流是一段时间内不受限制的事件序列。 流分析作业必须至少包含一个数据流输入。 事件中心、IoT 中心和 Blob 存储均可作为数据流输入源。 事件中心用于从多个设备和服务收集事件流。 这些流可能包括社交媒体活动源、股票交易信息或传感器数据。 IoT 中心经过优化以从物联网 (IoT) 方案中连接的设备收集数据。  Blob 存储可用作按流的形式引入大容量数据（如日志文件）的输入源。  

### <a name="reference-data-input"></a>引用数据输入
流分析还支持称为“引用数据”的输入。 此类数据为辅助数据，处于静态或者缓慢变化状态。 引用数据通常用于执行关联性操作和查找操作。 例如，可以将数据流输入中的数据联接到引用数据中的数据，就像执行 SQL 联接以查找静态值一样。 目前只支持使用 Azure Blob 存储作为引用数据的输入源。 引用数据源 Blob 的大小限制为 100 MB。

若要了解如何创建引用数据输入，请参阅[使用引用数据](stream-analytics-use-reference-data.md)。  

### <a name="compression"></a>压缩
流分析支持跨所有数据流输入源的压缩功能。 当前支持的引用类型为 None、GZip、和 Deflate 压缩。 对压缩的支持不可用于参考数据。 如果输入格式为压缩的 Avro 数据，则会以透明方式对其进行处理。 不需要通过 Avro 序列化指定压缩类型。 

## <a name="create-or-edit-inputs"></a>创建或编辑输入
若要创建新输入以及列出或编辑流式处理作业上的现有输入，可以使用 Azure 门户：
1. 打开 [Azure 门户](https://portal.azure.com)，找到并选择流分析作业。
2. 在“设置”标题下选择“输入”选项。 
4. “输入”页会列出任何现有的输入。 
5. 在“输入”页中选择“添加流输入”或“添加引用输入”，打开详细信息页。
6. 选择一个现有输入，对其详细信息进行编辑，然后选择“保存”，对编辑的现有输入进行保存。
7. 在输入详细信息页上选择“测试”，验证连接选项是否能够有效地使用。 
8. 右键单击现有输入的名称，根据需要选择“来自输入的示例数据”进行进一步的测试。

也可使用 [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput)、[.Net API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)、[REST API](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-input) 和 [Visual Studio](stream-analytics-tools-for-visual-studio.md) 来创建、编辑和测试流分析作业输入。

## <a name="stream-data-from-event-hubs"></a>从事件中心对数据进行流式传输

Azure 事件中心提供高度可缩放的发布-订阅事件引入器。 事件中心每秒可收集数百万个事件，使用户能够处理和分析互连设备与应用程序生成的海量数据。 事件中心和流分析一起为你提供进行实时分析所需的端到端解决方案。 可以通过事件中心将事件实时馈送到 Azure 中，以便流分析作业对这些事件进行实时处理。 例如，用户可以将 Web 点击操作、传感器读数或联机日志事件发送到事件中心。 然后可以创建流分析作业，将事件中心用作输入数据流，以便进行实时筛选、聚合和关联操作。

来自流分析中事件中心的事件默认时间戳是事件到达事件中心的时间戳，即 `EventEnqueuedUtcTime`。 若要在事件负载中使用时间戳以流方式处理数据，则必须使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 关键字。

### <a name="consumer-groups"></a>使用者组
应对每个流分析事件中心输入进行配置，使之拥有自己的使用者组。 如果作业包含自联接或具有多个输入，则某些输入可能会由下游的多个读取器读取。 这种情况会影响单个使用者组中的读取器数量。 为了避免超出针对事件中心设置的每个分区每个使用者组 5 个读取器的限制，最好是为每个流分析作业指定一个使用者组。 此外，还有一项限制，即每个事件中心只能有 20 个使用者组。 有关详细信息，请参阅[事件中心编程指南](../event-hubs/event-hubs-programming-guide.md)。

### <a name="stream-data-from-event-hubs"></a>从事件中心对数据进行流式传输
下表介绍了 Azure 门户的“新输入”页中用于从事件中心流式传输数据输入的每个属性：

| 属性 | 说明 |
| --- | --- |
| **输入别名** |在作业查询中用于引用此输入的友好名称。 |
| **订阅** | 选择事件中心资源所在的订阅。 | 
| **事件中心命名空间** | 事件中心命名空间是包含一组消息传递实体的容器。 创建新的事件中心后，另请创建命名空间。 |
| **事件中心名称** | 要用作输入的事件中心的名称。 |
| **事件中心策略名称** | 提供对事件中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 此选项会自动进行填充，除非已选择手动提供事件中心设置的选项。|
| **事件中心使用者组**（推荐） | 强烈建议对每个流分析作业使用不同的使用者组。 此字符串标识的使用者组用于从事件中心引入数据。 如果未指定任何使用者组，流分析作业将使用 $Default 使用者组。  |
| **事件序列化格式** | 传入数据流的序列化格式（JSON、CSV 或 Avro）。 |
| **编码** | 目前只支持 UTF-8 这种编码格式。 |
| **事件压缩类型** | 用于读取传入数据流的压缩类型，例如 None（默认）、GZip 或 Deflate。 |

如果数据来自事件中心流输入，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **EventProcessedUtcTime** |流分析处理事件的日期和时间。 |
| **EventEnqueuedUtcTime** |事件中心收到事件的日期和时间。 |
| **PartitionId** |输入适配器的从零开始的分区 ID。 |

例如，可以使用这些字段编写如以下示例所示的查询：

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> 当使用事件中心作为 IoT 中心路由的终结点时，可通过 [GetMetadataPropertyValue 函数](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx)访问 IoT 中心元数据。
> 

## <a name="stream-data-from-iot-hub"></a>从 IoT 中心流式传输数据
Azure Iot 中心是已针对 IoT 进行优化，具有高度伸缩性的发布-订阅事件引入器。

在流分析中，来自 IoT 中心的事件的默认时间戳是事件到达 IoT 中心的时间戳，即 `EventEnqueuedUtcTime`。 若要在事件负载中使用时间戳以流方式处理数据，则必须使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 关键字。

> [!NOTE]
> 只能处理使用 `DeviceClient` 属性发送的消息。
> 

### <a name="consumer-groups"></a>使用者组
应该对每个流分析 IoT 中心输入进行配置，使之拥有自己的使用者组。 如果作业包含自联接或具有多个输入，则某些输入可能会由下游的多个读取器读取。 这种情况会影响单个使用者组中的读取器数量。 为了避免超出针对 Azure IoT 中心设置的每个分区每个使用者组 5 个读取器的限制，最好是为每个流分析作业指定一个使用者组。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>将 IoT 中心配置为数据流输入
下表介绍了将 IoT 中心配置为流输入时，Azure 门户的“新输入”页中的每个属性。

| 属性 | 说明 |
| --- | --- |
| **输入别名** | 在作业查询中用于引用此输入的友好名称。|
| **订阅** | 选择 IoT 中心资源所在的订阅。 | 
| **IoT 中心** | 可用作输入的 IoT 中心的名称。 |
| **终结点** | IoT 中心终结点。|
| **共享访问策略名称** | 提供对 IoT 中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 |
| **共享访问策略密钥** | 用于授予对 IoT 中心的访问权限的共享访问密钥。  此选项会自动进行填充，除非已选择手动提供 IoT 中心设置的选项。 |
| **使用者组** | 强烈建议对每个流分析作业使用不同的使用者组。 用于从 IoT 中心引入数据的使用者组。 流分析使用 $Default 所有者组，除非指定了其他组。  |
| **事件序列化格式** | 传入数据流的序列化格式（JSON、CSV 或 Avro）。 |
| **编码** | 目前只支持 UTF-8 这种编码格式。 |
| **事件压缩类型** | 用于读取传入数据流的压缩类型，例如 None（默认）、GZip 或 Deflate。 |


如果使用的流数据来自 IoT 中心，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **EventProcessedUtcTime** | 处理事件的日期和时间。 |
| **EventEnqueuedUtcTime** | IoT 中心收到事件的日期和时间。 |
| **PartitionId** | 输入适配器的从零开始的分区 ID。 |
| **IoTHub.MessageId** | 用于关联 IoT 中心内的双向通信的 ID。 |
| **IoTHub.CorrelationId** | 用于 IoT 中心内的消息响应和反馈的 ID。 |
| **IoTHub.ConnectionDeviceId** | 用于发送此消息的身份验证 ID。 此值由 IoT 中心在服务绑定的消息上加盖标记。 |
| **IoTHub.ConnectionDeviceGenerationId** | 用于发送此消息的经过身份验证设备的生成 ID。 此值由 IoT 中心在服务绑定的消息上加盖标记。 |
| **IoTHub.EnqueuedTime** | IoT 中心收到消息的时间。 |
| **IoTHub.StreamId** | 由发送方设备添加的自定义事件属性。 |


## <a name="stream-data-from-blob-storage"></a>从 Blob 存储流式传输数据
对于需要将大量非结构化数据存储在云中的情况，Azure Blob 存储提供了一种经济高效且可伸缩的解决方案。 Blob 存储中的数据通常被视为静态数据。 但是，Blob 数据可以作为数据流由流分析进行处理。 

通过流分析来使用 Blob 存储输入时，日志处理是一种常用方案。 在此方案中，首先从某个系统捕获遥测数据文件，然后根据需要对这些数据进行分析和处理以提取有意义的数据。

流分析中 Blob 存储事件的默认时间戳是上次修改 Blob 的时间戳，即 `BlobLastModifiedUtcTime`。 若要在事件负载中使用时间戳以流方式处理数据，则必须使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 关键字。

CSV 格式的输入需要标头行来定义数据集的字段，并且所有标头行字段必须是唯一的。

流分析目前不支持对事件中心捕获或 IoT 中心 Azure 存储容器自定义终结点生成的 AVRO 消息执行反序列化操作。

> [!NOTE]
> 流分析不支持将内容添加到现有 blob 文件。 流分析将仅查看每个文件一次，并且在作业读取数据后对文件所做的任何更改都不会得到处理。 最佳做法是立即上传 blob 文件的全部数据，然后将其他较新的事件添加到其他全新的 blob 文件中。
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>将 Blob 存储配置为流输入 

下表介绍了将 Blob 存储配置为流输入时，Azure 门户的“新输入”页中的每个属性。

| 属性 | 说明 |
| --- | --- |
| **输入别名** | 在作业查询中用于引用此输入的友好名称。 |
| **订阅** | 选择 IoT 中心资源所在的订阅。 | 
| **存储帐户** | 存储 Blob 文件所在的存储帐户的名称。 |
| **存储帐户密钥** | 与存储帐户关联的密钥。 此选项会自动进行填充，除非已选择手动提供 Blob 存储设置的选项。 |
| **容器** | 用于 Blob 输入的容器。 容器对存储在 Microsoft Azure Blob 服务中的 blob 进行逻辑分组。 将 Blob 上传到 Azure Blob 存储服务时，必须为该 Blob 指定一个容器。 可以选择“使用现有”，以便使用现有的容器；也可以选择“新建”，以便创建新的容器。|
| **路径模式**（可选） | 用于定位指定容器中的 blob 的文件路径。 在路径中，可以指定以下 3 个变量的一个或多个实例：`{date}`、`{time}` 或 `{partition}`<br/><br/>示例 1：`cluster1/logs/{date}/{time}/{partition}`<br/><br/>示例 2：`cluster1/logs/{date}`<br/><br/>`*` 字符不是路径前缀允许使用的值。 仅允许使用有效的 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob 字符</a>。 |
| **日期格式**（可选） | 如果在路径中使用日期变量，则为组织文件的日期格式。 示例：`YYYY/MM/DD` |
| **时间格式**（可选） |  如果在路径中使用时间变量，则为组织文件的时间格式。 目前唯一支持的值是 `HH`，表示小时。 |
| **事件序列化格式** | 传入数据流的序列化格式（JSON、CSV 或 Avro）。 |
| **编码** | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| **压缩** | 用于读取传入数据流的压缩类型，例如 None（默认）、GZip 或 Deflate。 |

如果数据来自 Blob 存储源，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **BlobName** |提供事件的输入 blob 的名称。 |
| **EventProcessedUtcTime** |流分析处理事件的日期和时间。 |
| **BlobLastModifiedUtcTime** |上次修改 blob 的日期和时间。 |
| **PartitionId** |输入适配器的从零开始的分区 ID。 |

例如，可以使用这些字段编写如以下示例所示的查询：

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

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
