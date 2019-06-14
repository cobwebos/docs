---
title: 将警报从 Log Analytics 扩展到 Azure 政府云
description: 本文介绍可用于将警报从 Log Analytics 扩展到 Azure 警报的工具和 API。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60431028"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>将警报从 Log Analytics 扩展为 Azure 警报
OMS 门户中的警报功能正在被 Azure 政府云中的 Azure 警报所取代。 在此过渡过程中，最初在 Log Analytics 中配置的警报将扩展到 Azure 中。 如果不想等待它们自动移入 Azure，可以启动该过程：

- 从 Operations Management Suite 门户手动启动。 
- 使用 AlertsVersion API 以编程方式启动。  

> [!NOTE]
> 从 2019 年 3 月 1 日开始，Microsoft 会以系统性方式将在 Log Analytics 的 Azure 政府 OMS 门户实例中创建的警报自动扩展到 Azure 警报。 如果在创建[操作组](../../azure-monitor/platform/action-groups.md)的过程中出现任何问题，请执行[这些修正步骤](alerts-extend-tool.md#troubleshooting)，以便自动创建操作组。 在 2019 年 3 月 15 日之前，你可以在 Azure 政府 OMS 门户中使用这些步骤。

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>选项 1：从 Operations Management Suite 门户启动
以下步骤介绍了如何从 Azure 政府云的 Operations Management Suite 门户扩展工作区的警报。  

1. 在 Azure 门户中，选择“所有服务”。  在资源列表中，键入“Log Analytics”  。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”  。
2. 在 Log Analytics 订阅窗格中选择一个工作区，然后选择“OMS 门户”磁贴  。
![Log Analytics 订阅窗格的屏幕截图，其中突出显示了“OMS 门户”磁贴](media/alerts-extend-tool/azure-portal-01.png) 
3. 重定向到 Operations Management Suite 门户后，选择“设置”图标  。
![Operations Management Suite 门户的屏幕截图，其中突出显示了“设置”图标](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. 从“设置”页选择“警报”   。  
5. 选择“扩展到 Azure”  。
![Operations Management Suite 门户“警报设置”页的屏幕截图，其中突出显示了“扩展到 Azure”](media/alerts-extend-tool/ExtendInto.png)
6. “警报”窗格中会显示三个步骤的向导  。 阅读概述，然后选择“下一步”  。
![向导的步骤 1 的屏幕截图](media/alerts-extend-tool/ExtendStep1.png)  
7. 在第二个步骤中，可查看提议的更改摘要，其中列出了警报的相应[操作组](../../azure-monitor/platform/action-groups.md)。 如果在多个警报中出现类似操作，向导会提议将所有这些操作关联到单个操作组。  命名约定如下所示：*WorkspaceName_AG_#Number*。 若要继续，请选择“下一步”  。
![向导的步骤 2 的屏幕截图](media/alerts-extend-tool/ExtendStep2.png)  
8. 在向导的最后一个步骤中，选择“完成”，并在提示启动该过程时进行确认  。 或者，可以提供电子邮件地址，以便在完成该过程并且所有警报已成功移至 Azure 警报时通知你。
![向导的步骤 3 的屏幕截图](media/alerts-extend-tool/ExtendStep3.png)

向导完成后，在“警报设置”页上，用于将警报扩展到 Azure 的选项已删除  。 系统在后台将警报转移到 Azure，这可能需要一些时间。 在操作过程中，无法对来自 Operations Management Suite 门户的警报进行更改。 可以从门户顶部的横幅中查看当前状态。 如果之前提供了电子邮件地址，则在该过程成功完成时会收到一封电子邮件。  


警报在成功迁移到 Azure 后，仍会继续在 Operations Management Suite 门户中列出。
![Operations Management Suite 门户“警报设置”页的屏幕截图](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>选项 2：使用 AlertsVersion API
可以使用 Log Analytics AlertsVersion API 将警报从 Log Analytics 扩展到来自任何可调用 REST API 的客户端的 Azure警报。 可以使用 [ARMClient](https://github.com/projectkudu/ARMClient)（一种开源命令行工具）从 PowerShell 访问该 API。 可以输出 JSON 格式的结果。  

若要使用该 API，请先创建一个 GET 请求。 试图使用 POST 请求实际扩展到 Azure 之前，该请求会评估并返回提议的更改摘要。 结果以 JSON 格式列出警报和[操作组](../../azure-monitor/platform/action-groups.md)的建议列表。 如果在多个警报中出现类似操作，该服务会提议将所有这些操作关联到单个操作组。 命名约定如下所示：*WorkspaceName_AG_#Number*。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果 GET 请求成功，则返回 HTTP 状态代码 200 以及警报列表和 JSON 数据中建议的操作组。 以下是示例响应：

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
如果指定的工作区没有定义任何警报规则，则 JSON 数据会返回以下内容：

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

如果指定工作区中的所有警报规则已扩展到 Azure，则对 GET 请求的响应为：

```json
{
    "version": 2
}
```

若要启动将警报迁移到 Azure 的操作，请启动 POST 响应。 POST 响应会确认并接受意向，将警报从 Log Analytics 扩展到 Azure 警报。 根据之前执行 GET 响应时的结果计划活动并按指示处理警报。 （可选）可以提供电子邮件地址列表，在计划的迁移警报的后台进程成功完成时，Log Analytics 会向这些地址发送报告。 可以使用以下请求示例：

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> 将警报迁移到 Azure 警报的结果可能会因 GET 响应提供的摘要而有所不同。 在计划时，Log Analytics 中的警报暂时不可在 Operations Management Suite 门户中进行修改。 但是，可以创建新的警报。 

如果 POST 请求成功，它将返回 HTTP 200 正常状态以及以下响应：

```json
{
    "version": 2
}
```

此响应指示警报已成功扩展到 Azure 警报中。 版本属性仅用于检查警报是否已扩展到 Azure，并且与 [Log Analytics 搜索 API](../../azure-monitor/platform/api-alerts.md) 无关。 警报成功扩展到 Azure 时，会向随 POST 请求提供的任何电子邮件地址发送报告。 如果指定工作区中的所有警报都已计划扩展，则对 POST 请求的响应是已禁止该尝试（403 状态码）。 若要查看任何错误消息或了解该过程是否中断，可以提交 GET 请求。 如果出现错误消息，则返回该消息以及摘要信息。

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>故障排除 
在扩展警报的过程中，问题可以阻止系统创建必要的[操作组](../../azure-monitor/platform/action-groups.md)。 在这种情况下，Operations Management Suite 门户“警报”部分的横幅中或在对 API 执行的 GET 调用中可以看到错误消息  。

> [!IMPORTANT]
> 如果基于 Azure 政府云的 OMS 门户用户在 2019 年 3 月 15 日之前未采取以下补救措施，警报将在 Azure 中运行，但不会触发任何操作或通知。 若要获取警报的通知，必须在 Azure 中手动编辑其警报规则并添加[操作组](../../azure-monitor/platform/action-groups.md)

以下是针对每个错误的修正步骤：
- **错误：在订阅/资源组级别存在针对写入操作的作用域锁**： ![Operations Management Suite 门户“警报设置”页的屏幕截图，其中突出显示了“作用域锁”错误消息](media/alerts-extend-tool/ErrorScopeLock.png)

    启用作用域锁后，该功能会限制包含 Log Analytics (Operations Management Suite) 工作区的订阅或资源组中的任何新更改。 系统无法将警报扩展到 Azure 并创建必要的操作组。
    
    若要解决该问题，请删除包含该工作区的订阅或资源组上的 ReadOnly 锁  。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 执行此操作。 若要了解详细信息，请参阅[资源锁用法](../../azure-resource-manager/resource-group-lock-resources.md)。 
    
    通过文章中所示的步骤解决错误后，Operations Management Suite 会在第二天的计划运行中将警报扩展到 Azure。 不需要采取任何进一步操作或启动任何内容。

- **错误：在订阅/资源组级别存在策略**： ![Operations Management Suite 门户“警报设置”页的屏幕截图，其中突出显示了“策略”错误消息](media/alerts-extend-tool/ErrorPolicy.png)

    应用 [Azure Policy](../../governance/policy/overview.md) 后，它会限制包含 Log Analytics (Operations Management Suite) 工作区的订阅或资源组中的任何新资源。 系统无法将警报扩展到 Azure 并创建必要的操作组。
    
    若要解决此问题，请编辑导致 *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* 错误的策略，该策略阻止在包含工作区的订阅或资源组中创建新资源。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 执行此操作。 可以审核操作以找到导致故障的相应策略。 若要了解详细信息，请参阅[查看活动日志以审核操作](../../azure-resource-manager/resource-group-audit.md)。 
    
    通过文章中所示的步骤解决错误后，Operations Management Suite 会在第二天的计划运行中将警报扩展到 Azure。 不需要采取任何进一步操作或启动任何内容。


## <a name="next-steps"></a>后续步骤

* 了解新的 [Azure 警报体验](../../azure-monitor/platform/alerts-overview.md)的详细信息。
* 了解 [Azure 警报中的日志警报](alerts-unified-log.md)。

