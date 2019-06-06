---
title: Azure IoT 中心和事件网格 | Microsoft Docs
description: 使用 Azure 事件网格根据 IoT 中心发生的操作来触发流程。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: f08845dbf4168627d0198e81d2092a1fe56c6c89
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733866"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>通过使用事件网格触发操作来响应 IoT 中心事件

通过将 Azure IoT 中心与 Azure 事件网格进行集成，使你可以向其他服务发送事件通知，并触发下游流程。 配置商业应用程序来侦听 IoT 中心事件，以便安全可靠地以可缩放方式响应关键事件。 例如，生成一个应用程序来更新数据库、创建工作票证，并在每当有新的 IoT 设备注册到 IoT 中心时，发送一封电子邮件通知。

[Azure 事件网格](../event-grid/overview.md)是一种完全托管的事件路由服务，使用发布-订阅模型。 事件网格包含对 Azure 服务（如 [Azure Functions](../azure-functions/functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)）的内置支持，还可使用 Webhook 向非 Azure 服务传递事件警报。 有关受事件网格支持的事件处理程序的完整列表，请参阅 [Azure 事件网格简介](../event-grid/overview.md)。

![Azure 事件网格体系结构](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>区域可用性

事件网格集成适用于支持事件网格的区域中的 IoT 中心。 设备遥测事件的所有设备事件都已公开上市。 设备遥测事件处于公共预览状态，在除美国东部、 美国西部、 欧洲西部的所有区域均提供[Azure 政府版](/azure/azure-government/documentation-government-welcome)， [Azure 中国 21Vianet](/azure/china/china-welcome)，和[Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/). 有关区域的最新列表，请参阅 [Azure 事件网格简介](../event-grid/overview.md)。

## <a name="event-types"></a>事件类型

IoT 中心将发布以下事件类型：

| 事件类型 | 描述 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 当设备注册到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDeleted | 当设备从 IoT 中心删除时发布。 |
| Microsoft.Devices.DeviceConnected | 当设备连接到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDisconnected | 当设备与 IoT 中心断开连接时发布。 |
| Microsoft.Devices.DeviceTelemetry | 设备遥测消息发送到 IoT 中心时发布 |

使用 Azure 门户或 Azure CLI 配置从每个 IoT 中心发布的事件。 例如，请尝试学习教程[使用逻辑应用发送关于 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)。

## <a name="event-schema"></a>事件架构

IoT 中心事件包含响应设备生命周期中更改所需的全部信息。 可通过检查 eventType 属性是否以“Microsoft.Devices”开头来标识 IoT 中心事件  。 有关如何使用事件网格事件属性的详细信息，请参阅[事件网格事件架构](../event-grid/event-schema.md)。

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

### <a name="device-telemetry-schema"></a>设备遥测数据架构

设备遥测消息必须采用有效的 JSON 格式与 contentType 设置为 JSON 和 contentEncoding 消息中设置为 utf-8[系统属性](iot-hub-devguide-routing-query-syntax.md#system-properties)。 如果未设置，IoT 中心将在基的 64 编码格式中写入消息。

您可以丰富设备遥测事件发布到事件网格为事件网格中选择该终结点之前。 有关详细信息，请参阅[消息正概述](iot-hub-message-enrichments-overview.md)。

下面的示例显示了设备遥测事件的架构：

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

IoT 中心事件订阅可筛选根据事件类型、 数据内容和使用者，这是设备名称的事件。

事件网格可使[筛选](../event-grid/event-filtering.md)事件类型、 主题和数据内容。 在创建事件网格订阅，你可以选择所选 IoT 事件订阅。 事件网格中的使用者筛选器基于“开头为”（前缀）和“结尾为”（后缀）匹配进行筛选   。 该筛选器使用 `AND` 运算符，以便将含有与前缀和后缀都匹配的使用者的事件传送给订阅方。

IoT 事件使用者使用的格式：

```json
devices/{deviceId}
```

事件网格还允许每个事件，包括数据内容的属性进行筛选。 这样，您可以选择哪些事件传送的遥测消息基于的内容。 请参阅[高级筛选](../event-grid/event-filtering.md#advanced-filtering)若要查看示例。

对于非遥测事件，例如 DeviceConnected、 DeviceDisconnected、 DeviceCreated 和 DeviceDeleted，事件网格筛选可以使用创建订阅时。 遥测事件，还可以筛选在事件网格中，还可以在设备孪生、 消息属性和正文通过消息路由查询筛选用户。 我们将创建默认值[路由](iot-hub-devguide-messages-d2c.md)在 IoT 中心，根据事件网格订阅到设备遥测数据。 此单个路由可以处理所有事件网格订阅。 若要发送的遥测数据之前筛选消息，可以更新你[路由查询](iot-hub-devguide-routing-query-syntax.md)。 请注意该路由查询可应用于消息正文，仅当正文是 JSON。

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>设备已连接和设备已断开连接事件的限制

若要接收设备已连接和设备已断开连接事件，必须为设备打开 D2C 链路或 C2D 链路。 如果设备使用的是 MQTT 协议，IoT 中心将保持 C2D 链路打开。 对于 AMQP，可以通过调用[接收异步 API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) 来打开 C2D 链路。

如果正在发送遥测数据，则 D2C 链路是打开的。 闪烁的设备连接，如果设备连接和断开连接通常情况下，这意味着我们将不会发送每个单个连接状态，但将发布的快照每隔一分钟的连接状态。 如果 IoT 中心发生服务中断，我们将在服务中断结束后立即发布设备连接状态。 如果设备在服务中断期间断开连接，则设备已断开连接事件将在 10 分钟内发布。

## <a name="tips-for-consuming-events"></a>使用事件的提示

处理 IoT 中心事件的应用程序应遵循以下建议的操作：

* 可以配置多个订阅将事件路由至同一事件处理程序，因此不可假定事件均来自某个特定的源。 始终通过检查消息主题，保证事件来自预期的 IoT 中心。

* 请勿假定所接收的事件均为预期的类型。 在处理消息前，总是先检查 eventType。

* 消息可能不按顺序到达，或者延迟达到。 使用 etag 字段来了解对象的相关信息是最新的设备创建或删除的设备事件。

## <a name="next-steps"></a>后续步骤

* [请尝试学习 IoT 中心事件教程](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [了解如何订阅设备已连接和已断开连接事件](iot-hub-how-to-order-connection-state-events.md)

* [详细了解事件网格](../event-grid/overview.md)

* [比较路由 IoT 中心事件和消息之间的区别](iot-hub-event-grid-routing-comparison.md)
