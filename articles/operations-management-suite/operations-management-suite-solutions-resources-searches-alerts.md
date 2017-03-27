---
title: "OMS 解决方案中的保存的搜索和警报 | Microsoft Docs"
description: "OMS 中的解决方案通常会包括 Log Analytics 中保存的搜索，以便分析解决方案收集的数据。  它们可能还会定义警报，从而向用户发出通知或针对严重问题自动采取行动。  本文介绍如何在 ARM 模板中定义 Log Analytics 保存的搜索和警报，以便将其纳入管理解决方案。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: aaf5c442ef85edbc498aa2fd7815171f4701f960
ms.openlocfilehash: 262beba30c760335aafdf903d9f5cac6b0dd9669

---

# <a name="log-analytics-saved-searches-and-alerts-in-oms-solutions-preview"></a>OMS 解决方案中的 Log Analytics 保存的搜索和警报（预览）

> [!NOTE]
> 这是在 OMS 中创建管理解决方案的初步文档，当前仅提供预览版。 如下所述的全部架构均会有变动。   


[OMS 中的管理解决方案](operations-management-suite-solutions.md)通常会将 Log Analytics 中[保存的搜索](../log-analytics/log-analytics-log-searches.md)包括在内，以便分析解决方案收集的数据。  它们可能还会定义[警报](../log-analytics/log-analytics-alerts.md)，从而向用户发出通知或针对严重问题自动采取行动。  本文介绍如何在[资源管理模板](../resource-manager-template-walkthrough.md)中定义 Log Analytics 保存的搜索和警报，以便将其纳入[管理解决方案](operations-management-suite-solutions-creating.md)。

> [!NOTE]
> 本文中的示例使用管理解决方案需要或通用的参数和变量，[在 Operations Management Suite (OMS) 中创建管理解决方案](operations-management-suite-solutions-creating.md)对此进行了介绍  

## <a name="prerequisites"></a>先决条件
本文假设你已经熟悉如何[创建管理解决方案](operations-management-suite-solutions-creating.md)以及 [ARM 模板](../resource-group-authoring-templates.md)和解决方案文件的结构。


## <a name="log-analytics-workspace"></a>Log Analytics 工作区
Log Analytics 中的所有资源都包含在[工作区](../log-analytics/log-analytics-manage-access.md)中。  如 [OMS 工作区和自动化帐户](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)中所述，工作区不包括在管理解决方案中，但必须存在才可以安装解决方案。  如果没有，解决方案安装将失败。

工作区的名称包含在每个 Log Analytics 资源的名称中。  这是在具有 **workspace** 参数的解决方案中完成的，如以下 savedsearch 资源示例所示。

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"


## <a name="saved-searches"></a>保存的搜索
将[保存的搜索](../log-analytics/log-analytics-log-searches.md)纳入解决方案后，用户可查询由解决方案收集的数据。  保存的搜索将出现在 OMS 门户中的“收藏夹”下和 Azure 门户的“保存的搜索”下。  每个警报也需要一个保存的搜索。   

[Log Analytics 保存的搜索](../log-analytics/log-analytics-log-searches.md)资源的类型为 `Microsoft.OperationalInsights/workspaces/savedSearches` 且具有以下结构。 

    {
        "name": "<name-of-savedsearch>"
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "<api-version-of-resource>",
        "dependsOn": []
        "tags": {},
        "properties": {
            "etag": "*",
            "query": "<query-to-run>",
            "displayName": "<saved-search-display-name>",
            "category": ""<saved-search-category>"
        }
    }

下表描述了保存的搜索的每个属性。 

| 属性 | 说明 |
|:--- |:--- |
| category | 保存的搜索的类别。  同一解决方案中所有保存的搜索常共享一个类别，因此他们在控制台中组合在一起。 |
| displayname | 保存的搜索在门户中显示的名称。 |
| query | 要运行的查询。 |

> [!NOTE]
> 如果查询中包含可解释为 JSON 的字符，则可能需要在查询中使用转义字符。  例如，如果查询为 **Type:AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**，那么在解决方案文件中应将其写作 **Type:AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**。

## <a name="alerts"></a>警报
通过警报规则创建 [Log Analytics 警报](../log-analytics/log-analytics-alerts.md)，该规则按固定时间间隔运行保存的搜索。  如果查询结果与指定的条件相符，则会创建一个警报记录且会运行一个或多个操作。  

管理解决方案中的警报规则由以下三种不同资源组成。

- **保存的搜索。**  定义要运行的日志搜索。  多个警报规则可共享一个保存的搜索。
- **计划。**  定义运行日志搜索的频率。  每个警报规则有且仅有一个计划。
- **警报操作。**  每个警报规则都将具有一个类型为 **Alert** 的操作资源，它可定义警报的详细信息，例如定义创建警报记录的时间和警报严重性等条件。  操作资源将选择性定义一个邮件和 runbook 响应。
- **Webhook 操作（可选）。**  如果警报规则调用 webhook，则需要一个类型为 **webhook** 的额外操作资源。    

前面描述了保存的搜索资源。  下面会介绍其他资源。


### <a name="schedule-resource"></a>计划资源

保存的搜索可以拥有一个或多个计划，每个计划代表一个单独的警报规则。 计划定义搜索的运行频率和检索数据的时间间隔。  计划资源的类型为 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` 且具有以下结构。 

    {
      "name": "<name-of-schedule>",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
      "apiVersion": "<api-version-of-resource>",
      "dependsOn": [
        "<name-of-saved-search>"
      ],
      "properties": {  
        "etag": "*",               
        "interval": <schedule-interval-in-minutes>,
        "queryTimeSpan": <query-timespan-in-minutes>,
        "enabled": <schedule-interval-in-minutes>       
      }
    }

下表介绍了计划资源的属性。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| 已启用       | 是 | 说明创建警报后该警报是否启用。 |
| interval      | 是 | 查询运行的频率（以分钟为单位）。 |
| queryTimeSpan | 是 | 用于评估结果的时长（以分钟为单位）。 |

计划资源应该依赖于保存的搜索，以便在计划前创建资源。


### <a name="actions"></a>操作
存在两类由 **Type** 属性指定的操作资源。  一个计划需要一个 **Alert** 操作，该操作可定义警报规则的详细信息并在创建警报时定义要采取的操作。  如果应从警报调用 webhook，则它还可能包括 **Webhook** 操作。  

操作资源的类型为 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`。  

#### <a name="alert-actions"></a>警报操作

每个计划都将有一个 **Alert** 操作。  这可定义警报的详细信息，并选择性定义通知和修正操作的详细信息。  通知将一封电子邮件发送到一个或多个地址。  修正在 Azure 自动化中启动 runbook，尝试修正检测到的问题。

警报操作具有以下结构。

    {
        "name": "<name-of-the-action>",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "<api-version-of-resource>",
        "dependsOn": [
            <name-of-schedule>
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "<display-name-of-alert>",
            "description": "<description-of-alert>",
            "severity": "<severity-of-alert>",
            "threshold": {
                "operator": "<threshold-operator>",
                "value": "<threshold-value>"
                "metricsTrigger": {
                    "triggerCondition": "<trigger-condition>",
                    "operator": "<trigger-operator>",
                    "value": "<trigger-value>"
                },
            },
            "throttling": {
                "durationInMinutes": "<throttling-duration-in-minutes>"
            },
            "emailNotification": {
                "recipients": [
                    <mail-recipients>
                ],
                "subject": "<mail-subject>",
                "attachment": "None"
            },
            "remediation": {
                "runbookName": "<name-of-runbook>",
                "webhookUri": "<runbook-uri>"
            }
        }
    }

下表介绍了 Alert 操作资源的属性。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| 类型 | 是 | 操作的类型。  警报操作的类型是 **Alert**。 |
| 名称 | 是 | 警报的显示名称。  这是警报规则在控制台中的显示名称。 |
| 说明 | 否 | 警报的可选说明。 |
| 严重性 | 是 | 警报记录的严重等级包括以下值：<br><br> **严重**<br>**警告**<br>**信息性** |


##### <a name="threshold"></a>阈值
本部分是必需的。  它定义警报阈值的属性。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| 运算符 | 是 | 比较运算符包括以下值：<br><br>**gt = 大于<br>lt = 小于** |
| 值 | 是 | 要比较结果的值。 |


##### <a name="metricstrigger"></a>MetricsTrigger
本部分为可选。  将其包含在指标度量警报中。

> [!NOTE]
> 指标度量警报目前为公共预览版。 

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| TriggerCondition | 是 | 以下值指定该阈值是总违规次数还是连续违规次数：<br><br>**总次数<br>连续次数** |
| 运算符 | 是 | 比较运算符包括以下值：<br><br>**gt = 大于<br>lt = 小于** |
| 值 | 是 | 若要触发警报，该条件必须符合的次数。 |

##### <a name="throttling"></a>限制
本部分为可选。  创建警报后，若希望在一定时间内阻止通过同一规则创建的警报，请包含此部分。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| DurationInMinutes | 如果包含了限制元素，则为必需 | 从同一警报规则创建警报后，阻止警报的分钟数。 |

##### <a name="emailnotification"></a>EmailNotification
 此部分为可选。如果希望警报将邮件发送给一个或多个收件人，请将此部分包含在内。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| 收件人 | 是 | 由逗号分隔的电子邮件地址列表，创建警报后向这些地址发送通知，示例如下。<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| 使用者 | 是 | 邮件的主题行。 |
| 附件 | 否 | 目前不支持附件。  如果包含此元素，则它应为“无”。 |


##### <a name="remediation"></a>补救
此部分为可选。如果希望启动 runbook 以响应警报，请将此部分包含在内。 |

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| RunbookName | 是 | 要启动的 runbook 的名称。 |
| WebhookUri | 是 | 该 runbook 的 webhook 的 URI。 |
| Expiry | 否 | 修正过期的日期和时间。 |

#### <a name="webhook-actions"></a>Webhook 操作

Webhook 操作通过调用 URL 和提供要发送的负载（可选）启动进程。 Webhook 操作与修正操作类似，不同之处在于它们是用于 Webhook，可能调用 Azure 自动化 Runbook 之外的进程。 此外，它们还提供其他选项来提供要发送到远程进程的负载。

如果警报会调用 webhook，则除 **Alert** 操作资源外，它还需要一个类型为 **Webhook** 的操作资源。  

    {
        "name": "<name-of-the-action>",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "<api-version-of-resource>",
        "dependsOn": [
            <name-of-schedule>
            <name-of-alert-action>
        ],
        "properties": {
            "etag": "*",
            "type": "Webhook",
            "name": "<display-name-of-action>",
            "severity": "<severity-of-alert>",
            "customPayload": "<payload-to-send>"
        }
    }

下表介绍了 Alert 操作资源的属性。

| 元素名称 | 必选 | 说明 |
|:--|:--|:--|
| type | 是 | 操作的类型。  Webhook 操作的类型是 **Webhook**。 |
| 名称 | 是 | 操作的显示名称。  控制台中不显示此名称。 |
| wehookUri | 是 | Webhook 的 URI。 |
| customPayload | 否 | 发送到 Webhook 的自定义负载。 格式将取决于 Webhook 的期望。 |




### <a name="sample"></a>示例

以下是包含下列资源的解决方案示例：

- 保存的搜索
- 计划
- 警报操作
- Webhook 操作

此示例使用的是解决方案中常用的[标准解决方案参数](operations-management-suite-solutions-creating.md#parameters)变量，不同于资源定义中使用的硬编码值。

    {
        "$schema": "http://schemas.microsoft.org/azure/deploymentTemplate?api-version=2015-01-01#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
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
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
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
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


下方参数文件提供了此解决方案的示例值。

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
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>后续步骤
* [将视图](operations-management-suite-solutions-resources-views.md)添加到管理解决方案。
* [将自动化 runbook 和其他资源添加](operations-management-suite-solutions-resources-automation.md)到管理解决方案。




<!--HONumber=Jan17_HO4-->


