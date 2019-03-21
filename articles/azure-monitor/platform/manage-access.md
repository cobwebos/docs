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
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: be285b6a51ae5a0f4239b841ce64100f1875d785
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294342"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>管理日志数据和 Azure Monitor 中的工作区
Azure Monitor 将日志数据存储在 Log Analytics 工作区中，该工作区本质上是一个包含数据和配置信息的容器。 若要管理对日志数据的访问，需要对工作区执行各种管理任务。 你或组织中的其他成员可以使用多个工作区，管理收集自所有或部分 IT 基础结构的不同数据集。

本文介绍如何管理对日志的访问以及如何管理包含它们的工作区。 

## <a name="create-a-workspace"></a>创建工作区
若要创建 Log Analytics 工作区，需要：

1. 一个 Azure 订阅。
2. 选择工作区名称。
3. 将工作区与订阅和资源组之一相关联。
4. 选择地理位置。

请参阅以下文章，了解创建工作区的详细信息：

- [在 Azure 门户中创建 Log Analytics 工作区](../learn/quick-create-workspace.md)
- [使用 Azure CLI 2.0 创建 Log Analytics 工作区](../learn/quick-create-workspace-cli.md)
- [使用 Azure PowerShell 创建 Log Analytics 工作区](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>确定所需工作区数
Log Analytics 工作区是一种 Azure 资源，也是 Azure Monitor 中收集、聚合、分析和呈现数据的容器。 你可以为每个 Azure 订阅创建多个工作区，并且你可以访问多个工作区，并且可以轻松查询这些工作区。 本部分介绍有助于创建多个工作区的情况。

Log Analytics 工作区可提供：

* 数据存储的地理位置。
* 若要在工作区为中心模式中定义不同的用户访问权限的数据隔离。 在为中心的模式下不相关。
* 配置设置的作用域喜欢[定价层](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)，[保留](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)并[数据将达到上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)。
* 与数据引入和保留相关的费用是对工作区资源。

从使用角度来看，我们建议尽量减少创建的工作区数量。 这可带来更轻松快捷的管理和查询体验。 但是，基于上述特征，在以下情况下可能需要创建多个工作区：

* 贵公司是全球性公司，因数据所有权和合规性需要将日志数据存储于特定区域。
* 正在使用 Azure，并希望通过让工作区与它所管理的 Azure 资源位于同一区域，避免产生出站数据传输费用。
* 公司是托管服务提供商，需要为所管理的每位客户单独保留 Log Analytics 数据，即与其他客户的数据分开保存。
* 管理多个客户，并希望每个客户 / 部门 / 业务组能够看到他们自己的数据，但不是从其他数据，并且没有统一的跨客户没有业务需求 / 部门 / 业务组视图。"。

使用 Windows 代理收集数据时，可[将每个代理配置为向一个或多个工作区报告](../../azure-monitor/platform/agent-windows.md)。

如果使用 System Center Operations Manager，每个 Operations Manager 管理组仅可以连接一个工作区。 可以在 Operations Manager 管理的计算机上安装 Microsoft Monitoring Agent，并使代理向 Operations Manager 和不同 Log Analytics 工作区报告。

定义工作区体系结构后，应强制实施此策略通过 Azure 资源[Azure 策略](../../governance/policy/overview.md)。 这可以提供内置的定义会自动将应用于所有 Azure 资源。 例如，可以设置策略来确保某一特定区域中的所有 Azure 资源发送到特定的工作区及其所有诊断日志。

## <a name="view-workspace-details"></a>查看工作区详细信息
从在 Log Analytics 工作区中分析数据时**Azure Monitor** Azure 门户菜单中的，创建和管理中的工作区**Log Analytics 工作区**菜单。
 

1. 登录到 [Azure 门户](https://portal.azure.com)，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。  

    ![Azure 门户](media/manage-access/azure-portal-01.png)  

3. 从列表中选择你的工作区。

4. 工作区页将显示有关工作区、 入门、 配置和其他信息链接的详细信息。  

    ![工作区详细信息](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>工作区的权限和作用域
由下表中列出的多个因素决定用户有权访问的数据。 在以下各节介绍每。

| 因素 | 描述 |
|:---|:---|
| [访问模式](#access-modes) | 在用户使用到的方法访问工作区。  定义可用数据的作用域和应用的访问控制模式。 |
| [访问控制模式](#access-control-mode) | 设置定义是否在工作区或资源级别应用权限的工作区。 |
| [权限](#manage-accounts-and-users) | 应用于单个或一组工作区或资源的用户的权限。 定义哪些数据用户将有权访问。 |
| [表级别 RBAC](#table-level-rbac) | 适用于所有用户，而不考虑其访问模式或访问控件模式的可选粒度权限。 定义用户可以访问哪些数据类型。 |



## <a name="access-modes"></a>访问模式
_访问模式_指的是用户如何访问 Log Analytics 工作区，并定义他们可以访问的数据的作用域。 

**工作区以中心**:在此模式下，用户可以查看其有权在工作区中的所有日志。 在此模式下的查询的作用域中的工作区中的所有表的所有数据。 这是与工作区作为作用域，例如当您选择访问日志时使用的访问模式**日志**从**Azure Monitor**在 Azure 门户中的菜单。

**资源中心**:当访问特定资源，例如当您选择的工作区**日志**从 Azure 门户中的资源菜单中，可以仅该资源的日志查看有权访问的所有表中。 在此模式下的查询的作用域仅与该资源相关联的数据。 此模式还可以精细的基于角色的访问控制 (RBAC)。 

> [!NOTE]
> 日志是可用于为中心的查询，仅当它们是相关的资源与正确关联。 目前，以下资源提供了限制： 
> - Azure 外部的计算机
> - Service Fabric
> - Application Insights
> - 容器
> - 创建的 HTTP 数据收集器 API 的自定义日志
>
> 如果通过运行查询其资源与正确关联的日志，以及检查记录你感兴趣，可以进行测试。 如果正在正确的资源 ID [_ResourceId](log-standard-properties.md#_resourceid)属性，则数据可供资源为中心的查询。

### <a name="comparing-access-modes"></a>比较访问模式

下表总结了访问模式：

| | 为工作区的中心 | 资源中心 |
|:---|:---|:---|
| 每个模型适用于谁？ | 管理中心。 管理员需要配置数据收集和用户需要访问各种资源。 此外必须具有的用户需要访问位于 Azure 之外的资源的日志。 | 应用程序团队。 正在监视的 Azure 资源的管理员。 |
| 哪些用户需要查看日志？ | 工作区的权限。 请参阅**工作区的权限**中[管理帐户和用户](#manage-accounts-and-users)。 | 访问资源的读取权限。 请参阅**资源的权限**中[管理帐户和用户](#manage-accounts-and-users)。 权限可以是继承 （如从包含的资源组） 的还是直接分配给资源。 将自动分配到资源的日志的权限。 |
| 权限的作用域是什么？ | 工作区。 具有访问工作区的用户可以查询表中其有权该工作区中的所有日志。 请参阅[表访问控制](#table-access-control) | Azure 资源。 用户可以查询日志的资源，它们有权访问在任意工作区但不能查询日志的其他资源。 |
| 如何用户访问日志？ | 启动**日志**从**Azure Monitor**菜单或**Log Analytics 工作区**。 | 启动**日志**从菜单中为 Azure 资源。 |


## <a name="access-control-mode"></a>访问控制模式
_访问控制模式_是每个工作区设置，它定义如何为该工作区中确定权限。

**需要工作区的权限**:此控件模式下不允许精细 RBAC。 若要访问工作区的用户，他们必须授予到工作区或对特定表的权限。 

如果用户访问的工作区中的工作区为中心模式时，它们将有权访问的所有数据已被授予访问权限的任何表。 如果用户访问的工作区中为中心的模式，则会访问该资源仅数据已被授予访问权限的任何表中。

这是在 2019 年 3 月之前创建的所有工作区的默认设置。

**使用资源或工作区权限**:此控件模式允许精细 RBAC。 用户被授予仅与他们可以查看通过 Azure 权限，他们可以为其资源的资源关联的数据的访问权限`read`权限。 

当用户访问工作区为中心模式中的工作区时，将应用工作区的权限。 当用户访问的工作区中为中心的模式下时，将验证仅资源权限，并且工作区的权限将被忽略。 通过从工作区权限中删除它们，并允许其资源的权限被识别为用户启用 RBAC。

这是 2019 年 3 月之后创建的所有工作区的默认设置。

> [!NOTE]
> 如果用户仅具有资源权限到工作区，他们将只能访问工作区中使用[为中心模式](#access-modes)。


### <a name="define-access-control-mode-in-azure-portal"></a>在 Azure 门户中定义的访问控制模式
您可以查看当前工作区访问控件模式上**概述**页中的工作区**Log Analytics 工作区**菜单。

![查看工作区的访问控制模式](media/manage-access/view-access-control-mode.png)

您可以更改此设置在**属性**工作区的页。 如果你没有权限配置工作区，将禁用更改的设置。

![更改工作区的访问模式](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>在 PowerShell 中定义的访问控制模式

使用以下命令检查该订阅中的所有工作区的访问控制模式：

```PowerShell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

使用以下脚本设置特定的工作区的访问控制模式：

```PowerShell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用以下脚本在订阅中设置的所有工作区的访问控制模式

```PowerShell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>在资源管理器模板中定义的访问模式
若要在 Azure 资源管理器模板中配置的访问模式，设置**enableLogAccessUsingOnlyResourcePermissions**功能标志上的工作区为以下值之一。

- **False**:将工作区设置为工作区为中心的权限。 这是默认设置，如果未设置该标志。
- **True**:将工作区设置为中心的权限。


## <a name="manage-accounts-and-users"></a>管理帐户和用户
应用于特定用户对工作区的权限由其访问模式和[访问控制模式](#access-control-mode)工作区。 **工作区的权限**用户访问任何工作区使用时应用**工作区以中心**中[工作区为中心模式](#access-modes)。 **资源的权限**当用户访问工作区的应用**使用资源或工作区的权限**[访问控件模式](#access-control-mode)使用[资源为中心模式](#access-modes).

### <a name="workspace-permissions"></a>工作区权限
每个工作区可有多个与其关联的帐户，每个帐户可访问多个工作区。 通过 [Azure 基于角色的访问权限](../../role-based-access-control/role-assignments-portal.md)来管理访问权限。 


以下活动也需要 Azure 权限：

| 操作                                                          | 所需 Azure 权限 | 说明 |
|-----------------------------------------------------------------|--------------------------|-------|
| 添加和删除监视解决方案                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 需要在资源组或订阅级别授予这些权限。 |
| 更改定价层                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 查看*备份* 和 *Site Recovery* 解决方案磁贴中的数据 | 管理员/共同管理员 | 访问通过经典部署模型部署的资源 |
| 在 Azure 门户中创建工作区                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>管理对 Log Analytics 工作区使用 Azure 权限的访问 
若要使用 Azure 权限授予对 Log Analytics 工作区的访问权限，请执行[使用角色分配来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)中的步骤。

Azure 有两个适用于 Log Analytics 工作区的内置用户角色：
- Log Analytics 读者
- Log Analytics 参与者

Log Analytics 读者角色的成员可以：
- 查看和搜索所有监视数据 
- 查看监视设置，包括查看 Azure 诊断在所有 Azure 资源上的配置。

Log Analytics 读者角色包括以下 Azure 操作：

| Type    | 权限 | 描述 |
| ------- | ---------- | ----------- |
| 操作 | `*/read`   | 能够查看所有 Azure 资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置 |
| 操作 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 能够执行 Log Search v2 查询 |
| 操作 | `Microsoft.OperationalInsights/workspaces/search/action` | 能够执行 Log Search v1 查询 |
| 操作 | `Microsoft.Support/*` | 能够打开支持案例 |
|非操作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止读取工作区密钥，该密钥是使用数据集合 API 和安装代理所必需的。 这可以防止用户向工作区添加新资源 |


Log Analytics 参与者角色的成员可以：
- 像 Log Analytics 读者一样读取所有监视数据  
- 创建和配置自动化帐户  
- 添加和删除管理解决方案    
    > [!NOTE] 
    > 若要成功执行最后两个操作，需要在资源组或订阅级别授予此权限。  

- 读取存储帐户密钥   
- 从 Azure 存储配置日志集合  
- 编辑 Azure 资源的监视设置，包括
  - 将 VM 扩展添加到 VM
  - 在所有 Azure 资源上配置 Azure 诊断

> [!NOTE] 
> 可以使用此功能向虚拟机添加虚拟机扩展，获取对虚拟机的完全控制。

Log Analytics 参与者角色包括以下 Azure 操作：

| 权限 | 描述 |
| ---------- | ----------- |
| `*/read`     | 能够查看所有资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置 |
| `Microsoft.Automation/automationAccounts/*` | 能够创建和配置 Azure 自动化帐户，包括添加和编辑 runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 添加、更新和删除虚拟机扩展，包括 Microsoft Monitoring Agent 扩展和 OMS Agent for Linux 扩展 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 查看存储帐户密钥。 将 Log Analytics 配置为从 Azure 存储帐户读取日志所必需 |
| `Microsoft.Insights/alertRules/*` | 添加、更新和删除警报规则 |
| `Microsoft.Insights/diagnosticSettings/*` | 添加、更新和删除 Azure 资源上的诊断设置 |
| `Microsoft.OperationalInsights/*` | 添加、更新和删除 Log Analytics 工作区的配置 |
| `Microsoft.OperationsManagement/*` | 添加和删除管理解决方案 |
| `Microsoft.Resources/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |

若要添加和删除用户角色的用户，必须拥有 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 权限。

使用这些角色为用户提供不同范围的访问权限：
- 订阅 - 访问订阅中的所有工作区
- 资源组 - 访问资源组中的所有工作区
- 资源 - 仅访问指定工作区

您应该执行分配在资源级别 （工作区），以确保准确的访问控制。  使用[自定义角色](../../role-based-access-control/custom-roles.md)，创建具有所需的特定权限的角色。

### <a name="resource-permissions"></a>资源的权限 
当用户查询记录从工作区使用为中心的访问权限时，它们将具有资源的以下权限：

| 权限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>示例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 若要查看资源的所有日志数据的功能。  |


通常包括在角色中授予此权限 _\*/读取或_ _\*_ 如内置权限[读取器](../../role-based-access-control/built-in-roles.md#reader)和[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色。 请注意，自定义角色，其中包含特定操作或专用的内置角色可能不包括此权限。

请参阅[定义每个表的访问控制](#defining-per-table-access-control)下面如果你想要创建不同的表的不同的访问控制。


## <a name="table-level-rbac"></a>表级别 RBAC
**表级别 RBAC**使您可以提供更精细的控制，除了其他权限的 Log Analytics 工作区中的数据。 此控件，可定义仅供一组特定的用户访问的特定数据类型。

实现使用的表访问控制[Azure 自定义角色](../../role-based-access-control/custom-roles.md)以授予或拒绝对特定于访问[表](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized)工作区中。 这些角色应用到与工作区为中心或资源以为中心的工作区[访问控件模式](#access-control-modes)而不考虑用户的[访问模式](#access-mode)。

创建[自定义角色](../../role-based-access-control/custom-roles.md)以下操作可用于定义访问权限的表访问控制。

- 若要授予对表的访问，将其包含在**操作**角色定义的部分。
- 若要拒绝对表的访问，将其包含在**NotActions**角色定义的部分。
- 使用 * 指定的所有表。

例如，若要创建具有访问权限的角色_检测信号_并_AzureActivity_表，创建自定义角色，使用以下操作：

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要创建一个角色具有访问权限仅_SecurityBaseline_和任何其他表，创建自定义角色，使用以下操作：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
```

### <a name="custom-logs"></a>自定义日志
 通过自定义日志和 HTTP 数据收集器 API 等数据源创建自定义日志。 标识的日志类型的最简单方法是通过检查下所列的表[日志架构中的自定义日志](../log-query/get-started-portal.md#understand-the-schema)。

 您当前不能授予或拒绝访问各个自定义日志，但您可以授予或拒绝对所有自定义日志的访问。 若要创建一个角色有权访问所有自定义日志，请创建自定义角色，使用以下操作：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>注意事项

- 如果用户被授予全球读取权限与标准的读取者或参与者角色，包括 _\*/读取_操作，它将替代的每个表的访问控制和授予其对所有日志数据的访问。
- 如果用户被授予访问每个表，但没有其他任何权限，他们就能从 API，但不能从 Azure 门户访问日志数据。 若要提供对 Azure 门户的访问，请作为其基角色使用 Log Analytics 读者。
- 订阅管理员将有权访问所有数据类型，而不考虑任何其他权限设置。
- 工作区所有者一样为每个表的访问控制的任何其他用户。
- 应将角色分配给安全组而不是单个用户来减少分配的数。 这将帮助您使用现有的组管理工具来配置和验证的访问权限。




## <a name="next-steps"></a>后续步骤
* 请参阅 [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)，以从数据中心或其他云环境中的计算机收集数据。
* 请参阅[收集有关 Azure 虚拟机的数据](../../azure-monitor/learn/quick-collect-azurevm.md)，以配置 Azure VM 的数据收集。  

