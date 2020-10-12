---
title: 与 Azure Monitor 日志集成
description: 本文介绍如何将所需的状态配置报告数据从 Azure Automation State Configuration 发送到 Azure Monitor 日志。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 425a7ff0553ddeac502c59e240f5ab152d6e0d79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015147"
---
# <a name="integrate-with-azure-monitor-logs"></a>与 Azure Monitor 日志集成

Azure Automation State Configuration 将节点状态数据保留 30 天。 如果希望将此数据保留更长一段时间，可以将节点状态数据发送到 Log Analytics 工作区。 节点和节点配置中的单个 DSC 资源的符合性状态可以通过 Azure 门户或 PowerShell 查看。 

Azure Monitor 日志可以更直观地显示 Automation State Configuration 数据的运行情况，并且有助于更快地解决事件。 使用 Azure Monitor 日志，可以执行以下操作：

- 获取托管节点和单个资源的符合性信息。
- 基于符合性状态触发电子邮件或警报。
- 跨托管节点编写高级查询。
- 跨自动化帐户关联符合性状态。
- 使用自定义视图和搜索查询直观地显示 Runbook 结果、Runbook 作业状态以及其他相关的关键指标。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

若要开始将 Automation State Configuration 报告发送到 Azure Monitor 日志，需要准备：

- 2016 年 11 月或之后发布的 [Azure PowerShell](/powershell/azure/) (v2.3.0) 版本。
- 一个 Azure 自动化帐户。 有关详细信息，请参阅 [Azure 自动化简介](automation-intro.md)。
- 具有“自动化和控制”服务产品的 Log Analytics 工作区。 有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)。
- 至少一个 Azure Automation State Configuration 节点。 有关详细信息，请参阅[登记由 Azure Automation State Configuration 管理的计算机](automation-dsc-onboarding.md)。
- [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) 模块版本2.7.0.0 或更高版本。 有关安装步骤，请参阅 [Azure Automation Desired State Configuration 故障排查](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>设置与 Azure Monitor 日志的集成

若要开始将数据从 Azure Automation State Configuration 导入到 Azure Monitor 日志，请完成以下步骤：

1. 通过 PowerShell 登录 Azure 帐户。 请参阅[使用 Azure PowerShell 进行登录](/powershell/azure/authenticate-azureps)。
1. 通过运行以下 PowerShell cmdlet 获取自动化帐户的资源 ID。 如果有多个自动化帐户，请选择要配置的帐户的资源 ID。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 通过运行以下 PowerShell cmdlet 获取 Log Analytics 工作区的资源 ID。 如果有多个工作区，请选择要配置的工作区的资源 ID。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 运行以下 PowerShell cmdlet，将 `<AutomationResourceId>` 和 `<WorkspaceResourceId>` 替换为前面每个步骤中的 `ResourceId` 值。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 若要停止将数据从 Azure Automation State Configuration 导入到 Azure Monitor 日志，请运行以下 PowerShell cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>查看 Automation State Configuration 日志

为 Automation State Configuration 数据设置了与 Azure Monitor 日志的集成后，可以在“状态配置 (DSC)”页左窗格的“监视”部分中选择“日志”以便查看这些数据。

![日志](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

此时将打开“日志搜索”窗格，其中包含一个作用域为自动化帐户资源的查询区域。 可以通过在 Azure Monitor 日志中进行搜索，以便搜索 DSC 操作的 State Configuration 日志。 DSC 操作的记录存储在 `AzureDiagnostics` 表中。 例如，若要查找不符合的节点，请键入以下查询。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

筛选详细信息：

* 筛选 `DscNodeStatusData` 可返回每个 State Configuration 节点的操作。
* 筛选 `DscResourceStatusData` 可返回在应用于该资源的节点配置中调用的每个 DSC 资源的操作。 
* 筛选 `DscResourceStatusData` 可返回任何失败的 DSC 资源的错误信息。

若要详细了解如何构建日志查询以查找数据，请参阅 [Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 符合性检查失败时发送一封电子邮件

我们的一家重要客户提出的请求是，当 DSC 配置出现问题时能够发送电子邮件或短信。

若要创建警报规则，首先针对应调用警报的 State Configuration 报告记录创建日志搜索。 单击“新建警报规则”按钮以创建并配置警报规则。

1. 在 Log Analytics 工作区的“概述”页面中，单击“日志”。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   如果已设置在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。 从 `DscNodeStatusData` 记录搜索中的 `Resource` 字段派生自动化帐户名称。
1. 若要打开“创建规则”屏幕，请单击页面顶部的“新建警报规则”。 

若要详细了解用于配置警报的选项，请参阅[创建警报规则](../azure-monitor/platform/alerts-metric.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有节点中查找失败的 DSC 资源

使用 Azure Monitor 日志的一个优点是，可以在节点中搜索失败的检查。 若要查找失败的 DSC 资源的所有实例，请执行以下操作：

1. 在 Log Analytics 工作区的“概述”页面中，单击“日志”。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>查看历史 DSC 节点状态

若要显示不同时间段的 DSC 节点状态历史记录，可以使用以下查询：

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查询将显示不同时间段的节点状态图。

## <a name="azure-monitor-logs-records"></a>Azure Monitor 日志记录

Azure 自动化诊断将在 Azure Monitor 日志中创建以下两种类别的记录：

* 节点状态数据 (`DscNodeStatusData`)
* 资源状态数据 (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| properties | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |`DscNodeStatusData` 列中的一个值匹配。 |
| ResultType |指示节点是否符合要求的值。 |
| NodeName_s |托管节点的名称。 |
| NodeComplianceStatus_s |指定节点是否符合要求的状态值。 |
| DscReportStatus |指示符合性检查是否成功运行的状态值。 |
| ConfigurationMode | 用于将配置应用到节点的模式。 可能的值包括： <ul><li>`ApplyOnly`：DSC 将应用配置，且不执行进一步操作，除非有新配置被推送到目标节点或从服务器请求新配置。 首次应用新配置后，DSC 将不检查以前配置状态的偏离。 在 `ApplyOnly` 值生效之前，DSC 将尝试应用配置，直至成功。 </li><li>`ApplyAndMonitor`：这是默认值。 LCM 将应用任意新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异。 在 `ApplyAndMonitor` 值生效之前，DSC 将尝试应用配置，直至成功。</li><li>`ApplyAndAutoCorrect`：DSC 将应用任何新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异，然后重新应用当前配置。</li></ul> |
| HostName_s | 托管节点的名称。 |
| IPAddress | 托管节点的 IPv4 地址。 |
| 类别 | `DscNodeStatus` 列中的一个值匹配。 |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 用于为调用方标识租户的 GUID。 |
| NodeId_g | 标识托管节点的 GUID。 |
| DscReportId_g | 标识报表的 GUID。 |
| LastSeenTime_t | 上一次查看报表的日期和时间。 |
| ReportStartTime_t | 报表开始的日期和时间。 |
| ReportEndTime_t | 报表完成的日期和时间。 |
| NumberOfResources_d | 在应用于节点的配置中调用的 DSC 资源数。 |
| SourceSystem | 用于标识 Azure Monitor 日志如何收集数据的源系统。 对于 Azure 诊断，始终为 `Azure`。 |
| ResourceId |Azure 自动化帐户的资源标识符。 |
| ResultDescription | 此操作的资源说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID (GUID)。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId | 用作符合性报告相关性标识符的 GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| properties | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |`DscResourceStatusData` 列中的一个值匹配。|
| ResultType |资源是否符合。 |
| NodeName_s |托管节点的名称。 |
| 类别 | DscNodeStatus。 |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 用于为调用方标识租户的 GUID。 |
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
| SourceSystem | Azure Monitor 日志收集数据的方式。 对于 Azure 诊断，始终为 `Azure`。 |
| ResourceId |Azure 自动化帐户的标识符。 |
| ResultDescription | 此操作的说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID (GUID)。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId |用作符合性报告相关性 ID 的 GUID。 |

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [Azure Automation State Configuration 概述](automation-dsc-overview.md)。
- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)。
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译 DSC 配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Chocolatey 设置持续部署](automation-dsc-cd-chocolatey.md)。
- 若要详细了解如何使用 Azure Monitor 日志构建不同的搜索查询和查看 Automation State Configuration 日志，请参阅 [Azure Monitor 中的日志搜索](../azure-monitor/log-query/log-query-overview.md)。
- 若要详细了解 Azure Monitor 日志和数据收集源，请参阅[在 Azure Monitor 日志中收集 Azure 存储数据概述](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)。
