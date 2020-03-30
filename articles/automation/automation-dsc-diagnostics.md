---
title: 将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志
description: 本文演示如何将 Desired State Configuration (DSC) 报表数据从 Azure Automation State Configuration 发送到 Azure Monitor 日志，以便为用户提供附加见解和管理信息。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430714"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志

Azure Automation State Configuration 会将节点状态数据保留 30 天。
如果希望节点状态数据能够保留更长的时间，则可将其发送到 Log Analytics 工作区。
节点和节点配置中的单个 DSC 资源的符合性状态可以通过 Azure 门户或 PowerShell 查看。
可以使用 Azure Monitor 日志进行以下操作：

- 获取托管节点和单个资源的符合性信息
- 基于符合性状态触发电子邮件或警报
- 跨托管节点编写高级查询
- 跨自动化帐户关联符合性状态
- 随着时间的推移，可视化节点符合性历史记录

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

若要开始将 Automation State Configuration 报表发送到 Azure Monitor 日志，需要准备：

- 2016 年 11 月或之后发布的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0) 版本。
- 一个 Azure 自动化帐户。 有关详细信息，请参阅[Azure 自动化 简介](automation-intro.md)。
- 具有自动化&控制服务服务的日志分析工作区。 有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
- 至少一个 Azure Automation State Configuration 节点。 有关详细信息，请参阅[载入计算机，以便由 Azure 自动化状态配置进行管理](automation-dsc-onboarding.md)。
- [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) 模块版本 2.7.0.0 或更高版本。 有关安装步骤，请参阅[排除 Azure 自动化所需状态配置的疑难解答](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>设置与 Azure Monitor 日志的集成

若要开始将数据从 Azure Automation DSC 导入到 Azure Monitor 日志，请完成以下步骤：

1. 通过 PowerShell 登录 Azure 帐户。 请参阅[使用 Azure PowerShell 登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
1. 通过运行以下 PowerShell cmdlet 获取自动化帐户的资源 ID。 如果您有多个自动化帐户，请选择要配置的帐户的资源 ID。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 通过运行以下 PowerShell cmdlet 获取日志分析工作区的资源 ID。 如果您有多个工作区，请选择要配置的工作区的资源 ID。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 运行以下 PowerShell cmdlet，`<AutomationResourceId>`替换`<WorkspaceResourceId>`和使用上述步骤中的 *"资源 Id"* 值。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 如果要停止将数据从 Azure 自动化状态配置导入 Azure 监视器日志，请运行以下 PowerShell cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>查看 Automation State Configuration 日志

为自动化状态配置数据设置与 Azure 监视器日志的集成后，可以通过在状态配置 （DSC） 页面左侧窗格中的 **"监视**"部分中选择 **"日志**"来查看这些日志。

![日志](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

"**日志搜索"** 窗格将打开，查询区域将限定到您的自动化帐户资源。 通过在 Azure 监视器日志中搜索，可以搜索状态配置日志以查找 DSC 操作。 DSC 操作的记录存储在 Azure 诊断表中。 例如，要查找不符合要求的节点，请键入以下查询。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
筛选详细信息：

* 筛选*DscNode 状态数据*以返回每个状态配置节点的操作。
* 筛选*DscResourceStatusData，* 以返回应用于该资源的节点配置中调用的每个 DSC 资源的操作。 
* 筛选*DscResourceStatusData*以返回任何失败的 DSC 资源的错误信息。

要了解有关构造日志查询以查找数据的详细信息，请参阅[Azure 监视器 中的日志查询概述](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 符合性检查失败时发送一封电子邮件

我们的一家重要客户提出的请求是，当 DSC 配置出现问题时能够发送电子邮件或短信。

要创建警报规则，请首先为应调用警报的状态配置报表记录创建日志搜索。 单击“+ 新建警报规则”**** 按钮以创建并配置警报规则。

1. 在“Log Analytics 工作区概述”页中，单击“日志”****。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   如果已设置在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。 在搜索 DscNode 状态数据时，从资源字段派生自动化帐户名称。
1. 若要打开“创建规则”**** 屏幕，请单击页面顶部的“+ 新建警报规则”****。 

有关警报配置选项的详细信息，请参阅[创建警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有节点中查找失败的 DSC 资源

使用 Azure Monitor 日志的一个优点是，可以在节点中搜索失败的检查。
要查找已失败的 DSC 资源的所有实例，：

1. 在日志分析工作区概述页上，单击"**日志**"。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>查看历史 DSC 节点状态

要随着时间的推移可视化 DSC 节点状态历史记录，可以使用此查询：

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查询显示节点状态随时间的图表。

## <a name="azure-monitor-logs-records"></a>Azure Monitor 日志记录

Azure 自动化诊断在 Azure 监视器日志中创建两类记录：

* 节点状态数据（DscNode状态数据）
* 资源状态数据（数据资源状态数据）

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| properties | 描述 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |DscNode 状态数据。 |
| ResultType |节点是否符合。 |
| NodeName_s |托管节点的名称。 |
| NodeComplianceStatus_s |节点是否符合。 |
| DscReportStatus |符合性检查是否已成功运行。 |
| ConfigurationMode | 如何将配置应用到节点。 可能的值包括： <ul><li>ApplyOnly**：DSC 将应用配置，且不执行进一步操作，除非有新配置被推送到目标节点或从服务器请求新配置。 首次应用新配置后，DSC 将不检查以前配置状态的偏离。 DSC 尝试应用配置，直到该配置在 *"仅应用"* 值生效之前成功。 </li><li>*应用和监视器*：这是默认值。 LCM 将应用任意新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异。 DSC 尝试应用配置，直到*在应用和监视器*值生效之前成功。</li><li>*ApplyAndAutoCorrect*：DSC 将应用任何新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异，然后重新应用当前配置。</li></ul> |
| HostName_s | 托管节点的名称。 |
| IPAddress | 托管节点的 IPv4 地址。 |
| 类别 | DscNode 状态。 |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | GUID，用于为 Caller 标识租户。 |
| NodeId_g |标识托管节点的 GUID。 |
| DscReportId_g |标识报表的 GUID。 |
| LastSeenTime_t |上一次查看报表的日期和时间。 |
| ReportStartTime_t |报表开始的日期和时间。 |
| ReportEndTime_t |报表完成的日期和时间。 |
| NumberOfResources_d |在应用于节点的配置中调用的 DSC 资源数。 |
| SourceSystem | Azure Monitor 日志收集数据的方式。 Azure 诊断始终为"Azure"。 |
| ResourceId |Azure 自动化帐户的标识符。 |
| ResultDescription | 此操作的说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID （GUID）。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 微软。自动化。 |
| ResourceType | 自动化帐户。 |
| CorrelationId |GUID，它是合规性报告的相关标识符。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| properties | 描述 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |DscResourceStatusData|
| ResultType |资源是否符合。 |
| NodeName_s |托管节点的名称。 |
| 类别 | DscNode 状态。 |
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
| SourceSystem | Azure Monitor 日志收集数据的方式。 对于 Azure 诊断，始终为 Azure**。 |
| ResourceId |指定 Azure 自动化帐户。 |
| ResultDescription | 此操作的说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID （GUID）。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 微软。自动化。 |
| ResourceType | 自动化帐户。 |
| CorrelationId |GUID，它是合规性报告的相关 ID。 |

## <a name="summary"></a>总结

将 Automation State Configuration 数据发送到 Azure Monitor 日志后，可以通过以下操作更好地了解 Automation State Configuration 节点的状态：

- 设置警报，以便在出现问题时获得通知
- 使用自定义视图和搜索查询直观地显示 Runbook 结果、Runbook 作业状态，以及其他相关的关键指标。

Azure Monitor 日志可以更直观地显示 Automation State Configuration 数据的运行情况，并且有助于更快地解决事件。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [Azure Automation State Configuration](automation-dsc-overview.md)
- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)
- 若要详细了解如何使用 Azure Monitor 日志构造不同的搜索查询和查看 Automation State Configuration 日志，请参阅 [Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)
- 要了解有关 Azure 监视器日志和数据收集源的详细信息，请参阅[在 Azure 监视器日志概述中收集 Azure 存储数据](../azure-monitor/platform/collect-azure-metrics-logs.md)
