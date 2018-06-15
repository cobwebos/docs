---
title: Azure IoT 中心和事件网格 | Microsoft Docs
description: 使用 Azure 事件网格根据 IoT 中心发生的操作来触发流程。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: f187aa81ca519f2597657f01c2d7a630740b5348
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634305"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>通过使用事件网格触发操作来响应 IoT 中心事件 - 预览版

通过将 Azure IoT 中心与 Azure 事件网格进行集成，使你可以向其他服务发送事件通知，并触发下游流程。 配置商业应用程序来侦听 IoT 中心事件，以便安全可靠地以可缩放方式响应关键事件。 例如生成应用程序以执行各种操作，如更新数据库、创建票证等，并在每当有新的 IoT 设备注册到 IoT 中心时，发送一封电子邮件通知。 

[Azure 事件网格][lnk-eg-overview]是一种完全托管的事件路由服务，使用发布-订阅模型。 事件网格包含对 Azure 服务（如 [Azure Functions](../azure-functions/functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)）的内置支持，还可使用 Webhook 向非 Azure 服务传递事件警报。 有关受事件网格支持的事件处理程序的完整列表，请参阅 [Azure 事件网格简介][lnk-eg-overview]。 

![Azure 事件网格体系结构](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>区域可用性

事件网格集成适用于支持事件网格的区域中的 IoT 中心。 有关区域的最新列表，请参阅 [Azure 事件网格简介][lnk-eg-overview]。 

## <a name="event-types"></a>事件类型

IoT 中心将发布以下事件类型： 

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 当设备注册到 IoT 中心时发布。 |
| Microsoft.Devices.DeviceDeleted | 当设备从 IoT 中心删除时发布。 | 

使用 Azure 门户或 Azure CLI 配置从每个 IoT 中心发布的事件。 例如，请尝试学习教程[使用逻辑应用发送关于 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)。 

## <a name="event-schema"></a>事件架构

IoT 中心事件包含响应设备生命周期中更改所需的全部信息。 可通过检查 eventType 属性是否以“Microsoft.Devices”开头来标识 IoT 中心事件。 有关如何使用事件网格事件属性的详细信息，请参阅[事件网格事件架构](../event-grid/event-schema.md)。

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

有关各属性的详细说明，请参阅 [IoT 中心的 Azure 事件网格事件架构](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>筛选事件

IoT 中心事件订阅可根据事件类型和设备名筛选事件。 事件网格中的使用者筛选器基于“前缀”和“后缀”的匹配进行筛选。 该筛选器使用 `AND` 运算符，以便将含有与前缀和后缀都匹配的使用者的事件传送给订阅方。 

IoT 事件使用者使用的格式：

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>使用事件的提示

处理 IoT 中心事件的应用程序应遵循以下建议的操作：

* 可以配置多个订阅将事件路由至同一事件处理程序，因此不可假定事件均来自某个特定的源。 始终通过检查消息主题，保证事件来自预期的 IoT 中心。 
* 请勿假定所接收的事件均为预期的类型。 在处理消息前，总是先检查 eventType。
* 消息可能不按顺序到达，或者延迟达到。 使用 ETag 字段来了解对象的信息是否是最新的。



## <a name="next-steps"></a>后续步骤

* [请尝试学习 IoT 中心事件教程](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [了解事件网格的详细信息][lnk-eg-overview]
* [比较路由 IoT 中心事件和消息之间的区别][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md