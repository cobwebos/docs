---
title: 使用资源管理器模板创建日志警报
description: 了解如何使用 Azure 资源管理器模板和 API 创建日志警报。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5afa34a5eadf5367b3ab28749735197ca6ed82bd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263195"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>使用资源管理器模板创建日志警报
本文演示如何在 Azure 中通过 [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) 和 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 使用 [Azure 资源管理器模板](..//azure-resource-manager/resource-group-authoring-templates.md)，以编程方式大规模管理[日志警报](monitor-alerts-unified-log.md)。 目前，Azure 警报支持与来自 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 和 [Azure Application Insights](../application-insights/app-insights-analytics-tour.md) 的查询相关的日志警报。

## <a name="managing-log-alert-on-log-analytics"></a>在 Log Analytics 上管理日志警报
[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 的日志警报集成到[新的 Azure 警报体验](monitoring-overview-unified-alerts.md)；然而它仍然运行 Log Analytics API 并保持与之前用来管理 [OMS 门户中的警报](..//log-analytics/log-analytics-alerts-creating.md)的架构的兼容性。

> [!NOTE]
> 从 2018 年 5 月 14 日起，工作区的所有警报都将开始自动扩展到 Azure 。 在 2018 年 5 月 14 日之前，用户可以自愿开始将警报扩展到 Azure。 有关详细信息，请参阅[将警报从 OMS 扩展到 Azure](monitoring-alerts-extend.md)。 

### <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
通过警报规则创建 Log Analytics 的日志警报，该规则按固定时间间隔运行保存的搜索。 如果查询结果与指定的条件相符，则会创建一个警报记录且会运行一个或多个操作。 

文档的 Log Analytics 部分提供 [Log Analytics 保存搜索](../log-analytics/log-analytics-log-searches.md)和 [Log Analytics 警报](../log-analytics/log-analytics-alerts.md)的资源模板。 详细了解[添加 Log Analytics 保存搜索和警报](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)；其中包括说明性的示例和架构详细信息。

### <a name="using-resource-template-via-apipowershell"></a>通过 API/Powershell 使用资源模板
Log Analytics 警报 REST API 为 RESTful，可通过 Azure 资源管理器 REST API 访问。 因此，API 可以从 PowerShell 命令行访问，并输出 JSON 格式的搜索结果，以便用户可通过编程方式采用多种不同的方法使用结果。

详细了解[在 Log Analytics 中通过 REST API 创建和管理警报规则](../log-analytics/log-analytics-api-alerts.md)；包括从 Powershell 访问 API 的示例。

## <a name="managing-log-alert-on-application-insights"></a>在 Application Insights 上管理日志警报
Azure Application Insights 的日志警报已经引入 Azure Monitor 下的新 Azure 警报中。 因此，它作为 [Scheduled Query Rules](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/)（计划查询规则）REST 操作组在 Azure Monitor API 下运行。

### <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
Application Insights 资源的日志警报的一种类型为 `Microsoft.Insights/scheduledQueryRules/`。 有关此资源类型的详细信息，请参阅 [Azure Monitor - 计划查询规则 API 参考](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/)。

以下是基于资源模板的[计划查询规则创建](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate)结构，其中示例数据集作为变量。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> 使用 [Scheduled Query Rules ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/)（计划查询规则）API 调用或资源模板时，必须有包含目标资源隐藏链接的标记字段。 

针对此演练，上面的示例 json 可以保存为（例如）sampleScheduledQueryRule.json，并且可以使用 [Azure 门户中的 Azure 资源管理器](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)进行部署。

### <a name="using-resource-template-via-clipowershell"></a>通过 CLI/Powershell 使用资源模板
Azure Monitor - 计划查询规则 API 为 REST API 并与 Azure 资源管理器 REST API 完全兼容。 因此，可以使用资源管理器 cmdlet 和 Azure CLI 通过 Powershell 使用它。

以下演示针对之前显示的示例资源模板 (sampleScheduledQueryRule.json) 通过 Azure 资源管理器 PowerShell cmdlet 的使用情况：
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
以下演示针对之前显示的示例资源模板 (sampleScheduledQueryRule.json) 通过 Azure CLI 中的 Azure 资源管理器命令的使用情况：

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
成功执行操作后，将返回 201 声明新的警报规则创建，如果修改了现有警报规则，则返回 200。


## <a name="next-steps"></a>后续步骤
* 了解[用于日志警报的 Webhook 操作](monitor-alerts-unified-log-webhook.md)
* 了解新的 [Azure 警报](monitoring-overview-unified-alerts.md)
* 详细了解 [Application Insights](../application-insights/app-insights-analytics.md)
* 详细了解 [Log Analytics](../log-analytics/log-analytics-overview.md)。   
