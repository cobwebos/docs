---
title: 使用事件网格对 Azure Maps 事件做出响应
description: 了解如何应对涉及现成的 Azure Maps 事件。 请参阅如何侦听映射事件和如何使用事件网格将事件重新路由到事件处理程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 701c94237ef5348e11b5d7fbc85d4da1f20136ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88036804"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>使用事件网格对 Azure Maps 事件做出响应

Azure Maps 与 Azure 事件网格集成，以便用户可以将事件通知发送到其他服务并触发下游进程。 本文旨在帮助你将业务应用程序配置为侦听 Azure Maps 事件。 这允许用户以可靠、可缩放且安全的方式对关键事件做出反应。 例如，每次设备进入地域隔离区内时，用户都可以构建一个应用程序来更新数据库、创建票证并发送电子邮件通知。

Azure 事件网格是一种完全托管的事件路由服务，它使用发布-订阅模型。 事件网格提供对 Azure 服务（如 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 和 [Azure 逻辑应用](https://docs.microsoft.com/azure/azure-functions/functions-overview)）的内置支持。 它可以使用 Webhook 将事件警报传递到非 Azure 服务。 有关受事件网格支持的事件处理程序的完整列表，请参阅 [Azure 事件网格简介](https://docs.microsoft.com/azure/event-grid/overview)。


![Azure 事件网格功能模型](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps 事件类型

事件网格使用[事件订阅](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)将事件消息路由到订阅方。 Azure Maps 帐户发出以下事件类型： 

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 当接收的坐标从给定地域隔离区内的外部移动到内部时引发 |
| Microsoft.Maps.GeofenceExited | 当接收的坐标从给定的地域隔离区内中移到外部时引发 |
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

* 将多个订阅配置为将事件路由到同一事件处理程序。 不可假定事件均来自某个特定的源，这很重要。 请始终检查消息主题，以确保消息来自于所需的源。
* 使用 `X-Correlation-id` 响应标头中的字段来了解有关对象的信息是否是最新的。 消息可能不按顺序到达，或者延迟达到。
* 当在将 mode 参数设置为的情况下调用地域隔离区内 API 中的 GET 或 POST 请求时，将为 `EnterAndExit` 地域隔离区内中的每个几何图形（其状态已从以前的地域隔离区内 API 调用更改）生成一个 Enter 或退出事件。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用地理围栏功能来控制在构造站点进行的操作，请参阅：

> [!div class="nextstepaction"] 
> [使用 Azure Maps 设置地域隔离区](tutorial-geofence.md)
