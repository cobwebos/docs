---
title: "Azure 事件网格资源组事件架构"
description: "介绍针对 Azure 事件网格中的资源组事件提供的属性"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: c7435d87f9aaa906c3f6758186b64f3458cb9716
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>资源组的 Azure 事件网格事件架构

本文提供资源组事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

Azure 订阅和资源组发出相同的事件类型。 这些事件类型与资源中的更改相关。 主要区别是资源组针对资源组中的资源发出事件，Azure 订阅针对跨订阅的资源发出事件。 

## <a name="available-event-types"></a>可用事件类型

资源组可从 Azure 资源管理器发出管理事件，例如，在创建 VM 或删除存储帐户时。

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | 当资源创建或更新操作成功时引发。 |
| Microsoft.Resources.ResourceWriteFailure | 当资源创建或更新操作失败时引发。 |
| Microsoft.Resources.ResourceWriteCancel | 当资源创建或更新操作取消时引发。 |
| Microsoft.Resources.ResourceDeleteSuccess | 当资源删除操作成功时引发。 |
| Microsoft.Resources.ResourceDeleteFailure | 当资源删除操作失败时引发。 |
| Microsoft.Resources.ResourceDeleteCancel | 当资源删除操作取消时引发。 取消模板部署时会发生此事件。 |

## <a name="example-event"></a>示例事件

以下示例显示了资源创建事件的架构： 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

资源删除事件的架构与此类似：

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | 对象 | 资源组事件数据。 |

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| authorization | 字符串 | 操作请求的授权。 |
| 声明 | 字符串 | 声明的属性。 |
| correlationId | 字符串 | 用于故障排除的操作 ID。 |
| httpRequest | 字符串 | 操作的详细信息。 |
| resourceProvider | 字符串 | 执行该操作的资源提供程序。 |
| resourceUri | 字符串 | 操作中资源的 URI。 |
| operationName | 字符串 | 执行的操作。 |
| status | 字符串 | 操作状态。 |
| subscriptionId | 字符串 | 资源的订阅 ID。 |
| tenantId | 字符串 | 资源的租户 ID。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
