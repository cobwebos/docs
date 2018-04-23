---
title: 将管理事件的 Azure 警报迁移到活动日志警报 | Microsoft 文档
description: 有关管理事件的警报将于 10 月 1 日删除。 请准备通过迁移现有警报。
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 1cc02664a345da49c276a6876d5cdfaf5bdb8477
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>将管理事件的 Azure 警报迁移到活动日志警报


> [!WARNING]
> 有关管理事件的警报将于 10 月 1 日当天或之后关闭。 请使用以下说明进行操作，以了解你是否具有这些警报，如果有，请将其迁移。
>
> 

## <a name="what-is-changing"></a>有什么变化

Azure Monitor（以前称为 Azure Insights）提供了创建警报的功能，可触发管理事件并生成发送到 webhook URL 或电子邮件地址的通知。 你可能已通过下列任一方式创建了下列任一警报：
* 在某些资源类型的 Azure 门户中，在“监视”->“警报”->“添加警报”下，其中“警报”被设置为“事件”
* 通过运行 Add-AzureRmLogAlertRule PowerShell cmdlet
* 通过直接使用 odata.type = “ManagementEventRuleCondition” 以及 dataSource.odata.type = “RuleManagementEventDataSource” 的[警报 REST API](http://docs.microsoft.com/rest/api/monitor/alertrules)
 
以下 PowerShell 脚本将返回订阅中所具有的有关管理事件的所有警报列表，以及每个警报所设置的条件。

```powershell
Connect-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

如果你没有任何有关管理事件的警报，上面的 PowerShell cmdlet 将输出一系列类似下面的警告消息：

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

可以忽略这些警告消息。 如果有关于管理事件的警报，则此 PowerShell cmdlet 的输出将如下所示：

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

每个警报由虚线隔开，详细信息包括警报的资源 ID 和正在被监视的特定规则。

此功能已转换为 [Azure Monitor 活动日志警报](monitoring-activity-log-alerts.md)。 使用这些新警报，可让你设置有关活动日志事件的条件，并在新事件与条件匹配时接收通知。 另外，它们还会基于管理事件的警报提供多项改进：
* 你可以使用[操作组](monitoring-action-groups.md)，在多个警报中重复使用通知收件人（“操作”）组，从而在谁应接收警报方面减少更改的复杂性。
* 你可以直接在手机上将短信与操作组结合使用来接收通知。
* 可以[使用资源管理器模板创建活动日志警报](monitoring-create-activity-log-alerts-with-resource-manager-template.md)。
* 你可以创建具有更大灵活性和复杂性的条件，以满足你的特定需求。
* 可以更快地传递通知。
 
## <a name="how-to-migrate"></a>如何迁移
 
若要创建新的活动日志警报，你可以：
* 按照我们的有关[如何在 Azure 门户中创建警报的指南](monitoring-activity-log-alerts.md)进行操作
* 了解如何[使用资源管理器模板创建警报](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
有关你以前创建的管理事件的通知将不会自动迁移到活动日志警报。 你需要使用上述 PowerShell 脚本，列出当前已配置的管理事件警报，并手动将它们重新创建为活动日志警报。 此操作必须在 10 月 1 日之前完成，在此之后，有关管理事件的警报将不再显示在 Azure 订阅中。 其他类型的 Azure 警报，包括 Azure Monitor 指标警报、Application Insights 警报和 Log Analytics 警报都不会受此更改的影响。 如果你有任何问题，请在下面评论中提出。


## <a name="next-steps"></a>后续步骤

* 详细了解[活动日志](monitoring-overview-activity-logs.md)
* [通过 Azure 门户配置活动日志警报](monitoring-activity-log-alerts.md)
* [通过 Resource Manager 配置活动日志警报](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md)
* 详细了解[服务通知](monitoring-service-notifications.md)
* 详细了解[操作组](monitoring-action-groups.md)
