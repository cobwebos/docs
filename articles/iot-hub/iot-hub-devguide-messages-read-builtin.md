---
title: "了解 Azure IoT 中心内置终结点 | Microsoft Docs"
description: "开发人员指南：介绍如何使用与事件中心兼容的内置终结点来读取设备到云的消息。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: f864ca586e8e607168ae7b46a1eaa297eca1cfb8
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>通过内置终结点读取设备到云的消息

默认情况下，消息将路由到与[事件中心][lnk-event-hubs]兼容的内置面向服务的终结点 (messages/events) 中。 此终结点目前仅通过端口 5671 上的 [AMQP][lnk-amqp] 协议公开。 IoT 中心公开以下属性，以便用户控制内置的与事件中心兼容的消息传送终结点 **messages/events**。

| 属性            | 说明 |
| ------------------- | ----------- |
| **分区计数** | 在创建时设置此属性，以便为设备到云事件引入定义[分区][lnk-event-hub-partitions]数。 |
| **保留时间**  | 此属性指定 IoT 中心保留消息的时间（以天为单位）。 默认值为一天，但可以增加到七天。 |

IoT 中心还支持用户管理内置设备到云接收终结点上的使用者组。

默认情况下，不显式匹配消息路由规则的所有消息都将写入到内置终结点。 如果禁用此回退路由，将删除不显式匹配任何消息路由规则的消息。

可以通过 [IoT 中心资源提供程序 REST API][lnk-resource-provider-apis] 以编程方式修改保留期时间，或使用 [Azure 门户][lnk-management-portal]进行修改。

IoT 中心向后端服务公开 **messages/events** 内置终结点，让后端服务读取中心收到的设备到云消息。 该终结点与事件中心兼容，因此可以使用事件中心服务支持的任何机制读取消息。

## <a name="read-from-the-built-in-endpoint"></a>通过内置终结点读取

使用[适用于 .NET 的 Azure 服务总线 SDK][lnk-servicebus-sdk] 或[事件中心 - 事件处理器主机][lnk-eventprocessorhost]时，可以将任何 IoT 中心连接字符串与正确的权限配合使用。 然后使用**消息/事件**作为事件中心名称。

使用无法识别 IoT 中心的 SDK（或产品集成）时，必须从 IoT 中心设置检索与事件中心兼容的终结点和与事件中心兼容的名称：

1. 登录 [Azure 门户][lnk-management-portal]，并导航到 IoT 中心。
1. 单击“终结点” 。
1. 在“内置终结点”部分，单击“事件”。 
1. 属性页将打开，包含以下值：“与事件中心兼容的终结点”、“与事件中心兼容的名称”、“分区”、“保留时间”和“使用者组”。

    ![设备到云的设置][img-eventhubcompatible]

IoT 中心 SDK 需要 IoT 中心终结点名称，即“终结点”下所示的 messages/events。

如果当前使用的 SDK 需要“主机名”或“命名空间”值，请从“事件中心兼容的终结点”中删除方案。 例如，如果与事件中心兼容的终结点为 sb://iothub-ns-myiothub-1234.servicebus.windows.net/，则主机名为 iothub-ns-myiothub-1234.servicebus.windows.net。 命名空间为 iothub-ns-myiothub-1234。

然后，可以使用具有 **ServiceConnect** 权限的任何共享访问策略连接到指定的事件中心。

如果需要通过使用以前的信息构建事件中心连接字符串，请使用以下模式：

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

可与 IoT 中心公开的事件中心兼容的终结点配合使用的 SDK 和集成包含以下列表中的项目：

* [Java 事件中心客户端](https://github.com/Azure/azure-event-hubs-java)。
* [Apache Storm Spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。 可以在 GitHub 上查看 [Spout 源代码](https://github.com/apache/storm/tree/master/external/storm-eventhubs)。
* [Apache Spark 集成](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)。

## <a name="next-steps"></a>后续步骤

有关 IoT 中心终结点的详细信息，请参阅 [IoT 中心终结点][lnk-endpoints]。

[入门][lnk-get-started]教程说明如何从模拟设备发送设备到云的消息以及如何通过内置终结点读取消息。 有关更多详细信息，请参阅[使用路由处理 IoT 中心设备到云的消息][lnk-d2c-tutorial]教程。

如果想要将设备到云的消息路由到自定义终结点，请参阅[对设备到云的消息使用消息路由和自定义终结点][lnk-custom]。

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
