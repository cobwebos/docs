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
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284546"
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
| Microsoft.Devices.DeviceTelemetry | 在将设备遥测消息发送到 IoT 中心时发布 |

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

设备遥测消息必须是有效的 JSON 格式，其中 contentType 设置为**application/JSON** ，而 contentEncoding 设置为消息[系统属性](iot-hub-devguide-routing-query-syntax.md#system-properties)中的**utf-8** 。 这两个属性都不区分大小写。 如果未设置内容编码，则 IoT 中心将以基本64编码格式编写消息。

通过选择终结点作为事件网格，可以在将设备遥测事件发布到事件网格之前对其进行扩充。 有关详细信息，请参阅[Message 根据概述](iot-hub-message-enrichments-overview.md)。

下面的示例演示设备遥测事件的架构：

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

有关每个属性的详细说明，请参阅[IoT 中心的 Azure 事件网格事件架构](../event-grid/event-schema-iot-hub.md)。

## <a name="filter-events"></a>筛选事件

IoT 中心事件订阅可以根据事件类型、数据内容和使用者（设备名称）来筛选事件。

事件网格可对事件类型、主题和数据内容进行[筛选](../event-grid/event-filtering.md)。 创建事件网格订阅时，可以选择订阅所选 IoT 事件。 事件网格中的使用者筛选器基于“开头为”（前缀）和“结尾为”（后缀）匹配进行筛选。 该筛选器使用 `AND` 运算符，以便将含有与前缀和后缀都匹配的使用者的事件传送给订阅方。

IoT 事件使用者使用的格式：

```json
devices/{deviceId}
```

事件网格还允许筛选每个事件的属性，包括数据内容。 这允许您选择基于遥测消息内容传递的事件。 请参阅[高级筛选](../event-grid/event-filtering.md#advanced-filtering)以查看示例。 对于遥测消息正文的筛选，必须在消息[系统属性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中将 contentType 设置为**application/Json** ，将 contentEncoding 设置为**utf-8** 。 这两个属性都不区分大小写。

对于非遥测事件（如 DeviceConnected、DeviceDisconnected、DeviceCreated 和 DeviceDeleted），可以在创建订阅时使用事件网格筛选。 对于遥测事件，除了在事件网格中筛选以外，用户还可以通过消息路由查询来筛选设备孪生、消息属性和正文。 

通过事件网格订阅遥测事件时，IoT 中心会创建一个默认消息路由，用于向事件网格发送数据源类型设备消息。 有关消息路由的详细信息，请参阅[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)。 此路由将在门户中的 "IoT 中心" 下显示 > 消息路由 "。 无论为遥测事件创建了多少个订阅，都只会创建一个到事件网格的路由。 因此，如果您需要多个具有不同筛选器的订阅，可以在同一路由上对这些查询使用 OR 运算符。 通过事件网格通过遥测事件的订阅控制路由的创建和删除。 不能使用 IoT 中心消息路由创建或删除到事件网格的路由。

若要在发送遥测数据之前筛选消息，可以更新[路由查询](iot-hub-devguide-routing-query-syntax.md)。 请注意，仅当正文为 JSON 时，路由查询才能应用于消息正文。 还必须在消息[系统属性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中将 contentType 设置为**application/json** ，并将 contentEncoding 设置为**utf-8** 。

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>设备已连接和设备已断开连接事件的限制

若要接收设备连接状态事件，设备必须使用 Iot 中心执行 "对 ' 发送遥测数据" 或 "C2D 接收消息" 操作。 但请注意，如果设备使用 AMQP 协议连接到 Iot 中心，则建议它们执行 "C2D 接收消息" 操作，否则其连接状态通知可能会延迟几分钟。 如果设备使用的是 MQTT 协议，IoT 中心将保持 C2D 链路打开。 对于 AMQP，可以通过调用用于 IoT 中心C# SDK 的 "[接收异步 API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)" 或 "用于 AMQP 的[设备客户端](iot-hub-amqp-support.md#device-client)" 打开 "C2D" 链接。

如果正在发送遥测数据，则 D2C 链路是打开的。 

如果设备连接闪烁，这意味着设备会频繁地进行连接和断开连接，则不会发送每个连接状态，但会在定期快照上发布所创建的当前连接状态，直到闪烁继续。 接收具有不同序列号或不同连接状态事件的相同连接状态事件均意味着设备连接状态发生变化。

## <a name="tips-for-consuming-events"></a>使用事件的提示

处理 IoT 中心事件的应用程序应遵循以下建议的操作：

* 可以将多个订阅配置为将事件路由到相同的事件处理程序，因此不会假设事件来自特定的源。 始终通过检查消息主题，保证事件来自预期的 IoT 中心。

* 请勿假定所接收的事件均为预期的类型。 在处理消息前，总是先检查 eventType。

* 消息可能不按顺序到达，或者延迟达到。 使用 etag 字段来了解有关对象的信息对于设备创建或设备删除事件是否是最新的。

## <a name="next-steps"></a>后续步骤

* [请尝试学习 IoT 中心事件教程](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [了解如何订阅设备已连接和已断开连接事件](iot-hub-how-to-order-connection-state-events.md)

* [详细了解事件网格](../event-grid/overview.md)

* [比较路由 IoT 中心事件和消息之间的区别](iot-hub-event-grid-routing-comparison.md)

* [了解如何使用 IoT 遥测事件通过 Azure Maps 来实现 IoT 空间分析](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
