---
title: 服务总线队列和主题作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将服务总线队列和主题用作 Azure 事件网格事件的事件处理程序。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 201d3203d845ce84207d103750709fe2ff93f022
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596316"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>服务总线队列和主题作为 Azure 事件网格事件的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 几个 Azure 服务已自动配置为处理事件，Azure 服务总线是其中之一。 

可以使用服务队列或主题作为事件网格事件的处理程序。 

## <a name="service-bus-queues"></a>服务总线队列
可以将事件网格中的事件直接路由到服务总线队列，用于企业应用程序中的缓冲或命令和控制方案。

在 Azure 门户中，请在创建事件订阅时选择“服务总线队列”作为终结点类型，然后单击“选择终结点”以选择服务总线队列。

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>使用 CLI 添加服务总线队列处理程序

在 Azure CLI 中，以下示例可订阅事件网格主题并将其连接到服务总线队列：

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>服务总线主题

可以将事件网格中的事件直接路由到服务总线主题，以使用服务总线主题处理 Azure 系统事件，或者将其用于命令和控制消息方案。

在 Azure 门户中，请在创建事件订阅时选择“服务总线主题”作为终结点类型，然后单击“选择终结点”以选择服务总线主题。

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>使用 CLI 添加服务总线主题处理程序

在 Azure CLI 中，以下示例可订阅事件网格主题并将其连接到服务总线队列：

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>消息属性
如果使用服务总线主题或队列作为事件网格事件的事件处理程序，请设置以下消息标头： 

| 属性名称 | 说明 |
| ------------- | ----------- | 
| aeg-subscription-name | 事件订阅的名称。 |
| aeg-delivery-count | <p>针对该事件进行尝试的次数。</p> <p>示例：“1”</p> |
| aeg-event-type | <p>事件的类型。</p><p> 示例："Microsoft.Storage.blobCreated"</p> | 
| aeg-metadata-version | <p>事件的元数据版本。</p> <p>示例："1"。</p><p> 对于“事件网格事件架构”，此属性表示元数据版本；对于“云事件架构”，此属性表示规范版本。 </p>|
| aeg-data-version | <p>事件的数据版本。</p><p>示例："1"。</p><p>对于“事件网格事件架构”，此属性表示数据版本；对于“云事件架构”，此属性不适用。</p> |

## <a name="message-headers"></a>消息标头
将事件作为中转消息发送到服务总线队列或主题时，中转消息的 `messageid` 是事件 ID。

事件 ID 会在事件重新传递期间保留，这样就可以在服务总线实体上启用“重复项检测”，避免重复传递。 建议在服务总线实体上启用重复项检测持续时间，并将其设为事件的生存时间 (TTL) 或最大重试持续时间（以较长者为准）。

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
