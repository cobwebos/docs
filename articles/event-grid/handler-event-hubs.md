---
title: 事件中心作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将时间中心用作 Azure 事件网格事件的事件处理程序。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9ce4e7da51005dcb06c9df420d80f4d2c7b93e9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596016"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>事件中心作为 Azure 事件网格事件的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行操作来处理事件。 几个 Azure 服务已自动配置为处理事件，Azure 事件中心是其中之一。 

如果解决方案从事件网格获取事件的速度快于处理事件的速度，请使用事件中心。 事件位于事件中心后，应用程序可以按自己的计划处理事件中心的事件。 可以通过缩放事件处理来处理传入的事件。

## <a name="tutorials"></a>教程
请看以下示例： 

|标题  |说明  |
|---------|---------|
| [快速入门：使用 Azure CLI 将自定义事件路由到 Azure 事件中心](custom-event-to-eventhub.md) | 将自定义事件发送到事件中心以供应用程序处理。 |
| [资源管理器模板：创建事件网格自定义主题，并将事件发送到事件中心](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 用于创建自定义主题的订阅的资源管理器模板。 它将事件发送到 Azure 事件中心。 |

## <a name="message-properties"></a>消息属性
如果使用事件中心作为事件网格事件的事件处理程序，请设置以下消息标头： 

| 属性名称 | 说明 |
| ------------- | ----------- | 
| aeg-subscription-name | 事件订阅的名称。 |
| aeg-delivery-count | <p>针对该事件进行尝试的次数。</p> <p>示例：“1”</p> |
| aeg-event-type | <p>事件的类型。</p><p> 示例：“Microsoft.Storage.blobCreated”</p> | 
| aeg-metadata-version | <p>事件的元数据版本。</p> <p>示例：“1”。</p><p> 对于“事件网格事件架构”，此属性表示元数据版本；对于“云事件架构”，此属性表示规范版本。 </p>|
| aeg-data-version | <p>事件的数据版本。</p><p>示例：“1”。</p><p>对于“事件网格事件架构”，此属性表示数据版本；对于“云事件架构”，此属性不适用。</p> |
| aeg-output-event-id | 事件网格事件的 ID。 |


## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
