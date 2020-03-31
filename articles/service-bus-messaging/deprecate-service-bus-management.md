---
title: Azure 消息传递服务 - 资源管理器的服务管理器
description: 本文提供已弃用的 Azure 服务管理器 REST API & PowerShell cmdlet 映射到资源管理器 REST API & PowerShell cmdlet。
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589904"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>弃用 Azure 服务总线、中继和事件中心 Azure 服务管理器支持

资源管理器，我们的下一代云基础架构堆栈，正在完全取代"经典"Azure 服务管理模式（经典部署模型）。 因此，经典部署模型 REST API 和对服务总线、中继和事件中心的支持将于 2021 年 11 月 1 日停用。 这种弃用首次在[微软技术社区公告](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)上宣布，但我们最近决定从最初宣布的时间再延长两年的弃用期。 为了便于识别，这些 API`management.core.windows.net`在其 URI 中具有。 有关弃用 API 及其 Azure 资源管理器 API 版本的列表，请参阅下表，现在应使用这些 API 及其 Azure 资源管理器 API 版本。

要继续使用服务总线、中继和事件中心，请于 2021 年 10 月 31 日前转到资源管理器。 我们鼓励仍在使用旧 API 的所有客户尽快进行切换，以利用资源管理器的额外优势，包括资源分组、标记、简化的部署和管理流程以及细粒度访问使用基于角色的访问控制 （RBAC） 进行控制。

有关 Azure 资源管理器与 Azure 服务管理器的详细信息，请参阅[TechNet 博客](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)。

有关 Azure 服务总线、中继和事件中心的服务管理器和资源管理器 API 的详细信息，请参阅 REST API 文档：

- [Azure 服务总线](/rest/api/servicebus/)
- [Azure 事件中心](/rest/api/eventhub/)
- [Azure 中继](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>服务管理器 REST API - 资源管理器 REST API

| 服务管理器 API（已弃用） | 资源管理器 - 服务总线 API | 资源管理器 - 事件中心 API | 资源管理器 - 中继 API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **命名空间-获取命名空间同步** <br/>[服务总线获取命名空间](/rest/api/servicebus/get-namespace)<br/>[事件中心获取命名空间](/rest/api/eventhub/get-event-hub)<br/>[中继获取命名空间](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **连接详细信息 -获取连接详细信息**<br/>服务总线/事件集线器/继电器接线<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [列表键](/rest/api/servicebus/namespaces/listkeys) | [列表键](/rest/api/eventhub/namespaces/listkeys) | [列表键](/rest/api/relay/namespaces/listkeys) |
| **主题-获取主题同步**<br/>服务总线<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [列表](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **队列-获取队列同步** <br/>服务总线<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **继电器-获取中继同步**<br/>[获取继电器](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [列表](/rest/api/relay/wcfrelays/listbynamespace) | 
| **命名空间授权规则-获取命名空间授权规则Async**<br/>服务总线/事件集线器/中继获取命名空间 Auth 规则<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [获取授权规则](/rest/api/servicebus/namespaces/getauthorizationrule) |[获取授权规则](/rest/api/eventhub/namespaces/getauthorizationrule) | [获取授权规则](/rest/api/relay/namespaces/getauthorizationrule) |
| **命名空间-删除命名空间同步**<br/>[服务总线删除命名空间](/rest/api/servicebus/delete-namespace)<br/>[事件中心删除命名空间](/rest/api/eventhub/delete-event-hub)<br/>[中继 删除命名空间](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [删除](/rest/api/servicebus/namespaces/delete) | [删除](/rest/api/eventhub/namespaces/delete) | [删除](/rest/api/relay/namespaces/delete) | 
| **消息库平面-获取平面同步**<br/>服务总线/事件集线器/中继获取命名空间<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **消息库计划-更新计划同步**<br/>服务总线/事件集线器/中继获取命名空间<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [创建器更新](/rest/api/servicebus/namespaces/createorupdate) | [创建器更新](/rest/api/eventhub/namespaces/createorupdate) | [创建器更新](/rest/api/relay/namespaces/createorupdate) |
| **命名空间授权规则-更新命名空间授权规则Async**<br/>服务总线/事件集线器/中继获取命名空间<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [创建器更新](/rest/api/servicebus/namespaces/createorupdate) | [创建或更新授权规则](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [创建或更新授权规则](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **命名空间授权规则-创建命名空间授权规则Async**<br/> 
服务总线/事件集线器/中继<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[创建器更新](/rest/api/servicebus/namespaces/createorupdate) | [创建或更新授权规则](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [创建或更新授权规则](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **命名空间属性-获取命名空间属性同步**<br/>[服务总线获取命名空间](/rest/api/servicebus/get-namespace)<br/>[事件中心获取命名空间](/rest/api/eventhub/get-event-hub)<br/>[中继获取命名空间](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **区域代码-获取区域代码同步**<br/>服务总线/事件中心/中继获取命名空间<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [列斯特比斯库](/rest/api/servicebus/regions/listbysku) | [列斯特比斯库](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **命名空间属性-更新命名空间属性同步**<br/>服务总线/事件中心/中继<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [创建器更新](/rest/api/servicebus/namespaces/createorupdate) | [创建器更新](/rest/api/eventhub/namespaces/createorupdate) | [创建器更新](/rest/api/relay/namespaces/createorupdate) |
| **事件中心克鲁德-列表事件中心同步**<br/>[列出事件中心](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [列表](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **事件中心Crud-获取事件Hubasync**<br/>[获取事件中心](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **命名空间授权规则-删除命名空间授权规则Async**<br/>服务总线/事件集线器/中继<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [删除授权规则](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [删除授权规则](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [删除授权规则](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **命名空间授权规则-获取命名空间授权规则Async**<br/>服务总线/事件中心/中继<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [列表授权规则](/rest/api/servicebus/namespaces/listauthorizationrules) | [列表授权规则](/rest/api/eventhub/namespaces/listauthorizationrules) | [列表授权规则](/rest/api/relay/namespaces/listauthorizationrules) |
| **命名空间可用性 -正名空间可用**<br/>[服务总线命名空间可用性](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **命名空间-创建或更新命名空间同步**<br/>服务总线/事件集线器/中继<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [创建器更新](/rest/api/servicebus/namespaces/createorupdate) | [创建器更新](/rest/api/eventhub/namespaces/createorupdate) | [创建器更新](/rest/api/relay/namespaces/createorupdate) | 
| **主题-获取主题同步**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>服务管理器 PowerShell - 资源管理器 PowerShell
| 服务管理器 PowerShell 命令（已弃用） | 新的资源管理器命令 | 更新资源管理器命令 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [获取服务总线授权规则](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [获取 AzureRm 服务总线地理配置](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [获取服务总线地理配置](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [获取服务总线命名空间](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [新服务总线授权规则](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [新服务总线命名空间](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [删除-AzureRm 中继授权规则](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [删除-服务总线授权规则](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [删除-服务总线命名空间](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [设置-服务总线授权规则](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>后续步骤
请参阅以下文档： 

- 最新的 REST API 文档
    - [Azure 服务总线](/rest/api/servicebus/)
    - [Azure 事件中心](/rest/api/eventhub/)
    - [Azure 中继](/rest/api/relay/)
- 最新的 PowerShell 文档
    - [Azure 服务总线](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure 事件中心](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure 事件网格](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
