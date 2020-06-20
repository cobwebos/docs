---
title: 了解 Azure IoT 中心内置终结点 | Microsoft Docs
description: 开发人员指南：介绍如何使用与事件中心兼容的内置终结点来读取设备到云的消息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: amqp
ms.openlocfilehash: 791d41aae7dde3e1ef2ae511de4ffd84141f0232
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310778"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>通过内置终结点读取设备到云的消息

默认情况下，消息将路由到与[事件中心](https://azure.microsoft.com/documentation/services/event-hubs/)兼容的内置面向服务的终结点 (messages/events) 中。 此终结点目前仅通过端口 5671 上的 [AMQP](https://www.amqp.org/) 协议公开。 IoT 中心公开以下属性，以便用户控制内置的与事件中心兼容的消息传送终结点 **messages/events**。

| properties            | 说明 |
| ------------------- | ----------- |
| **分区计数** | 在创建时设置此属性，以便为设备到云事件引入定义[分区](../event-hubs/event-hubs-features.md#partitions)数。 |
| **保留时间**  | 此属性指定 IoT 中心保留消息的时间（以天为单位）。 默认值为一天，但可以增加到七天。 |

IoT 中心允许内置事件中心的最长数据保留期为 7 天。 可以在创建 IoT 中心时设置保留时间。 IoT 中心的数据保留时间取决于你的 IoT 中心层和单位类型。 就大小而言，内置事件中心可以保留最大消息大小至少为 24 小时配额的消息。 例如，对于 1 S1 单位，IoT 中心提供足够的存储空间来至少保留 400K 消息（每个大小为 4k）。 如果设备发送的消息较小，则可能会保留更长时间（最长 7 天），具体取决于使用的存储空间。 我们保证至少在指定的保留期内保留数据。 消息将会过期，并且在保留时间后将无法访问。 

IoT 中心还支持用户管理内置设备到云接收终结点上的使用者组。 每个 IoT 中心最多可以有 20 个使用者组。

如果使用[消息路由](iot-hub-devguide-messages-d2c.md)，并启用了[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route)，则与任何路由上的查询不匹配的所有消息都会写入内置终结点。 如果禁用此回退路由，将删除与任何查询都不匹配的消息。

可以使用 [IoT 中心资源提供程序 REST API](/rest/api/iothub/iothubresource) 以编程方式修改保留期时间，或通过 [Azure 门户](https://portal.azure.com)进行修改。

IoT 中心向后端服务公开 **messages/events** 内置终结点，让后端服务读取中心收到的设备到云消息。 该终结点与事件中心兼容，因此可以使用事件中心服务支持的任何机制读取消息。

## <a name="read-from-the-built-in-endpoint"></a>通过内置终结点读取

某些产品集成和事件中心 SDK 可识别 IoT 中心，并且可让你使用 IoT 中心服务连接字符串连接到内置终结点。

使用无法识别 IoT 中心的事件中心 SDK 或产品集成时，需要与事件中心兼容的终结点和与事件中心兼容的名称。 可以从门户中检索这些值，如下所示：

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。

2. 单击“内置终结点”。

3. “事件”部分包含以下值：“分区”、“与事件中心兼容的名称”、“与事件中心兼容的终结点”、“保留时间”和“使用者组”。    

    ![设备到云的设置](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

在门户中，“与事件中心兼容的终结点”字段包含完整的事件中心连接字符串，如下所示：Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456。 如果你使用的 SDK 需要其他值，则这些值将是：

| 名称 | 值 |
| ---- | ----- |
| 端点 | sb://abcd1234namespace.servicebus.windows.net/ |
| 主机名 | abcd1234namespace.servicebus.windows.net |
| 命名空间 | abcd1234namespace |

然后，可以使用具有 **ServiceConnect** 权限的任何共享访问策略连接到指定的事件中心。

可用于连接到 IoT 中心公开的内置的与事件中心兼容的终结点的 SDK 包括：

| 语言 | SDK 中 IsInRole 中的声明 | 示例 |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [快速入门](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [快速入门](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [快速入门](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | https://github.com/Azure-Samples/azure-iot-samples-python/tree/master/iot-hub/Quickstarts/read-d2c-messages |

可用于 IoT 中心公开的内置的与事件中心兼容的终结点的产品集成包括：

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。 请参阅[使用 Azure Functions 从 IoT 中心处理数据](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)。
* [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)。 请参阅[将数据作为流分析的输入进行流式传输](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。
* [时序见解](https://docs.microsoft.com/azure/time-series-insights/)。 请参阅[向时序见解环境添加 IoT 中心事件源](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 可以在 GitHub 上查看 [Spout 源代码](https://github.com/apache/storm/tree/master/external/storm-eventhubs)。
* [Apache Spark 集成](../hdinsight/spark/apache-spark-eventhub-streaming.md)。
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)。

## <a name="next-steps"></a>后续步骤

* 有关 IoT 中心终结点的详细信息，请参阅 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。

* [快速入门](quickstart-send-telemetry-node.md)介绍如何从模拟设备发送设备到云的消息，以及如何从内置终结点读取消息。 

有关更多详细信息，请参阅[使用路由处理 IoT 中心设备到云的消息](tutorial-routing.md)教程。

* 如果想要将设备到云的消息路由到自定义终结点，请参阅[对设备到云的消息使用消息路由和自定义终结点](iot-hub-devguide-messages-read-custom.md)。
