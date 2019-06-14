---
title: 使用事件网格对 Azure Maps 事件做出响应 | Microsoft Docs
description: 了解如何使用事件网格对 Azure Maps 事件做出响应。
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799187"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>使用事件网格对 Azure Maps 事件做出响应 

通过将 Azure Maps 与 Azure 事件网格进行集成，使你可以向其他服务发送事件通知，并触发下游流程。 本文的目的是帮助你配置商业应用程序来侦听 Azure Maps 事件，以便安全可靠地以可缩放方式响应关键事件。 例如生成应用程序以执行多种操作，如更新数据库、创建票证等，并在每当有设备进入地理围栏时，发送一封电子邮件通知。

Azure 事件网格是一种完全托管的事件路由服务，使用发布-订阅模型。 事件网格包含对 Azure 服务（如 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 和 [Azure 逻辑应用](https://docs.microsoft.com/azure/azure-functions/functions-overview)）的内置支持，还可使用 Webhook 向非 Azure 服务传递事件警报。 有关受事件网格支持的事件处理程序的完整列表，请参阅 [Azure 事件网格简介](https://docs.microsoft.com/azure/event-grid/overview)。


![Azure 事件网格功能模型](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps 事件类型

事件网格使用[事件订阅](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)将事件消息路由到订阅方。 Azure Maps 帐户发出以下事件类型： 

| 事件类型 | 描述 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 当接收的坐标从给定地理围栏的外部进入内部时引发 |
| Microsoft.Maps.GeofenceExited | 当接收的坐标从给定地理围栏的内部移到外部时引发 |
| Microsoft.Maps.GeofenceResult | 当地理围栏查询返回结果时引发，不管状态如何。 |

## <a name="event-schema"></a>事件架构

以下示例显示 GeofenceResult 的架构

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

* 可以配置多个订阅，将事件路由至同一事件处理程序。 不可假定事件均来自某个特定的源，这很重要。 始终通过检查消息主题，保证事件来自预期的源。
* 消息可能不按顺序到达，或者延迟达到。 在响应标头中使用 `X-Correlation-id` 字段来了解对象的信息是否是最新的。
* 在调用 Get 和 POST 地理围栏 API 时，如果将 mode 参数设置为 `EnterAndExit`，则会为地理围栏中其状态不同于以前的地理围栏 API 调用的每个几何图形生成“进入”或“退出”事件。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用地理围栏功能来控制在构造站点进行的操作，请参阅：

> [!div class="nextstepaction"] 
> [使用 Azure Maps 设置地理围栏](tutorial-geofence.md)