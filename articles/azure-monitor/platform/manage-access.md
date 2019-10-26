---
title: 管理 Azure Monitor 中的 Log Analytics 工作区 | Microsoft Docs
description: 可以使用资源、工作区或表级权限管理对 Azure Monitor 中的 Log Analytics 工作区中存储的数据的访问。 本文详细介绍了如何完成此操作。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 890e2fb06b9194bba49b94eae4b8ea3f0bfed1d7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932183"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>管理 Azure Monitor 中的日志数据和工作区的访问权限

Azure Monitor 将[日志](data-platform-logs.md)数据存储在 Log Analytics 工作区中。 工作区是包含数据和配置信息的容器。 若要管理对日志数据的访问，可执行与工作区相关的各种管理任务。

本文介绍如何管理对日志的访问权限，以及如何管理包含这些日志的工作区，包括如何授予对的访问权限： 

* 使用工作区权限的工作区。
* 需要使用 Azure 基于角色的访问控制（RBAC）从特定资源访问日志数据的用户。
* 需要使用 Azure RBAC 对工作区中特定表中的日志数据进行访问的用户。

## <a name="configure-access-control-mode"></a>配置访问控制模式

你可以从 "Azure 门户" 或 "Azure PowerShell 的工作区上查看配置的访问控制模式。  你可以使用以下受支持的方法之一来更改此设置：

* Azure 门户

* Azure PowerShell

* Azure 资源管理器模板

### <a name="from-the-azure-portal"></a>通过 Azure 门户

可以在 " **Log Analytics 工作区**" 菜单的工作区的 "**概述**" 页上查看当前工作区访问控制模式。

![查看工作区访问控制模式](media/manage-access/view-access-control-mode.png)

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
1. 在 Azure 门户中，选择工作区 > Log Analytics 工作区。

你可以从工作区的 "**属性**" 页中更改此设置。 如果没有配置工作区的权限，则将禁用更改设置。

![更改工作区访问模式](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>使用 PowerShell

使用以下命令检查订阅中的所有工作区的访问控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

输出应如下所示：

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

如果值为 `False`，则表示使用工作区上下文访问模式配置工作区。  值 `True` 表示工作区配置为具有资源上下文访问模式。

> [!NOTE]
> 如果在未使用布尔值的情况下返回工作区且为空白，则这还会与 `False` 值的结果相匹配。
>

使用以下脚本将特定工作区的访问控制模式设置为资源上下文权限：

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用以下脚本为资源上下文权限的订阅中的所有工作区设置访问控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="using-a-resource-manager-template"></a>使用资源管理器模板

若要在 Azure 资源管理器模板中配置访问模式，请将工作区上的**enableLogAccessUsingOnlyResourcePermissions**功能标志设置为以下值之一。

* **false**：将工作区设置为工作区上下文权限。 如果未设置标志，则这是默认设置。
* **true**：将工作区设置为资源上下文权限。

## <a name="manage-access-using-workspace-permissions"></a>使用工作区权限管理访问权限

每个工作区可有多个与其关联的帐户，每个帐户可访问多个工作区。 使用[Azure 基于角色的访问](../../role-based-access-control/role-assignments-portal.md)权限管理访问权限。

以下活动也需要 Azure 权限：

|行动 |所需 Azure 权限 |说明 |
|-------|-------------------------|------|
| 添加和删除监视解决方案 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 需要在资源组或订阅级别授予这些权限。 |
| 更改定价层 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 查看*备份* 和 *Site Recovery* 解决方案磁贴中的数据 | 管理员/共同管理员 | 访问通过经典部署模型部署的资源 |
| 在 Azure 门户中创建工作区 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 查看工作区基本属性并在门户中输入工作区边栏选项卡 | `Microsoft.OperationalInsights/workspaces/read` ||
| 使用任何接口查询日志 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 使用查询访问所有日志类型 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 访问特定的日志表 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 读取工作区密钥，以允许将日志发送到此工作区 | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>使用 Azure 权限管理访问权限

若要使用 Azure 权限授予对 Log Analytics 工作区的访问权限，请执行[使用角色分配来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)中的步骤。 有关自定义角色的示例，请参阅[示例自定义角色](#custom-role-examples)

Azure 有两个适用于 Log Analytics 工作区的内置用户角色：

* Log Analytics 读者
* Log Analytics 参与者

Log Analytics 读者角色的成员可以：

* 查看和搜索所有监视数据
* 查看监视设置，包括查看 Azure 诊断在所有 Azure 资源上的配置。

Log Analytics 读者角色包括以下 Azure 操作：

| Type    | 权限 | 描述 |
| ------- | ---------- | ----------- |
| 行动 | `*/read`   | 能够查看所有 Azure 资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区，它允许完全无限制的权限来读取工作区设置并对数据执行查询。 请参阅上面更细化的选项。 |
| 行动 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 不推荐使用，无需将其分配给用户。 |
| 行动 | `Microsoft.OperationalInsights/workspaces/search/action` | 不推荐使用，无需将其分配给用户。 |
| 行动 | `Microsoft.Support/*` | 能够打开支持案例 |
|非操作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止读取工作区密钥，该密钥是使用数据集合 API 和安装代理所必需的。 这可以防止用户向工作区添加新资源 |

Log Analytics 参与者角色的成员可以：

* 包括*Log Analytics 读取者角色*的所有权限，使用户能够读取所有监视数据
* 创建和配置自动化帐户
* 添加和删除管理解决方案

    > [!NOTE]
    > 若要成功执行最后两个操作，需要在资源组或订阅级别授予此权限。

* 读取存储帐户密钥
* 配置 Azure 存储中的日志集合
* 编辑 Azure 资源的监视设置，包括
  * 将 VM 扩展添加到 VM
  * 在所有 Azure 资源上配置 Azure 诊断

> [!NOTE]
> 可以使用此功能向虚拟机添加虚拟机扩展，获取对虚拟机的完全控制。

Log Analytics 参与者角色包括以下 Azure 操作：

| 权限 | 描述 |
| ---------- | ----------- |
| `*/read`     | 能够查看所有资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区，它允许完全无限制的权限来读取工作区设置并对数据执行查询。 请参阅上面更细化的选项。 |
| `Microsoft.Automation/automationAccounts/*` | 能够创建和配置 Azure 自动化帐户，包括添加和编辑 runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 添加、更新和删除虚拟机扩展，包括 Microsoft Monitoring Agent 扩展和 OMS Agent for Linux 扩展 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 查看存储帐户密钥。 在将 Log Analytics 配置为从 Azure 存储帐户读取日志时需要 |
| `Microsoft.Insights/alertRules/*` | 添加、更新和删除警报规则 |
| `Microsoft.Insights/diagnosticSettings/*` | 添加、更新和删除 Azure 资源上的诊断设置 |
| `Microsoft.OperationalInsights/*` | 为 Log Analytics 工作区添加、更新和删除配置。 若要编辑工作区高级设置，用户需要 `Microsoft.OperationalInsights/workspaces/write`。 |
| `Microsoft.OperationsManagement/*` | 添加和删除管理解决方案 |
| `Microsoft.Resources/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |

若要添加和删除用户角色的用户，必须拥有 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 权限。

使用这些角色为用户提供不同范围的访问权限：

* 订阅 - 访问订阅中的所有工作区
* 资源组 - 访问资源组中的所有工作区
* 资源 - 仅访问指定工作区

建议在资源级别（工作区）执行分配，以确保正确的访问控制。 使用[自定义角色](../../role-based-access-control/custom-roles.md)，创建具有所需的特定权限的角色。

### <a name="resource-permissions"></a>资源权限

当用户使用资源上下文访问从工作区查询日志时，他们将对资源具有以下权限：

| 权限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>示例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 能够查看资源的所有日志数据。  |
| `Microsoft.Insights/diagnosticSettings/write` | 能够将诊断设置配置为允许设置此资源的日志。 |

`/read` 权限通常从包含 _\*/read 或_ _\*_ 权限的角色（例如内置的 "[读取](../../role-based-access-control/built-in-roles.md#reader)者" 和 "[参与者](../../role-based-access-control/built-in-roles.md#contributor)" 角色）中授予。 包括特定操作或专用内置角色的自定义角色可能不包括此权限。

如果要为不同的表创建不同的访问控制，请参阅下面[的定义每个表的访问控制](#table-level-rbac)。

## <a name="custom-role-examples"></a>自定义角色示例

1. 若要授予用户访问其资源的日志数据的权限，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 向用户授予对其资源 `*/read` 或 `Microsoft.Insights/logs/*/read` 权限。 如果已在工作区中为其分配了[Log Analytics 读取](../../role-based-access-control/built-in-roles.md#reader)者角色，则该角色就已足够。

2. 若要授予用户对其资源中的日志数据的访问权限并将其资源配置为向工作区发送日志，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 向用户授予对工作区的以下权限： `Microsoft.OperationalInsights/workspaces/read` 和 `Microsoft.OperationalInsights/workspaces/sharedKeys/action`。 如果具有这些权限，用户将无法执行任何工作区级查询。 它们只能枚举工作区，并将其用作诊断设置或代理配置的目标。

    * 向用户授予对其资源的以下权限： `Microsoft.Insights/logs/*/read` 和 `Microsoft.Insights/diagnosticSettings/write`。 如果已为其分配 "[参与者 Log Analytics](../../role-based-access-control/built-in-roles.md#contributor) " 角色，则分配有 "读取者" 角色，或授予对此资源的 `*/read` 权限，这就足够了。

3. 若要授予用户对其资源中的日志数据的访问权限，而不能读取安全事件和发送数据，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 向用户授予对其资源的以下权限： `Microsoft.Insights/logs/*/read`。

    * 添加以下 NonAction 以阻止用户读取 SecurityEvent 类型： `Microsoft.Insights/logs/SecurityEvent/read`。 NonAction 应与提供读取权限的操作具有相同的自定义角色（`Microsoft.Insights/logs/*/read`）。 如果用户从分配给此资源的另一个角色或向订阅或资源组中继承读取操作，则他们将能够读取所有日志类型。 如果它们继承 `*/read` （例如，具有读取者或参与者角色），则也是如此。

4. 若要授予用户对其资源中的日志数据的访问权限并读取所有 Azure AD 登录并从工作区读取更新管理解决方案日志数据，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 向用户授予对工作区的以下权限： 

        * `Microsoft.OperationalInsights/workspaces/read` –必需，以便使用可以枚举工作区并在 Azure 门户中打开工作区边栏选项卡
        * `Microsoft.OperationalInsights/workspaces/query/read` –每个可以执行查询的用户都需要
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` –能够读取登录日志 Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` –能够读取解决方案日志更新管理
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` –能够读取解决方案日志更新管理
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` –能够读取更新管理日志
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` –需要能够使用更新管理解决方案
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` –需要能够使用更新管理解决方案

    * 向用户授予对其资源的以下权限： `*/read`，分配给 "读者" 角色，或 `Microsoft.Insights/logs/*/read`。 

## <a name="table-level-rbac"></a>表级别 RBAC

使用**表级 RBAC** ，还可以定义对 Log Analytics 工作区中的数据进行更精细的控制以及其他权限。 此控件允许您定义只能由一组特定用户访问的特定数据类型。

使用[Azure 自定义角色](../../role-based-access-control/custom-roles.md)实现表访问控制，以授予或拒绝对工作区中特定[表](../log-query/logs-structure.md)的访问权限。 无论用户的[访问模式](design-logs-deployment.md#access-mode)如何，都可以将这些角色应用于工作区上下文或资源上下文[访问控制模式](design-logs-deployment.md#access-control-mode)的工作区。

使用以下操作创建[自定义角色](../../role-based-access-control/custom-roles.md)，以定义对表访问控制的访问权限。

* 若要授予对表的访问权限，请将其包含在角色定义的 "**操作**" 部分中。
* 若要拒绝对表的访问，请将其包含在角色定义的**NotActions**节中。
* 使用 * 指定所有表。

例如，若要创建对_检测信号_表和_AzureActivity_表具有访问权限的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要创建只具有访问_SecurityBaseline_和其他表的权限的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
"NotActions":  [
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
```

### <a name="custom-logs"></a>自定义日志

 自定义日志是从数据源（例如自定义日志和 HTTP 数据收集器 API）创建的。 确定日志类型的最简单方法是检查[日志架构中 "自定义日志](../log-query/get-started-portal.md#understand-the-schema)" 下列出的表。

 目前不能授予或拒绝对单独自定义日志的访问权限，但可以授予或拒绝对所有自定义日志的访问权限。 若要创建具有对所有自定义日志的访问权限的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>注意事项

* 如果用户被授予具有标准读取器或包含 _\*/read_操作的参与者角色的全局读取权限，则它将覆盖每个表的访问控制，并向其授予对所有日志数据的访问权限。
* 如果用户被授予每个表的访问权限，但没有其他权限，则他们可以从 API 访问日志数据，但不能从 Azure 门户访问日志数据。 若要从 Azure 门户提供访问权限，请使用 Log Analytics 读取器作为其基本角色。
* 无论其他任何权限设置如何，订阅管理员都可以访问所有数据类型。
* 工作区所有者的处理方式与针对每个表的访问控制的其他任何用户一样。
* 建议为安全组而不是单个用户分配角色，以减少分配的数量。 这还将帮助你使用现有组管理工具来配置和验证访问权限。

## <a name="next-steps"></a>后续步骤

* 请参阅 [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)，以从数据中心或其他云环境中的计算机收集数据。

* 请参阅[收集有关 azure 虚拟机的数据](../../azure-monitor/learn/quick-collect-azurevm.md)，配置从 azure vm 收集数据。
