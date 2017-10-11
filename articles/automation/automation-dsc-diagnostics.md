---
title: "转发到 OMS 日志分析报告数据的 Azure 自动化 DSC |Microsoft 文档"
description: "本文演示如何发送到 Microsoft Operations Management Suite 日志分析以提供其他见解和管理报表数据 Desired State Configuration (DSC)。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: eslesar
ms.openlocfilehash: 316031c5297a0201c8db4a9e177298c78962c673
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="forward-azure-automation-dsc-reporting-data-to-oms-log-analytics"></a>转发到 OMS 日志分析报告数据的 Azure 自动化 DSC

自动化可将 DSC 节点状态数据发送到 Microsoft Operations Management Suite (OMS) 日志分析工作区。  
符合性状态将显示在 Azure 门户中，或使用 PowerShell，节点和节点配置中的单个 DSC 资源。 使用日志分析，你可以：

* 获得托管的节点和各个资源的符合性信息
* 触发电子邮件或基于符合性状态的警报
* 在托管节点编写高级的查询
* 关联的自动化帐户的符合性状态
* 随着时间的推移可视化节点符合性历史记录

## <a name="prerequisites"></a>必备条件

若要开始将您的自动化 DSC 报告发送到日志分析，你需要：

* 2016 或更高版本年 11 月发行的[Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
* Azure 自动化帐户。 有关详细信息，请参阅[Getting Started with Azure 自动化](automation-offering-get-started.md)
* 具有的日志分析工作区**自动化与控制**服务产品。 有关详细信息，请参阅[入门日志分析](../log-analytics/log-analytics-get-started.md)。
* 至少一个 Azure 自动化 DSC 节点。 有关详细信息，请参阅[载入机以进行管理的 Azure 自动化 DSC](automation-dsc-onboarding.md) 

## <a name="set-up-integration-with-log-analytics"></a>设置的日志分析的集成

若要开始将数据从 Azure 自动化 DSC 导入日志分析，请完成以下步骤：

1. 登录到你在 PowerShell 中的 Azure 帐户。 请参阅[登录 Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. 获取_ResourceId_你的自动化帐户通过运行以下 PowerShell 命令: (如果有多个 automation 帐户，选择_ResourceID_你想要配置的帐户)。

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. 获取_ResourceId_你通过运行以下 PowerShell 命令的日志分析工作区的: (如果有多个工作区中，选择_ResourceID_你想要配置工作区)。

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. 运行以下 PowerShell 命令，替换`<AutomationResourceId>`和`<WorkspaceResourceId>`与_ResourceId_每前面的步骤中的值：

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

如果你想要停止将数据从 Azure 自动化 DSC 导入日志分析，运行以下 PowerShell 命令。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>查看 DSC 日志

自动化 DSC 数据，设置的日志分析的集成后**日志搜索**按钮将出现在**DSC 节点**你的自动化帐户的边栏选项卡。 单击**日志搜索**按钮，以查看 DSC 节点数据的日志。

![日志搜索按钮](media/automation-dsc-diagnostics/log-search-button.png)

**日志搜索**边栏选项卡将打开，并且你看到**DscNodeStatusData**操作对于每个 DSC 节点，和一个**DscResourceStatusData**每个操作[DSC 资源](https://msdn.microsoft.com/powershell/dsc/resources)调用中应用于该节点的节点配置。

**DscResourceStatusData**操作包含信息失败的任何 DSC 资源时出错。

单击要查看该操作的数据的列表中每个操作。

你还可以通过以下方式查看日志通过 [中日志分析搜索。 请参阅[找到使用日志搜索数据](../log-analytics/log-analytics-log-searches.md)。
键入以下查询以查找 DSC 日志：`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

由操作名称，还可以将查询范围缩小。 例如: 类型 = AzureDiagnostics ResourceProvider ="MICROSOFT。自动化"类别"DscNodeStatus"OperationName = ="DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>DSC 法规遵从性检查失败时发送一封电子邮件

我们顶部客户请求之一是用于发送一封电子邮件或文本时出现问题时 DSC 配置的能力。   

若要创建警报规则时，启动时通过创建的警报应调用的 DSC 报表记录的日志搜索。  单击**警报**按钮以创建并配置警报的规则。

1. 在日志分析概述页中，单击**日志搜索**。
1. 通过键入以下搜索查询字段中创建你的警报的日志搜索查询：`Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  如果你设置了日志从多个 Automation 帐户或订阅到工作区，你可以通过订阅和自动化帐户你警报进行分组。  
  自动化帐户名称可以派生自 DscNodeStatusData 搜索中的资源字段。  
1. 若要打开**添加警报规则**屏幕上，单击**警报**页顶部。 有关配置警报的选项的详细信息，请参阅[日志分析中的警报](../log-analytics/log-analytics-alerts.md#alert-rules)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有节点中查找失败的 DSC 资源

使用日志分析的一个优点是，你可以在节点搜索有关失败的检查。
若要查找失败的 DSC 资源的所有实例。

1. 在日志分析概述页中，单击**日志搜索**。
1. 通过键入以下搜索查询字段中创建你的警报的日志搜索查询：`Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>查看历史 DSC 节点状态

最后，你可能想要随着时间的推移可视化你 DSC 节点状态的历史记录。  
可以使用此查询来搜索随着时间的推移你 DSC 节点状态的状态。

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

随着时间的推移，这将显示节点状态的图。

## <a name="log-analytics-records"></a>日志分析记录

Azure 自动化中的诊断日志分析中创建两种类别的记录。

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 属性 | 描述 |
| --- | --- |
| TimeGenerated |日期和法规遵从性检查运行的时间。 |
| OperationName |DscNodeStatusData |
| ResultType |节点是否合规。 |
| NodeName_s |托管节点的名称。 |
| NodeComplianceStatus_s |节点是否合规。 |
| DscReportStatus |是否符合性检查已成功运行。 |
| ConfigurationMode | 如何配置应用到节点。 可能的值为__"ApplyOnly"__，__"ApplyandMonitior"__，和__"ApplyandAutoCorrect"__。 <ul><li>__ApplyOnly__: DSC 将应用配置，除非新配置推送到目标节点或从服务器请求新配置，则它不执行任何进一步操作。 后首次应用新配置，DSC 不检查偏离以前配置的状态。 DSC 尝试，直到成功之前应用配置__ApplyOnly__将生效。 </li><li> __ApplyAndMonitor__： 这是默认值。 LCM 将应用任意新配置。 后首次应用新配置，如果目标节点偏离适当状态，DSC 会报告日志中的差异。 DSC 尝试，直到成功之前应用配置__ApplyAndMonitor__将生效。</li><li>__ApplyAndAutoCorrect__: DSC 将应用任意新配置。 后首次应用新配置，如果目标节点偏离适当状态，DSC 会报告在日志中，差异，然后重新应用当前配置。</li></ul> |
| HostName_s | 托管节点的名称。 |
| IPAddress | 托管节点的 IPv4 地址。 |
| Category | DscNodeStatus |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 标识对调用方的租户的 GUID。 |
| NodeId_g |用于托管的节点标识的 GUID。 |
| DscReportId_g |标识报表的 GUID。 |
| LastSeenTime_t |日期和上一次查看报表的时间。 |
| ReportStartTime_t |日期和时间发起报表时。 |
| ReportEndTime_t |完成日期和时间时报表。 |
| NumberOfResources_d |在应用于节点的配置中调用 DSC 资源数。 |
| SourceSystem | 日志分析收集数据的方式。 始终*Azure*有关 Azure 诊断。 |
| 资源 Id |指定的 Azure 自动化帐户。 |
| resultDescription | 此操作的说明。 |
| SubscriptionId | Azure 订阅 Id (GUID) 的自动化帐户。 |
| 资源组 | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT。自动化 |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |是相容性报告的相关 Id 的 GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 属性 | 描述 |
| --- | --- |
| TimeGenerated |日期和法规遵从性检查运行的时间。 |
| OperationName |DscResourceStatusData|
| ResultType |该资源是否符合。 |
| NodeName_s |托管节点的名称。 |
| Category | DscNodeStatus |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 标识对调用方的租户的 GUID。 |
| NodeId_g |用于托管的节点标识的 GUID。 |
| DscReportId_g |标识报表的 GUID。 |
| DscResourceId_s |DSC 资源实例的名称。 |
| DscResourceName_s |DSC 资源的名称。 |
| DscResourceStatus_s |DSC 资源是否符合规范。 |
| DscModuleName_s |包含 DSC 资源的 PowerShell 模块的名称。 |
| DscModuleVersion_s |包含 DSC 资源的 PowerShell 模块的版本。 |
| DscConfigurationName_s |配置应用于该节点的名称。 |
| ErrorCode_s | 如果资源已失败错误代码。 |
| ErrorMessage_s |如果资源已失败的错误消息。 |
| DscResourceDuration_d |以秒为单位，DSC 资源运行时间。 |
| SourceSystem | 日志分析收集数据的方式。 始终*Azure*有关 Azure 诊断。 |
| 资源 Id |指定的 Azure 自动化帐户。 |
| resultDescription | 此操作的说明。 |
| SubscriptionId | Azure 订阅 Id (GUID) 的自动化帐户。 |
| 资源组 | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT。自动化 |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |是相容性报告的相关 Id 的 GUID。 |

## <a name="summary"></a>“摘要”

通过将你的自动化 DSC 数据发送到日志分析，你可以获得更好地了解由你自动化 DSC 的节点的状态：

* 设置警报以通知你时出现问题
* 使用自定义视图和搜索查询来可视化你的 runbook 结果可，runbook 作业状态，以及其他相关的关键指标。  

日志分析提供到你的自动化 DSC 数据的更高运营可见性，并且可以更快地帮助解决这些事件。  

## <a name="next-steps"></a>后续步骤

* 若要了解有关如何构造不同的搜索查询，并查看自动化 DSC 日志的日志分析的详细信息，请参阅[中日志分析日志搜索](../log-analytics/log-analytics-log-searches.md)
* 若要了解有关使用 Azure 自动化 DSC 的详细信息，请参阅[开始使用 Azure 自动化 DSC](automation-dsc-getting-started.md)
* 若要了解有关 OMS 日志分析和数据集合源的详细信息，请参阅[收集 Azure 日志分析概述中的存储数据](../log-analytics/log-analytics-azure-storage.md)

