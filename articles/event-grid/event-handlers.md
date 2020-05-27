---
title: Azure 事件网格事件处理程序
description: 介绍了 Azure 事件网格支持的事件处理程序。 Azure 自动化, Functions, 事件中心, 混合连接, 逻辑应用, 服务总线, 队列存储, Webhook。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592451"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure 事件网格中的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 会自动将多个 Azure 服务配置为处理事件。 也可以使用任意 Webhook 来处理事件。 Webhook 不需要托管在 Azure 中来处理事件。 事件网格仅支持 HTTPS Webhook 终结点。

## <a name="supported-event-handlers"></a>支持的事件处理程序
下面是支持的事件处理程序： 

- [WebHook](handler-webhooks.md)。 通过 Webhook 支持 Azure 自动化 runbook 和逻辑应用。 
- [Azure Functions](handler-functions.md)
- [事件中心](handler-event-hubs.md)
- [中继混合连接](handler-relay-hybrid-connections.md)
- [服务总线队列和主题](handler-service-bus.md)
- [存储队列](handler-storage-queues.md)

## <a name="next-steps"></a>后续步骤
- 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
