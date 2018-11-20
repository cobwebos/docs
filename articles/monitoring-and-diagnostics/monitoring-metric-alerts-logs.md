---
title: 在 Azure Monitor 中创建日志的指标警报
description: 有关针对常用 Log Analytics 数据创建近实时指标警报的教程。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 66a10cdd6324147509bcb45dad9e9b40b5335fef
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684904"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>在 Azure Monitor 中创建日志的指标警报  

## <a name="overview"></a>概述
Azure Monitor 支持比[经典警报](alert-metric-classic.md)更具优势的[指标警报类型](monitoring-near-real-time-metric-alerts.md)。 指标可用于 [Azure 服务的大型列表](monitoring-supported-metrics.md)。 本文解释某个资源子集的用法 - `Microsoft.OperationalInsights/workspaces`。 

可对提取为指标的常用 Log Analytics 日志使用指标警报。这些指标是日志中的指标一部分，包括 Azure 中或本地的资源。 下面列出了支持的 Log Analytics 解决方案：
- 适用于 Windows 和 Linux 计算机的[性能计数器](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [代理运行状况的检测信号记录](../monitoring/monitoring-solution-agenthealth.md)
- [更新管理](../automation/automation-update-management.md)记录
- [事件数据](../log-analytics/log-analytics-data-sources-windows-events.md)日志
 
与 Azure 中基于查询的[日志警报](alert-log.md)相比，使用**日志的指标警报**可带来多种优势；下面列出了其中的某些优势：
- 指标警报提供近实时监视功能，日志的指标警报从日志源克隆数据以确保相同
- 指标警报是有状态的 - 只会在激发警报以及解决警报时才通知一次；相反，日志警报是无状态的，只要满足警报条件，它就会按时间间隔保持激发
- 日志的指标警报提供多个维度，可以简化根据特定的值（例如“计算机”、“OS 类型”等）进行筛选的操作；无需在 Analytics 中编写查询

> [!NOTE]
> 只有在选定期间内存在其数据时，特定的指标和/或维度才会显示。 这些指标适用于使用 Azure Log Analytics 工作区的客户。

## <a name="metrics-and-dimensions-supported-for-logs"></a>日志支持的指标和维度
 指标警报支持针对使用维度的指标发出警报。 可以使用维度将指标筛选到适当级别。 受支持的解决方案将提供 [Log Analytics 工作区](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)中受日志支持的指标的完整列表。

> [!NOTE]
> 若要通过 [Azure Monitor - 指标](monitoring-metric-charts.md)查看从 Log Analytics 工作区中提取的受支持指标，必须为所述指标创建日志的指标警报。 只能通过“Azure Monitor - 指标”浏览在“日志的指标警报”中选择的维度。

# <a name="creating-metric-alert-for-log-analytics"></a>为 Log Analytics 创建指标警报
在 Log Analytics 中处理常用日志中的指标数据之前，会先通过管道将其传送到“Azure Monitor - 指标”。 这样，用户便可以利用指标平台的功能以及指标警报 - 包括创建频率低至 1 分钟的警报。 下面列出了为日志创建指标警报的方式。

## <a name="prerequisites-for-metric-alert-for-logs"></a>日志的指标警报的先决条件
在针对 Log Analytics 数据收集的日志指标正常工作之前，必须设置以下各项，并确保这些项可用：
1. **活动的 Log Analytics 工作区**：有效且活动的 Log Analytics 工作区必须存在。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md)。
2. **为 Log Analytics 工作区配置代理**：需要为 Azure VM 和/或本地 VM 配置代理，以便将数据发送到前一步骤中使用的 Log Analytics 工作区。 有关详细信息，请参阅 [Log Analytics - 代理概述](../monitoring/monitoring-overview-azure-agents.md)。
3. **安装受支持的 Log Analytics 解决方案**：Log Analytics 解决方案应已进行配置并可将数据发送到 Log Analytics 工作区 - 支持的解决方案为[适用于 Windows 和 Linux 的性能计数器](../log-analytics/log-analytics-data-sources-performance-counters.md)、[代理运行状况的检测信号记录](../monitoring/monitoring-solution-agenthealth.md)、更新管理和[事件数据](../log-analytics/log-analytics-data-sources-windows-events.md)。
4. **配置为发送日志的 Log Analytics 解决方案**：Log Analytics 解决方案应已启用与 [Log Analytics 工作区支持的指标](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)对应的所需日志/数据。 例如，必须先在[性能计数器](../log-analytics/log-analytics-data-sources-performance-counters.md)解决方案中配置它的“可用内存百分比”计数器。

## <a name="configuring-metric-alert-for-logs"></a>配置日志的指标警报
 可以使用 Azure 门户、资源管理器模板、REST API、PowerShell 和 Azure CLI 来创建和管理指标警报。 由于日志的指标警报是指标警报的变体，在满足先决条件后，可为指定的 Log Analytics 工作区创建日志的指标警报。 [指标警报](monitoring-near-real-time-metric-alerts.md)的所有特征和功能同样适用于日志的指标警报，包括有效负载架构、适用的配额限制和计费价格。

有关详细分步说明和示例，请参阅[创建和管理指标警报](https://aka.ms/createmetricalert)。 具体而言，对于日志的指标警报，请遵照说明管理指标警报，并确保：
- 指标警报的目标是有效的 Log Analytics 工作区
- 为选定 Log Analytics 工作区的指标警报选择的信号类型为“指标”
- 使用维度筛选器根据特定的条件或资源进行筛选；日志的指标是多维的
- 配置信号逻辑时，可以创建单个警报来跨越多个维度值（例如“计算机”）
- 如果**不**使用 Azure 门户为选定的 Log Analytics 工作区创建指标警报，则用户必须先手动创建一个显式规则，以便使用 [Azure Monitor - 计划的查询规则](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules
)将日志数据转换为指标。 **

> [!NOTE]
> 通过 Azure 门户为 Log Analytics 工作区创建指标警报时，会自动在后台创建相应的规则用于通过 [Azure Monitor - 计划的查询规则](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)将日志数据转换为指标，无需任何用户干预或操作。 如果不使用 Azure 门户创建日志的指标警报，请参阅[日志的指标警报的资源模板](#resource-template-for-metric-alerts-for-logs)部分，了解在创建指标警报之前，创建基于 ScheduledQueryRule 的“日志到指标”转换规则的示例方法 - 否则没有任何数据可用于创建日志的指标警报。

## <a name="resource-template-for-metric-alerts-for-logs"></a>日志的指标警报的资源模板
如前所述，从日志创建指标警报的过程由两个部分组成：
1. 使用 scheduledQueryRule API 创建用于从支持的日志中提取指标的规则
2. 针对从日志中提取的（步骤 1）和从用作目标资源的 Log Analytics 工作区中提取的指标创建指标警报

为实现相同的效果，可以使用下面的示例 Azure 资源管理器模板 - 只有在成功创建了用于通过 scheduledQueryRule 从日志中提取指标的规则之后，才能创建指标警报。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],   
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}

```
假设上述 JSON 保存为 metricfromLogsAlert.json，可将其与某个参数 JSON 文件相结合，在资源模板中创建警报。 下面列出了示例参数 JSON 文件：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan" 
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }    
}
```
假设上述参数文件保存为 metricfromLogsAlert.parameters.json，则可以使用 [Azure 门户中用于创建警报的资源模板](../azure-resource-manager/resource-group-template-deploy-portal.md)来创建日志的指标警报。 

也可以使用以下 Azure Powershell 命令：
```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlert.json TemplateParameterFile metricfromLogsAlert.parameters.json
```

或使用 Azure CLI 部署资源模板：
```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlert.json --parameters @metricfromLogsAlert.parameters.json
```

## <a name="next-steps"></a>后续步骤

* 详细了解[指标警报](https://aka.ms/createmetricalert)。
* 了解 [Azure 中的日志警报](monitor-alerts-unified-log.md)。
* 了解 [Azure 中的警报](monitoring-overview-alerts.md)。
