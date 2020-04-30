---
title: 将 Azure 自动化作业数据转发到 Azure Monitor 日志
description: 本文演示如何将作业状态和 Runbook 作业流发送到 Azure Monitor 日志，以提供更多见解和管理信息。
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457682"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>将作业状态和作业流从自动化转发到 Azure Monitor 日志

自动化可以将 Runbook 作业状态和作业流发送到 Log Analytics 工作区。 此过程不涉及工作区链接，并且完全独立。 可在 Azure 门户中或使用 PowerShell 查看单个作业的作业日志和作业流，这使用户可执行简单的调查。 现在，可以使用 Azure Monitor 日志执行以下操作：

* 了解自动化作业的状态。
* 基于 Runbook 作业状态（例如失败或暂停）触发电子邮件或警报。
* 编写跨作业流的高级查询。
* 跨自动化帐户关联作业。
* 使用自定义视图和搜索查询直观显示 runbook 结果、runbook 作业状态，以及其他相关的关键指标或指标。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="prerequisites-and-deployment-considerations"></a>先决条件和部署注意事项

若要开始将自动化日志发送到 Azure Monitor 日志，需要：

* 最新版本的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)。
* Log Analytics 工作区。 有关详细信息，请参阅[Azure Monitor 日志入门](../log-analytics/log-analytics-get-started.md)。
* Azure 自动化帐户的资源 ID。

使用以下命令查找 Azure 自动化帐户的资源 ID：

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

若要查找 Log Analytics 工作区的资源 ID，请运行以下 PowerShell 命令：

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

如果在上述命令的输出中有多个自动化帐户或工作区，请找到需要配置的名称，并复制资源 ID 的值。

1. 在 Azure 门户中，从 "**自动化帐户**" 边栏选项卡中选择自动化帐户，并选择 "**所有设置**"。 
2. 在 "**所有设置**" 边栏选项卡中，在 "**帐户设置**" 下选择 "**属性**"。  
3. 在 "**属性**" 边栏选项卡中，记下如下所示的属性。

    ![自动化帐户属性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Azure Monitor 日志记录

Azure 自动化诊断在 Azure Monitor 日志中创建两种类型的记录， `AzureDiagnostics`标记为。 以下各节中的表是 Azure 自动化生成的记录的示例，以及日志搜索结果中显示的数据类型。

### <a name="job-logs"></a>作业日志

| 属性 | 说明 |
| --- | --- |
| TimeGenerated |执行 Runbook 作业的日期和时间。 |
| RunbookName_s |Runbook 的名称。 |
| Caller_s |启动操作的调用方。 可能的值为电子邮件地址或计划作业的系统。 |
| Tenant_g | 标识调用方的租户的 GUID。 |
| JobId_g |标识 runbook 作业的 GUID。 |
| ResultType |Runbook 作业的状态。 可能的值为：<br>- New（新）<br>- Created（已创建）<br>- Started（已启动）<br>- Stopped（已停止）<br>- Suspended（已暂停）<br>- Failed（失败）<br>- Completed（已完成） |
| 类别 | 数据类型的分类。 对于自动化，该值为 JobLogs。 |
| OperationName | 在 Azure 中执行的操作的类型。 对于自动化，该值为 Job。 |
| 资源 | 自动化帐户的名称 |
| SourceSystem | Azure Monitor 日志使用来收集数据的系统。 对于 Azure 诊断，值始终为 Azure。 |
| ResultDescription |Runbook 作业结果状态。 可能的值为：<br>- 作业已启动<br>- 作业失败<br>- 作业已完成 |
| CorrelationId |Runbook 作业的关联 GUID。 |
| ResourceId |Runbook 的 Azure 自动化帐户资源 ID。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 GUID。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 资源提供程序。 值是 MICROSOFT。自动化. |
| ResourceType | 资源类型。 值为 AUTOMATIONACCOUNTS。 |

### <a name="job-streams"></a>作业流
| 属性 | 说明 |
| --- | --- |
| TimeGenerated |执行 Runbook 作业的日期和时间。 |
| RunbookName_s |Runbook 的名称。 |
| Caller_s |启动操作的调用方。 可能的值为电子邮件地址或计划作业的系统。 |
| StreamType_s |作业流的类型。 可能的值为：<br>- Progress（进度）<br>- Output（输出）<br>- Warning（警告）<br>- Error（错误）<br>- Debug（调试）<br>- Verbose（详细） |
| Tenant_g | 标识调用方的租户的 GUID。 |
| JobId_g |标识 runbook 作业的 GUID。 |
| ResultType |Runbook 作业的状态。 可能的值为：<br>- In Progress |
| 类别 | 数据类型的分类。 对于自动化，该值为 JobStreams。 |
| OperationName | 在 Azure 中执行的操作的类型。 对于自动化，该值为 Job。 |
| 资源 | 自动化帐户的名称。 |
| SourceSystem | Azure Monitor 日志使用来收集数据的系统。 对于 Azure 诊断，值始终为 Azure。 |
| ResultDescription |包括来自 runbook 的输出流的说明。 |
| CorrelationId |Runbook 作业的关联 GUID。 |
| ResourceId |Runbook 的 Azure 自动化帐户资源 ID。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 GUID。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 资源提供程序。 值是 MICROSOFT。自动化. |
| ResourceType | 资源类型。 值为 AUTOMATIONACCOUNTS。 |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>设置与 Azure Monitor 日志的集成

1. 在计算机上，从“开始”**** 屏幕启动 Windows PowerShell。
2. 运行以下 PowerShell 命令，并将和`[your resource ID]` `[resource ID of the log analytics workspace]`的值与上一节中的值进行编辑。

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

运行此脚本后，可能需要一小时才能开始在新`JobLogs`的或`JobStreams`写入 Azure Monitor 日志中查看记录。

若要查看日志，请在 Log Analytics 日志搜索中运行以下查询：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>验证配置

若要确认自动化帐户是否正在向 Log Analytics 工作区发送日志，请使用以下 PowerShell 命令检查是否已在自动化帐户上正确配置了诊断。

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

在输出中，请确保：

* 在`Logs`下，的值`Enabled`为 True。
* `WorkspaceId`设置为 Log Analytics 工作`ResourceId`区的值。

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>在 Azure Monitor 日志中查看自动化日志

现在，你已开始将自动化作业日志发送到 Azure Monitor 日志，接下来让我们看看可以在 Azure Monitor 日志中对这些日志执行哪些操作。

若要查看日志，请运行以下查询：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook 作业失败或暂停时发送电子邮件

客户的主要诉求之一是，当 Runbook 作业出现问题时能够发送电子邮件或短信。

若要创建警报规则，请首先为应调用警报的 runbook 作业记录创建日志搜索。 单击“警报”**** 按钮以创建和配置警报的规则。

1. 在“Log Analytics 工作区概述”页中，单击“查看日志”****。
2. 通过在查询字段中键入以下搜索，为警报创建日志搜索查询：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>还可以通过使用以下方式按 runbook 名称进行分组：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   如果设置了在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。 可在的搜索`Resource`字段中找到自动化帐户名称。 `JobLogs`
3. 若要打开 "**创建规则**" 屏幕，请单击页面顶部的 "**新建警报规则**"。 有关用于配置警报的选项的详细信息，请参阅 [Azure 中的日志警报](../azure-monitor/platform/alerts-unified-log.md)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>查找已完成但出错的所有作业

除了在失败时发出警报外，还可以发现 Runbook 作业何时发生非终止错误。 在这些情况下，PowerShell 会生成错误流，但非终止错误不会导致作业挂起或失败。

1. 在 Log Analytics 工作区中单击“日志”****。
2. 在查询字段中，键入`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`。
3. 单击 "**搜索**" 按钮。

### <a name="view-job-streams-for-a-job"></a>查看作业的作业流

调试作业时，可能还需要查看作业流。 以下查询会显示 GUID 为 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的单个作业的所有流：

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>查看历史作业状态

最后，你可能需要在一段时间内可视化作业历史记录。 可以使用此查询来搜索作业在不同时间段的状态。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 历史作业状态图标](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>正在删除诊断设置

若要从自动化帐户中删除诊断设置，请运行以下命令：

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>后续步骤

* 有关对 Log Analytics 进行故障排除的帮助，请参阅[对 Log Analytics 不再收集数据的原因进行故障排除](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)。
* 若要详细了解如何使用 Azure Monitor 日志构造不同的搜索查询和查看自动化作业日志，请参阅 [Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)。
* 若要了解如何通过 Runbook 创建和检索输出及错误消息，请参阅 [Runbook 输出和消息](automation-runbook-output-and-messages.md)。
* 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅[跟踪 Runbook 作业](automation-runbook-execution.md)。
* 若要了解有关 Azure Monitor 日志和数据收集源的详细信息，请参阅[在 Azure Monitor 日志中收集 Azure 存储数据概述](../azure-monitor/platform/collect-azure-metrics-logs.md)。