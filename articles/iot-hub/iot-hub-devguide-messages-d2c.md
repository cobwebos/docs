---
title: "了解 Azure IoT 中心设备到云的消息传送 | Microsoft Docs"
description: "开发人员指南 - 如何使用 IoT 中心进行设备到云的消息传送。 包含有关发送遥测和非遥测数据以及使用路由传递消息的信息。"
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
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: d856e26084ee79386a2e8e0e527804bda86b477b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017


---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>将设备到云的消息发送到 IoT 中心

若要将时间序列遥测和警报从设备发送到解决方案后端，请将设备到云的消息从设备发送到 IoT 中心。 有关 IoT 中心支持的设备到云的其他选项的讨论，请参阅[设备到云的通信指南][lnk-d2c-guidance]。

通过面向设备的终结点 (**/devices/{deviceId}/messages/events**) 发送从设备到云的消息。 路由规则随后将消息路由到 IoT 中心内面向服务的终结点之一。 路由规则使用流经中心的设备到云消息的标头和正文来确定将消息路由到的位置。 默认情况下，消息将路由到与[事件中心][lnk-event-hubs]兼容的面向服务的内置终结点 (messages/events) 中。 因此，可以在解决方案后端中使用标准[事件中心集成和 SDK][lnk-compatible-endpoint] 接受从设备到云的消息。

IoT 中心使用流式消息传递模式实现设备到云的消息传递。 与[事件中心][lnk-event-hubs]*事件*和[服务总线][lnk-servicebus]*消息*相比，IoT 中心的设备到云消息更类似前者，类似之处在于有大量事件通过可供多个读取器读取的服务。

使用 IoT 中心进行的设备到云消息传递具有以下特征：

* 设备到云的消息可持久保留在 IoT 中心的默认 messages/events 终结点长达七天。
* 设备到云的消息最大可为 256 KB，而且可分成多个批以优化发送。 Batch 最大可为 256 KB。
* 如[控制对 IoT 中心的访问][lnk-devguide-security]部分所述，IoT 中心允许基于设备的身份验证和访问控制。
* IoT 中心最多允许创建 10 个自定义终结点。 基于 IoT 中心上配置的路由，将消息传递到终结点。 有关详细信息，请参阅[路由规则](#routing-rules)。
* IoT 中心启动数百万同时连接的设备（请参阅[配额和限制][lnk-quotas]）。
* IoT 中心不允许任意分区。 从设备到云的消息根据其源于的 **deviceId** 进行分区。

有关 IoT 中心和事件中心服务差异的详细信息，请参阅 [Azure IoT 中心与 Azure 事件中心的比较][lnk-comparison]。

## <a name="send-non-telemetry-traffic"></a>发送非遥测流量

通常，除了遥测数据点以外，设备还会发送消息以及要求在解决方案后端中单独执行和处理。 例如，关键警报必须在后端触发特定操作。 可轻松编写[路由规则][lnk-devguide-custom]，根据消息的标头或信息正文的值，将这些类型的消息发送到专用于处理这些消息的终结点 。

有关此类消息的最佳处理方式的详细信息，请参阅[教程：如何处理 IoT 中心从设备到云的消息][lnk-d2c-tutorial]教程。

## <a name="route-device-to-cloud-messages"></a>路由设备到云的消息

有两个选项可用于将设备到云的消息路由到后端应用：

* 使用内置的[与事件中心兼容的终结点][lnk-compatible-endpoint]，使后端应用能够读取中心收到的设备到云消息。 若要了解内置的与事件中心兼容的终结点，请参阅[通过内置终结点读取设备到云的消息][lnk-devguide-builtin]。
* 使用路由规则将消息发送到 IoT 中心的自定义终结点。 自定义终结点使后端应用可以通过事件中心、服务总线队列或者服务总线主题读取设备到云信息。 若要了解路由和自定义终结点，请参阅[对设备到云的消息使用自定义终结点和路由规则][lnk-devguide-custom]。

## <a name="anti-spoofing-properties"></a>反欺骗属性

为了避免设备到云的消息中出现设备欺骗，IoT 中心使用以下属性在所有消息上加上戳记：

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

根据[设备标识属性][lnk-device-properties]，前两个属性包含源设备的 **deviceId** 和 **generationId**。

**ConnectionAuthMethod** 属性包含具有以下属性的 JSON 序列化对象：

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>后续步骤

有关可以用来发送设备到云消息的 SDK 的详细信息，请参阅 [Azure IoT SDK][lnk-sdks]。

[入门][lnk-get-started]教程介绍了如何从模拟设备和物理设备中发送设备到云的消息。 有关更多详细信息，请参阅[使用路由处理 IoT 中心设备到云的消息][lnk-d2c-tutorial]教程。

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

