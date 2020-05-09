---
title: 了解 Azure IoT 中心内置终结点 | Microsoft Docs
description: 开发人员指南：介绍如何使用与事件中心兼容的内置终结点来读取设备到云的消息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: amqp
ms.openlocfilehash: bf7c4118e17727c6c8141570ab146026d5383059
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996938"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>从内置终结点读取设备到云的消息

默认情况下，消息将路由到与[事件中心](https://azure.microsoft.com/documentation/services/event-hubs/)兼容的内置面向服务的终结点 (**messages/events**) 中。 目前仅在端口 5671 上使用 [AMQP](https://www.amqp.org/) 协议公开此终结点。 IoT 中心公开以下属性，以便用户控制内置的与事件中心兼容的消息传送终结点 **messages/events**。

| 属性            | 说明 |
| ------------------- | ----------- |
| **分区计数** | 在创建时设置此属性，以便为设备到云事件引入定义[分区](../event-hubs/event-hubs-features.md#partitions)数。 |
| **保留时间**  | 此属性指定 IoT 中心保留消息的时间（以天为单位）。 默认值为一天，但可以增加到七天。 |

IoT 中心允许将数据保留在内置的事件中心，最长保留期为 7 天。 可以在创建 IoT 中心期间设置保留时间。 IoT 中心的数据保留时间取决于 IoT 中心层和单元类型。 就大小来说，内置事件中心能够保留的消息的最大大小为至少 24 小时的配额。 例如，使用 1 个 S1 单元时，在每条消息的大小为 4k 的情况下，IoT 中心提供的存储足以保留至少 40 万条消息。 如果设备发送的消息较小，则这些消息可能会保留更长的时间（最多 7 天），具体取决于使用的存储。 我们保证至少将数据保留指定的保留时间。 消息将会过期，并且在保留时间过去后将无法访问。 

IoT 中心还支持用户管理内置设备到云接收终结点上的使用者组。 每个 IoT 中心最多可以有 20 个使用者组。

如果使用[消息路由](iot-hub-devguide-messages-d2c.md)，并启用了[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route)，则与任何路由上的查询不匹配的所有消息都会进入内置终结点。 如果禁用此回退路由，将删除与任何查询都不匹配的消息。

可以使用 [IoT 中心资源提供程序 REST API](/rest/api/iothub/iothubresource) 以编程方式修改保留期时间，或通过 [Azure 门户](https://portal.azure.com)进行修改。

IoT 中心向后端服务公开 **messages/events** 内置终结点，让后端服务读取中心收到的设备到云消息。 该终结点与事件中心兼容，因此可以使用事件中心服务支持的任何机制读取消息。

## <a name="read-from-the-built-in-endpoint"></a>通过内置终结点读取

某些产品集成和事件中心 SDK 可以感知 IoT 中心，并让你使用 IoT 中心服务连接字符串连接到内置的终结点。

使用无法感知 IoT 中心的事件中心 SDK 或产品集成时，需要一个与事件中心兼容的终结点以及与事件中心兼容的名称。 可以从门户检索这些值，如下所示：

1. 登录到[Azure 门户](https://portal.azure.com)并导航到 IoT 中心。

2. 单击“内置终结点”****。

3. **Events**节包含以下值：**分区**、**与事件中心兼容的名称**、与**事件中心兼容的终结点**、**保留时间**和**使用者组**。

    ![设备到云的设置](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

在门户中，"与事件中心兼容的终结点" 字段包含一个完整的事件中心连接字符串，如下所示： **endpoint = sb：//abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName = iothubowner;SharedAccessKey = keykeykeykeykeykey =;EntityPath = iothub-ehub-1234-123456**。 如果所用 SDK 需求其他值，则这些值将会是：

| 名称 | 值 |
| ---- | ----- |
| 终结点 | sb://abcd1234namespace.servicebus.windows.net/ |
| 主机名 | abcd1234namespace.servicebus.windows.net |
| 命名空间 | abcd1234namespace |

然后，可以使用具有 **ServiceConnect** 权限的任何共享访问策略连接到指定的事件中心。

可以用来连接到内置的、与事件中心兼容的且由 IoT 中心公开的终结点的 SDK 包括：

| 语言 | SDK | 示例 |
| -------- | --- | ------ |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [快速入门](quickstart-send-telemetry-dotnet.md) |
 Java | https://github.com/Azure/azure-event-hubs-java | [快速入门](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [快速入门](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | https://github.com/Azure-Samples/azure-iot-samples-python/tree/master/iot-hub/Quickstarts/read-d2c-messages |

可以与内置的、与事件中心兼容的且由 IoT 中心公开的终结点配合使用的产品集成包括：

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。 请参阅[利用 Azure Functions 处理 IoT 中心的数据](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)。
* [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)。 请参阅[将数据作为流分析的输入进行流式传输](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。
* 时序[见解](https://docs.microsoft.com/azure/time-series-insights/)。 请参阅[向时序见解环境添加 IoT 中心事件源](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 可以在 GitHub 上查看 [Spout 源代码](https://github.com/apache/storm/tree/master/external/storm-eventhubs)。
* [Apache Spark 集成](../hdinsight/spark/apache-spark-eventhub-streaming.md)。
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)。

## <a name="next-steps"></a>后续步骤

* 有关 IoT 中心终结点的详细信息，请参阅 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。

* [快速入门](quickstart-send-telemetry-node.md)介绍如何从模拟设备发送设备到云的消息，以及如何从内置终结点读取消息。 

有关更多详细信息，请参阅[使用路由处理 IoT 中心设备到云的消息](tutorial-routing.md)教程。

* 如果想要将设备到云的消息路由到自定义终结点，请参阅[对设备到云的消息使用消息路由和自定义终结点](iot-hub-devguide-messages-read-custom.md)。
