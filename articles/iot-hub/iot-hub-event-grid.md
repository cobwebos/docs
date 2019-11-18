---
title: Azure IoT 中心和事件网格 | Microsoft Docs
description: 使用 Azure 事件网格根据 IoT 中心发生的操作来触发流程。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 2969791204474a7d73493ce6397c52255f7eab4a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151309"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>通过使用事件网格触发操作来响应 IoT 中心事件

通过将 Azure IoT 中心与 Azure 事件网格进行集成，使你可以向其他服务发送事件通知，并触发下游流程。 配置商业应用程序来侦听 IoT 中心事件，以便安全可靠地以可缩放方式响应关键事件。 例如，生成一个更新数据库的应用程序，创建一个工作票证，并在每次将新的 IoT 设备注册到 IoT 中心时提供电子邮件通知。

[Azure 事件网格](../event-grid/overview.md)是一种完全托管的事件路由服务，使用发布-订阅模型。 事件网格包含对 Azure 服务（如 [Azure Functions](../azure-functions/functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)）的内置支持，还可使用 Webhook 向非 Azure 服务传递事件警报。 有关受事件网格支持的事件处理程序的完整列表，请参阅 [Azure 事件网格简介](../event-grid/overview.md)。

![Azure 事件网格体系结构](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>区域可用性

事件网格集成适用于支持事件网格的区域中的 IoT 中心。 有关区域的最新列表，请参阅 [Azure 事件网格简介](../event-grid/overview.md)。

## <a name="event-types"></a>事件类型

IoT 中心将发布以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 当设备注册到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDeleted | 当设备从 IoT 中心删除时发布。 |
| Microsoft.Devices.DeviceConnected | 当设备连接到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDisconnected | 当设备与 IoT 中心断开连接时发布。 |
| Microsoft.Devices.DeviceTelemetry | 将设备遥测消息发送到 IoT 中心时发布。 |

使用 Azure 门户或 Azure CLI 配置从每个 IoT 中心发布的事件。 例如，请尝试学习教程[使用逻辑应用发送关于 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)。

## <a name="event-schema"></a>事件架构

IoT 中心事件包含响应设备生命周期中更改所需的全部信息。 可通过检查 eventType 属性是否以“Microsoft.Devices”开头来标识 IoT 中心事件。 有关如何使用事件网格事件属性的详细信息，请参阅[事件网格事件架构](../event-grid/event-schema.md)。

### <a name="device-connected-schema"></a>设备已连接架构

以下示例显示了设备已连接事件的架构：

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>设备遥测架构

设备遥测消息必须采用有效的 JSON 格式，并且在消息**系统属性**中将 contentType 设置为 **application/json**，将 contentEncoding 设置为 [UTF-8](iot-hub-devguide-routing-query-syntax.md#system-properties)。 这两个属性都不区分大小写。 如果未设置内容编码，则 IoT 中心将以 base 64 编码格式写入消息。

在通过选择终结点作为事件网格来将设备遥测事件发布到事件网格之前，你可以扩充设备遥测事件。 有关详细信息，请参阅[消息扩充概述](iot-hub-message-enrichments-overview.md)。

以下示例显示了设备遥测事件的架构：

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>设备创建架构

以下示例显示了设备创建事件的架构：

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

有关各属性的详细说明，请参阅 [IoT 中心的 Azure 事件网格事件架构](../event-grid/event-schema-iot-hub.md)。

## <a name="filter-events"></a>筛选事件

IoT 中心事件订阅可以根据事件类型、数据内容和使用者（即设备名称）来筛选事件。

事件网格启用了基于事件类型、使用者和数据内容的[筛选](../event-grid/event-filtering.md)。 创建事件网格订阅时，可以选择订阅所选择的 IoT 事件。 事件网格中的使用者筛选器基于“开头为”（前缀）和“结尾为”（后缀）匹配进行筛选。 该筛选器使用 `AND` 运算符，以便将含有与前缀和后缀都匹配的使用者的事件传送给订阅方。

IoT 事件使用者使用的格式：

```json
devices/{deviceId}
```

事件网格还允许基于每个事件的属性（包括数据内容）进行筛选。 这允许你选择基于遥测消息的内容传送哪些事件。 请参阅[高级筛选](../event-grid/event-filtering.md#advanced-filtering)来查看示例。 若要对遥测消息正文进行筛选，必须在消息**系统属性**中将 contentType 设置为 **application/json**，将 contentEncoding 设置为 [UTF-8](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)。 这两个属性都不区分大小写。

对于非遥测事件，例如 DeviceConnected、DeviceDisconnected、DeviceCreated 和 DeviceDeleted，在创建订阅时，可以使用事件网格筛选。 对于遥测事件，除了在事件网格中进行筛选之外，用户还可以通过消息路由查询基于设备孪生、消息属性和正文进行筛选。 

通过事件网格订阅遥测事件时，IoT 中心会创建一个默认消息路由，用于向事件网格发送数据源类型设备消息。 有关消息路由的详细信息，请参阅[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)。 此路由将在门户中的 "IoT 中心" 下显示 > 消息路由 "。 无论为遥测事件创建了多少个订阅，都只会创建一个到事件网格的路由。 因此，如果您需要多个具有不同筛选器的订阅，可以在同一路由上对这些查询使用 OR 运算符。 通过事件网格通过遥测事件的订阅控制路由的创建和删除。 不能使用 IoT 中心消息路由创建或删除到事件网格的路由。

若要在发送遥测数据之前筛选消息，可以更新你的[路由查询](iot-hub-devguide-routing-query-syntax.md)。 请注意，只有当消息正文为 JSON 时，才能将路由查询应用于消息正文。 还必须在消息**系统属性**中将 contentType 设置为 **application/json**，将 contentEncoding 设置为 [UTF-8](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)。

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>设备已连接和设备已断开连接事件的限制

若要接收设备已连接和设备已断开连接事件，必须为设备打开 D2C 链路或 C2D 链路。 如果设备使用的是 MQTT 协议，IoT 中心将保持 C2D 链路打开。 对于 AMQP，可以通过调用[接收异步 API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) 来打开 C2D 链路。

如果正在发送遥测数据，则 D2C 链路是打开的。 

如果设备连接闪烁（这意味着设备会频繁连接和断开），则不会发送每个连接状态，但会发布*最后一个*连接状态，该状态最终是一致的。 例如，如果设备最初处于连接状态，则连接会闪烁几秒钟，然后恢复到已连接状态。 自初始连接状态后，不会发布新的设备连接状态事件。 

如果 IoT 中心发生服务中断，我们将在服务中断结束后立即发布设备连接状态。 如果设备在服务中断期间断开连接，则设备已断开连接事件将在 10 分钟内发布。

## <a name="tips-for-consuming-events"></a>使用事件的提示

处理 IoT 中心事件的应用程序应遵循以下建议的操作：

* 可以配置多个订阅将事件路由至同一事件处理程序，因此不可假定事件均来自某个特定的源。 始终通过检查消息主题，保证事件来自预期的 IoT 中心。

* 请勿假定所接收的事件均为预期的类型。 在处理消息前，总是先检查 eventType。

* 消息可能不按顺序到达，或者延迟达到。 对于设备已创建或设备已删除事件，请使用 ETag 字段来了解对象的信息是否是最新的。

## <a name="next-steps"></a>后续步骤

* [请尝试学习 IoT 中心事件教程](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [了解如何订阅设备已连接和已断开连接事件](iot-hub-how-to-order-connection-state-events.md)

* [详细了解事件网格](../event-grid/overview.md)

* [比较路由 IoT 中心事件和消息之间的区别](iot-hub-event-grid-routing-comparison.md)

* [了解如何使用 IoT 遥测事件通过 Azure Maps 来实现 IoT 空间分析](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
