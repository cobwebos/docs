---
title: 将警报从 Log Analytics 扩展到 Azure
description: 本文介绍可用于将警报从 Log Analytics 扩展到 Azure 警报的工具和 API。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 21ba95a7b3efff177afe63d22da3f6ba9848ded2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301025"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>将警报从 Log Analytics 扩展为 Azure 警报
Azure 警报正在取代 Azure Log Analytics 中的警报功能。 在此过渡过程中，最初在 Log Analytics 中配置的警报将扩展到 Azure 中。 如果不想等待它们自动移入 Azure，可以启动该过程：

- 从 Operations Management Suite 门户手动启动。 
- 使用 AlertsVersion API 以编程方式启动。  

> [!NOTE]
> 从 2018 年 5 月 14 日开始，Microsoft 会将 Log Analytics 中创建的警报自动扩展到 Azure 警报，此过程会一直重复到完成为止。 Microsoft 计划将警报迁移到 Azure，并且在此过渡过程中，可以从 Operations Management Suite 门户和 Azure 门户管理警报。 此过程不具有破坏性，也不会带来中断。  

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>选项 1：从 Operations Management Suite 门户启动
以下步骤介绍如何从 Operations Management Suite 门户扩展工作区的警报。  

1. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在 Log Analytics 订阅窗格中选择一个工作区，然后选择“OMS 门户”磁贴。
![Log Analytics 订阅窗格的屏幕截图，其中突出显示了“OMS 门户”磁贴](./media/monitor-alerts-extend/azure-portal-01.png) 
3. 重定向到 Operations Management Suite 门户后，选择“设置”图标。
![Operations Management Suite 门户的屏幕截图，其中突出显示了“设置”图标](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. 从“设置”页选择“警报”。  
5. 选择“扩展到 Azure”。
![Operations Management Suite 门户“警报设置”页的屏幕截图，其中突出显示了“扩展到 Azure”](./media/monitor-alerts-extend/ExtendInto.png)
6. “警报”窗格中会显示三个步骤的向导。 阅读概述，然后选择“下一步”。
![向导的步骤 1 的屏幕截图](./media/monitor-alerts-extend/ExtendStep1.png)  
7. 在第二个步骤中，可查看提议的更改摘要，其中列出了警报的相应[操作组](monitoring-action-groups.md)。 如果在多个警报中出现类似操作，向导会提议将所有这些操作关联到单个操作组。  命名约定如下：WorkspaceName_AG_＃Number。 若要继续，请选择“下一步”。
![向导的步骤 2 的屏幕截图](./media/monitor-alerts-extend/ExtendStep2.png)  
8. 在向导的最后一个步骤中，选择“完成”，并在提示启动该过程时进行确认。 或者，可以提供电子邮件地址，以便在完成该过程并且所有警报已成功移至 Azure 警报时通知你。
![向导的步骤 3 的屏幕截图](./media/monitor-alerts-extend/ExtendStep3.png)

向导完成后，在“警报设置”页上，用于将警报扩展到 Azure 的选项已删除。 系统在后台将警报转移到 Azure，这可能需要一些时间。 在操作过程中，无法对来自 Operations Management Suite 门户的警报进行更改。 可以从门户顶部的横幅中查看当前状态。 如果之前提供了电子邮件地址，则在该过程成功完成时会收到一封电子邮件。  


警报在成功迁移到 Azure 后，仍会继续在 Operations Management Suite 门户中列出。
![Operations Management Suite 门户“警报设置”页的屏幕截图](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>选项 2：使用 AlertsVersion API
可以使用 Log Analytics AlertsVersion API 将警报从 Log Analytics 扩展到来自任何可调用 REST API 的客户端的 Azure警报。 可以使用 [ARMClient](https://github.com/projectkudu/ARMClient)（一种开源命令行工具）从 PowerShell 访问该 API。 可以输出 JSON 格式的结果。  

若要使用该 API，请先创建一个 GET 请求。 试图使用 POST 请求实际扩展到 Azure 之前，该请求会评估并返回提议的更改摘要。 结果以 JSON 格式列出警报和[操作组](monitoring-action-groups.md)的建议列表。 如果在多个警报中出现类似操作，该服务会提议将所有这些操作关联到单个操作组。 命名约定如下：WorkspaceName_AG_＃Number。

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
                            "serviceUri": "http://test.com"
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

此响应指示警报已成功扩展到 Azure 警报中。 版本属性仅用于检查警报是否已扩展到 Azure，并且与 [Log Analytics 搜索 API](../log-analytics/log-analytics-api-alerts.md) 无关。 警报成功扩展到 Azure 时，会向随 POST 请求提供的任何电子邮件地址发送报告。 如果指定工作区中的所有警报都已计划扩展，则对 POST 请求的响应是已禁止该尝试（403 状态码）。 若要查看任何错误消息或了解该过程是否中断，可以提交 GET 请求。 如果出现错误消息，则返回该消息以及摘要信息。

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
                            "serviceUri": "http://test.com"
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


## <a name="option-3-use-a-custom-powershell-script"></a>选项 3：使用自定义 PowerShell 脚本
 如果 Microsoft 没有成功地将警报从 Operations Management Suite 门户扩展到 Azure，则可以在 2018 年 7 月 5 日之前手动执行此操作。 前两个部分中介绍了手动扩展的两个选项。

2018 年 7 月 5 日之后，所有来自 Operations Management Suite 门户的警报都会扩展到 Azure。 由于缺少关联的[操作组](monitoring-action-groups.md)，未采取[建议的必要补救措施](#troubleshooting)的用户将会在不触发操作或通知的情况下运行警报。 

若要在 Log Analytics 中手动创建警报的[操作组](monitoring-action-groups.md)，请使用以下示例脚本：
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>关于自定义 PowerShell 脚本 
以下是有关使用该脚本的重要信息：
- 先决条件是安装 [ARMclient](https://github.com/projectkudu/ARMClient)，这是一种可简化 Azure 资源管理器 API 调用的开源命令行工具。
- 若要运行脚本，必须在 Azure 订阅中具有参与者或所有者角色。
- 必须提供以下参数：
    - $ subscriptionId：与 Operations Management Suite Log Analytics 工作区关联的 Azure 订阅 ID。
    - $ resourceGroup：Operations Management Suite Log Analytics 工作区的 Azure 资源组。
    - $ workspaceName：Operations Management Suite Log Analytics 工作区的名称。

### <a name="output-of-the-custom-powershell-script"></a>自定义 PowerShell 脚本的输出
该脚本非常详细，并在运行时输出这些步骤： 
- 它显示摘要，其中包含有关工作区中现有 Operations Management Suite Log Analytics 警报的信息。 摘要还包含有关 Azure 操作组的信息，该操作组是为与其关联的操作而创建的。 
- 系统提示继续使用扩展，或在查看摘要后退出。
- 如果继续使用扩展，则会创建新的 Azure 操作组，并将所有现有警报与之关联。 
- 通过显示消息“扩展完成！”退出该脚本 如果发生任何中间故障，脚本会显示后续错误。

## <a name="troubleshooting"></a>故障排除 
在扩展警报的过程中，问题可以阻止系统创建必要的[操作组](monitoring-action-groups.md)。 在这种情况下，Operations Management Suite 门户“警报”部分的横幅中或在对 API 执行的 GET 调用中可以看到错误消息。

> [!IMPORTANT]
> 如果在 2018 年 7 月 5 日之前未采取以下补救措施，警报将在 Azure 中运行，但不会触发任何操作或通知。 若要获取警报通知，则必须手动编辑和添加[操作组](monitoring-action-groups.md)，或使用上述[自定义 PowerShell 脚本](#option-3---using-custom-powershell-script)。

以下是针对每个错误的修正步骤：
- **错误：在订阅/资源组级别存在针对写入操作的作用域锁**：![Operations Management Suite 门户“警报设置”页的屏幕截图，其中突出显示了“作用域锁”错误消息](./media/monitor-alerts-extend/ErrorScopeLock.png)

    启用作用域锁后，该功能会限制包含 Log Analytics (Operations Management Suite) 工作区的订阅或资源组中的任何新更改。 系统无法将警报扩展到 Azure 并创建必要的操作组。
    
    若要解决该问题，请删除包含该工作区的订阅或资源组上的 ReadOnly 锁。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 执行此操作。 若要了解详细信息，请参阅[资源锁用法](../azure-resource-manager/resource-group-lock-resources.md)。 
    
    通过文章中所示的步骤解决错误后，Operations Management Suite 会在第二天的计划运行中将警报扩展到 Azure。 不需要采取任何进一步操作或启动任何内容。

- **错误：在订阅/资源组级别存在策略**：![Operations Management Suite 门户“警报设置”页的屏幕截图，其中突出显示了“策略”错误消息](./media/monitor-alerts-extend/ErrorPolicy.png)

    应用 [Azure 策略](../azure-policy/azure-policy-introduction.md)后，它会限制包含 Log Analytics (Operations Management Suite) 工作区的订阅或资源组中的任何新资源。 系统无法将警报扩展到 Azure 并创建必要的操作组。
    
    若要解决此问题，请编辑导致 *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* 错误的策略，该策略阻止在包含工作区的订阅或资源组中创建新资源。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 执行此操作。 可以审核操作以找到导致故障的相应策略。 若要了解详细信息，请参阅[查看活动日志以审核操作](../azure-resource-manager/resource-group-audit.md)。 
    
    通过文章中所示的步骤解决错误后，Operations Management Suite 会在第二天的计划运行中将警报扩展到 Azure。 不需要采取任何进一步操作或启动任何内容。


## <a name="next-steps"></a>后续步骤

* 了解新的 [Azure 警报体验](monitoring-overview-unified-alerts.md)的详细信息。
* 了解 [Azure 警报中的日志警报](monitor-alerts-unified-log.md)。
