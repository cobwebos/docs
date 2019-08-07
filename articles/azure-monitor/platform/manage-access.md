---
title: 管理 Azure Monitor 中的 Log Analytics 工作区 | Microsoft Docs
description: 可以通过对用户、帐户、工作区和 Azure 帐户使用各种管理任务来管理 Azure Monitor 中的 Log Analytics 工作区。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: ce1e107c397f791e4863a90b5622c961d42d6aa5
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814207"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>管理 Azure Monitor 中的日志数据和工作区

Azure Monitor 将日志数据存储在 Log Analytics 工作区中，该工作区本质上是一个包含数据和配置信息的容器。 若要管理对日志数据的访问, 可执行与工作区相关的各种管理任务。

本文介绍如何管理对日志的访问权限, 以及如何管理包含这些日志的工作区, 包括:

* 如何使用 Azure 基于角色的访问控制 (RBAC) 向需要访问特定资源中的日志数据的用户授予访问权限。

* 如何使用工作区权限授予对工作区的访问权限。

* 如何使用 Azure RBAC 向需要访问工作区中特定表中的日志数据的用户授予访问权限。

## <a name="define-access-control-mode-in-azure-portal"></a>在 Azure 门户中定义访问控制模式

可以在工作区“概述”页上的“Log Analytics 工作区”菜单中查看当前的工作区访问控制模式。 

![查看工作区访问控制模式](media/manage-access/view-access-control-mode.png)

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
1. 在 Azure 门户中, 选择工作区 > Log Analytics 工作区。  

你可以从工作区的 "**属性**" 页中更改此设置。 如果你无权配置工作区，则会禁止更改此设置。

![更改工作区访问模式](media/manage-access/change-access-control-mode.png)

## <a name="define-access-control-mode-using-powershell"></a>使用 PowerShell 定义访问控制模式

使用以下命令检查订阅中所有工作区的访问控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

输出应如下所示：

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

值`False`为表示使用工作区上下文访问模式配置工作区。  值`True`为表示使用资源上下文访问模式配置工作区。 

>[!NOTE]
>如果在未使用布尔值的情况下返回工作区且为空白, 则这还会`False`匹配值的结果。
>

使用以下脚本将特定工作区的访问控制模式设置为资源上下文权限:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用以下脚本为资源上下文权限的订阅中的所有工作区设置访问控制模式:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

## <a name="define-access-mode-using-resource-manager-template"></a>使用资源管理器模板定义访问模式

若要在 Azure 资源管理器模板中配置访问模式，请将工作区中的 **enableLogAccessUsingOnlyResourcePermissions** 功能标志设置为以下值之一。

* **false**：将工作区设置为工作区上下文权限。 如果未设置该标志，则这是默认设置。
* **true**：将工作区设置为资源上下文权限。

## <a name="manage-accounts-and-users"></a>管理帐户和用户

应用于特定用户的工作区的权限由其[访问模式](design-logs-deployment.md#access-mode)和工作区的[访问控制模式](design-logs-deployment.md#access-control-mode)定义。 使用**工作区上下文**, 你可以查看你有权使用的工作区中的所有日志, 因为此模式中的查询的作用域为工作区中所有表中的所有数据。 使用**资源上下文**时, 可以在从你有权访问的 Azure 门户中的资源直接执行搜索时, 查看特定资源、资源组或订阅的工作区中的日志数据。 在此模式下，只能查询与该资源关联的数据。

### <a name="workspace-permissions"></a>工作区权限

每个工作区可有多个与其关联的帐户，每个帐户可访问多个工作区。 使用[Azure 基于角色的访问](../../role-based-access-control/role-assignments-portal.md)权限管理访问权限。

以下活动也需要 Azure 权限：

|Action |所需 Azure 权限 |说明 |
|-------|-------------------------|------|
| 添加和删除监视解决方案 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 需要在资源组或订阅级别授予这些权限。 |
| 更改定价层 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 查看*备份* 和 *Site Recovery* 解决方案磁贴中的数据 | 管理员/共同管理员 | 访问通过经典部署模型部署的资源 |
| 在 Azure 门户中创建工作区 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 查看工作区基本属性并在门户中输入工作区边栏选项卡 | `Microsoft.OperationalInsights/workspaces/read` ||
| 使用任何接口查询日志 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 使用查询访问所有日志类型 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 访问特定的日志表 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 读取工作区密钥, 以允许将日志发送到此工作区 | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>使用 Azure 权限管理访问权限

若要使用 Azure 权限授予对 Log Analytics 工作区的访问权限，请执行[使用角色分配来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)中的步骤。

Azure 有两个适用于 Log Analytics 工作区的内置用户角色：

* Log Analytics 读者
* Log Analytics 参与者

Log Analytics 读者角色的成员可以：

* 查看和搜索所有监视数据
* 查看监视设置，包括查看 Azure 诊断在所有 Azure 资源上的配置。

Log Analytics 读者角色包括以下 Azure 操作：

| type    | 权限 | 描述 |
| ------- | ---------- | ----------- |
| Action | `*/read`   | 能够查看所有 Azure 资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区, 它允许完全无限制的权限来读取工作区设置并对数据执行查询。 请参阅上面更细化的选项。 |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 不推荐使用, 无需将其分配给用户。 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | 不推荐使用, 无需将其分配给用户。 |
| Action | `Microsoft.Support/*` | 能够打开支持案例 |
|非操作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止读取工作区密钥，该密钥是使用数据集合 API 和安装代理所必需的。 这可以防止用户向工作区添加新资源 |

Log Analytics 参与者角色的成员可以：

* 像 Log Analytics 读者一样读取所有监视数据
* 创建和配置自动化帐户
* 添加和删除管理解决方案

    > [!NOTE]
    > 若要成功执行最后两个操作，需要在资源组或订阅级别授予此权限。  

* 读取存储帐户密钥
* 从 Azure 存储配置日志收集  
* 编辑 Azure 资源的监视设置，包括
  * 将 VM 扩展添加到 VM
  * 在所有 Azure 资源上配置 Azure 诊断

> [!NOTE]
> 可以使用此功能向虚拟机添加虚拟机扩展，获取对虚拟机的完全控制。

Log Analytics 参与者角色包括以下 Azure 操作：

| 权限 | 描述 |
| ---------- | ----------- |
| `*/read`     | 能够查看所有资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区, 它允许完全无限制的权限来读取工作区设置并对数据执行查询。 请参阅上面更细化的选项。 |
| `Microsoft.Automation/automationAccounts/*` | 能够创建和配置 Azure 自动化帐户，包括添加和编辑 runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 添加、更新和删除虚拟机扩展，包括 Microsoft Monitoring Agent 扩展和 OMS Agent for Linux 扩展 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 查看存储帐户密钥。 在将 Log Analytics 配置为从 Azure 存储帐户读取日志时需要 |
| `Microsoft.Insights/alertRules/*` | 添加、更新和删除警报规则 |
| `Microsoft.Insights/diagnosticSettings/*` | 添加、更新和删除 Azure 资源上的诊断设置 |
| `Microsoft.OperationalInsights/*` | 添加、更新和删除 Log Analytics 工作区的配置 |
| `Microsoft.OperationsManagement/*` | 添加和删除管理解决方案 |
| `Microsoft.Resources/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |

若要添加和删除用户角色的用户，必须拥有 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 权限。

使用这些角色为用户提供不同范围的访问权限：

* 订阅 - 访问订阅中的所有工作区
* 资源组 - 访问资源组中的所有工作区
* 资源 - 仅访问指定工作区

应在资源（工作区）级别执行分配，以确保准确的访问控制。  使用[自定义角色](../../role-based-access-control/custom-roles.md)，创建具有所需的特定权限的角色。

### <a name="resource-permissions"></a>资源权限

当用户使用资源上下文访问从工作区查询日志时, 他们将对资源具有以下权限:

| 权限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>示例:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 可以查看资源的所有日志数据。  |
| `Microsoft.Insights/diagnosticSettings/write ` | 能够将诊断设置配置为允许设置此资源的日志。 |

`/read`权限通常是从包括 _\*/read 或_ _\*_ 权限 (如内置[读取器](../../role-based-access-control/built-in-roles.md#reader)和[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色) 的角色授予的。 请注意，含有特定操作的自定义角色或专用内置角色可能没有此权限。

若要针对不同的表创建不同的访问控制，请参阅下面的[定义按表进行的访问控制](#table-level-rbac)。

## <a name="table-level-rbac"></a>表级 RBAC

使用**表级 RBAC** , 还可以定义对 Log Analytics 工作区中的数据进行更精细的控制以及其他权限。 使用此控制措施可以定义仅供特定一组用户访问的特定数据类型。

使用 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)实现表访问控制，以授予或拒绝对工作区中特定[表](../log-query/logs-structure.md)的访问权限。 无论用户的[访问模式](design-logs-deployment.md#access-mode)如何, 都可以将这些角色应用于工作区上下文或资源上下文[访问控制模式](design-logs-deployment.md#access-control-mode)的工作区。

使用以下操作创建[自定义角色](../../role-based-access-control/custom-roles.md)，以定义对表访问控制的访问权限。

* 若要授予对某个表的访问权限，请将该表包含在角色定义的 **Actions** 节中。
* 若要拒绝对某个表的访问权限，请将该表包含在角色定义的 **NotActions** 节中。
* 使用 * 可指定所有表。

例如，若要创建一个有权访问 _Heartbeat_ 和 _AzureActivity_ 表的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要创建一个只有权访问 _SecurityBaseline_，而无权访问其他任何表的角色，请使用以下操作创建自定义角色：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>自定义日志

 自定义日志是从数据源 (例如自定义日志和 HTTP 数据收集器 API) 创建的。 识别日志类型的最简单方法是查看[日志架构中的自定义日志](../log-query/get-started-portal.md#understand-the-schema)下所列的表。

 目前无法授予或拒绝对单个自定义日志的访问权限，但可以授予或拒绝对所有自定义日志的访问权限。 若要创建一个有权访问所有自定义日志的角色，请使用以下操作创建自定义角色：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>注意事项

* 如果为某个用户授予全局读取权限以及含有 _\*/read_ 操作的标准“读取者”或“参与者”角色，则会替代按表进行的访问控制，并向该用户授予所有日志数据的访问权限。
* 如果为某个用户授予按表访问权限但未授予其他任何权限，则该用户可以通过 API 访问日志数据，但不能通过 Azure 门户进行访问。 若要从 Azure 门户提供访问权限, 请使用 Log Analytics 读取器作为其基本角色。
* 无论其他任何权限设置如何, 订阅管理员都可以访问所有数据类型。
* 应用按表进行的访问控制时，工作区所有者被视为类似于其他任何用户。
* 应该将角色分配到安全组而不是个人用户，以减少分配数目。 这还有助于使用现有的组管理工具来配置和验证访问权限。

## <a name="next-steps"></a>后续步骤

* 请参阅 [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)，以从数据中心或其他云环境中的计算机收集数据。

* 请参阅[收集有关 azure 虚拟机的数据](../../azure-monitor/learn/quick-collect-azurevm.md), 配置从 azure vm 收集数据。