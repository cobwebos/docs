---
title: 使用 Azure Monitor 进行存档和报告 - Azure AD 权利管理
description: 了解如何在 Azure Active Directory 权利管理中使用 Azure Monitor 存档日志和创建报表。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89d6379f3fa41036836288ed5c75fbdaad0031da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88783807"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>在 Azure AD 权利管理中使用 Azure Monitor 存档日志和进行报告

Azure AD 在审核日志中将审核事件存储最长 30 天。 但是，可以根据 [Azure AD 将报告数据存储多长时间？](../reports-monitoring/reference-reports-data-retention.md)中所述，使审核数据的保留时长超过默认的保留期，只需将其路由到 Azure 存储帐户或使用 Azure Monitor 即可。 然后，可对这些数据使用工作簿和自定义查询，并基于这些数据生成报表。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>将 Azure AD 配置为使用 Azure Monitor
使用 Azure Monitor 工作簿之前，必须将 Azure AD 配置为向 Azure Monitor 发送其审核日志的副本。

存档 Azure AD 审核日志需要在 Azure 订阅中安装 Azure Monitor。 可以在 [Azure Monitor 中的 Azure AD 活动日志](../reports-monitoring/concept-activity-logs-azure-monitor.md)中详细了解使用 Azure Monitor 所要满足的先决条件和预估成本。

**必备角色**：全局管理员

1. 以充当全局管理员的用户身份登录到 Azure 门户。请确保你有权访问包含 Azure Monitor 工作区的资源组。
 
1. 选择“Azure Active Directory”，然后在左侧导航菜单中的“监视”下单击“诊断设置”。  检查是否已有一个用于将审核日志发送到该工作区的设置。

1. 如果没有此类设置，请单击“添加诊断设置”。 按照[将 Azure AD 日志与 Azure Monitor 日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor)一文中的说明，将 Azure AD 审核日志发送到 Azure Monitor 工作区。

    ![诊断设置窗格](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 将日志发送到 Azure Monitor 后，选择“Log Analytics 工作区”，然后选择包含 Azure AD 审核日志的工作区。

1. 选择“使用情况和预估成本”并单击“数据保留”。  根据审核要求，将滑块移到所需的数据保留天数。

    ![“Log Analytics 工作区”窗格](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. 以后若要查看工作区中保留的日期范围，可以使用“存档的日志日期范围”工作簿：  
    
    1. 选择“Azure Active Directory”，然后单击“工作簿”。  
    
    1. 展开“Azure Active Directory 故障排除”部分，然后单击“存档的日志日期范围”。  


## <a name="view-events-for-an-access-package"></a>查看访问包的事件  

若要查看访问包的事件，必须有权访问基础 Azure Monitor 工作区（有关信息，请参阅[在 Azure Monitor 中管理对日志数据和工作区的访问](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions)）并充当以下角色之一： 

- 全局管理员  
- 安全管理员  
- 安全读取者  
- 报告读取者  
- 应用程序管理员  

使用以下过程来查看事件： 

1. 在 Azure 门户中选择“Azure Active Directory”，然后单击“工作簿”。  如果你只有一个订阅，请转到步骤 3。 

1. 如果你有多个订阅，请选择包含工作区的订阅。  

1. 选择名为“访问包活动”的工作簿。 

1. 在该工作簿中选择一个时间范围（如果不确定，请更改为“全部”），然后从所有访问包的下拉列表中，选择在该时间范围内发生了活动的访问包 ID。 随后会显示在所选时间范围内该访问包发生的相关事件。  

    ![查看访问包事件](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    每行包含时间、访问包 ID、操作名称、对象 ID、UPN，以及启动该操作的用户的显示名称。  JSON 中包含更多详细信息。   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>使用 Azure 门户创建自定义 Azure Monitor 查询
你可以对 Azure AD 审核事件（包括权利管理事件）创建自己的查询。  

1. 在 Azure 门户的“Azure Active Directory”中，在左侧导航菜单中的“监视”部分下单击“日志”，以创建新的查询页。

1. 工作区应显示在查询页的左上方。 如果你有多个 Azure Monitor 工作区，而用于存储 Azure AD 审核事件的工作区未显示，请单击“选择范围”。 然后选择正确的订阅和工作区。

1. 接下来，在查询文本区域中，删除字符串“search *”并将其替换为以下查询：

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 然后单击“运行”。 

    ![单击“运行”以启动查询](./media/entitlement-management-logs-and-reporting/run-query.png)

默认情况下，该表会显示权利管理在过去一小时的审核日志事件。 可以更改“时间范围”设置以查看更早的事件。 但是，更改此设置只会显示在将 Azure AD 配置为向 Azure Monitor 发送事件之后发生的事件。

若要了解 Azure Monitor 中保留的最早和最新审核事件，请使用以下查询：

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

有关 Azure Monitor 中为审核事件存储的列的详细信息，请参阅[解释 Azure Monitor 中的 Azure AD 审核日志架构](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>使用 Azure PowerShell 创建自定义 Azure Monitor 查询

将 Azure AD 配置为向 Azure Monitor 发送日志后，可以通过 PowerShell 访问日志。 然后，无需成为租户中的全局管理员，就能通过脚本或 PowerShell 命令行发送查询。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>确保为用户或服务主体分配了正确的角色

确保要向 Azure AD 进行身份验证的用户或服务主体在 Log Analytics 工作区中充当适当的 Azure 角色。 角色选项为“Log Analytics 读取者”或“Log Analytics 参与者”。 如果你已充当这些角色之一，请跳到[检索包含一个 Azure 订阅的 Log Analytics ID](#retrieve-log-analytics-id-with-one-azure-subscription)。

若要设置角色分配并创建查询，请执行以下步骤：

1. 在 Azure 门户中找到“Log Analytics 工作区”。[](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)

1. 选择“访问控制 (IAM)”。

1. 然后单击“添加”以添加角色分配。

    ![添加角色分配](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>安装 Azure PowerShell 模块

分配适当的角色后，启动 PowerShell，然后键入以下命令来[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps?view=azps-3.3.0)（如果尚未安装）：

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
现已准备好向 Azure AD 进行身份验证，并检索要查询的 Log Analytics 工作区的 ID。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>检索包含一个 Azure 订阅的 Log Analytics ID
如果你只有一个 Azure 订阅和一个 Log Analytics 工作区，请键入以下命令以向 Azure AD 进行身份验证，连接到该订阅，然后检索该工作区：
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>检索包含多个 Azure 订阅的 Log Analytics ID

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) 每次在一个订阅中运行。 因此，如果你有多个 Azure 订阅，则需确保连接到包含适当 Log Analytics 工作区（包含 Azure AD 日志的工作区）的订阅。 
 
 以下 cmdlet 显示订阅列表，并查找包含该 Log Analytics 工作区的订阅的 ID：
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
可以使用类似于 `Connect-AzAccount –Subscription $subs[0].id` 的命令向该订阅重新进行身份验证，并将 PowerShell 会话与其关联。 若要详细了解如何通过 PowerShell 在 Azure 中进行身份验证（包括非交互式身份验证），请参阅[使用 Azure PowerShell 进行登录](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)。

如果你在该订阅中有多个 Log Analytics 工作区，则 cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) 会返回工作区列表。 然后，你可以找到包含 Azure AD 日志的工作区。 此 cmdlet 返回的 `CustomerId` 字段与 Azure 门户上 Log Analytics 工作区概览中显示的“工作区 ID”值相同。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>将查询发送到 Log Analytics 工作区
最后，在确定工作区后，可以使用 [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) 将 Kusto 查询发送到该工作区。 这些查询以 [Kusto 查询语言](/azure/kusto/query/)编写。
 
例如，可以使用 PowerShell cmdlet 发送如下所示的查询，从 Log Analytics 工作区检索审核事件记录的日期范围：
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

还可以使用如下所示的查询检索权利管理事件：

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>后续步骤：
- [使用 Azure Monitor 工作簿创建交互式报表](../../azure-monitor/platform/workbooks-overview.md)