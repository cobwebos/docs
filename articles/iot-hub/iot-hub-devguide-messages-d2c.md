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
ms.openlocfilehash: 6ee9e334c10bd2d0f291b5fd1bb547ba3ba83ddb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877189"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>使用 IoT 中心消息路由将设备到云消息发送到不同的终结点

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

消息路由使你能够以自动、可缩放以及可靠的方式将消息从设备发送到云服务。 消息路由可用于： 

* **发送设备遥测消息以及事件**（即设备生命周期事件和设备孪生更改事件）到内置终结点和自定义终结点。 了解有关[路由终结点](#routing-endpoints)。

* **在将数据路由到各个终结点之前对数据进行筛选**，筛选方法是通过应用丰富的查询。 消息路由允许你查询消息属性和消息正文以及设备孪生标记和设备孪生属性。 深入了解如何使用[消息路由中的查询](iot-hub-devguide-routing-query-syntax.md)。

IoT 中心需要这些服务终结点的写入权限，以便使用消息路由。 如果通过 Azure 门户配置终结点，则为你添加必要权限。 请确保将服务配置为支持预期吞吐量。 在首次配置 IoT 解决方案时，可能需要监视附加终结点，并针对实际负载进行任意的必要调整。

IoT 中心为所有“设备到云”消息传递定义[通用格式](iot-hub-devguide-messages-construct.md)，以实现跨协议的互操作性。 如果某条消息与多个路由匹配，而这些路由指向同一终结点，则 IoT 中心仅向该终结点传递一次消息。 因此无需在服务总线队列或主题中配置重复数据删除。 在分区队列中，分区相关性可保障消息排序。 使用本教程了解如何[配置消息路由](tutorial-routing.md)。

## <a name="routing-endpoints"></a>路由终结点

IoT 中心有一个默认的内置终结点（消息/事件），此终结点与事件中心兼容。 可以通过将订阅中的其他服务链接到中心来创建要将消息路由到的[自定义终结点](iot-hub-devguide-endpoints.md#custom-endpoints)。 IoT 中心目前支持将以下服务作为自定义终结点：

### <a name="built-in-endpoint"></a>内置终结点

可以使用标准[事件中心集成和 SDK](iot-hub-devguide-messages-read-builtin.md) 接收来自内置终结点（消息/事件）的设备到云的消息。 在创建一个路由后，数据将停止流向内置终结点，除非创建了到该终结点的路由。

### <a name="azure-blob-storage"></a>Azure Blob 存储

IoT 中心支持将数据以 [Apache Avro](https://avro.apache.org/) 格式和 JSON 格式写入 Azure Blob 存储。 编码 JSON 格式的功能通常可在 IoT 中心可用的所有区域中使用。 默认值为 AVRO。 只有在配置 Blob 存储终结点时才能设置编码格式。 不能编辑现有终结点的格式。 使用 JSON 编码时, 必须将 contentType 设置为**application/JSON** , 并将 contentEncoding 设置为消息[系统属性](iot-hub-devguide-routing-query-syntax.md#system-properties)中的**utf-8** 。 这两个值都是不区分大小写的。 如果未设置内容编码, 则 IoT 中心将以基本64编码格式编写消息。 可以使用 IoT 中心的创建或更新 REST API（具体说来就是 [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)、Azure 门户、[Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) 或 [Azure Powershell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)）选择编码格式。 下图说明如何在 Azure 门户中选择编码格式。

![Blob 存储终结点编码](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT 中心还支持将消息路由到 ADLS Gen2 帐户, 这是在 Blob 存储基础之上构建的启用了[分层命名空间](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)的存储帐户。 此功能处于公共预览阶段, 适用于美国西部2和美国中部的新 ADLS Gen2 帐户。 即将推出此功能到所有云区域。

IoT 中心将在消息达到特定大小或在经过一定的事件后，对消息进行批处理并将数据写入 Blob。 IoT 中心默认为以下文件命名约定：

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

你可以使用任何文件命名约定，但必须使用所有列出的令牌。 如果没有要写入的数据，IoT 中心会写入到一个空 blob。

在路由到 blob 存储时，请列出 blob，再循环访问它们，以确保读取所有容器而不进行任何分区假设。 在 [Microsoft 发起的故障转移](iot-hub-ha-dr.md#microsoft-initiated-failover)或 IoT 中心[手动故障转移](iot-hub-ha-dr.md#manual-failover)期间，分区范围可能发生变化。 可以使用 [List Blobs API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 来枚举 Blob 的列表。 请将以下示例作为指南来查看。

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

### <a name="service-bus-queues-and-service-bus-topics"></a>服务总线队列和服务总线主题

用作 IoT 中心终结点的服务总线队列和主题不得启用**会话**或**重复检测**选项。 如果启用了其中任一选项，该终结点会在 Azure 门户中显示为**无法访问**。

### <a name="event-hubs"></a>事件中心

除了与事件中心兼容的内置终结点外，还可以将数据路由到事件中心类型的自定义终结点。 

## <a name="reading-data-that-has-been-routed"></a>读取已路由的数据

可以按照此[教程](tutorial-routing.md)配置一个路由。

使用以下教程了解如何从终结点读取消息。

* 从[内置终结点](quickstart-send-telemetry-node.md)进行读取

* 从 [Blob 存储](../storage/blobs/storage-blob-event-quickstart.md)进行读取

* 从[事件中心](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)进行读取

* 从[服务总线队列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)进行读取

* 从[服务总线主题](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)进行读取

## <a name="fallback-route"></a>回退路由

回退路由将所有不满足任何现有路由上的查询条件的消息发送到与[事件中心](/azure/event-hubs/)兼容的内置事件中心（消息/事件）。 如果已启用消息路由，则可以启用此回退路由功能。 在创建一个路由后，数据将停止流向内置终结点，除非创建了到该终结点的路由。 如果没有到内置终结点的路由并且已启用回退路由，则仅与路由上的任何查询条件不匹配的消息将被发送到内置终结点。 此外，如果已删除现有路由，必须启用回退路由才能接收内置终结点处的所有数据。

可以在 "Azure 门户 > 消息路由" 边栏选项卡中启用/禁用回退路由。 还可以将 Azure 资源管理器用于 [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 来为回退路由使用自定义终结点。

## <a name="non-telemetry-events"></a>非遥测事件

除了设备遥测以外, 消息路由还启用发送设备克隆更改事件、设备生命周期事件和数字克隆更改事件 (公共预览版)。 例如，如果使用数据源创建一个设置为到**设备孪生更改事件**的路由，IoT 中心会将消息发送到包含设备孪生更改的终结点。 同样, 如果创建了一个路由, 并将数据源设置为**设备生命周期事件**, 则 IoT 中心将发送一条消息, 指示设备是否已删除或创建。 最后, 作为[IoT 即插即用公共预览版](../iot-pnp/overview-iot-plug-and-play.md)的一部分, 开发人员可以创建路由, 并将数据源设置为**数字克隆更改事件**, 而 IoT 中心会在每次设置或更改数字克隆[属性](../iot-pnp/iot-plug-and-play-glossary.md)时发送消息, 即[数字输出](../iot-pnp/iot-plug-and-play-glossary.md)将被替换, 或者基础设备克隆发生更改事件时。

[IoT 中心还集成了 Azure 事件网格](iot-hub-event-grid.md)来发布设备事件以支持基于这些事件的工作流的实时集成和自动化。 请参阅[消息路由和事件网格之间的主要区别](iot-hub-event-grid-routing-comparison.md)来了解哪种更适合你的方案。

## <a name="testing-routes"></a>测试路由

在创建新路由或编辑现有路由时，应通过示例消息来测试路由查询。 可以测试单个路由或一次测试所有路由，并且在测试期间，不会有消息被路由到终结点。 Azure 门户、Azure 资源管理器、Azure PowerShell 和 Azure CLI 可用于测试。 结果有助于确定示例消息是否与查询匹配、消息是否与查询不匹配, 或测试无法运行, 因为示例消息或查询语法不正确。 若要了解详细信息，请参阅[测试路由](/rest/api/iothub/iothubresource/testroute)和[测试所有路由](/rest/api/iothub/iothubresource/testallroutes)。

## <a name="latency"></a>延迟

使用内置终结点路由设备到云遥测消息时，在创建第一个路由后，端到端延迟略微增大。

大多数情况下, 延迟的平均增加时间小于500毫秒。 可以使用路由：消息/事件的消息延迟或 d2c.endpoints.latency.builtIn.events IoT 中心指标来监视延迟。 在创建第一个路由后创建或删除任何路由不会影响端到端延迟。

## <a name="monitoring-and-troubleshooting"></a>监视和故障排除

IoT 中心提供了多个与路由和终结点相关的指标，使你能够大致了解你的中心的运行状况和已发送的消息数。 你可以组合来自多项指标的信息来确定问题的根本原因。 例如，使用指标路由：已删除的遥测消息数或 d2c.telemetry.egress.dropped 来确定当消息与任何路由上的查询不匹配并且已禁用回退路由时已删除的消息数。 [IoT 中心指标](iot-hub-metrics.md)列出了默认为 IoT 中心启用的所有指标。

可以使用 REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 获取终结点的[运行状况状态](iot-hub-devguide-endpoints.md#custom-endpoints)。 当终结点运行状况为故障或不正常时，建议使用与路由消息延迟相关的 [IoT 中心指标](iot-hub-metrics.md)来标识并调试错误。 例如，对于终结点类型“事件中心”，可以监视 **d2c.endpoints.latency.eventHubs**。 当 IoT 中心建立了最终一致的运行状况状态以后，系统会将不正常终结点的状态更新为正常。

通过使用 Azure Monitor [诊断设置](../iot-hub/iot-hub-monitor-resource-health.md)中的**路由**诊断日志，可以跟踪发生在路由查询和终结点运行状况的评估期间、由 IoT 中心所察觉到的错误，例如某个终结点已失效。 可以将这些诊断日志发送到 Azure Monitor 日志、事件中心或 Azure 存储进行自定义处理。

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建消息路由，请参阅[使用路由处理 IoT 中心的设备到云消息](tutorial-routing.md)。

* [如何发送设备到云消息](quickstart-send-telemetry-node.md)

* 有关可以用来发送设备到云消息的 SDK 的详细信息，请参阅 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
