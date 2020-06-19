---
title: 在 Azure 门户中创建和管理器操作组
description: 了解如何在 Azure 门户中创建和管理操作组。
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 8075574556375b7c07de2abd6c5aff792880b497
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738812"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 门户中创建和管理器操作组
操作组是由 Azure 订阅的所有者定义的通知首选项的集合。 Azure Monitor 和服务运行状况警报使用操作组来通知用户某个警报已触发。 各种警报可以使用相同的操作组或不同的操作组，具体取决于用户的要求。 可以在订阅中最多配置 2,000 个操作组。

如果将操作配置为通过电子邮件或短信通知某个人员，则他们将收到确认消息，指示他们已被添加到操作组中。

本文演示如何在 Azure 门户中创建和管理操作组。

每个操作包含以下属性：

* **Name**：操作组中的唯一标识符。  
* **操作类型**：已执行的操作。 示例包括发送语音呼叫、短信、电子邮件，或者触发各种类型的自动化操作。 请参阅本文下文中的“类型”。
* **详细信息**：因“操作类型”而异的相应详细信息。

有关如何使用 Azure 资源管理器模板以配置操作组的信息，请参阅[操作组资源管理器模板](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 门户创建操作组

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“监视”。 “监视”窗格将所有监视设置和数据合并到一个视图中。

1. 选择“警报”，然后选择“管理操作”。

    ![“管理操作”按钮](./media/action-groups/manage-action-groups.png)
    
1. 选择“添加操作组”，并填写字段。

    ![“添加操作组”命令](./media/action-groups/add-action-group.png)
    
1. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

      ![“添加操作组”对话框](./media/action-groups/action-group-define.png)

1. “订阅”框会自动填充当前订阅。 此“订阅”是在其中保存操作组的订阅。

1. 选择在其中保存操作组的“资源组”。

1. 定义操作的列表。 为每个操作提供以下内容：

    1. **Name**：输入此操作的唯一标识符。

    1. **操作类型**：选择自动化 Runbook、Azure 函数、通过电子邮件发送 Azure 资源管理器角色、电子邮件/短信/推送/语音、ITSM、逻辑应用、安全 Webhook、Webhook。

    1. **详细信息**：根据操作类型，输入电话号码、电子邮件地址、webhook URI、Azure 应用、ITSM 连接或自动化 runbook。 对于 ITSM 操作，另外指定 ITSM 工具需要的“工作项”和其他字段。
    
    1. **常见警报架构**：可以选择启用[常见警报架构](https://aka.ms/commonAlertSchemaDocs)，这可获得在 Azure Monitor 中的所有警报服务中具有单个可扩展和统一的警报有效负载的优势。

1. 选择“确定”创建操作组。

## <a name="manage-your-action-groups"></a>管理操作组

创建操作组后，可以通过从“监视”窗格中的“警报”登陆页面中选择“管理操作”来查看“操作组”   。 选择要管理的操作组：

* 添加、编辑或删除操作。
* 删除操作组。

## <a name="action-specific-information"></a>特定于操作的信息

> [!NOTE]
> 请参阅[针对监视的订阅服务限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits)，了解下面每个项的数值限制。  

### <a name="automation-runbook"></a>自动化 Runbook
有关针对 Runbook 有效负载的限制，请参阅 [Azure 订阅服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

操作组中的 Runbook 操作数可能有限。 

### <a name="azure-app-push-notifications"></a>Azure 应用推送通知
操作组中的 Azure 应用操作数可能有限。

### <a name="email"></a>电子邮件
将从以下电子邮件地址发送电子邮件。 确保电子邮件筛选正确配置
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

操作组中的电子邮件操作数可能有限。 请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="email-azure-resource-manager-role"></a>通过电子邮件发送 Azure 资源管理器角色
向订阅角色的成员发送电子邮件。 电子邮件将仅发送给该角色的“Azure AD 用户”成员。 不会将电子邮件发送到 Azure AD 组或服务主体。

操作组中的电子邮件操作数可能有限。 请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="function"></a>函数
调用 [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app) 中的现有 HTTP 触发器终结点。

操作组中的函数操作数可能有限。

### <a name="itsm"></a>ITSM
ITSM 操作需要 ITSM 连接。 了解如何创建 [ITSM 连接](../../azure-monitor/platform/itsmc-overview.md)。

操作组中的 ITSM 操作数可能有限。 

### <a name="logic-app"></a>逻辑应用
操作组中的逻辑应用操作数可能有限。

### <a name="secure-webhook"></a>安全 Webhook
操作组 Webhook 操作使你能够利用 Azure Active Directory 来保护操作组和受保护的 Web API（Webhook 终结点）之间的连接。 下面介绍了利用此功能的整个工作流。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)。

1. 为受保护的 Web API 创建 Azure AD 应用程序。 请参阅 https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview 。
    - 将受保护的 API 配置为由守护程序应用调用。
    
1. 启用操作组以使用 Azure AD 应用程序。

    > [!NOTE]
    > 此脚本必须由 [Azure AD 应用程序管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)的成员执行。
    
    - 修改 PowerShell 脚本的 Connect-AzureAD 调用以使用 Azure AD 租户 ID。
    - 修改 PowerShell 脚本的变量 $myAzureADApplicationObjectId 以使用 Azure AD 应用程序的对象 ID
    - 运行修改后的脚本。
    
1. 配置操作组安全 Webhook 操作。
    - 从脚本中复制值 $myApp.ObjectId，并将其输入到 Webhook 操作定义中的“应用程序对象 ID”字段。
    
    ![安全 Webhook 操作](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>安全 Webhook PowerShell 脚本

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
有关其他重要信息，请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)和[短信警报行为](../../azure-monitor/platform/alerts-sms-behavior.md)。 

操作组中的短信操作数可能有限。

> [!NOTE]
> 如果在 Azure 门户操作组用户界面无法选择你的国家/地区代码，则表示你所在的国家/地区不支持短信。  如果你的国家/地区代码不可用，则可以在[用户之声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)投票以请求添加你的国家/地区。 此时，一个解决办法是使操作组向你所在国家/地区支持的第三方短信提供商调用 Webhook。  

受支持国家/地区的定价在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)中列出。
  

### <a name="voice"></a>语音
有关其他重要行为，请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

操作组中的语音操作数可能有限。

> [!NOTE]
> 如果在 Azure 门户操作组用户界面无法选择你的国家/地区代码，则表示你所在的国家/地区不支持语音呼叫。 如果你的国家/地区代码不可用，则可以在[用户之声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)投票以请求添加你的国家/地区。  此时，一个解决办法是使操作组向你所在国家/地区支持的第三方语音呼叫提供商调用 Webhook。  

受支持国家/地区的定价在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)中列出。

### <a name="webhook"></a>Webhook
将使用以下规则重试 Webhook。 当返回以下 HTTP 状态代码时，最多可以重试 2 次 Webhook 调用：最多可以重试 2 次 Webhook 调用。 首次重试在 10 秒后发生。 第二次重试在 100 秒后发生。 两次失败后，没有操作组会在 30 分钟内调用终结点。 

源 IP 地址范围
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

若要接收有关这些 IP 地址更改的更新，建议配置监视有关操作组服务的信息通知的服务运行状况警报。

操作组中的 Webhook 操作数可能有限。



## <a name="next-steps"></a>后续步骤
* 详细了解[短信警报行为](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 获取[对活动日志警报 webhook 架构的了解](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 了解有关 [ITSM 连接器](../../azure-monitor/platform/itsmc-overview.md)的详细信息
* 详细了解有关警报的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 获取[活动日志警报概述](../../azure-monitor/platform/alerts-overview.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。
