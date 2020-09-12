---
title: 将 Azure 自动化作业数据转发到 Azure Monitor 日志
description: 本文介绍如何将作业状态和 Runbook 作业流发送到 Azure Monitor 日志。
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 6dcd2005971927de30ca96173cb2bdb063e46663
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397418"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>将 Azure 自动化作业数据转发到 Azure Monitor 日志

Azure 自动化可将 Runbook 作业状态和作业流发送到 Log Analytics 工作区。 此过程不涉及工作区链接，并且完全独立。 可在 Azure 门户中或使用 PowerShell 查看单个作业的作业日志和作业流，这使用户可执行简单的调查。 借助 Azure Monitor 日志，你现可：

* 深入了解自动化作业的状态。
* 基于 Runbook 作业状态（例如失败或暂停）触发电子邮件或警报。
* 编写跨作业流的高级查询。
* 跨自动化帐户关联作业。
* 使用自定义视图和搜索查询直观呈现 Runbook 结果、Runbook 作业状态以及其他相关的关键指标。

## <a name="prerequisites"></a>先决条件

要开始将自动化日志发送到 Azure Monitor 日志，需要：

* 最新版本的 [Azure PowerShell](/powershell/azure/)。

* Log Analytics 工作区及其资源 ID。 有关详细信息，请参阅 [Azure Monitor 日志入门](../azure-monitor/overview.md)。

* Azure 自动化帐户的资源 ID。

## <a name="how-to-find-resource-ids"></a>如何查找资源 Id

1. 使用以下命令查找 Azure 自动化帐户的资源 ID：

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. 复制 **ResourceID**的值。

3. 使用以下命令查找 Log Analytics 工作区的资源 ID：

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. 复制 **ResourceID**的值。

若要从特定的资源组中返回结果，请包含 `-ResourceGroupName` 参数。 有关详细信息，请参阅 [AzResource](/powershell/module/az.resources/get-azresource)。

如果上述命令的输出中有多个自动化帐户或工作区，可通过执行以下操作来查找自动化帐户的完整资源 ID 中包含的名称和其他相关属性：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，从“自动化帐户”页面选择你的自动化帐户。
1. 在所选自动化帐户的页面，在“帐户设置”下选择“属性” 。
1. 在“属性”页面上，注意下面显示的详细信息。

    ![自动化帐户属性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>配置诊断设置

自动化诊断设置支持转发以下平台日志和指标数据：

* JobLogs
* JobStreams
* DSCNodeStatus
* 指标-作业总数、更新部署计算机运行总数、更新部署运行总数

若要开始将自动化日志发送到 Azure Monitor 日志，请查看 [创建诊断设置](../azure-monitor/platform/diagnostic-settings.md) 以了解可用于配置诊断设置以发送平台日志的功能和方法。

## <a name="azure-monitor-log-records"></a>Azure Monitor 日志记录

Azure 自动化诊断将在 Azure Monitor 日志中创建两种类型的记录，标记为 `AzureDiagnostics`。 在以下各部分的表格中，有 Azure 自动化生成的记录和日志搜索结果中显示的数据类型的示例。

### <a name="job-logs"></a>作业日志

| properties | 说明 |
| --- | --- |
| TimeGenerated |执行 Runbook 作业的日期和时间。 |
| RunbookName_s |Runbook 的名称。 |
| Caller_s |启动操作的调用方。 可能的值为电子邮件地址或计划作业的系统。 |
| Tenant_g | 用于为调用方标识租户的 GUID。 |
| JobId_g |标识 Runbook 作业的 GUID。 |
| ResultType |Runbook 作业的状态。 可能的值包括：<br>- 新<br>- 已创建<br>- Started（已启动）<br>- Stopped（已停止）<br>- Suspended（已暂停）<br>- Failed（失败）<br>- 已完成 |
| 类别 | 数据类型的分类。 对于自动化，该值为 JobLogs。 |
| OperationName | Azure 中执行的操作的类型。 对于自动化，该值为 Job。 |
| 资源 | 自动化帐户的名称 |
| SourceSystem | Azure Monitor 日志用来收集数据的系统。 对于 Azure 诊断，值始终为 Azure。 |
| ResultDescription |Runbook 作业结果状态。 可能的值包括：<br>- 作业已启动<br>- 作业失败<br>- 作业已完成 |
| CorrelationId |Runbook 作业的关联 GUID。 |
| ResourceId |Runbook 的 Azure 自动化帐户资源 ID。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 GUID。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 资源提供程序。 值为 MICROSOFT.AUTOMATION。 |
| ResourceType | 资源类型。 值为 AUTOMATIONACCOUNTS。 |

### <a name="job-streams"></a>作业流
| properties | 说明 |
| --- | --- |
| TimeGenerated |执行 Runbook 作业的日期和时间。 |
| RunbookName_s |Runbook 的名称。 |
| Caller_s |启动操作的调用方。 可能的值为电子邮件地址或计划作业的系统。 |
| StreamType_s |作业流的类型。 可能的值包括：<br>\- 进度<br>- Output（输出）<br>- Warning（警告）<br>- Error（错误）<br>- Debug（调试）<br>- 详细 |
| Tenant_g | 用于为调用方标识租户的 GUID。 |
| JobId_g |标识 Runbook 作业的 GUID。 |
| ResultType |Runbook 作业的状态。 可能的值包括：<br>- In Progress |
| 类别 | 数据类型的分类。 对于自动化，该值为 JobStreams。 |
| OperationName | Azure 中执行的操作的类型。 对于自动化，该值为 Job。 |
| 资源 | 自动化帐户的名称。 |
| SourceSystem | Azure Monitor 日志用来收集数据的系统。 对于 Azure 诊断，值始终为 Azure。 |
| ResultDescription |包括来自 Runbook 的输出流的说明。 |
| CorrelationId |Runbook 作业的关联 GUID。 |
| ResourceId |Runbook 的 Azure 自动化帐户资源 ID。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 GUID。 |
| ResourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | 资源提供程序。 值为 MICROSOFT.AUTOMATION。 |
| ResourceType | 资源类型。 值为 AUTOMATIONACCOUNTS。 |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>在 Azure Monitor 日志中查看自动化日志

现在，你已开始将自动化作业流和日志发送到 Azure Monitor 日志，接下来让我们看看可以在 Azure Monitor 日志中对这些日志执行哪些操作。

若要查看日志，请运行以下查询：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook 作业失败或暂停时发送电子邮件

以下步骤说明如何在 Azure Monitor 中设置警报，以便在 Runbook 作业出现问题时通知你。

要创建警报规则，请先针对应调用警报的 Runbook 作业记录创建日志搜索。 单击“警报”按钮以创建和配置警报的规则。

1. 在 Log Analytics 工作区的“概述”页面中，单击“查看日志”。

2. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>也可使用以下命令按 Runbook 名称进行分组：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   如果设置了在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。 可在 `JobLogs` 搜索中的 `Resource` 字段中找到自动化帐户名称。

3. 若要打开“创建规则”屏幕，请单击页面顶部的“新建警报规则” 。 要详细了解用于配置警报的选项，请参阅 [Azure 中的日志警报](../azure-monitor/platform/alerts-unified-log.md)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>查找已完成但出错的所有作业

除了在失败时发出警报外，还可以发现 Runbook 作业何时发生非终止错误。 在这些情况下，PowerShell 会生成一个错误流，但非终止错误不会导致作业暂停或失败。

1. 在 Log Analytics 工作区中，单击“日志”。

2. 在查询字段中，键入 `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`。

3. 单击“搜索”按钮。

### <a name="view-job-streams-for-a-job"></a>查看作业的作业流

调试作业时，你可能还希望深入查看作业流。 以下查询使用 GUID 显示单个作业的所有流 `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` ：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>查看历史作业状态

最后，可能需要直观显示一段时间内的作业历史记录。 可以使用此查询来搜索作业在不同时间段的状态。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Log Analytics 历史作业状态图标](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>筛选器作业状态输出已转换为 JSON 对象

最近，我们更改了如何将自动化日志数据写入 Log Analytics 服务中的表的行为，在此情况 `AzureDiagnostics` 下，它不再将 JSON 属性分解为单独的字段。 如果你将 runbook 配置为将输出流中的对象格式设置为单独列的 JSON 格式，则需要重新配置查询以将该字段解析为 JSON 对象，以便访问这些属性。 这是使用 [parsejson](../azure-monitor/log-query/json-data-structures.md#parsejson) 访问已知路径中的特定 JSON 元素实现的。

例如，runbook 将输出流中的 *ResultDescription* 属性设置为具有多个字段的 JSON 格式。 若要搜索状态为 "**状态**" 的字段中的 "失败" 状态的作业的状态，请使用此示例查询搜索状态为 "**失败**" 的*ResultDescription* ：

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics 历史作业流 JSON 格式](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>后续步骤

* 若要了解如何使用 Azure Monitor 日志构建搜索查询和查看自动化作业日志，请参阅 [Azure Monitor 日志中的日志搜索](../azure-monitor/log-query/log-query-overview.md)。
* 若要了解如何从 Runbook 创建和检索输出及错误消息，请参阅[监视 Runbook 输出](automation-runbook-output-and-messages.md)。
* 若要详细了解 Runbook 执行情况、Runbook 作业的监视方式以及其他技术详细信息，请参阅[在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)。
* 若要详细了解 Azure Monitor 日志和数据收集源，请参阅[“在 Azure Monitor 日志中收集 Azure 存储数据”概述](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)。
* 若在排查 Log Analytics 问题时需要帮助，请参阅[排查 Log Analytics 不再收集数据的原因](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)。
