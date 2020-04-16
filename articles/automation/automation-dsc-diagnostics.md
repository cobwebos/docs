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
ms.openlocfilehash: badd8ba676ef25c33a5034bb04d616faeb4ef1b0
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392103"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志

Azure Automation State Configuration 会将节点状态数据保留 30 天。 如果希望节点状态数据能够保留更长的时间，则可将其发送到 Log Analytics 工作区。 节点和节点配置中的单个 DSC 资源的符合性状态可以通过 Azure 门户或 PowerShell 查看。 

Azure Monitor 日志可以更直观地显示 Automation State Configuration 数据的运行情况，并且有助于更快地解决事件。 可以使用 Azure Monitor 日志进行以下操作：

- 获取托管节点和单个资源的合规性信息。
- 根据合规性状态触发电子邮件或警报。
- 跨托管节点编写高级查询。
- 关联自动化帐户的合规性状态。
- 使用自定义视图和搜索查询来可视化 Runbook 结果、Runbook 作业状态以及其他相关关键指标或指标。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="prerequisites"></a>先决条件

若要开始将 Automation State Configuration 报表发送到 Azure Monitor 日志，需要准备：

- 2016 年 11 月或之后发布的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0) 版本。
- 一个 Azure 自动化帐户。 有关详细信息，请参阅[Azure 自动化 简介](automation-intro.md)。
- 具有自动化&控制服务服务的日志分析工作区。 有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
- 至少一个 Azure Automation State Configuration 节点。 有关详细信息，请参阅[载入计算机，以便由 Azure 自动化状态配置进行管理](automation-dsc-onboarding.md)。
- [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) 模块版本 2.7.0.0 或更高版本。 有关安装步骤，请参阅[排除 Azure 自动化所需状态配置的疑难解答](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>设置与 Azure Monitor 日志的集成

要开始将数据从 Azure 自动化状态配置导入 Azure 监视器日志，请参阅以下步骤：

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

1. 运行以下 PowerShell cmdlet，`<AutomationResourceId>`替换`<WorkspaceResourceId>`和使用`ResourceId`上述每个步骤的值。

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

"日志搜索"窗格将打开，查询区域将限定到您的自动化帐户资源。 通过在 Azure 监视器日志中搜索，可以搜索状态配置日志以查找 DSC 操作。 DSC 操作的记录存储在`AzureDiagnostics`表中。 例如，要查找不符合要求的节点，请键入以下查询。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

筛选详细信息：

* 筛选`DscNodeStatusData`以返回每个状态配置节点的操作。
* 筛选`DscResourceStatusData`以返回应用于该资源的节点配置中调用的每个 DSC 资源的操作。 
* 筛选`DscResourceStatusData`以返回任何失败的 DSC 资源的错误信息。

要了解有关构造日志查询以查找数据的详细信息，请参阅[Azure 监视器 中的日志查询概述](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 符合性检查失败时发送一封电子邮件

我们的一家重要客户提出的请求是，当 DSC 配置出现问题时能够发送电子邮件或短信。

要创建警报规则，请首先为应调用警报的状态配置报表记录创建日志搜索。 单击 **"新警报规则"** 按钮以创建和配置警报规则。

1. 在“Log Analytics 工作区概述”页中，单击“日志”****。
1. 通过在查询字段中键入以下搜索，为警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   如果已设置在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。 从搜索`Resource``DscNodeStatusData`记录中的字段派生自动化帐户名称。
1. 要打开"**创建规则"** 屏幕，请单击页面顶部**的新警报规则**。 

有关警报配置选项的详细信息，请参阅[创建警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有节点中查找失败的 DSC 资源

使用 Azure Monitor 日志的一个优点是，可以在节点中搜索失败的检查。 要查找已失败的 DSC 资源的所有实例，：

1. 在日志分析工作区概述页上，单击"**日志**"。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>查看历史 DSC 节点状态

要随着时间的推移可视化 DSC 节点状态历史记录，可以使用此查询：

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查询显示节点状态随时间的图表。

## <a name="azure-monitor-logs-records"></a>Azure Monitor 日志记录

Azure 自动化诊断在 Azure 监视器日志中创建两类记录：

* 节点状态数据`DscNodeStatusData`（ ）
* 资源状态数据`DscResourceStatusData`（ ）

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| properties | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |`DscNodeStatusData`. |
| ResultType |指示节点是否合规的值。 |
| NodeName_s |托管节点的名称。 |
| NodeComplianceStatus_s |指定节点是否符合的状态值。 |
| DscReportStatus |状态值，指示合规性检查是否成功运行。 |
| ConfigurationMode | 用于将配置应用于节点的模式。 可能的值包括： <ul><li>`ApplyOnly`：DSC 应用配置，除非将新配置推送到目标节点或从服务器提取新配置时，否则不会执行任何进一步操作。 首次应用新配置后，DSC 将不检查以前配置状态的偏离。 DSC 尝试应用配置，直到`ApplyOnly`该配置在值生效之前成功。 </li><li>`ApplyAndMonitor`：默认值。 LCM 将应用任意新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异。 DSC 尝试应用配置，直到`ApplyAndMonitor`该配置在值生效之前成功。</li><li>`ApplyAndAutoCorrect`：DSC 应用任何新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异，然后重新应用当前配置。</li></ul> |
| HostName_s | 托管节点的名称。 |
| IPAddress | 托管节点的 IPv4 地址。 |
| 类别 | `DscNodeStatus`. |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 标识调用方的租户的 GUID。 |
| NodeId_g | 标识托管节点的 GUID。 |
| DscReportId_g | 标识报表的 GUID。 |
| LastSeenTime_t | 上一次查看报表的日期和时间。 |
| ReportStartTime_t | 报表开始的日期和时间。 |
| ReportEndTime_t | 报表完成的日期和时间。 |
| NumberOfResources_d | 在应用于节点的配置中调用的 DSC 资源数。 |
| SourceSystem | 源系统标识 Azure 监视器日志如何收集数据。 始终`Azure`用于 Azure 诊断。 |
| ResourceId |Azure 自动化帐户的资源标识符。 |
| ResultDescription | 此操作的资源描述。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID （GUID）。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 微软。自动化。 |
| ResourceType | 自动化帐户。 |
| CorrelationId | 作为合规性报告的相关标识符的 GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| properties | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |`DscResourceStatusData`.|
| ResultType |资源是否符合。 |
| NodeName_s |托管节点的名称。 |
| 类别 | DscNode 状态。 |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 标识调用方的租户的 GUID。 |
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
| SourceSystem | Azure Monitor 日志收集数据的方式。 始终`Azure`用于 Azure 诊断。 |
| ResourceId |Azure 自动化帐户的标识符。 |
| ResultDescription | 此操作的说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID （GUID）。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 微软。自动化。 |
| ResourceType | 自动化帐户。 |
| CorrelationId |GUID，它是合规性报告的相关 ID。 |


## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[Azure 自动化状态配置](automation-dsc-overview.md)。
- 要开始，请参阅[开始使用 Azure 自动化状态配置](automation-dsc-getting-started.md)。
- 要了解如何编译 DSC 配置以便将它们分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 要查看在连续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置进行持续部署和巧克力](automation-dsc-cd-chocolatey.md)。
- 要了解有关如何构造不同的搜索查询和使用 Azure 监视器日志查看自动化状态配置日志的更多信息，请参阅[Azure 监视器日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)。
- 若要了解有关 Azure Monitor 日志和数据收集源的详细信息，请参阅[在 Azure Monitor 日志中收集 Azure 存储数据概述](../azure-monitor/platform/collect-azure-metrics-logs.md)。
