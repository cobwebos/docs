---
title: 了解 Azure IoT 中心消息路由 | Microsoft Docs
description: 开发人员指南 - 如何使用消息路由发送设备到云的消息。 包含有关发送遥测和非遥测数据的信息。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370451"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>使用 IoT 中心消息路由将设备到云消息发送到不同的终结点

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

消息路由使你能够以自动、可缩放以及可靠的方式将消息从设备发送到云服务。 消息路由可用于： 

* **发送设备遥测消息以及事件**（即设备生命周期事件和设备孪生更改事件）到内置终结点和自定义终结点。 了解有关[路由终结点](#routing-endpoints)。

* **在将数据路由到各个终结点之前对数据进行筛选**，筛选方法是通过应用丰富的查询。 消息路由允许你查询消息属性和消息正文以及设备孪生标记和设备孪生属性。 深入了解如何使用[消息路由中的查询](iot-hub-devguide-routing-query-syntax.md)。

IoT 中心需要这些服务终结点的写入权限，以便使用消息路由。 如果通过 Azure 门户配置终结点，则将添加必要权限。 请确保将服务配置为支持预期吞吐量。 例如，如果将事件中心用作自定义终结点，则必须配置该事件中心的**吞吐量单位**，使其能够处理计划通过 IoT 中心消息路由发送的事件的入口。 同样，使用服务总线队列作为终结点时，必须配置**最大大小**，以确保队列可以包含所有数据引入，直到使用者出口。 在首次配置 IoT 解决方案时，可能需要监视附加终结点，并针对实际负载进行任意的必要调整。

IoT 中心为所有设备到云的消息传送定义了[格式](iot-hub-devguide-messages-construct.md)，以便实现跨协议互操作性。 如果某条消息与多个路由匹配，而这些路由指向同一终结点，则 IoT 中心仅向该终结点传递一次消息。 因此无需在服务总线队列或主题中配置重复数据删除。 在分区队列中，分区相关性可保障消息排序。 使用本教程了解如何[配置消息路由](tutorial-routing.md)。

## <a name="routing-endpoints"></a>路由终结点

IoT 中心有一个默认的内置终结点（消息/事件），此终结点与事件中心兼容。 可以通过将订阅中的其他服务链接到中心来创建要将消息路由到的[自定义终结点](iot-hub-devguide-endpoints.md#custom-endpoints)。 

每条消息都路由到其路由查询匹配的所有终结点。 换句话说，消息可以路由到多个终结点。

IoT 中心目前支持将以下服务作为自定义终结点：

### <a name="built-in-endpoint"></a>内置终结点

可以使用标准[事件中心集成和 SDK](iot-hub-devguide-messages-read-builtin.md) 接收来自内置终结点（消息/事件）的设备到云的消息。 创建路由后，数据将停止流向内置终结点，除非该终结点创建了路由。

### <a name="azure-storage"></a>Azure 存储

有两个存储服务 IoT 中心可将消息路由到-- [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)和[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) （ADLS Gen2）帐户。 Azure Data Lake Storage 帐户是在 blob 存储基础之上构建的启用了[分层命名空间](../storage/blobs/data-lake-storage-namespace.md)的存储帐户。 这两种方法都使用 blob 来存储它们。

IoT 中心支持以[Apache Avro](https://avro.apache.org/)格式和 JSON 格式将数据写入 Azure 存储。 默认值为 AVRO。 只能在配置 blob 存储终结点时设置编码格式。 无法编辑现有终结点的格式。 使用 JSON 编码时，必须将 contentType 设置为**application/JSON** ，并将 contentEncoding 设置为消息[系统属性](iot-hub-devguide-routing-query-syntax.md#system-properties)中的**utf-8** 。 这两个值都是不区分大小写的。 如果未设置内容编码，则 IoT 中心将以基本64编码格式编写消息。 您可以使用 IoT 中心的 Create 或 Update REST API （特别是[RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)、Azure 门户、 [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)或[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)）选择编码格式。 下图显示了如何在 Azure 门户中选择编码格式。

![Blob 存储终结点编码](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

每当批达到某个大小或特定时间时，IoT 中心将对消息进行批处理，并将数据写入存储。 IoT 中心默认为以下文件命名约定： 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

你可以使用任何文件命名约定，但必须使用所有列出的令牌。 如果没有要写入的数据，IoT 中心会写入到一个空 blob。

建议列出 blob 或文件，然后循环访问它们，以确保读取所有 blob 或文件而不进行分区假设。 在 [Microsoft 发起的故障转移](iot-hub-ha-dr.md#microsoft-initiated-failover)或 IoT 中心[手动故障转移](iot-hub-ha-dr.md#manual-failover)期间，分区范围可能发生变化。 你可以使用[列表 BLOB API](https://docs.microsoft.com/rest/api/storageservices/list-blobs)来枚举 Blob 或[列表 ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list)的列表，以获取文件列表。 请参阅以下示例作为指导。

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

> [!NOTE]
> 如果你的存储帐户具有限制 IoT 中心连接的防火墙配置，请考虑使用[Microsoft 受信任的第一方例外](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing)（在使用托管服务标识的 IoT 中心的选择区域中提供）。

若要创建 Azure Data Lake Gen2 兼容的存储帐户，请在 "**高级**" 选项卡上创建新的 V2 存储帐户，并在 "*分层命名空间*" 字段上选择 "*启用*"，如下图所示：

![选择 Azure Date Lake Gen2 storage](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>服务总线队列和服务总线主题

用作 IoT 中心终结点的服务总线队列和主题不得启用**会话**或**重复检测**选项。 如果启用了其中任一选项，该终结点会在 Azure 门户中显示为**无法访问**。

> [!NOTE]
> 如果你的服务总线资源具有限制 IoT 中心连接的防火墙配置，请考虑使用[Microsoft 受信任的第一方例外](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing)（在使用托管服务标识的 IoT 中心的选择区域中提供）。


### <a name="event-hubs"></a>事件中心

除了与事件中心兼容的内置终结点外，还可以将数据路由到事件中心类型的自定义终结点。 

> [!NOTE]
> 如果事件中心资源具有限制 IoT 中心连接的防火墙配置，请考虑使用[Microsoft 受信任的第一方例外](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing)（在使用托管服务标识的 IoT 中心的选择区域中提供）。


## <a name="reading-data-that-has-been-routed"></a>读取已路由的数据

可以按照此[教程](tutorial-routing.md)配置一个路由。

使用以下教程了解如何从终结点读取消息。

* 从[内置终结点](quickstart-send-telemetry-node.md)进行读取

* 从 [Blob 存储](../storage/blobs/storage-blob-event-quickstart.md)进行读取

* 从[事件中心](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)进行读取

* 从[服务总线队列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)进行读取

* 从[服务总线主题](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)进行读取


## <a name="fallback-route"></a>回退路由

回退路由将所有不满足任何现有路由上的查询条件的消息发送到与[事件中心](/azure/event-hubs/)兼容的内置事件中心（消息/事件）。 如果已启用消息路由，则可以启用此回退路由功能。 创建路由后，数据将停止流向内置终结点，除非该终结点创建了路由。 如果没有到内置终结点的路由并且已启用回退路由，则仅与路由上的任何查询条件不匹配的消息将被发送到内置终结点。 此外，如果已删除现有路由，必须启用回退路由才能接收内置终结点处的所有数据。

可以在 "Azure 门户 > 消息路由" 边栏选项卡中启用/禁用回退路由。 还可以将 Azure 资源管理器用于 [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 来为回退路由使用自定义终结点。

## <a name="non-telemetry-events"></a>非遥测事件

除了设备遥测以外，消息路由还启用发送设备克隆更改事件、设备生命周期事件和数字克隆更改事件（公共预览版）。 例如，如果使用数据源创建一个设置为到**设备孪生更改事件**的路由，IoT 中心会将消息发送到包含设备孪生更改的终结点。 同样，如果创建了一个路由，并将数据源设置为**设备生命周期事件**，则 IoT 中心将发送一条消息，指示设备是否已删除或创建。 最后，作为[IoT 即插即用公共预览版](../iot-pnp/overview-iot-plug-and-play.md)的一部分，开发人员可以创建路由，并将数据源设置为**数字克隆更改事件**，IoT 中心会在数据源[属性](../iot-pnp/iot-plug-and-play-glossary.md)设置或更改时发送消息，[并在基础](../iot-pnp/iot-plug-and-play-glossary.md)设备克隆发生更改事件时发送消息。

[IoT 中心还与 Azure 事件网格集成](iot-hub-event-grid.md)，以发布设备事件，以支持基于这些事件的实时工作流的集成和自动化。 请参阅[消息路由和事件网格之间的主要区别](iot-hub-event-grid-routing-comparison.md)来了解哪种更适合你的方案。

## <a name="testing-routes"></a>测试路由

在创建新路由或编辑现有路由时，应通过示例消息来测试路由查询。 可以测试单个路由或一次测试所有路由，并且在测试期间，不会有消息被路由到终结点。 Azure 门户、Azure 资源管理器、Azure PowerShell 和 Azure CLI 可用于测试。 结果有助于确定示例消息是否与查询匹配、消息是否与查询不匹配，或测试无法运行，因为示例消息或查询语法不正确。 若要了解详细信息，请参阅[测试路由](/rest/api/iothub/iothubresource/testroute)和[测试所有路由](/rest/api/iothub/iothubresource/testallroutes)。

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>至少传递一次后订购保证

IoT 中心消息路由可保证按顺序传递消息并至少将消息传递到终结点。 这意味着可以有重复的消息，并且可以重新传输一系列消息，从而满足原始消息顺序。 例如，如果原始消息顺序为 [1，2，3，4]，则可能会收到如下消息序列： [1，2，1，2，3，1，2，3，4]。 排序保证是，如果您收到消息 [1]，它将始终后跟 [2，3，4]。

为了处理消息重复，我们建议在源位置（通常是设备或模块）的消息的应用程序属性中标记唯一标识符。 使用此标识符的服务可以处理重复消息。

## <a name="latency"></a>延迟

使用内置终结点路由设备到云遥测消息时，在创建第一个路由后，端到端延迟略微增大。

大多数情况下，延迟的平均增加时间小于500毫秒。 可以使用路由：消息/事件的消息延迟或 d2c.endpoints.latency.builtIn.events IoT 中心指标来监视延迟。 在创建第一个路由后创建或删除任何路由不会影响端到端延迟。

## <a name="monitoring-and-troubleshooting"></a>监视和故障排除

IoT 中心提供了几个与路由和终结点相关的指标，使你可以大致了解你的中心和所发送消息的运行状况。 你可以组合来自多项指标的信息来确定问题的根本原因。 例如，使用度量**路由：丢弃的遥测消息**数或**d2c** ，用于识别在禁用了任何路由和回退路由上的查询时丢弃的消息数。 [IoT 中心指标](iot-hub-metrics.md)列出了默认为 IoT 中心启用的所有指标。

你可以使用 REST API[获取终结点运行状况](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)，以获取终结点的[运行状况状态](iot-hub-devguide-endpoints.md#custom-endpoints)。 建议使用与路由消息延迟相关的[IoT 中心指标](iot-hub-metrics.md)来确定和调试终结点运行状况不正常或不正常时的错误。 例如，对于终结点类型事件中心，可以监视**d2c. eventHubs**。 当 IoT 中心已建立最终一致的运行状况状态时，不正常终结点的状态将更新为 "正常"。

使用**路由**诊断日志 Azure Monitor[诊断设置](../iot-hub/iot-hub-monitor-resource-health.md)，你可以跟踪在评估路由查询期间发生的错误以及 IoT 中心感知到的终结点运行状况（例如，当终结点处于不活动状态时）。 这些诊断日志可以发送到 Azure Monitor 日志、事件中心或 Azure 存储进行自定义处理。

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建消息路由，请参阅[使用路由处理 IoT 中心的设备到云消息](tutorial-routing.md)。

* [如何发送设备到云消息](quickstart-send-telemetry-node.md)

* 有关可以用来发送设备到云消息的 SDK 的详细信息，请参阅 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
