---
title: 对 Azure 应用程序配置键值对事件做出反应 |Microsoft Docs
description: 使用 Azure 事件网格订阅应用配置事件。
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735641"
---
# <a name="reacting-to-azure-app-configuration-events"></a>对 Azure 应用程序配置事件做出反应

Azure 应用程序配置事件使应用程序的密钥值中的变化做出反应。 这是无需复杂的代码或昂贵低效的轮询服务。 相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向订阅者（如 [Azure Functions](https://azure.microsoft.com/services/functions/) 或 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)，甚至是你的自定义 HTTP 侦听器）推送事件，且仅需为已使用的内容付费。

Azure 应用程序配置事件发送到 Azure 事件网格提供可靠的传递服务添加到您通过丰富的应用程序重试策略和死信传递。 若要了解详细信息，请参阅[事件网格消息传送和重试](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

常见的应用配置事件方案包括刷新应用程序配置，触发部署或任何面向配置的工作流。 基于事件的体系结构对于鲜少更改，但要求立即响应的情况尤为有效。

看一看[将事件路由 Azure 应用配置到自定义 web 终结点-CLI](./howto-app-configuration-event.md)快速示例。 

![事件网格模型](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>可用的 Azure 应用程序配置事件
事件网格使用[事件订阅](../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Azure 应用程序配置事件订阅包括两种类型的事件：  

> |事件名称|描述|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|当创建或替换密钥值时触发|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|删除键-值时触发|

## <a name="event-schema"></a>事件架构
Azure 应用程序配置事件包含响应你的数据中的更改所需的所有信息。 因为 eventType 属性以"Microsoft.AppConfiguration"开头，可以标识应用程序配置事件。 关于事件网格事件属性使用情况的其他信息，请参阅文档[事件网格事件架构](../event-grid/event-schema.md)。  

> |属性|Type|描述|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |主题|字符串|发出该事件的应用配置的完整 Azure 资源管理器 id。|
> |subject|string|事件的使用者密钥值的 URI。|
> |EventTime|字符串|生成事件，将日期/时间以 ISO 8601 格式。|
> |eventType|字符串|"Microsoft.AppConfiguration.KeyValueModified"或者"Microsoft.AppConfiguration.KeyValueDeleted"。|
> |Id|字符串|此事件的唯一标识符。|
> |dataVersion|字符串|数据对象的架构版本。|
> |metadataVersion|字符串|顶级属性的架构版本。|
> |数据|对象|Azure 应用程序配置特定的事件数据的集合|
> |data.key|字符串|已修改或删除的密钥值的键。|
> |data.label|字符串|该标签，如果任何已修改或删除的密钥值。|
> |data.etag|字符串|有关`KeyValueModified`新密钥值的 etag。 有关`KeyValueDeleted`已删除的密钥值的 etag。|

下面是 KeyValueModified 事件的一个示例：
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

有关详细信息，请参阅[Azure 应用程序配置的事件架构](../event-grid/event-schema-app-configuration.md)。

## <a name="practices-for-consuming-events"></a>使用事件的做法
处理应用程序配置事件的应用程序应遵循以下建议的做法：
> [!div class="checklist"]
> * 因为可以将多个订阅配置为将事件路由至同一事件处理程序，务必不要假定事件来自特定源，而是检查以确保它来自在需要的应用程序配置该消息的主题。
> * 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
> * 消息在一段延迟时间后会无序到达，请使用 etag 字段来了解对象的相关信息是否是最新的。  此外，还可使用 sequencer 字段来了解任何特定对象的事件顺序。
> * 使用主题字段来访问密钥值已修改。


## <a name="next-steps"></a>后续步骤

了解有关事件网格的详细信息，并尝试一下 Azure 应用程序配置事件：

- [关于事件网格](../event-grid/overview.md)
- [将 Azure 应用程序配置事件路由到自定义 web 终结点](./howto-app-configuration-event.md)