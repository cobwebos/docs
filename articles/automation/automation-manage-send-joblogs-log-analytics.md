---
title: "将 Azure 自动化作业数据转发到 OMS Log Analytics | Microsoft Docs"
description: "本文演示如何将作业状态和 Runbook 作业流发送到 Microsoft Operations Management Suite Log Analytics，以提供附加见解和管理信息。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 878149521edc969dc3e15e198ff3b2ead978cf86
ms.contentlocale: zh-cn
ms.lasthandoff: 09/02/2017

---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>将作业状态和作业流从自动化转发到 Log Analytics (OMS)
自动化可以将 Runbook 作业状态和作业流发送到 Microsoft Operations Management Suite (OMS) Log Analytics 工作区。  可在 Azure 门户中或使用 PowerShell 查看单个作业的作业日志和作业流，这使用户可执行简单的调查。 现在，使用 Log Analytics，可以：

* 获取有关自动化作业的见解
* 基于 Runbook 作业状态（例如失败或暂停）触发电子邮件或警报
* 针对所有作业流编写高级查询
* 跨自动化帐户关联作业
* 可视化不同时间段的作业历史记录     

## <a name="prerequisites-and-deployment-considerations"></a>先决条件和部署注意事项
要开始会自动化日志发送到 Log Analytics，需要准备：

1. 2016 年 11 月或之后发布的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0) 版本。
2. Log Analytics 工作区。 有关详细信息，请参阅 [Log Analytics 入门](../log-analytics/log-analytics-get-started.md)。 
3. Azure 自动化帐户的 ResourceId

要查找 Azure 自动化帐户和 Log Analytics 工作区的 ResourceId，请运行以下 PowerShell：

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

如果有多个自动化帐户或工作区，请在上述命令的输出中找到需要配置的名称，并复制 ResourceId 的值。

要查找自动化帐户的名称，请在 Azure 门户中，从“自动化帐户”边栏选项卡选择自动化帐户，并选择“所有设置”。  在“所有设置”边栏选项卡中，选择“帐户设置”下面的“属性”。  在“属性”边栏选项卡中，可以记下这些值。<br> ![自动化帐户属性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)。

## <a name="set-up-integration-with-log-analytics"></a>设置与 Log Analytics 的集成
1. 在计算机上，从“开始”屏幕启动 **Windows PowerShell**。  
2. 复制和粘贴以下 PowerShell，并编辑 `$workspaceId` 和 `$automationAccountId` 的值。  对于 `-Environment` 参数，有效值为 *AzureCloud* 或 *AzureUSGovernment*，具体取决于运行的云环境。     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }

# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

运行此脚本后，会在写入新 JobLogs 或 JobStreams 的 10 分钟内在 Log Analytics 中看到记录。

若要查看日志，在 Log Analytics 日志搜索中运行以下查询：`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>验证配置
要确认自动化帐户是否会将日志发送到 Log Analytics 工作空间，请使用以下 PowerShell 检查自动化帐户上的诊断是否设置正确：

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

在输出中确保：
+ Logs 下的 Enabled 的值为 True
+ WorkspaceId  的值设置为 Log Analytics 工作区的资源 ResourceId


## <a name="log-analytics-records"></a>Log Analytics 记录
来自 Azure 自动化的诊断将在 Log Analytics 中创建两种类型的记录，并标记为 Type=AzureDiagnostics。

### <a name="job-logs"></a>作业日志
| 属性 | 说明 |
| --- | --- |
| TimeGenerated |执行 Runbook 作业的日期和时间。 |
| RunbookName_s |Runbook 的名称。 |
| Caller_s |谁启动了该操作。  可能的值为电子邮件地址或计划作业的系统。 |
| Tenant_g | GUID，用于为 Caller 标识租户。 |
| JobId_g |用作 Runbook 作业 ID 的 GUID。 |
| ResultType |Runbook 作业的状态。  可能的值包括：<br>- New（新）<br>- Started（已启动）<br>- Stopped（已停止）<br>- Suspended（已暂停）<br>- Failed（失败）<br>- Completed（已完成） |
| 类别 | 数据类型的分类。  对于自动化，该值为 JobLogs。 |
| OperationName | 指定在 Azure 中执行的操作类型。  对于自动化，该值为 Job。 |
| 资源 | 自动化帐户的名称 |
| SourceSystem | Log Analytics 收集数据的方式。 对于 Azure 诊断，始终为 Azure。 |
| ResultDescription |描述 Runbook 作业结果状态。  可能的值包括：<br>- 作业已启动<br>- 作业失败<br>- 作业已完成 |
| CorrelationId |用作 Runbook 作业相关性 ID 的 GUID。 |
| ResourceId |指定 Runbook 的 Azure 自动化帐户资源 ID。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID (GUID)。 |
| resourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>作业流
| 属性 | 说明 |
| --- | --- |
| TimeGenerated |执行 Runbook 作业的日期和时间。 |
| RunbookName_s |Runbook 的名称。 |
| Caller_s |谁启动了该操作。  可能的值为电子邮件地址或计划作业的系统。 |
| StreamType_s |作业流的类型。 可能的值包括：<br>- Progress（进度）<br>- Output（输出）<br>- Warning（警告）<br>- Error（错误）<br>- Debug（调试）<br>- Verbose（详细） |
| Tenant_g | GUID，用于为 Caller 标识租户。 |
| JobId_g |用作 Runbook 作业 ID 的 GUID。 |
| ResultType |Runbook 作业的状态。  可能的值包括：<br>- In Progress |
| 类别 | 数据类型的分类。  对于自动化，该值为 JobStreams。 |
| OperationName | 指定在 Azure 中执行的操作类型。  对于自动化，该值为 Job。 |
| 资源 | 自动化帐户的名称 |
| SourceSystem | Log Analytics 收集数据的方式。 对于 Azure 诊断，始终为 Azure。 |
| ResultDescription |包括来自 Runbook 的输出流。 |
| CorrelationId |用作 Runbook 作业相关性 ID 的 GUID。 |
| ResourceId |指定 Runbook 的 Azure 自动化帐户资源 ID。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID (GUID)。 |
| resourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>在 Log Analytics 中查看自动化日志
既然已开始会自动化作业日志发送到 Log Analytics，来看看可以在 Log Analytics 中对这些日志执行哪些操作。

若要查看日志，请运行以下查询：`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook 作业失败或暂停时发送电子邮件
我们的一家重要客户提出的要求是，当 Runbook 作业出现问题时能够发送电子邮件或短信。   

若要创建警报规则，首先请针对应该调用警报的 Runbook 作业记录创建日志搜索。  单击“警报”按钮以创建和配置警报的规则。

1. 在 Log Analytics“概述”页面中，单击“日志搜索”。
2. 通过在查询字段中键入以下搜索，为警报创建日志搜索查询：`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)`  还可以使用以下命令按 RunbookName 分组：`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   如果已设置在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。  自动化帐户名称可能派生自 JobLogs 搜索中的 Resource 字段。  
3. 若要打开“添加警报规则”屏幕，请单击页面顶部的“警报”。 若要详细了解用于配置警报的选项，请参阅 [Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md#alert-rules)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>查找已完成但出错的所有作业
除了在失败时发出警报外，还可以发现 Runbook 作业何时发生非终止错误。 在这些情况下，PowerShell 会生成错误流，但非终止错误不会导致作业暂停或失败。    

1. 在 Log Analytics 工作区中，单击“日志搜索”。
2. 在“查询”字段中键入 `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g`，并单击“**搜索**”。

### <a name="view-job-streams-for-a-job"></a>查看作业的作业流
调试作业时，可能想要深入查看作业流。  以下查询会显示 GUID 为 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的单个作业的所有流：   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>查看历史作业状态
最后，可能需要随时间对作业历史记录进行可视化。  可以使用此查询来搜索作业在不同时间段的状态。

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![OMS 历史作业状态图表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>摘要
将自动化作业状态和流数据发送到 Log Analytics 后，可以通过以下操作更好地了解自动化作业的状态：
+ 设置警报，以便在出现问题时获得通知
+ 使用自定义视图和搜索查询直观地显示 Runbook 结果、Runbook 作业状态，以及其他相关的关键指标。  

Log Analytics 可以更直观地显示自动化作业的运行情况，并且可以帮助更快地解决事件。  

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何使用 Log Analytics 构造不同的搜索查询和查看自动化作业日志，请参阅 [Log searches in Log Analytics](../log-analytics/log-analytics-log-searches.md)（Log Analytics 中的日志搜索）
* 若要了解如何创建输出和错误消息以及从 Runbook 检索此类消息，请参阅 [Runbook 输出和消息](automation-runbook-output-and-messages.md)
* 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅 [Track a runbook job](automation-runbook-execution.md)（跟踪 Runbook 作业）
* 若要了解有关 OMS Log Analytics 和数据收集源的详细信息，请参阅 [Collecting Azure storage data in Log Analytics overview](../log-analytics/log-analytics-azure-storage.md)（在 Log Analytics 中收集 Azure 存储数据概述）

