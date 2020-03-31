---
title: 使用事件网格响应映射事件 |微软 Azure 地图
description: 在本文中，您将学习如何通过使用事件网格对 Microsoft Azure 映射事件做出反应。
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335717"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>使用事件网格对 Azure Maps 事件做出响应 

Azure 映射与 Azure 事件网格集成，以便用户可以将事件通知发送到其他服务并触发下游进程。 本文的目的是帮助您配置业务应用程序以侦听 Azure 地图事件。 这允许用户以可靠、可扩展和安全的方式对关键事件做出反应。 例如，用户可以构建应用程序以更新数据库、创建票证和在每次设备进入地理围栏时发送电子邮件通知。

Azure 事件网格是一个完全托管的事件路由服务，它使用发布-订阅模型。 事件网格具有对[Azure 函数](https://docs.microsoft.com/azure/azure-functions/functions-overview)和[Azure 逻辑应用](https://docs.microsoft.com/azure/azure-functions/functions-overview)等 Azure 服务的内置支持。 它可以使用 Webhook 向非 Azure 服务提供事件警报。 有关受事件网格支持的事件处理程序的完整列表，请参阅 [Azure 事件网格简介](https://docs.microsoft.com/azure/event-grid/overview)。


![Azure 事件网格功能模型](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps 事件类型

事件网格使用[事件订阅](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)将事件消息路由到订阅方。 Azure Maps 帐户发出以下事件类型： 

| 事件类型 | 描述 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 接收坐标时从给定地理围栏外部移动到内部时引发 |
| Microsoft.Maps.GeofenceExited | 接收坐标从给定的地理围栏内移动到外部时引发 |
| Microsoft.Maps.GeofenceResult | 当地理围栏查询返回结果时引发，不管状态如何。 |

## <a name="event-schema"></a>事件架构

下面的示例显示了 GeofenceResult 的架构：

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>使用事件的提示

处理 Azure Maps 地理围栏事件的应用程序应遵循以下建议的做法：

* 配置多个订阅以将事件路由到同一事件处理程序。 不可假定事件均来自某个特定的源，这很重要。 始终检查消息主题，以确保消息来自您期望的源。
* 使用响应`X-Correlation-id`标头中的字段了解有关对象的信息是否最新。 消息可能不按顺序到达，或者延迟达到。
* 当使用模式参数设置为`EnterAndExit`调用 Geofence API 中的 GET 或 POST 请求时，将针对状态与以前的 Geofence API 调用更改的地理围栏中的每个几何体生成 Enter 或 Exit 事件。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用地理围栏功能来控制在构造站点进行的操作，请参阅：

> [!div class="nextstepaction"] 
> [使用 Azure Maps 设置地域隔离区](tutorial-geofence.md)
