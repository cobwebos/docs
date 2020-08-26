---
title: 管理 Azure Monitor 中的 Log Analytics 工作区 | Microsoft Docs
description: 可以使用资源、工作区或表级权限来管理对 Azure Monitor 的 Log Analytics 工作区中存储的数据的访问。 本文详细介绍如何完成这些操作。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 5b450254648cb253d6631397d703430401009f14
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925628"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>管理对 Azure Monitor 中的日志数据和工作区的访问

Azure Monitor 将[日志](data-platform-logs.md)数据存储在 Log Analytics 工作区中。 工作区是包含数据和配置信息的容器。 若要管理对日志数据的访问，需要对工作区执行各种管理任务。

本文介绍如何管理对日志的访问，以及如何管理包含日志的工作区，包括如何执行以下操作： 

* 使用工作区权限授予对工作区的访问权限。
* 需要使用基于角色的访问控制从特定资源访问日志数据的用户 (Azure RBAC) -也称为[资源上下文](design-logs-deployment.md#access-mode)
* 使用 Azure RBAC 对需要访问工作区中特定表中的日志数据的用户授予访问权限。

要了解有关 RBAC 和访问策略的日志概念，请阅读[设计 Azure Monitor 日志部署](design-logs-deployment.md)

## <a name="configure-access-control-mode"></a>配置访问控制模式

可以通过 Azure 门户或 Azure PowerShell 查看对工作区配置的[访问控制模式](design-logs-deployment.md)。  可使用以下支持的方法之一更改此设置：

* Azure 门户

* Azure PowerShell

* Azure Resource Manager 模板

### <a name="from-the-azure-portal"></a>通过 Azure 门户

可以在工作区“概述”页上的“Log Analytics 工作区”菜单中查看当前的工作区访问控制模式。 

![查看工作区访问控制模式](media/manage-access/view-access-control-mode.png)

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
1. 在 Azure 门户中，选择“Log Analytics 工作区”> 你的工作区。

可以在工作区的“属性”页中更改此设置。 如果你无权配置工作区，则会禁止更改此设置。

![更改工作区访问模式](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>使用 PowerShell

使用以下命令检查订阅中所有工作区的访问控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

输出应如下所示：

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

`False` 值表示使用工作区上下文访问模式配置了工作区。  `True` 值表示使用资源上下文访问模式配置了工作区。

> [!NOTE]
> 如果返回的工作区不包含布尔值且为空，这也符合 `False` 值的结果。
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

使用以下脚本将订阅中所有工作区的访问控制模式设置为资源上下文权限：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>使用资源管理器模板

若要在 Azure 资源管理器模板中配置访问模式，请将工作区中的 **enableLogAccessUsingOnlyResourcePermissions** 功能标志设置为以下值之一。

* **false**：将工作区设置为工作区上下文权限。 如果未设置该标志，则这是默认设置。
* **true**：将工作区设置为资源上下文权限。

## <a name="manage-access-using-workspace-permissions"></a>使用工作区权限管理访问

每个工作区可有多个与其关联的帐户，每个帐户可访问多个工作区。 使用 azure [RBAC)  (，使用 azure 基于角色的访问控制](../../role-based-access-control/role-assignments-portal.md)来管理访问权限。

以下活动也需要 Azure 权限：

|操作 |所需 Azure 权限 |注释 |
|-------|-------------------------|------|
| 添加和删除监视解决方案 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 需要在资源组或订阅级别授予这些权限。 |
| 更改定价层 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 查看*备份* 和 *Site Recovery* 解决方案磁贴中的数据 | 管理员/共同管理员 | 访问通过经典部署模型部署的资源 |
| 在 Azure 门户中创建工作区 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 查看工作区基本属性并进入门户中的工作区边栏选项卡 | `Microsoft.OperationalInsights/workspaces/read` ||
| 使用任何接口查询日志 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 使用查询访问所有日志类型 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 访问特定的日志表 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 读取工作区密钥，以便能够将日志发送到此工作区 | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>使用 Azure 权限管理访问

若要使用 Azure 权限授予对 Log Analytics 工作区的访问权限，请执行[使用角色分配来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)中的步骤。 如需自定义角色的示例，请参阅 [自定义角色示例](#custom-role-examples)

Azure 有两个适用于 Log Analytics 工作区的内置用户角色：

* Log Analytics 读者
* Log Analytics 参与者

Log Analytics 读者角色的成员可以：

* 查看和搜索所有监视数据
* 查看监视设置，包括查看 Azure 诊断在所有 Azure 资源上的配置。

Log Analytics 读者角色包括以下 Azure 操作：

| 类型    | 权限 | 说明 |
| ------- | ---------- | ----------- |
| 操作 | `*/read`   | 能够查看所有 Azure 资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区，它允许使用完全不受限制的权限来读取工作区设置，并对数据执行查询。 请参阅上述更细化的选项。 |
| 操作 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 已弃用，无需将其分配给用户。 |
| 操作 | `Microsoft.OperationalInsights/workspaces/search/action` | 已弃用，无需将其分配给用户。 |
| 操作 | `Microsoft.Support/*` | 能够打开支持案例 |
|非操作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止读取工作区密钥，该密钥是使用数据集合 API 和安装代理所必需的。 这可以防止用户向工作区添加新资源 |

Log Analytics 参与者角色的成员可以：

* 包括“Log Analytics 读取者角色”的所有特权，允许用户读取所有监视数据
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

| 权限 | 说明 |
| ---------- | ----------- |
| `*/read`     | 能够查看所有资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区，它允许使用完全不受限制的权限来读取工作区设置，并对数据执行查询。 请参阅上述更细化的选项。 |
| `Microsoft.Automation/automationAccounts/*` | 能够创建和配置 Azure 自动化帐户，包括添加和编辑 runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 添加、更新和删除虚拟机扩展，包括 Microsoft Monitoring Agent 扩展和 OMS Agent for Linux 扩展 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 查看存储帐户密钥。 在将 Log Analytics 配置为从 Azure 存储帐户读取日志时需要 |
| `Microsoft.Insights/alertRules/*` | 添加、更新和删除警报规则 |
| `Microsoft.Insights/diagnosticSettings/*` | 添加、更新和删除 Azure 资源上的诊断设置 |
| `Microsoft.OperationalInsights/*` | 添加、更新和删除 Log Analytics 工作区的配置。 若要编辑工作区高级设置，用户需要 `Microsoft.OperationalInsights/workspaces/write`。 |
| `Microsoft.OperationsManagement/*` | 添加和删除管理解决方案 |
| `Microsoft.Resources/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |

若要添加和删除用户角色的用户，必须拥有 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 权限。

使用这些角色为用户提供不同范围的访问权限：

* 订阅 - 访问订阅中的所有工作区
* 资源组 - 访问资源组中的所有工作区
* 资源 - 仅访问指定工作区

我们建议在资源级别（工作区）执行分配，以确保准确的访问控制。 使用[自定义角色](../../role-based-access-control/custom-roles.md)，创建具有所需的特定权限的角色。

### <a name="resource-permissions"></a>资源权限

当用户使用资源上下文访问权限查询工作区中的日志时，他们对资源拥有以下权限：

| 权限 | 说明 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>示例:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 可以查看资源的所有日志数据。  |
| `Microsoft.Insights/diagnosticSettings/write` | 可配置诊断设置以允许设置此资源的日志。 |

`/read` 权限通常是从含有 _\*/read 或_ _\*_ 权限的角色授予的，例如内置的[读取者](../../role-based-access-control/built-in-roles.md#reader)和[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色。 包含特定操作的自定义角色或专用内置角色可能没有此权限。

若要针对不同的表创建不同的访问控制，请参阅下面的[定义按表进行的访问控制](#table-level-rbac)。

## <a name="custom-role-examples"></a>自定义角色示例

1. 若要授予用户从其资源访问日志数据的权限，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 向用户授予对其资源的 `*/read` 或 `Microsoft.Insights/logs/*/read` 权限。 如果已经为用户分配了对工作区的“[Log Analytics 读取者](../../role-based-access-control/built-in-roles.md#reader)”角色，则不需要执行额外的操作。

2. 若要授予用户从其资源访问日志数据的权限并将其资源配置为向工作区发送日志，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 为用户授予对工作区的以下权限：`Microsoft.OperationalInsights/workspaces/read` 和 `Microsoft.OperationalInsights/workspaces/sharedKeys/action`。 用户无法使用这些权限执行任何工作区级别的查询。 他们只能枚举工作区，并将其用作诊断设置或代理配置的目标。

    * 为用户授予对其资源的以下权限：`Microsoft.Insights/logs/*/read` 和 `Microsoft.Insights/diagnosticSettings/write`。 如果已经为用户分配了 [Log Analytics 参与者](../../role-based-access-control/built-in-roles.md#contributor)角色、“读取者”角色或者为其授予了对此资源的 `*/read` 权限，则不需要执行额外的操作。

3. 若要为用户授予从其资源访问日志数据的权限，但不允许他们读取安全事件和发送数据，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 为用户授予对其资源的以下权限：`Microsoft.Insights/logs/*/read`。

    * 添加以下 NonAction 以阻止用户读取 SecurityEvent 类型：`Microsoft.Insights/logs/SecurityEvent/read`。 NonAction 应该与提供读取权限的操作 (`Microsoft.Insights/logs/*/read`) 包含在同一个自定义角色中。 如果用户从已分配到此资源或已分配到订阅或资源组的另一个角色继承读取操作，他们将能够读取所有日志类型。 如果他们继承 `*/read`（例如，“读取者”或“参与者”角色存在的此操作），也是如此。

4. 若要授予用户从其资源访问日志数据，以及从工作区读取所有 Azure AD 登录和更新管理解决方案日志数据的权限，请执行以下操作：

    * 将工作区访问控制模式配置为**使用工作区或资源权限**

    * 为用户授予对工作区的以下权限： 

        * `Microsoft.OperationalInsights/workspaces/read`–必需，以便用户可以枚举工作区并在 Azure 门户中打开工作区边栏选项卡
        * `Microsoft.OperationalInsights/workspaces/query/read`–每个可以执行查询的用户都需要
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`–能够读取 Azure AD 登录日志
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`–能够读取更新管理解决方案日志
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`–能够读取更新管理解决方案日志
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`–能够读取更新管理日志
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`–要求能够使用更新管理解决方案
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`–要求能够使用更新管理解决方案

    * 为用户授予对其资源的以下权限：分配给“读取者”角色的 `*/read`，或 `Microsoft.Insights/logs/*/read`。 

## <a name="table-level-rbac"></a>表级 RBAC

使用**表级 RBAC** 可以针对 Log Analytics 工作区中的数据定义更精细的控制，此外还能分配其他权限。 使用此控制措施可以定义仅供特定一组用户访问的特定数据类型。

使用 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)实现表访问控制，以授予对工作区中特定[表](../log-query/logs-structure.md)的访问权限。 无论用户的[访问模式](design-logs-deployment.md#access-mode)是什么，这些角色都会应用到使用工作区上下文或者资源上下文[访问控制模式](design-logs-deployment.md#access-control-mode)的工作区。

使用以下操作创建[自定义角色](../../role-based-access-control/custom-roles.md)，以定义对表访问控制的访问权限。

* 若要授予对某个表的访问权限，请将该表包含在角色定义的 **Actions** 节中。 若要从允许的**操作**中减去访问权限，请将其包含在 **NotActions** 节中。
* 使用“Microsoft.OperationalInsights/workspaces/query/*”指定所有表。

例如，若要创建一个有权访问 _Heartbeat_ 和 _AzureActivity_ 表的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要创建只能访问 _SecurityBaseline_ 表的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
以上示例定义了允许的表的允许列表。 此示例显示了当用户可以访问除 SecurityAlert 表之外的所有表时的阻止列表定义：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>自定义日志

 自定义日志是基于自定义日志和 HTTP 数据收集器 API 等数据源创建的。 识别日志类型的最简单方法是查看[日志架构中的自定义日志](../log-query/get-started-portal.md#understand-the-schema)下所列的表。

 无法授予对单个自定义日志的访问权限，但可以授予对所有自定义日志的访问权限。 若要创建一个有权访问所有自定义日志的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
管理对自定义日志访问权限的另一种方法是将其分配给 Azure 资源，然后使用资源上下文范例管理访问权限。 若要使用此方法，必须在通过 [HTTP 数据收集器 API](data-collector-api.md) 将数据引入到 Log Analytics 时，通过在 [x-ms-AzureResourceId](data-collector-api.md#request-headers) 标头中指定资源 ID 来将其包含在内。 资源 ID 必须有效，并且具有适用的访问规则。 如此处所述，引入日志后，对资源具有读取访问权限的用户可对其进行访问。

有时，自定义日志来自与特定资源不直接关联的源。 在这种情况下，只需创建一个资源组来管理对这些日志的访问权限。 资源组不会产生任何费用，但会提供有效的资源 ID 来控制对自定义日志的访问。 例如，如果特定防火墙正在发送自定义日志，创建一个名为“MyFireWallLogs”的资源组，并确保 API 请求中包含资源 ID“MyFireWallLogs”。 然后，仅有权访问 MyFireWallLogs 或具有完整工作区访问权限的用户才能访问防火墙日志记录。          

### <a name="considerations"></a>注意事项

* 如果为某个用户授予全局读取权限以及含有 _\*/read_ 操作的标准“读取者”或“参与者”角色，则会替代按表进行的访问控制，并向该用户授予所有日志数据的访问权限。
* 如果为某个用户授予按表访问权限但未授予其他任何权限，则该用户可以通过 API 访问日志数据，但不能通过 Azure 门户进行访问。 若要从 Azure 门户提供访问权限，请使用“Log Analytics 读取者”作为用户的基本角色。
* 无论任何其他权限设置如何，订阅管理员和所有者都有权访问所有数据类型。
* 应用按表进行的访问控制时，工作区所有者被视为类似于其他任何用户。
* 我们建议将角色分配到安全组而不是个人用户，以减少分配数目。 这还有助于使用现有的组管理工具来配置和验证访问权限。

## <a name="next-steps"></a>后续步骤

* 请参阅 [Log Analytics 代理概述](./log-analytics-agent.md)，以从数据中心或其他云环境中的计算机收集数据。

* 请参阅[收集有关 Azure 虚拟机的数据](../learn/quick-collect-azurevm.md)，以配置 Azure VM 的数据收集。
