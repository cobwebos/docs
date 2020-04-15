---
title: 使用 Azure 监视器存档&报表 - Azure AD 授权管理
description: 了解如何在 Azure 活动目录授权管理中使用 Azure 监视器存档日志和创建报表。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380198"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure 监视器中的存档日志和 Azure AD 授权管理报告

Azure AD 在审核日志中存储审核事件长达 30 天。 但是，通过将审核数据路由到 Azure 存储帐户或使用 Azure 监视器，可以将审核数据保留的时间超过默认保留期（Azure [AD 存储数据多长时间）](../reports-monitoring/reference-reports-data-retention.md)中概述。 然后，您可以使用工作簿、自定义查询和报表来处理此数据。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>将 Azure AD 配置为使用 Azure 监视器
在使用 Azure 监视器工作簿之前，必须配置 Azure AD 以将其审核日志的副本发送到 Azure 监视器。

存档 Azure AD 审核日志需要将 Azure 监视器保留在 Azure 订阅中。 您可以在 Azure 监视器[中的 Azure AD 活动日志](../reports-monitoring/concept-activity-logs-azure-monitor.md)中了解有关使用 Azure 监视的先决条件和估计成本。

**先决条件角色**： 全局管理员

1. 以全局管理员的用户身份登录到 Azure 门户。请确保有权访问包含 Azure 监视器工作区的资源组。
 
1. 选择**Azure 活动目录**，然后单击左侧导航菜单中的"监视"下的 **"诊断设置**"。 检查是否已设置将审核日志发送到该工作区。

1. 如果尚未设置，请单击"**添加诊断设置**"。 使用文章中的说明[将 Azure AD 日志与 Azure 监视器日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor)，将 Azure AD 审核日志发送到 Azure 监视器工作区。

    ![诊断设置窗格](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 将日志发送到 Azure 监视器后，选择**日志分析工作区**，然后选择包含 Azure AD 审核日志的工作区。

1. 选择**使用情况和估计成本**，然后单击 **"数据保留**"。 将滑块更改为要保留数据的天数，以满足审核要求。

    ![日志分析工作区窗格](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. 稍后，要查看工作区中保留的日期范围，可以使用*存档日志日期范围*工作簿：  
    
    1. 选择**Azure 活动目录**，然后单击**工作簿**。 
    
    1. 展开**Azure 活动目录故障排除**部分，然后单击 **"存档日志日期范围**"。 


## <a name="view-events-for-an-access-package"></a>查看访问包的事件  

要查看访问包的事件，您必须有权访问基础 Azure 监视器工作区（请参阅[管理 Azure 监视器中日志数据和工作区的访问](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)）以及以下角色之一： 

- 全局管理员  
- 安全管理员  
- 安全读取者  
- 报告阅读器  
- 应用程序管理员  

使用以下过程查看事件： 

1. 在 Azure 门户中，选择**Azure 活动目录**，然后单击**工作簿**。 如果只有一个订阅，则继续执行步骤 3。 

1. 如果您有多个订阅，请选择包含工作区的订阅。  

1. 选择名为 *"访问包活动*"的工作簿。 

1. 在该工作簿中，选择时间范围（如果不确定，则更改为**全部**），并从该时间范围内具有活动的所有访问包的下拉列表中选择访问包 ID。 将显示与选定时间范围内发生的访问包相关的事件。  

    ![查看访问包事件](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    每行包括时间、访问包 ID、操作的名称、对象 ID、UPN 以及启动操作的用户的显示名称。  JSON 中包含其他详细信息。   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>使用 Azure 门户创建自定义 Azure 监视器查询
您可以在 Azure AD 审核事件（包括授权管理事件）上创建自己的查询。  

1. 在 Azure 门户的 Azure 活动目录中，单击左侧导航菜单中的"监视"部分下的 **"日志"** 以创建新的查询页。

1. 工作区应显示在查询页的左上角。 如果有多个 Azure 监视器工作区，并且未显示用于存储 Azure AD 审核事件的工作区，请单击"**选择范围**"。 然后，选择正确的订阅和工作区。

1. 接下来，在查询文本区域中，删除字符串"search +"并将其替换为以下查询：

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 然后单击 **“运行”**。 

    ![单击"运行"开始查询](./media/entitlement-management-logs-and-reporting/run-query.png)

默认情况下，该表将显示从最后一小时开始用于授权管理的审核日志事件。 您可以更改"时间范围"设置以查看较旧的事件。 但是，更改此设置将仅显示 Azure AD 配置为将事件发送到 Azure 监视器后发生的事件。

如果想知道 Azure 监视器中保持的最旧和最新的审核事件，请使用以下查询：

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

有关为 Azure 监视器中的审核事件存储的列的详细信息，请参阅在[Azure 监视器 中解释 Azure AD 审核日志架构](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>使用 Azure PowerShell 创建自定义 Azure 监视器查询

在将 Azure AD 配置为将日志发送到 Azure 监视器后，可以通过 PowerShell 访问日志。 然后，从脚本或 PowerShell 命令行发送查询，而无需成为租户中的全局管理员。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>确保用户或服务主体具有正确的角色分配

请确保您是要向 Azure AD 进行身份验证的用户或服务主体，在日志分析工作区中处于相应的 Azure 角色中。 角色选项是日志分析读取器或日志分析参与者。 如果您已在这些角色之一中，请跳过以[使用一个 Azure 订阅检索日志分析 ID。](#retrieve-log-analytics-id-with-one-azure-subscription)

要设置角色分配并创建查询，请执行以下步骤：

1. 在 Azure 门户中，找到[日志分析工作区](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)。

1. 选择**访问控制 （IAM）。**

1. 然后单击"**添加**"以添加角色分配。

    ![添加角色分配](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>安装 Azure PowerShell 模块

获得适当的角色分配后，启动 PowerShell 并[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps?view=azps-3.3.0)（如果尚未分配），通过键入：

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
现在，您可以对 Azure AD 进行身份验证，并检索要查询的日志分析工作区的 ID。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>使用一个 Azure 订阅检索日志分析 ID
如果只有一个 Azure 订阅和单个日志分析工作区，则键入以下内容以验证到 Azure AD、连接到该订阅并检索该工作区：
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>使用多个 Azure 订阅检索日志分析 ID

 [获取 Az 操作见解工作区](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)一次在一个订阅中运行。 因此，如果您有多个 Azure 订阅，则需要确保连接到具有 Azure AD 日志的日志分析工作区的订阅。 
 
 以下 cmdlet 显示订阅列表，并查找具有日志分析工作区的订阅 ID：
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
您可以使用 命令（如`Connect-AzAccount –Subscription $subs[0].id`）重新验证 PowerShell 会话并将其关联到该订阅。 要了解有关如何从 PowerShell 进行身份验证（包括非交互式）的更多详细信息，请参阅[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)。

如果该订阅中有多个日志分析工作区，则 cmdlet [Get-Az操作见解工作区](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)将返回工作区列表。 然后，您可以找到具有 Azure AD 日志的日志。 此`CustomerId`cmdlet 返回的字段与日志分析工作区概述中的 Azure 门户中显示的"工作区 ID"的值相同。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>将查询发送到日志分析工作区
最后，在确定工作区后，可以使用[Invoke-Az操作见解查询](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
)向该工作区发送 Kusto 查询。 这些查询是用[Kusto 查询语言](https://docs.microsoft.com/azure/kusto/query/)编写的。
 
例如，可以从日志分析工作区检索审核事件记录的日期范围，使用 PowerShell cmdlet 发送查询，如下所示：
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

您还可以使用如下查询检索授权管理事件：

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>后续步骤：
- [使用 Azure Monitor 工作簿创建交互式报表](../../azure-monitor/app/usage-workbooks.md) 

