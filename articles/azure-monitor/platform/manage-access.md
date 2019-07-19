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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254461"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>管理 Azure Monitor 中的日志数据和工作区
Azure Monitor 将日志数据存储在 Log Analytics 工作区中，该工作区本质上是一个包含数据和配置信息的容器。 若要管理对日志数据的访问，需要对工作区执行各种管理任务。 你或组织中的其他成员可以使用多个工作区，管理收集自所有或部分 IT 基础结构的不同数据集。

本文介绍如何管理对日志的访问，以及如何管理包含日志的工作区。 

## <a name="create-a-workspace"></a>创建工作区
若要创建 Log Analytics 工作区，需要：

1. 一个 Azure 订阅。
2. 选择工作区名称。
3. 将工作区与订阅和资源组之一相关联。
4. 选择地理位置。

有关创建工作区的详细信息，请参阅以下文章：

- [在 Azure 门户中创建 Log Analytics 工作区](../learn/quick-create-workspace.md)
- [使用 Azure CLI 2.0 创建 Log Analytics 工作区](../learn/quick-create-workspace-cli.md)
- [使用 Azure PowerShell 创建 Log Analytics 工作区](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>确定所需工作区数
Log Analytics 工作区是一种 Azure 资源，也是 Azure Monitor 中收集、聚合、分析和呈现数据的容器。 你可以为每个 Azure 订阅创建多个工作区，并且你可以访问多个工作区，并且可以轻松查询这些工作区。 本部分介绍有助于创建多个工作区的情况。

Log Analytics 工作区可提供：

* 数据存储的地理位置。
* 数据隔离，用于定义以工作区为中心的模式下的不同用户访问权限。 在以资源为中心的模式下操作时，此功能没有作用。
* 设置配置的范围，如[定价层级](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)和[数据上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)。
* 与数据引入和保留相关的费用是根据工作区资源计收的。

从使用角度来看，我们建议尽量减少创建的工作区数量。 这可带来更轻松快捷的管理和查询体验。 但是，基于上述特征，在以下情况下可能需要创建多个工作区：

* 贵公司是全球性公司，因数据所有权和合规性需要将日志数据存储于特定区域。
* 正在使用 Azure，并希望通过让工作区与它所管理的 Azure 资源位于同一区域，避免产生出站数据传输费用。
* 公司是托管服务提供商，需要为所管理的每位客户单独保留 Log Analytics 数据，即与其他客户的数据分开保存。
* 你要管理多个客户，并希望每个客户/部门/业务组能够看到自己的数据，但看不到其他人的数据；没有在整个客户/部门/业务组中整合视图的业务需求。

使用 Windows 代理收集数据时，可[将每个代理配置为向一个或多个工作区报告](../../azure-monitor/platform/agent-windows.md)。

如果使用 System Center Operations Manager，每个 Operations Manager 管理组仅可以连接一个工作区。 可以在 Operations Manager 管理的计算机上安装 Microsoft Monitoring Agent，并使代理向 Operations Manager 和不同 Log Analytics 工作区报告。

定义工作区体系结构后，应使用 [Azure Policy](../../governance/policy/overview.md) 对 Azure 资源强制实施此策略。 这可以提供自动应用于所有 Azure 资源的内置定义。 例如，可以设置一个策略，以确保特定区域中的所有 Azure 资源都将其所有诊断日志发送到特定工作区。

## <a name="view-workspace-details"></a>查看工作区详细信息
通过 Azure 门户中的“Azure Monitor”  菜单分析 Log Analytics 工作区中的数据时，可以在“Log Analytics 工作区”  菜单中创建和管理工作区。
 

1. 登录到 [Azure 门户](https://portal.azure.com)，单击“所有服务”  。 在资源列表中，键入“Log Analytics”  。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”  。  

    ![Azure 门户](media/manage-access/azure-portal-01.png)  

3. 从列表中选择你的工作区。

4. 工作区页会显示有关工作区、入门、配置的详细信息，以及其他信息的链接。  

    ![工作区详细信息](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>工作区权限和范围
用户有权访问的数据由下表中列出的多种因素决定。 后续部分会描述每种因素。

| 因素 | 描述 |
|:---|:---|
| [访问模式](#access-modes) | 用户访问工作区的方法。  定义可用数据的范围，以及应用的访问控制模式。 |
| [访问控制模式](#access-control-mode) | 工作区中的设置，用于定义是要在工作区级别还是资源级别应用权限。 |
| [权限](#manage-accounts-and-users) | 应用到工作区或资源的个人用户或用户组的权限。 定义用户有权访问哪些数据。 |
| [表级 RBAC](#table-level-rbac) | 应用到所有用户（无论他们使用的是访问模式还是访问控制模式）的可选精细权限。 定义用户可以访问哪些数据类型。 |



## <a name="access-modes"></a>访问模式
访问模式是指用户如何访问 Log Analytics 工作区，并定义他们有权访问的数据范围  。 

**以工作区为中心**：在此模式下，用户可以查看他们有权访问的工作区中的所有日志。 在此模式下，只能查询该工作区中所有表内的所有数据。 使用工作区作为范围来访问日志时（例如，在 Azure 门户上的“Azure Monitor”菜单中选择“日志”时），将使用此访问模式   。

**以资源为中心**：访问特定资源的工作区时（例如，在 Azure 门户上的资源菜单中选择“日志”时），只能查看所有表中你有权访问的资源的日志  。 在此模式下，只能查询与该资源关联的数据。 此模式还会启用精细的基于角色的访问控制 (RBAC)。 

> [!NOTE]
> 仅当日志已适当地关联到相关资源时，才能对日志进行以资源为中心的查询。 目前，以下资源存在限制： 
> - Azure 外部的计算机
> - Service Fabric
> - Application Insights
> - 容器
>
> 可以通过运行一个查询并检查所需的记录，来测试日志是否已适当关联到其资源。 如果 [_ResourceId](log-standard-properties.md#_resourceid) 属性中包含正确的资源 ID，则可以对数据进行以资源为中心的查询。

### <a name="comparing-access-modes"></a>比较访问模式

下表汇总了访问模式：

| | 以工作区为中心 | 以资源为中心 |
|:---|:---|:---|
| 每种模式适合哪类用户？ | 集中管理。 需要配置数据收集的管理员，以及需要访问各种资源的用户。 此外，必须访问 Azure 外部资源的日志的用户目前也需要使用此模式。 | 应用程序团队。 受监视 Azure 资源的管理员。 |
| 用户需要哪些权限才能查看日志？ | 对工作区的权限。 请参阅[管理帐户和用户](#manage-accounts-and-users)中的“工作区权限”。  | 对资源的读取访问权限。 请参阅[管理帐户和用户](#manage-accounts-and-users)中的“资源权限”。  权限可以继承（例如，从包含资源组继承），也可以直接分配给资源。 系统会自动分配对资源日志的权限。 |
| 权限范围是什么？ | 工作区。 有权访问工作区的用户可以通过他们有权访问的表查询该工作区中的所有日志。 请参阅[表访问控制](#table-level-rbac) | Azure 资源。 用户可以通过任何工作区查询他们有权访问的资源的日志，但无法查询其他资源的日志。 |
| 用户如何访问日志？ | 从“Azure Monitor”菜单或“Log Analytics 工作区”启动“日志”。    | 从 Azure 资源的菜单启动“日志”。  |


## <a name="access-control-mode"></a>访问控制模式
访问控制模式是每个工作区中的一项设置，定义如何确定该工作区的权限。 

**需要工作区权限**：此控制模式不允许精细的 RBAC。 用户若要访问工作区，必须获得对该工作区或特定表的权限。 

如果用户使用以工作区为中心的模式访问工作区，将可以访问他们有权访问的任何表中的所有数据。 如果用户使用以资源为中心的模式访问工作区，只能访问他们有权访问的任何表中的此类资源的数据。

这是在 2019 年 3 月之前创建的所有工作区的默认设置。

**使用资源或工作区权限**：此控制模式允许精细的 RBAC。 用户只有权访问他们可以通过 Azure 权限查看的资源的关联数据，他们对这些资源拥有 `read` 权限。 

当用户使用以工作区为中心的模式访问工作区时，将应用工作区权限。 当用户使用以资源为中心的模式访问工作区时，只会验证资源权限，而会忽略工作区权限。 要为用户启用 RBAC，可将其从工作区权限中删除，并允许识别其资源权限。

这是在 2019 年 3 月之后创建的所有工作区的默认设置。

> [!NOTE]
> 如果用户只对工作区拥有资源权限，则他们只能使用[以资源为中心的模式](#access-modes)访问工作区。


### <a name="define-access-control-mode-in-azure-portal"></a>在 Azure 门户中定义访问控制模式
可以在工作区“概述”页上的“Log Analytics 工作区”菜单中查看当前的工作区访问控制模式。  

![查看工作区访问控制模式](media/manage-access/view-access-control-mode.png)

可以在工作区的“属性”页上更改此设置。  如果你无权配置工作区，则会禁止更改此设置。

![更改工作区访问模式](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>在 PowerShell 中定义访问控制模式

使用以下命令检查订阅中所有工作区的访问控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

使用以下脚本设置特定工作区的访问控制模式：

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用以下脚本为订阅中的所有工作区设置访问控制模式

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>在资源管理器模板中定义访问模式
若要在 Azure 资源管理器模板中配置访问模式，请将工作区中的 **enableLogAccessUsingOnlyResourcePermissions** 功能标志设置为以下值之一。

- **false**：将工作区设置为以工作区为中心的权限。 如果未设置该标志，则这是默认设置。
- **true**：将工作区设置为以资源为中心的权限。


## <a name="manage-accounts-and-users"></a>管理帐户和用户
应用到特定用户的工作区权限由该用户的访问模式以及工作区的[访问控制模式](#access-control-mode)定义。 当用户在[以工作区为中心的模式](#access-modes)下访问使用**以工作区为中心**的任何工作区时，将应用**工作区权限**。 当用户在[以资源为中心的模式](#access-modes)下，使用“使用资源或工作区权限”[访问控制模式](#access-control-mode)访问工作区时，将应用**资源权限**。 

### <a name="workspace-permissions"></a>工作区权限
每个工作区可有多个与其关联的帐户，每个帐户可访问多个工作区。 通过 [Azure 基于角色的访问权限](../../role-based-access-control/role-assignments-portal.md)来管理访问权限。 


以下活动也需要 Azure 权限：

||Action |所需 Azure 权限 |说明 |
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



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>使用 Azure 权限管理对 Log Analytics 工作区的访问 
若要使用 Azure 权限授予对 Log Analytics 工作区的访问权限，请执行[使用角色分配来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)中的步骤。

Azure 有两个适用于 Log Analytics 工作区的内置用户角色：
- Log Analytics 读者
- Log Analytics 参与者

 Log Analytics 读者角色的成员可以：
- 查看和搜索所有监视数据 
- 查看监视设置，包括查看 Azure 诊断在所有 Azure 资源上的配置。

Log Analytics 读者角色包括以下 Azure 操作：

| 类型    | 权限 | 描述 |
| ------- | ---------- | ----------- |
| Action | `*/read`   | 能够查看所有 Azure 资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区, 它允许完全无限制的权限来读取工作区设置并对数据执行查询。 请参阅上面更细化的选项。 |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 不推荐使用, 无需将其分配给用户。 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | 不推荐使用, 无需将其分配给用户。 |
| Action | `Microsoft.Support/*` | 能够打开支持案例 |
|非操作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止读取工作区密钥，该密钥是使用数据集合 API 和安装代理所必需的。 这可以防止用户向工作区添加新资源 |


 Log Analytics 参与者角色的成员可以：
- 像 Log Analytics 读者一样读取所有监视数据  
- 创建和配置自动化帐户  
- 添加和删除管理解决方案    
    > [!NOTE] 
    > 若要成功执行最后两个操作，需要在资源组或订阅级别授予此权限。  

- 读取存储帐户密钥   
- 从 Azure 存储配置日志收集  
- 编辑 Azure 资源的监视设置，包括
  - 将 VM 扩展添加到 VM
  - 在所有 Azure 资源上配置 Azure 诊断

> [!NOTE] 
> 可以使用此功能向虚拟机添加虚拟机扩展，获取对虚拟机的完全控制。

Log Analytics 参与者角色包括以下 Azure 操作：

| 权限 | 描述 |
| ---------- | ----------- |
| `*/read`     | 能够查看所有 Azure 资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置。 <br> 对于工作区, 它允许完全无限制的权限来读取工作区设置并对数据执行查询。 请参阅上面更细化的选项。 |
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
- 订阅 - 访问订阅中的所有工作区
- 资源组 - 访问资源组中的所有工作区
- 资源 - 仅访问指定工作区

应在资源（工作区）级别执行分配，以确保准确的访问控制。  使用[自定义角色](../../role-based-access-control/custom-roles.md)，创建具有所需的特定权限的角色。

### <a name="resource-permissions"></a>资源权限 
当用户使用以资源为中心的访问权限查询工作区中的日志时，他们对资源拥有以下权限：

| 权限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>示例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 可以查看资源的所有日志数据。  |
| `Microsoft.Insights/diagnosticSettings/write ` | 能够将诊断设置配置为允许设置此资源的日志。 |

此权限通常是从含有 _\*/read 或_ _\*_ 权限的角色授予的，例如内置的[读取者](../../role-based-access-control/built-in-roles.md#reader)和[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色。 请注意，含有特定操作的自定义角色或专用内置角色可能没有此权限。

若要针对不同的表创建不同的访问控制，请参阅下面的[定义按表进行的访问控制](#table-level-rbac)。


## <a name="table-level-rbac"></a>表级 RBAC
使用**表级 RBAC** 可以针对 Log Analytics 工作区中的数据提供更精细的控制，此外还能分配其他权限。 使用此控制措施可以定义仅供特定一组用户访问的特定数据类型。

使用 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)实现表访问控制，以授予或拒绝对工作区中特定[表](../log-query/logs-structure.md)的访问权限。 无论用户的[访问模式](#access-modes)是什么，这些角色都会应用到使用以工作区为中心或者以资源为中心[访问控制模式](#access-control-mode)的工作区。

使用以下操作创建[自定义角色](../../role-based-access-control/custom-roles.md)，以定义对表访问控制的访问权限。

- 若要授予对某个表的访问权限，请将该表包含在角色定义的 **Actions** 节中。
- 若要拒绝对某个表的访问权限，请将该表包含在角色定义的 **NotActions** 节中。
- 使用 * 可指定所有表。

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
 自定义日志是由自定义日志和 HTTP 数据收集器 API 等数据源创建的。 识别日志类型的最简单方法是查看[日志架构中的自定义日志](../log-query/get-started-portal.md#understand-the-schema)下所列的表。

 目前无法授予或拒绝对单个自定义日志的访问权限，但可以授予或拒绝对所有自定义日志的访问权限。 若要创建一个有权访问所有自定义日志的角色，请使用以下操作创建自定义角色：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>注意事项

- 如果为某个用户授予全局读取权限以及含有 _\*/read_ 操作的标准“读取者”或“参与者”角色，则会替代按表进行的访问控制，并向该用户授予所有日志数据的访问权限。
- 如果为某个用户授予按表访问权限但未授予其他任何权限，则该用户可以通过 API 访问日志数据，但不能通过 Azure 门户进行访问。 若要提供对 Azure 门户的访问权限，请使用“Log Analytics 读取者”作为用户的基本角色。
- 无论其他任何权限设置如何，订阅管理员都有权访问所有数据类型。
- 应用按表进行的访问控制时，工作区所有者被视为类似于其他任何用户。
- 应该将角色分配到安全组而不是个人用户，以减少分配数目。 这还有助于使用现有的组管理工具来配置和验证访问权限。




## <a name="next-steps"></a>后续步骤
* 请参阅 [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)，以从数据中心或其他云环境中的计算机收集数据。
* 请参阅[收集有关 Azure 虚拟机的数据](../../azure-monitor/learn/quick-collect-azurevm.md)，以配置 Azure VM 的数据收集。  

