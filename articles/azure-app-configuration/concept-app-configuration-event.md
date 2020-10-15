---
title: 响应 Azure 应用配置键值事件
description: 使用 Azure 事件网格订阅应用程序配置事件，使应用程序能够对键值中的更改做出反应，而不需要复杂的代码。
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 640be797b2653f9e6c969306b7e2b99393b99c39
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078198"
---
# <a name="reacting-to-azure-app-configuration-events"></a>响应 Azure 应用配置事件

通过 Azure 应用配置事件，应用程序能够响应键值更改。 实现这一点不需要复杂的代码或成本高昂但效率低下的轮询服务， 而是可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向订阅方（如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)，或者甚至是你自己的自定义 HTTP 侦听器）推送事件。 至关重要的是，你只为你使用的资源付费。

Azure 应用配置事件会发送到 Azure 事件网格，该服务通过丰富的重试策略和死信传递向应用程序提供可靠的传递服务。 若要了解详细信息，请参阅[事件网格消息传递和重试](../event-grid/delivery-and-retry.md)。

常见的应用配置事件方案包括刷新应用程序配置、触发部署或任何面向配置的工作流。 基于事件的体系结构对于鲜少更改，但要求立即响应的情况尤为有效。

请参阅[将 Azure 应用配置事件路由到自定义 Web 终结点 - CLI](./howto-app-configuration-event.md)，以获取简明示例。 

![事件网格模型](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>可用的 Azure 应用配置事件
事件网格使用[事件订阅](../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Azure 应用配置事件订阅可以包括两种类型的事件：  

> |事件名称|描述|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|在创建或替换键值时触发|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|在删除键值时触发|

## <a name="event-schema"></a>事件架构
Azure 应用配置事件包含响应数据更改所需的全部信息。 你可以识别应用配置事件，因为 eventType 属性以“Microsoft.AppConfiguration”开头。 关于事件网格事件属性使用情况的其他信息，请参阅文档[事件网格事件架构](../event-grid/event-schema.md)。  

> |属性|类型|说明|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |主题|字符串|发出该事件的应用配置的完整 Azure 资源管理器 ID。|
> |subject|字符串|作为事件主题的键值的 URI。|
> |EventTime|字符串|生成事件的日期/时间，采用 ISO 8601 格式。|
> |eventType|字符串|“Microsoft.AppConfiguration.KeyValueModified”或“Microsoft.AppConfiguration.KeyValueDeleted”。|
> |ID|字符串|此事件的唯一标识符。|
> |dataVersion|string|数据对象的架构版本。|
> |metadataVersion|字符串|顶级属性的架构版本。|
> |data|对象 (object)|Azure 应用配置特定事件数据的集合|
> |data.key|字符串|已修改或已删除的键/值的键。|
> |data.label|字符串|已修改或已删除的键/值的标签（如果有）。|
> |data.etag|字符串|对于 `KeyValueModified`，为新键/值的 etag。 对于 `KeyValueDeleted`，为已删除的键/值的 etag。|

下面是 KeyValueModified 事件的示例：
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

有关详细信息，请参阅 [Azure 应用配置事件架构](../event-grid/event-schema-app-configuration.md)。

## <a name="practices-for-consuming-events"></a>使用事件的做法
处理应用配置事件的应用程序应当遵循以下建议的做法：
> [!div class="checklist"]
> * 可以配置多个订阅，以将事件路由到同一事件处理程序，因此，请勿假定事件来自某个特定源， 而应检查消息的主题，以确保应用配置实例发送该事件。
> * 检查 eventType，并且不要假定接收的所有事件都会是期望的类型。
> * 使用 etag 字段以了解有关对象的信息是否仍是最新的。  
> * 使用 sequencer 字段以了解任意特定对象上事件的顺序。
> * 使用 subject 字段以访问修改后的键值。


## <a name="next-steps"></a>后续步骤

详细了解事件网格并试用 Azure 应用配置事件：

- [关于事件网格](../event-grid/overview.md)
- [将 Azure 应用配置事件路由到自定义 Web 终结点](./howto-app-configuration-event.md)