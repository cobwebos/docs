---
title: Azure 服务总线 - 暂停消息实体
description: 本文介绍如何暂时暂停和重新激活 Azure 服务总线消息实体（队列、主题和订阅）。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2dad0b774f271ed719ca09b1e749559d5e1868bd
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078848"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暂停（禁用）和重新激活消息实体

可以暂停队列、主题和订阅。 暂停后，实体处于禁用状态，其中所有消息都保留在存储中。 不过，无法删除或添加消息，相应的协议操作也会导致错误出现。

暂停实体通常是出于紧急管理方面的原因。 一种情况是，部署的接收程序发生故障，不仅会将消息从队列中剔除、无法处理消息，甚至还会错误地完成并删除消息。 如果诊断出这样的行为，就会禁用队列的接收行为，直到部署了更正后的代码，并且可以防止故障代码进一步加剧数据丢失问题为止。

暂停或重新激活可以由用户或系统执行。 系统只会出于重大管理原因（如达到订阅支出限制），才会暂停实体。 系统禁用的实体不能被用户重新激活，但在暂停原因消除后就会还原。

在门户中，相应实体的 "**概述**" 部分允许更改状态;当前状态以超链接的形式显示在 "**状态**" 下。

以下屏幕截图显示了可通过选择超链接更改实体的可用状态： 

![概述中的服务总线功能的屏幕截图，用于更改实体状态选项。][1]

门户只容许完全禁用队列。 也可以单独禁用发送和接收操作，方法是在 .NET Framework SDK 中使用服务总线 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API，或通过 Azure CLI 或 Azure PowerShell 使用 Azure 资源管理器模板。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>暂停状态

可以为队列设置如下状态：

-   **Active**：队列处于活动状态。
-   **Disabled**：队列处于暂停状态。
-   **SendDisabled**：队列部分处于暂停状态，允许执行接收操作。
-   **ReceiveDisabled**：队列部分处于暂停状态，允许执行发送操作。

对于订阅和主题，只能设置“Active”**** 和“Disabled”****。

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 枚举还定义了一组只能由系统设置的过渡状态。 下面的示例展示了用于禁用队列的 PowerShell 命令。 重新激活命令等同于以下示例，只需将 `Status` 设置为“Active”**** 即可。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

