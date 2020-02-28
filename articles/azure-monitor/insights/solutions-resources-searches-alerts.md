---
title: 管理解决方案中已保存的搜索 |Microsoft Docs
description: 管理解决方案通常包括保存的日志查询，用于分析解决方案收集的数据。 本文介绍如何在资源管理器模板中定义 Log Analytics 保存的搜索。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663022"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>将 Log Analytics 保存的搜索和警报添加到管理解决方案（预览版）

> [!IMPORTANT]
> 如[前文所](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)述，在*2019 年6月 1*日之后创建的 log analytics 工作区，将能够**仅**使用 Azure scheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)、 [azure 资源管理器模板](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template)和[PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell)来管理警报规则。 对于较旧的工作区，客户可以轻松地[切换其首选的警报规则管理方法](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)，以利用 Azure Monitor scheduledQueryRules 作为默认功能，并获得许多[新的好处](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api)，例如使用本机 PowerShell cmdlet、在规则中增加 lookback 时间段、在单独的资源组或订阅中创建规则，等等。

> [!NOTE]
> 这是用于创建当前处于预览版的管理解决方案的初步文档。 如下所述的全部架构均会有变动。

[管理解决方案](solutions.md)通常会将 Log Analytics 中[保存的搜索](../../azure-monitor/log-query/log-query-overview.md)包括在内，以便分析解决方案收集的数据。 它们可能还会定义[警报](../../azure-monitor/platform/alerts-overview.md)，从而向用户发出通知或针对严重问题自动采取行动。 本文介绍如何在[资源管理模板](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)中定义 Log Analytics 保存的搜索和警报，以便将其纳入[管理解决方案](solutions-creating.md)。

> [!NOTE]
> 本文中的示例使用管理解决方案所需或通用的参数和变量，[在 Azure 中设计和构建管理解决方案](solutions-creating.md)中对它们进行了介绍

## <a name="prerequisites"></a>必备条件
本文假设你已经熟悉如何[创建管理解决方案](solutions-creating.md)以及[资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)和解决方案文件的结构。


## <a name="log-analytics-workspace"></a>Log Analytics 工作区
Log Analytics 中的所有资源都包含在[工作区](../../azure-monitor/platform/manage-access.md)中。 如 [Log Analytics 工作区和自动化帐户](solutions.md#log-analytics-workspace-and-automation-account)中所述，工作区不包括在管理解决方案中，但必须存在才可以安装解决方案。 如果不存在工作区，解决方案安装将失败。

工作区的名称包含在每个 Log Analytics 资源的名称中。 这是在具有 **workspace** 参数的解决方案中完成的，如以下 SavedSearch 资源示例所示。

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API 版本
资源管理器模板中定义的所有 Log Analytics 资源均包含 apiVersion属性，该属性将定义资源应使用的 API 版本。

下表列出了此示例中使用的资源的 API 版本。

| 资源类型 | API 版本 | 查询 |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>保存的搜索
将[保存的搜索](../../azure-monitor/log-query/log-query-overview.md)纳入解决方案后，用户可查询由解决方案收集的数据。 保存的搜索将在 Azure 门户的“保存的搜索”下显示。 每个警报也需要一个保存的搜索。

[Log Analytics 保存的搜索](../../azure-monitor/log-query/log-query-overview.md)资源的类型为 `Microsoft.OperationalInsights/workspaces/savedSearches` 且具有以下结构。 这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

下表介绍了保存的搜索的各个属性。

| properties | 说明 |
|:--- |:--- |
| category | 保存的搜索的类别。  同一解决方案中所有保存的搜索常共享一个类别，因此他们在控制台中组合在一起。 |
| displayname | 保存的搜索在门户中显示的名称。 |
| query | 要运行的查询。 |

> [!NOTE]
> 如果查询中包含可解释为 JSON 的字符，则可能需要在查询中使用转义字符。 例如，如果查询为 AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"，应在解决方案文件中将它编写为 AzureActivity | OperationName:/**Microsoft.Compute/virtualMachines/write\"\"** 。

## <a name="alerts"></a>警报
[Azure 日志警报](../../azure-monitor/platform/alerts-unified-log.md)是由定期运行指定日志查询的 Azure 警报规则创建的。 如果查询结果与指定的条件相符，则会创建一个警报记录，并且会使用[操作组](../../azure-monitor/platform/action-groups.md)运行一个或多个操作。

对于将警报扩展到 Azure 的用户，现在可以在 Azure 操作组中控制操作。 当工作区及其警报扩展到 Azure 后，可以使用[操作组 - Azure 资源管理器模板](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)检索或添加操作。
旧管理解决方案中的警报规则由以下三种不同资源组成。

- **保存的搜索。** 定义运行的日志搜索。 多个警报规则可共享一个保存的搜索。
- **计划。** 定义运行日志搜索的频率。 每个警报规则有且仅有一个计划。
- **警报操作。** 每个警报规则都具有一个类型为“Alert”的操作组资源或操作资源（旧版），它可定义警报的详细信息，例如定义创建警报记录的时间和警报严重性等条件。 [操作组](../../azure-monitor/platform/action-groups.md)资源可提供列出触发警报时可采取的配置操作的列表，例如：语音呼叫、短信、电子邮件、Webhook、ITSM 工具、自动化 runbook、逻辑应用等。

前面描述了保存的搜索资源。 下面会介绍其他资源。

### <a name="schedule-resource"></a>计划资源

保存的搜索可以拥有一个或多个计划，每个计划代表一个单独的警报规则。 计划定义搜索的运行频率和检索数据的时间间隔。 计划资源的类型为 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` 且具有以下结构。 这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
下表介绍了计划资源的属性。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| 已启用       | 是 | 说明创建警报后该警报是否启用。 |
| interval      | 是 | 查询运行的频率（以分钟为单位）。 |
| queryTimeSpan | 是 | 用于评估结果的时长（以分钟为单位）。 |

计划资源应该依赖于保存的搜索，以便在计划前创建资源。
> [!NOTE]
> 在一个给定的工作区中，计划名称必须是唯一的；两个计划 ID 不能一样，即使它们与不同的已保存搜索相关联。 此外，所有已保存的搜索、计划和使用 Log Analytics API 创建的操作的名称必须小写。

### <a name="actions"></a>操作
一个计划可以有多个操作。 操作可以定义一个或多个要执行的进程，例如发送邮件或启动 Runbook，也可以定义确定搜索结果与某些条件何时匹配的阈值。 某些操作将同时定义这两者，以便达到阈值时执行这些进程。
可使用 [操作组] 资源或操作资源定义操作。

存在两类由 **Type** 属性指定的操作资源。 一个计划需要一个“Alert”操作，该操作可定义警报规则的详细信息并在创建警报时定义要采取的操作。 操作资源的类型为 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`。

警报操作具有以下结构。 这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

下表介绍了 Alert 操作资源的属性。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| `type` | 是 | 操作的类型。  警报操作的类型是 Alert。 |
| `name` | 是 | 警报的显示名称。  这是警报规则在控制台中的显示名称。 |
| `description` | 否 | 警报的可选说明。 |
| `severity` | 是 | 警报记录的严重等级包括以下值：<br><br> 严重<br>警告<br>信息性

#### <a name="threshold"></a>阈值
本部分是必需的。 它定义警报阈值的属性。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| `Operator` | 是 | 比较运算符包括以下值：<br><br>**gt = 大于<br>lt = 小于** |
| `Value` | 是 | 要比较结果的值。 |

##### <a name="metricstrigger"></a>MetricsTrigger
本部分为可选。 将其包含在指标度量警报中。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| `TriggerCondition` | 是 | 以下值指定该阈值是总违规次数还是连续违规次数：<br><br>**总次数<br>连续次数** |
| `Operator` | 是 | 比较运算符包括以下值：<br><br>**gt = 大于<br>lt = 小于** |
| `Value` | 是 | 若要触发警报，该条件必须符合的次数。 |


#### <a name="throttling"></a>限制
本部分为可选。 创建警报后，若希望在一定时间内阻止通过同一规则创建的警报，请包含此部分。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| DurationInMinutes | 如果包含了限制元素，则为必需 | 从同一警报规则创建警报后，阻止警报的分钟数。 |

#### <a name="azure-action-group"></a>Azure 操作组
Azure 中的所有警报都使用操作组作为用来处理操作的默认机制。 使用操作组，可以将操作指定一次，然后将操作组关联到 Azure 中的多个警报。 不需要一再重复声明相同的操作。 操作组支持多个操作 - 包括电子邮件、SMS、语音呼叫、ITSM 连接、自动化 Runbook、Webhook URI，等等。

对于已将其警报扩展到 Azure 中的用户- 一个计划现在应当将操作组详细信息与阈值一起传递，以便能够创建警报。 在创建警报前，需要先在操作组中定义电子邮件详细信息、Webhook URL、Runbook 自动化详细信息以及其他操作；可以在门户中[通过 Azure Monitor 创建操作组](../../azure-monitor/platform/action-groups.md)，也可以使用[操作组 - 资源模板](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| AzNsNotification | 是 | Azure 操作组的资源 ID 应与警报相关联，以在满足警报条件时执行必要操作。 |
| CustomEmailSubject | 否 | 将邮件的自定义主题行发送到关联操作组中指定的所有地址。 |
| CustomWebhookPayload | 否 | 在关联操作组中定义要发送到所有 Webhook 终结点的自定义有效负载。 根据 Webhook 的需要确定格式，且格式应为有效的序列化 JSON。 |

## <a name="sample"></a>示例

以下是包含下列资源的解决方案示例：

- 保存的搜索
- 计划
- 操作组

此示例使用的是解决方案中常用的[标准解决方案参数]( solutions-solution-file.md#parameters)变量，不同于资源定义中使用的硬编码值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

下方参数文件提供了此解决方案的示例值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>后续步骤
* [将视图](solutions-resources-views.md)添加到管理解决方案。
* [将自动化 runbook 和其他资源添加](solutions-resources-automation.md)到管理解决方案。
