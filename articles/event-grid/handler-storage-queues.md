---
title: 存储队列作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将 Azure 存储队列用作 Azure 事件网格事件的事件处理程序。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596266"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>存储队列作为 Azure 事件网格事件的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 多个 Azure 服务已自动配置为处理事件，Azure 队列存储就是其中之一。 

使用队列存储接收需拉取的事件。 如果正在运行的进程需要很长时间才能响应，可能会使用队列存储。 通过向队列存储发送事件，应用程序可以按照自己的计划拉取和处理事件。

## <a name="tutorials"></a>教程
有关使用队列存储作为事件处理程序的示例，请参阅以下教程。 

|标题  |说明  |
|---------|---------|
| [快速入门：使用 Azure CLI 和事件网格将自定义事件路由到 Azure 队列存储](custom-event-to-queue-storage.md) | 说明如何将自定义事件发送到队列存储。 |

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
