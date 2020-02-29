---
title: Azure Monitor Azure AD 的权利管理存档 & 报表
description: 了解如何在 Azure Active Directory 授权管理中存档日志并创建具有 Azure Monitor 的报表。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/27/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f89af42e32783de479c4302b19c0a7ddc1289bb8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202177"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>存档日志和报告，Azure AD 中的权利管理 Azure Monitor

Azure AD 会在审核日志中存储最多30天的审核事件。 但是，你可以将审核数据的保留时间超过默认保留期，如[Azure AD 存储报表数据的时间长度](../reports-monitoring/reference-reports-data-retention.md)所述，将其路由到 Azure 存储帐户或使用 Azure Monitor。 然后，您可以对此数据使用工作簿和自定义查询和报表。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>配置要使用的 Azure AD Azure Monitor
使用 Azure Monitor 工作簿之前，必须配置 Azure AD 将其审核日志的副本发送到 Azure Monitor。

存档 Azure AD 审核日志要求在 Azure 订阅中 Azure Monitor。 你可以[在 Azure Monitor 中详细了解在 Azure AD 活动日志](../reports-monitoring/concept-activity-logs-azure-monitor.md)中使用 Azure Monitor 的先决条件和预估成本。

**必备角色**：全局管理员

1. 以全局管理员用户身份登录到 Azure 门户。请确保有权访问包含 "Azure Monitor" 工作区的资源组。
 
1. 选择**Azure Active Directory**然后在左侧导航菜单中单击 "监视" 下的 "**诊断设置**"。 检查是否已存在将审核日志发送到该工作区的设置。

1. 如果尚未设置，请单击 "**添加诊断设置**"。 使用将[Azure AD 日志与 Azure Monitor 日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor)一文中的说明，将 Azure AD 审核日志发送到 Azure Monitor 工作区。

    ![诊断设置窗格](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 将日志发送到 Azure Monitor 后，选择 " **Log Analytics 工作区**"，然后选择包含 Azure AD 审核日志的工作区。

1. 选择 "**使用情况和估计成本**"，然后单击 "**数据保留**"。 将滑块更改为要保留数据的天数，以满足审核要求。

    ![Log Analytics 工作区窗格](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>使用 Azure 门户创建自定义 Azure Monitor 查询
你可以创建你自己的查询 Azure AD 审核事件，包括权利管理事件。  

1. 在 Azure 门户 Azure Active Directory 中，在左侧导航菜单中单击 "监视" 部分下的 "**日志**"，以创建新的查询页。

1. 工作区应显示在 "查询" 页的左上角。 如果有多个 Azure Monitor 工作区，并且用于存储 Azure AD 审核事件的工作区未显示，请单击 "**选择作用域**"。 然后选择正确的 "订阅" 和 "工作区"。

1. 接下来，在 "查询文本" 区域中，删除字符串 "search *" 并将其替换为以下查询：

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 然后单击 **“运行”** 。 

    ![单击 "运行" 以启动查询](./media/entitlement-management-logs-and-reporting/run-query.png)

默认情况下，该表将显示最近一小时内的资格管理的审核日志事件。 您可以更改 "时间范围" 设置以查看较旧的事件。 但是，更改此设置将只显示在将事件发送到 Azure Monitor Azure AD 之后发生的事件。

如果要了解 Azure Monitor 中包含的最早和最新审核事件，请使用以下查询：

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

有关为 Azure Monitor 中的审核事件存储的列的详细信息，请参阅[Azure Monitor 中的解释 Azure AD 审核日志架构](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>使用 Azure PowerShell 创建自定义 Azure Monitor 查询

将 Azure AD 配置为将日志发送到 Azure Monitor 后，可以通过 PowerShell 访问日志。 然后，从脚本或 PowerShell 命令行发送查询，无需成为租户中的全局管理员。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>确保用户或服务主体的角色分配正确

请确保要 Azure AD 的身份验证用户或服务主体在 Log Analytics 工作区中处于适当的 Azure 角色中。 角色选项 Log Analytics 读取器或 Log Analytics 参与者。 如果你已在这些角色之一中，请跳到[检索包含一个 Azure 订阅 LOG ANALYTICS ID](#retrieve-log-analytics-id-with-one-azure-subscription)。

若要设置角色分配并创建查询，请执行以下步骤：
1. 在 Azure 门户中，找到[Log Analytics 工作区](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)。

1. 选择“访问控制 (IAM)”。

1. 然后单击 "**添加**" 以添加角色分配。

    ![添加角色分配](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>安装 Azure PowerShell 模块

完成适当的角色分配后，请通过键入以下内容，启动 PowerShell 并[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps?view=azps-3.3.0)（如果尚未安装）：

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
现在，你已准备好对 Azure AD 进行身份验证，并检索所查询 Log Analytics 工作区的 id。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>检索包含一个 Azure 订阅 Log Analytics ID
如果你只有一个 Azure 订阅和一个 Log Analytics 工作区，则键入以下内容以对 Azure AD 进行身份验证，连接到该订阅，并检索该工作区：
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>检索具有多个 Azure 订阅 Log Analytics ID

 [AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)一次在一个订阅中运行。 如果有多个 Azure 订阅，则需要确保连接到具有 Azure AD 日志的 "Log Analytics" 工作区的订阅。 
 
 以下 cmdlet 显示订阅列表，并查找具有 "Log Analytics" 工作区的订阅的 id：
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
你可以使用命令（如 `Connect-AzAccount –Subscription $subs[0].id`）对 PowerShell 会话进行身份验证并将其与该订阅关联。 若要详细了解如何从 PowerShell 对 Azure 进行身份验证，包括非交互，请参阅[利用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)。

如果在该订阅中有多个 Log Analytics 工作区，则 cmdlet [AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)返回工作区列表。 然后，可以找到具有 Azure AD 日志的日志。 此 cmdlet 返回的 `CustomerId` 字段与 Log Analytics 工作区概述的 Azure 门户中显示的 "工作区 id" 的值相同。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>将查询发送到 Log Analytics 工作区
最后，一旦确定了工作区，就可以使用[AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
)将 Kusto 查询发送到该工作区。 这些查询以[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)编写。
 
例如，可以从 "Log Analytics" 工作区中检索审核事件记录的日期范围，使用 PowerShell cmdlet 来发送查询，如下所示：
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

你还可以使用类似如下的查询来检索权限管理事件：

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>后续步骤：
- [Azure Monitor 工作簿创建交互式报表](../../azure-monitor/app/usage-workbooks.md) 

