---
title: 在 Azure 门户中创建和管理器操作组
description: 了解如何在 Azure 门户中创建和管理操作组。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6b3d1ff76d4f7611da8e08dd4ce42293c805978e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423849"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 门户中创建和管理器操作组
操作组是由 Azure 订阅的所有者定义的通知首选项的集合。 Azure Monitor 和服务运行状况警报使用操作组来通知用户某个警报已触发。 各种警报可以使用相同的操作组或不同的操作组，具体取决于用户的要求。 可以在订阅中最多配置 2,000 个操作组。

当配置操作来通过电子邮件或短信通知某个人员时，该人员将收到确认，指出其已被添加到操作组。

本文演示如何在 Azure 门户中创建和管理操作组。

每个操作包含以下属性：

* 名称：操作组中的唯一标识符。  
* **操作类型**：执行的操作。 示例包括发送语音呼叫、短信、电子邮件，或者触发各种类型的自动化操作。 请参阅本文下文中的“类型”。
* **详细信息**：根据*操作类型*变化的相应详细信息。

有关如何使用 Azure 资源管理器模板以配置操作组的信息，请参阅[操作组资源管理器模板](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 门户创建操作组

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 "**监视器**"。 “监视器”窗格将所有监视设置和数据合并到一个视图中。

1. 选择“警报”，然后选择“管理操作”。

    ![“管理操作”按钮](./media/action-groups/manage-action-groups.png)
    
1. 选择“添加操作组”，并填写字段。

    ![“添加操作组”命令](./media/action-groups/add-action-group.png)
    
1. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

      ![“添加操作组”对话框](./media/action-groups/action-group-define.png)

1. “订阅”框会自动填充当前订阅。 此“订阅”是在其中保存操作组的订阅。

1. 选择在其中保存操作组的“资源组”。

1. 定义一个操作列表。 为每个操作提供以下信息：

    1. 名称：输入此操作的唯一标识符。

    1. **操作类型**：选择电子邮件/短信/推送/语音、逻辑应用、Webhook、ITSM 或自动化 Runbook。

    1. **详细信息**：根据操作类型，输入电话号码、电子邮件地址、webhook URI、Azure 应用、ITSM 连接或自动化 runbook。 对于 ITSM 操作，另外指定 ITSM 工具需要的“工作项”和其他字段。
    
    1. **常见的警报架构**：你可以选择启用[常见的警报架构](https://aka.ms/commonAlertSchemaDocs)，该架构提供了在 Azure Monitor 中的所有警报服务之间具有单个可扩展和统一的警报负载的优点。

1. 选择“确定”创建操作组。

## <a name="manage-your-action-groups"></a>管理操作组

创建操作组后，它会显示在“监视器”边栏选项卡的“操作组”部分中。 选择要管理的操作组：

* 添加、编辑或删除操作。
* 删除操作组。

## <a name="action-specific-information"></a>特定于操作的信息

> [!NOTE]
> 有关以下每项的数值限制，请参阅[用于监视的订阅服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits)。  

### <a name="automation-runbook"></a>自动化 Runbook
有关 Runbook 有效负载的限制，请参考 [Azure 订阅服务限制](../../azure-subscription-service-limits.md)。

一个操作组中可以存在有限数量的 Runbook 操作。 

### <a name="azure-app-push-notifications"></a>Azure 应用推送通知
一个操作组中可以存在有限数量的 Azure 应用操作。

### <a name="email"></a>Email
将从以下电子邮件地址发送电子邮件。 确保电子邮件筛选正确配置
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

一个操作组中可以存在有限数量的电子邮件操作。 请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="email-azure-resource-manager-role"></a>电子邮件 Azure 资源管理器角色
向订阅角色的成员发送电子邮件。

一个操作组中可以存在有限数量的电子邮件操作。 请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="function"></a>函数
配置为操作的 Function App 的函数密钥通过函数 API 读取。目前，这需要 v2 函数应用将应用设置“AzureWebJobsSecretStorageType”配置为“files”。 有关详细信息，请参阅 [Functions V2 中对密钥管理的更改]( https://aka.ms/funcsecrets)。

一个操作组中可以存在有限数量的 Function 操作。

### <a name="itsm"></a>ITSM
ITSM 操作需要 ITSM 连接。 了解如何创建 [ITSM 连接](../../azure-monitor/platform/itsmc-overview.md)。

一个操作组中可以存在有限数量的 ITSM 操作。 

### <a name="logic-app"></a>逻辑应用
一个操作组中可以存在有限数量的逻辑应用操作。

### <a name="secure-webhook"></a>安全 Webhook
**安全 Webhook 功能目前为预览版。**

可以通过“操作组 Webhook”操作利用 Azure Active Directory 来保护操作组和受保护 Web API（Webhook 终结点）之间的连接。 利用此功能的完整工作流如下所述。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)。

1. 针对受保护的 Web API 创建 Azure AD 应用程序。 请参阅 https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview。
    - 将受保护的 API 配置为通过守护程序应用进行调用。
    
1. 允许操作组使用 Azure AD 应用程序。

    > [!NOTE]
    > 你必须是 [Azure AD 应用程序管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)的成员才能执行此脚本。
    
    - 修改 PowerShell 脚本的 Connect-AzureAD 调用，以便使用 Azure AD 租户 ID。
    - 修改 PowerShell 脚本的变量 $myAzureADApplicationObjectId，以便使用 Azure AD 应用程序的对象 ID。
    - 运行修改的脚本。
    
1. 配置操作组保护 Webhook 操作。
    - 从脚本中复制 $myApp.ObjectId 值，将其输入 Webhook 操作定义的“应用程序对象 ID”字段中。
    
    ![保护 Webhook 操作](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>保护 Webhook PowerShell 脚本

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

一个操作组中可以存在有限数量的短信操作。  

### <a name="voice"></a>语音
请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

一个操作组中可以存在有限数量的语音操作。

### <a name="webhook"></a>Webhook
Webhook 使用以下规则进行重试。 返回以下 HTTP 状态代码时，webhook 调用最多重试2次：408、429、503、504或 HTTP 终结点未响应。 首次重试在 10 秒后发生。 第二次重试在 100 秒后发生。 在失败两次后，操作组在 30 分钟内不会再调用该终结点。 

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

若要接收有关这些 IP 地址更改的更新，我们建议你配置服务运行状况警报，以监视有关操作组服务的信息通知。

一个操作组中可以存在有限数量的 Webhook 操作。



## <a name="next-steps"></a>后续步骤
* 详细了解[短信警报行为](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 获取[对活动日志警报 webhook 架构的了解](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 了解有关 [ITSM 连接器](../../azure-monitor/platform/itsmc-overview.md)的详细信息
* 详细了解有关警报的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 获取[活动日志警报概述](../../azure-monitor/platform/alerts-overview.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。
