---
title: Azure 服务总线暂停消息实体 | Microsoft Docs
description: 暂停和重新激活 Azure 服务总线消息实体。
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 1984b113f695107f8d4d80e5bbf25c7dc39d13f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197020"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暂停（禁用）和重新激活消息实体

可以暂停队列、主题和订阅。 暂停后，实体处于禁用状态，其中所有消息都保留在存储中。 不过，无法删除或添加消息，相应的协议操作也会导致错误出现。

暂停实体通常是出于紧急管理方面的原因。 一种情况是，部署的接收程序发生故障，不仅会将消息从队列中剔除、无法处理消息，甚至还会错误地完成并删除消息。 如果诊断出这样的行为，就会禁用队列的接收行为，直到部署了更正后的代码，并且可以防止故障代码进一步加剧数据丢失问题为止。

暂停或重新激活可以由用户或系统执行。 系统只会出于重大管理原因（如达到订阅支出限制），才会暂停实体。 系统禁用的实体不能被用户重新激活，但在暂停原因消除后就会还原。

在门户中各个实体的“属性”部分内，可以更改状态；下面的屏幕截图展示了队列状态的切换开关：

![][1]

门户只容许完全禁用队列。 也可以单独禁用发送和接收操作，方法是在 .NET Framework SDK 中使用服务总线 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API，或通过 Azure CLI 或 Azure PowerShell 使用 Azure 资源管理器模板。

## <a name="suspension-states"></a>暂停状态

可以为队列设置如下状态：

-   **Active**：队列处于活动状态。
-   **Disabled**：队列处于暂停状态。
-   **SendDisabled**：队列部分处于暂停状态，允许执行接收操作。
-   **ReceiveDisabled**：队列部分处于暂停状态，允许执行发送操作。

对于订阅和主题，只能设置“Active”和“Disabled”。

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 枚举还定义了一组只能由系统设置的过渡状态。 下面的示例展示了用于禁用队列的 PowerShell 命令。 重新激活命令等同于以下示例，只需将 `Status` 设置为“Active”即可。

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

