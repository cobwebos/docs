---
title: "Azure Monitor 的角色、权限和安全入门 | Microsoft Docs"
description: "了解如何使用 Azure Monitor 的内置角色和权限限制对监视资源的访问。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: f8767073bb7a6723088bb2727346d23ec8872cd1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Azure Monitor 的角色、权限和安全入门
很多团队需要严格控制对监视数据和设置的访问。 例如，如果有专门负责监视的团队成员（支持工程师、DevOps 工程师），或者使用托管服务提供程序，则可能希望向他们授予仅访问监视数据的权限，同时限制其创建、修改或删除资源的能力。 本文介绍如何快速地将内置监视 RBAC 角色应用到 Azure 中的用户，或者为需要有限的监视权限的用户构建自己的自定义角色。 然后讨论与 Azure Monitor 相关资源的安全注意事项，以及如何限制对它们所含数据的访问。

## <a name="built-in-monitoring-roles"></a>内置监视角色
Azure Monitor 的内置角色设计为帮助限制对订阅中资源的访问，同时使负责监视基础结构的用户能够获取并配置他们需要的数据。 Azure Monitor 提供了两个现成的角色：监视查阅者和监视参与者。

### <a name="monitoring-reader"></a>监视查阅者
分配了监视查阅者角色的人员可以查看订阅中的所有监视数据，但不能修改任何资源或编辑与监视资源相关的任何设置。 此角色适用于组织中的用户，如支持人员或操作工程师，他们需要能够：

* 在门户中查看监视仪表板和创建自己专用的监视仪表板。
* 使用 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx)、[PowerShell cmdlet](insights-powershell-samples.md) 或 [跨平台 CLI](insights-cli-samples.md) 查询指标。
* 使用门户、Azure Monitor REST API、PowerShell cmdlet 或跨平台 CLI 查询活动日志。
* 查看资源的[诊断设置](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)。
* 查看订阅的[日志配置文件](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)。
* 查看自动调整规模设置。
* 查看警报活动和设置。
* 访问 Application Insights 数据和在 AI Analytics 中查看数据。
* 搜索 Log Analytics 工作区数据（包括工作区的使用情况数据）。
* 查看 Log Analytics 管理组。
* 检索 Log Analytics 搜索架构。
* 列出 Log Analytics 智能包。
* 检索并执行 Log Analytics 已保存的搜索。
* 检索 Log Analytics 存储配置。

> [!NOTE]
> 此角色不授予已传输到事件中心或存储在存储帐户的日志数据的读取访问权限。 有关配置这些资源的访问权限的信息，请[参阅下面章节](#security-considerations-for-monitoring-data)。
> 
> 

### <a name="monitoring-contributor"></a>监视参与者
分配了监视参与者角色的人员可以查看订阅中的所有监视数据和创建或修改监视设置，但不能修改任何其他资源。 此角色是监视查阅者角色的一个超集，适用于组织的监视团队成员或托管服务提供商，除了上述权限外，他们还需要能够：

* 将监视仪表板发布为共享仪表板。
* 设置资源的[诊断设置](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)。*
* 设置订阅的[日志配置文件](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)。*
* 设置警报活动和设置。
* 创建 Application Insights Web 测试和组件。
* 列出 Log Analytics 工作区的共享密钥。
* 启用或禁用 Log Analytics 智能包。
* 创建、删除和执行 Log Analytics 已保存的搜索。
* 创建和删除 Log Analytics 存储配置。

*用户还必须分别被授予目标资源（存储帐户或事件中心命名空间）的 ListKeys 权限，才能设置日志配置文件或诊断设置。

> [!NOTE]
> 此角色不授予已传输到事件中心或存储在存储帐户的日志数据的读取访问权限。 有关配置这些资源的访问权限的信息，请[参阅下面章节](#security-considerations-for-monitoring-data)。
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>监视权限和自定义 RBAC 角色
如果上述的内置角色不能满足团队的确切需求，则可以[创建具有更加细化的权限的自定义 RBAC 角色](../active-directory/role-based-access-control-custom-roles.md)。 以下是常见的 Azure Monitor RBAC 操作及其说明。

| 操作 | 说明 |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |读取/写入/删除操作组。 |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |读取/写入/删除活动日志警报。 |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |读取/写入/删除警报规则（指标警报）。 |
| Microsoft.Insights/AlertRules/Incidents/Read |列出警报规则的事件（触发警报规则的历史记录）。 仅适用于门户。 |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |读取/写入/删除自动调整规模设置。 |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |读取/写入/删除诊断设置。 |
| Microsoft.Insights/EventCategories/Read |枚举活动日志中所有可能的类别。 由 Azure 门户使用。 |
| Microsoft.Insights/eventtypes/digestevents/Read |此权限对于需要通过门户访问活动日志的用户是必需的。 |
| Microsoft.Insights/eventtypes/values/Read |列出订阅中的活动日志事件（管理事件）。 此权限适用于对活动日志的编程和门户访问。 |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | 读取/写入/删除网络流日志的诊断设置。 |
| Microsoft.Insights/LogDefinitions/Read |此权限对于需要通过门户访问活动日志的用户是必需的。 |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |读取/写入/删除日志配置文件（将活动日志流式传输到事件中心或存储帐户）。 |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |读取/写入/删除准实时指标警报（公开预览版）。 |
| Microsoft.Insights/MetricDefinitions/Read |读取指标定义（资源的可用指标类型的列表）。 |
| Microsoft.Insights/Metrics/Read |读取资源的指标。 |
| Microsoft.Insights/Register/Action |注册 Azure Monitor 资源提供程序。 |


> [!NOTE]
> 对资源的警报、诊断设置和指标的访问需要用户具有资源类型和该资源的作用域的读取访问权限。 创建（“写入”）存档到存储帐户或传输到事件中心的诊断设置或日志配置文件需要用户还具有目标资源的 ListKeys 权限。
> 
> 

例如，使用上面的表格可以为“Activity Log Reader”创建如下的自定义 RBAC 角色：

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>监视数据的安全注意事项
监视的数据 — 尤其是日志文件 — 可能包含敏感信息，如 IP 地址或用户名。 在 Azure 中监视的数据有三种基本形式：

1. 活动日志，描述 Azure 订阅的所有控制平面操作。
2. 诊断日志，由资源发出的日志文件。
3. 指标，由资源发出。

所有这三种数据类型可以存储在存储帐户或传输到事件中心，两者都是通用的 Azure 资源。 由于这些是通用的资源，因此创建、删除和访问它们是一项通常预留给管理员的权限操作。 我们建议对监视相关的资源使用以下做法，以防止误用：

* 使用单个、专用存储帐户来监视数据。 如果需要将监视数据划分到多个存储帐户，那么请勿在监视数据和非监视数据之间共享存储帐户的使用，因为这可能会无意中给予那些仅需要访问监视数据（例如 第三方 SIEM）的人员非监视数据的访问权限。
* 与上述原因相同，请对所有诊断设置使用单个、专用的服务总线或事件中心命名空间。
* 通过将监视相关的存储帐户或事件中心保存在单独的资源组中来限制对它们的访问，并对监视角色[使用范围](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure)以限制仅访问该资源组。
* 当用户只需访问监视数据时，请勿授予订阅范围内的存储帐户或事件中心的 ListKeys 权限。 取而代之的是给予用户资源或资源组（如果有专用的监视资源组）范围的权限。

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>限制对监视相关的存储帐户的访问权限
当用户或应用程序需要访问存储帐户中的监视数据时，应使用 blob 存储的服务级别的只读访问权限在包含监视数据的存储帐户上[生成帐户 SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx)。 在 PowerShell 中，此操作如下所示：

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

然后，可将令牌授予需要从存储帐户中进行读取的实体，该实体可以列出并读取存储帐户中的所有 blob。

或者，如果需要控制此 RBAC 的权限，可以授予该实体特定存储帐户的 Microsoft.Storage/storageAccounts/listkeys/action 权限。 这对于需要能够设置存档到存储帐户的诊断设置或日志配置文件的用户来说是必需的。 例如，可以为只需读取一个存储帐户的用户或应用程序创建以下自定义 RBAC 角色：

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> ListKeys 权限使用户能够列出主要和辅助存储帐户密钥。 这些密钥授予用户该存储帐户中的所有签名服务（blob、队列、表、文件）中的所有签名权限（读取、写入、创建 blob、删除 blob 等等）。 如果可以，我们建议使用上述的帐户 SAS。
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>限制对监视相关的事件中心的访问权限
事件中心可以遵循类似的模式，但首先需要创建专用的侦听授权规则。 如果想要将访问权限授予只需侦听与监视相关的事件中心的应用程序，请执行以下操作：

1. 为事件中心创建共享访问策略，该事件中心是为传输仅包含侦听声明的监视数据而创建的。 可以在门户中完成此操作。 例如，可以称它为“monitoringReadOnly”。 如果可以，会希望将密钥直接提供给用户，并跳过下一步。
2. 如果用户需要能够临时获取密钥，则授予用户对事件中心的 ListKeys 操作。 这对于需要能够设置传输到事件中心的诊断设置或日志配置文件的用户来说也是必需的。 例如，可以创建 RBAC 规则：
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>后续步骤
* [阅读有关 Resource Manager 中的 RBAC 和权限的信息](../active-directory/role-based-access-control-what-is.md)
* [阅读 Azure 中的监视概述](monitoring-overview.md)

