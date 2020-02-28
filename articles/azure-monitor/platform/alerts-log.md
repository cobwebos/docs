---
title: 使用 Azure Monitor 创建、查看和管理日志警报 |Microsoft Docs
description: 使用 Azure Monitor 创作、查看和管理 Azure 中的日志警报规则。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667846"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理日志警报

## <a name="overview"></a>概述
本文展示了如何使用 Azure 门户中的警报界面设置日志警报。 警报规则的定义分为三个部分：
- 目标：要监视的特定 Azure 资源
- 条件：特定的条件或逻辑，出现在“信号”中时，应触发操作
- 操作：发送到通知接收方 - 电子邮件、短信、Webhook 等的特定调用。

术语**日志警报**来描述警报，其中的信号是[Log Analytics 工作区](../learn/tutorial-viewdata.md)或[Application Insights](../app/analytics.md)中的日志查询。 从[日志警报 - 概述](alerts-unified-log.md)中详细了解功能、术语和类型。

> [!NOTE]
> [Log Analytics 工作区](../../azure-monitor/learn/tutorial-viewdata.md)中的常用日志数据现在也可用于 Azure Monitor 的指标平台。 有关详细信息，请查看[日志的指标警报](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>从 Azure 门户中管理日志警报

接下来的详细信息是通过 Azure 门户界面使用日志警报的分步指南。

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>使用 Azure 门户创建日志警报规则

1. 在[门户](https://portal.azure.com/)中选择“监视器”，然后在“监视器”部分下选择“警报”。

    ![监视](media/alerts-log/AlertsPreviewMenu.png)

1. 选择“新建警报规则”按钮，在 Azure 中创建新警报。

    ![添加警报](media/alerts-log/AlertsPreviewOption.png)

1. 此时会显示“创建警报”部分，其中包括三个组成部分：“定义警报条件”、“定义警报详细信息”和“定义操作组”。

    ![创建规则](media/alerts-log/AlertsPreviewAdd.png)

1. 定义警报条件：使用“选择资源”链接，然后通过选择资源来指定目标。 进行筛选：选择“订阅”和“资源类型”，以及所需的资源。

   > [!NOTE]
   > 创建日志警报 - 在继续操作之前，请验证**日志**信号是否可用于所选资源。
   >  ![选择资源](media/alerts-log/Alert-SelectResourceLog.png)

1. *日志警报*：确保“资源类型”是分析源（例如 Log Analytics 或 Application Insights），且信号类型为“日志”，并选择相应的资源，然后单击“完成”。 接下来，使用“添加条件”按钮查看适用于该资源的信号选项列表，并针对所选日志监视服务（如 **Log Analytics** 或 **Application Insights**）从信号列表中选择“自定义日志搜索”选项。

   ![选择资源 - 自定义日志搜索](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > 警报列表可以导入分析查询作为信号类型 - **日志（已保存查询）** ，如上图所示。 因此，用户可以在分析中优化查询，然后将其保存以供将来在警报中使用，更详细地了解如何使用 Azure Monitor 中的日志查询或在[application insights Analytics 中共享查询](../app/app-insights-overview.md)中使用[日志查询](../log-query/log-query-overview.md)。

1. *日志警报*：选择后，可以在“搜索查询”字段中指定警报查询；如果查询语法不正确，该字段将以红色显示错误。 如果查询语法正确 - 将以图表形式显示指定查询的历史数据供参考，同时显示用于调整时间范围（过去六个小时到过去一周）的选项。

    ![配置警报规则](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > 仅当查询结果包含时间详细信息时，才能显示历史数据可视化效果。 如果查询生成了汇总数据或特定列值 - 则以单一绘图的形式显示相同的数据。
   > 对于使用 Application insights 或[切换到新的 API](alerts-log-api-switch.md) 的指标度量类型的日志警报，可以使用“聚合基于”选项指定要使用哪个特定变量对数据进行分组，如下面所示：
   > 
   > ![“聚合基于”选项](media/alerts-log/aggregate-on.png)

1. *日志警报*：打开可视化效果后，可以从显示的“条件”、“聚合”和“阈值”选项中选择“警报逻辑”。 最后，使用“时间段”选项在逻辑中指定评估指定条件的时间。 此外，通过选择“频率”来指定运行警报服务的频率。 日志警报可以基于：
    - [记录数目](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules)：如果查询返回的记录计数大于或小于提供的值，则创建警报。
    - [指标度量](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)：如果结果中的每个聚合值超过提供的阈值并且是“分组依据”选定值，则创建警报。 警报违规数是在选定时间段内超过阈值的次数。 可以为结果集中的任何违规组合指定总违规数，或指定连续违规数以要求违规必须在连续采样时发生。


1. 第二个步骤是在“警报规则名称”字段中定义警报的名称，提供**说明**用于详细描述该警报的具体信息，并从提供的选项中指定“严重性”值。 在 Azure Monitor 发送的所有警报电子邮件、通知或推送内容中，将重用这些详细信息。 此外，用户可以通过相应地切换“创建后启用规则”选项，选择在创建后立即激活该警报规则。

    在警报详细信息中可以使用一些附加的功能（仅适用于**日志警报**）：

    - **阻止警报**：如果打开警报规则的阻止功能，则新建警报之后会在定义的时间段内禁用该规则的操作。 此规则仍在运行中，并且会在满足条件的情况下创建警报记录。 这是为了让你有时间更正问题，而无需运行重复操作。

        ![对日志警报禁止显示警报](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > 指定的禁止显示警报值应大于警报的频率，以确保在没有重叠的情况下停止通知

1. 第三个步骤（也是最后一个步骤）是指定在满足警报条件的情况下，是否需要对警报规则触发任何**操作组**。 可以选择包含警报的任何现有操作组，也可以创建新的操作组。 根据选定的操作组，触发警报时，Azure 将会：发送电子邮件、发送短信、调用 Webhook、使用 Azure Runbook 进行补救、推送到 ITSM 工具，等等。 详细了解[操作组](action-groups.md)。

    > [!NOTE]
    > 有关通过 Azure 操作组为日志警报触发的 Runbook 有效负载的限制，请参考 [Azure 订阅服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)

    对于**日志警报**，提供了一些附加功能用于替代默认操作：

    - **电子邮件通知**：如果所述操作组中存在一个或多个电子邮件操作，替代通过操作组发送的电子邮件中的电子邮件主题。 无法修改邮件正文，并且该字段不能用于电子邮件地址。
    - **包括自定义 Json 有效负载**：如果所述操作组中存在一个或多个 Webhook 操作，替代操作组所使用的 Webhook JSON。 用户可以指定所有在关联的操作组中配置的 Webhook 所使用的 JSON 格式；有关 Webhook 格式的详细信息，请参阅[针对日志警报的 Webhook 操作](../../azure-monitor/platform/alerts-log-webhook.md)。 提供了“查看 Webhook”选项来使用示例 JSON 数据检查格式。

        ![日志警报的操作替代](media/alerts-log/AlertsPreviewOverrideLog.png)


1. 如果所有字段有效并且附带绿色的勾选标记，则可以单击“创建警报规则”按钮，在“Azure Monitor - 警报”中创建警报。 可以从警报仪表板查看所有警报。

     ![创建规则](media/alerts-log/AlertsPreviewCreate.png)

     几分钟后，警报将处于活动状态，并按前面所述进行触发。

用户还可以在[log analytics](../log-query/portals.md)中完成其分析查询，然后将其推送到通过 "设置警报" 按钮创建警报，然后按照上述教程的步骤6中的说明进行操作。

 ![Log Analytics - 设置警报](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>在 Azure 门户中查看和管理日志警报

1. 在[门户](https://portal.azure.com/)中选择“监视器”，然后在“监视器”部分下选择“警报”。

1. **此时将显示警报仪表板** - 其中，所有 Azure 警报（包括日志警报）都显示在单个面板中；包括你的日志警报规则触发时间的每个实例。 若要了解详细信息，请参阅[警报管理](https://aka.ms/managealertinstances)。
    > [!NOTE]
    > 日志警报规则包括由用户提供的基于自定义查询的逻辑，因此不存在已解决状态。 因此，每当满足日志警报规则中指定的条件时，它都会触发。

1. 在顶部栏中选择“管理规则”按钮，导航到规则管理部分 - 其中列出了创建的所有警报规则，包括已禁用的警报。
    ![管理警报规则](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>使用 Azure 资源模板管理日志警报

Azure Monitor 中的日志警报与资源类型 `Microsoft.Insights/scheduledQueryRules/` 相关联。 有关此资源类型的详细信息，请参阅 [Azure Monitor - 计划查询规则 API 参考](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)。 可以使用[计划查询规则 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) 为 Application Insights 或 Log Analytics 创建日志警报。

> [!NOTE]
> 还可以使用旧式 [Log Analytics 警报 API](api-alerts.md) 以及 [Log Analytics 保存的搜索和警报](../insights/solutions-resources-searches-alerts.md)的旧式模板管理 Log Analytics 的日志警报。 有关默认使用此处详述的新 ScheduledQueryRules API 的详细信息，请参阅[切换到 Log Analytics 警报的新 API](alerts-log-api-switch.md)。


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>使用 Azure 资源模板创建日志警报示例

以下是基于资源模板的[计划查询规则创建](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate)结构，它使用[结果类型日志警报的数量](alerts-unified-log.md#number-of-results-alert-rules)的标准日志搜索查询，其中示例数据集作为变量。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
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
        "properties":{
            "description": "[variables('alertDescription')]",
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
    } ]
}

```

针对此演练，上面的示例 json 可以保存为（例如）sampleScheduledQueryRule.json，并且可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)进行部署。


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>使用 Azure 资源模板进行跨资源查询的日志警报

以下是基于资源模板的[计划查询规则创建](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate)结构，它使用[指标度量值类型日志警报](../../azure-monitor/log-query/cross-workspace-query.md)的[跨资源日志搜索查询](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)，其中示例数据集作为变量。

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
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
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> 使用日志警报中的跨资源查询时，必须使用 [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) 且用户必须有权访问所述的资源列表

针对此演练，上面的示例 json 可以保存为（例如）sampleScheduledQueryRule.json，并且可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)进行部署。

## <a name="managing-log-alerts-using-powershell"></a>使用 PowerShell 管理日志警报

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor[计划的查询规则 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)是一个 REST API 并与 Azure 资源管理器 REST API 完全兼容。 下面列出的和 PowerShell cmdlet 可用于利用[计划的查询规则 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)。

1. [AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) ： Powershell cmdlet，用于创建新的日志警报规则。
1. 用于更新现有日志警报规则的[AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) ： Powershell cmdlet。
1. [AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) ： Powershell cmdlet，用于创建或更新指定日志警报的源参数的对象。 用作[AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule)和[AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 的输入。
1. [AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule)： Powershell cmdlet，用于创建或更新指定日志警报计划参数的对象。 用作[AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule)和[AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 的输入。
1. [AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) ： Powershell cmdlet，用于创建或更新指定日志警报的操作参数的对象。 用作[AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule)和[AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 的输入。
1. [AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) ： Powershell cmdlet，用于创建或更新指定操作组日志警报参数的对象。 用作[AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet 的输入。
1. [AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) ： Powershell cmdlet，用于创建或更新指定日志警报的触发器条件参数的对象。 用作[AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet 的输入。
1. [AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) ： Powershell cmdlet，用于创建或更新为[指标度量类型日志警报](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)指定指标触发器条件参数的对象。 用作[AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet 的输入。
1. [AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) ： Powershell cmdlet 列出现有的日志警报规则或特定的日志警报规则
1. [AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) ： Powershell cmdlet，用于启用或禁用日志预警规则
1. [AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule)： Powershell cmdlet 用于删除现有的日志警报规则

> [!NOTE]
> ScheduledQueryRules PowerShell cmdlet 只能管理创建的规则或使用 Azure Monitor[计划的查询规则 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)。 使用旧[Log Analytics 警报 API](api-alerts.md)创建的日志警报规则和[Log Analytics 保存的搜索](../insights/solutions-resources-searches-alerts.md)的旧模板，仅在用户将[API 首选项用于 Log Analytics 警报](alerts-log-api-switch.md)后，才可以使用 ScheduledQueryRules PowerShell cmdlet 管理这些警报。

下面是使用 scheduledQueryRules PowerShell cmdlet 创建示例日志警报规则的步骤。
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>使用 CLI 或 API 管理日志警报

Azure Monitor[计划的查询规则 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)是一个 REST API 并与 Azure 资源管理器 REST API 完全兼容。 因此，可通过 Powershell 使用 Azure CLI 资源管理器命令来使用。


> [!NOTE]
> 还可以使用旧式 [Log Analytics 警报 API](api-alerts.md) 以及 [Log Analytics 保存的搜索和警报](../insights/solutions-resources-searches-alerts.md)的旧式模板管理 Log Analytics 的日志警报。 有关默认使用此处详述的新 ScheduledQueryRules API 的详细信息，请参阅[切换到 Log Analytics 警报的新 API](alerts-log-api-switch.md)。

当前没有专用 CLI 命令的日志警报;但如下图所示，可通过 Azure 资源管理器 CLI 命令用于资源模板部分中的示例资源模板（sampleScheduledQueryRule）：

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

成功执行操作后，将返回 201 声明新的警报规则创建，如果修改了现有警报规则，则返回 200。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure 警报中的日志警报](../../azure-monitor/platform/alerts-unified-log.md)
* 了解[用于日志警报的 Webhook 操作](../../azure-monitor/platform/alerts-log-webhook.md)
* 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)
* 详细了解[日志查询](../log-query/log-query-overview.md)。
