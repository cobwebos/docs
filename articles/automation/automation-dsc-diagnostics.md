---
title: 将 Azure 自动化状态配置报告数据转发到 Azure Monitor 日志
description: 本文演示如何将所需状态配置（DSC）报告数据从 Azure 自动化状态配置发送到 Azure Monitor 日志，以提供更多见解和管理。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69801909c6bc8d215ca7dd3ccb7ac349201e8774
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198558"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>将 Azure 自动化状态配置报告数据转发到 Azure Monitor 日志

Azure 自动化状态配置保留了30天的节点状态数据。
如果希望将此数据保留更长一段时间，可以向 Log Analytics 工作区发送节点状态数据。
节点和节点配置中的单个 DSC 资源的符合性状态可以通过 Azure 门户或 PowerShell 查看。
利用 Azure Monitor 日志，您可以：

- 获取托管节点和单个资源的符合性信息
- 基于符合性状态触发电子邮件或警报
- 跨托管节点编写高级查询
- 跨自动化帐户关联符合性状态
- 随着时间的推移，可视化节点符合性历史记录

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

若要开始将自动化状态配置报表发送到 Azure Monitor 日志，需要：

- 2016 年 11 月或之后发布的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0) 版本。
- 一个 Azure 自动化帐户。 有关详细信息，请参阅[Azure 自动化简介](automation-intro.md)。
- 具有自动化 & 控制服务产品的 Log Analytics 工作区。 有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
- 至少一个 Azure Automation State Configuration 节点。 有关详细信息，请参阅[通过 Azure 自动化状态配置载入计算机进行管理](automation-dsc-onboarding.md)。
- [XDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0)模块版本2.7.0.0 或更高版本。 有关安装步骤，请参阅[排查 Azure Automation Desired State Configuration](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>设置与 Azure Monitor 日志的集成

若要开始将数据从 Azure Automation DSC 导入 Azure Monitor 日志，请完成以下步骤：

1. 通过 PowerShell 登录 Azure 帐户。 请参阅[登录 Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
1. 通过运行以下 PowerShell cmdlet 获取自动化帐户的资源 ID。 如果有多个自动化帐户，请选择想要配置的帐户的资源 ID。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 通过运行以下 PowerShell cmdlet 获取 Log Analytics 工作区的资源 ID。 如果有多个工作区，请选择要配置的工作区的资源 ID。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 运行以下 PowerShell cmdlet，并将 `<AutomationResourceId>` 和 `<WorkspaceResourceId>` 替换为上述每个步骤中的*ResourceId*值。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 如果要停止将数据从 Azure 自动化状态配置导入 Azure Monitor 日志，请运行以下 PowerShell cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>查看 Automation State Configuration 日志

设置与自动化状态配置数据的 Azure Monitor 日志集成后，可以通过在 "状态配置（DSC）" 页左窗格中的 "**监视**" 部分选择 "**日志**" 来查看这些数据。

![日志](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

此时将打开 "**日志搜索**" 窗格，其中包含一个作用域为自动化帐户资源的查询区域。 可以通过在 Azure Monitor 日志中搜索来搜索 DSC 操作的状态配置日志。 DSC 操作的记录存储在 AzureDiagnostics 表中。 例如，若要查找不符合的节点，请键入以下查询。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
筛选详细信息：

* 筛选*DscNodeStatusData*以返回每个状态配置节点的操作。
* 筛选*DscResourceStatusData* ，以返回应用于该资源的节点配置中调用的每个 DSC 资源的操作。 
* 筛选*DscResourceStatusData*以返回任何失败的 DSC 资源的错误信息。

若要详细了解如何构建日志查询以查找数据，请参阅[Azure Monitor 中的日志查询概述](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 符合性检查失败时发送一封电子邮件

我们的一家重要客户提出的请求是，当 DSC 配置出现问题时能够发送电子邮件或短信。

若要创建警报规则，请首先为应调用警报的状态配置报表记录创建日志搜索。 单击“+ 新建警报规则”按钮以创建并配置警报规则。

1. 在 Log Analytics 工作区概述 "页中，单击"**日志**"。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   如果已设置在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。 从 DscNodeStatusData 的搜索中的 "资源" 字段派生自动化帐户名称。
1. 若要打开“创建规则”屏幕，请单击页面顶部的“+ 新建警报规则”。 

有关用于配置警报的选项的详细信息，请参阅[创建警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有节点中查找失败的 DSC 资源

使用 Azure Monitor 日志的一个优点是，您可以跨节点搜索失败的检查。
查找已失败的 DSC 资源的所有实例：

1. 在 Log Analytics 工作区概述 "页上，单击"**日志**"。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>查看历史 DSC 节点状态

若要在一段时间内可视化 DSC 节点状态历史记录，可以使用以下查询：

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查询显示一段时间内节点状态的图表。

## <a name="azure-monitor-logs-records"></a>Azure Monitor 日志记录

Azure 自动化诊断在 Azure Monitor 日志中创建两类记录：

* 节点状态数据（DscNodeStatusData）
* 资源状态数据（DscResourceStatusData）

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 属性 | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |DscNodeStatusData. |
| ResultType |节点是否符合。 |
| NodeName_s |托管节点的名称。 |
| NodeComplianceStatus_s |节点是否符合。 |
| DscReportStatus |符合性检查是否已成功运行。 |
| ConfigurationMode | 如何将配置应用到节点。 可能的值包括： <ul><li>ApplyOnly：DSC 将应用配置，且不执行进一步操作，除非有新配置被推送到目标节点或从服务器请求新配置。 首次应用新配置后，DSC 不会检查是否偏离以前配置的状态。 DSC 将尝试应用配置，直到成功， *ApplyOnly*值才会生效。 </li><li>ApplyAndMonitor：这是默认值。 LCM 将应用任意新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异。 DSC 将尝试应用配置，直到成功， *ApplyAndMonitor*值才会生效。</li><li>*ApplyAndAutoCorrect*：DSC 将应用任何新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异，然后重新应用当前配置。</li></ul> |
| HostName_s | 托管节点的名称。 |
| IPAddress | 托管节点的 IPv4 地址。 |
| 类别 | DscNodeStatus. |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | GUID，用于为 Caller 标识租户。 |
| NodeId_g |标识托管节点的 GUID。 |
| DscReportId_g |标识报表的 GUID。 |
| LastSeenTime_t |上一次查看报表的日期和时间。 |
| ReportStartTime_t |报表开始的日期和时间。 |
| ReportEndTime_t |报表完成的日期和时间。 |
| NumberOfResources_d |在应用于节点的配置中调用的 DSC 资源数。 |
| SourceSystem | Azure Monitor 日志收集数据的方式。 对于 Azure 诊断，始终为 "Azure"。 |
| ResourceId |Azure 自动化帐户的标识符。 |
| ResultDescription | 此操作的说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID （GUID）。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 微软.自动化. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID，它是相容性报告的相关标识符。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 属性 | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |DscResourceStatusData|
| ResultType |资源是否符合。 |
| NodeName_s |托管节点的名称。 |
| 类别 | DscNodeStatus. |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | GUID，用于为 Caller 标识租户。 |
| NodeId_g |标识托管节点的 GUID。 |
| DscReportId_g |标识报表的 GUID。 |
| DscResourceId_s |DSC 资源实例的名称。 |
| DscResourceName_s |DSC 资源的名称。 |
| DscResourceStatus_s |DSC 资源是否具有符合性。 |
| DscModuleName_s |包含 DSC 资源的 PowerShell 模块的名称。 |
| DscModuleVersion_s |包含 DSC 资源的 PowerShell 模块的版本。 |
| DscConfigurationName_s |应用于节点的配置的名称。 |
| ErrorCode_s | 资源失败时的错误代码。 |
| ErrorMessage_s |资源失败时的错误消息。 |
| DscResourceDuration_d |DSC 资源运行的时间（以秒为单位）。 |
| SourceSystem | Azure Monitor 日志收集数据的方式。 对于 Azure 诊断，始终为 Azure。 |
| ResourceId |指定 Azure 自动化帐户。 |
| ResultDescription | 此操作的说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID （GUID）。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 微软.自动化. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |作为相容性报告的相关性 ID 的 GUID。 |

## <a name="summary"></a>摘要

通过将自动化状态配置数据发送到 Azure Monitor 日志，可以通过以下方式更好地了解自动化状态配置节点的状态：

- 设置警报，以便在出现问题时获得通知
- 使用自定义视图和搜索查询直观地显示 Runbook 结果、Runbook 作业状态，以及其他相关的关键指标。

Azure Monitor 日志可为自动化状态配置数据提供更高的操作可见性，有助于更快地解决事件。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [Azure Automation State Configuration](automation-dsc-overview.md)
- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)
- 若要详细了解如何构造不同的搜索查询和查看带有 Azure Monitor 日志的自动化状态配置日志，请参阅[Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)
- 若要详细了解 Azure Monitor 日志和数据收集源，请参阅[在 Azure Monitor 日志中收集 Azure 存储数据概述](../azure-monitor/platform/collect-azure-metrics-logs.md)
