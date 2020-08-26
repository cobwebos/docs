---
title: 在 Azure 门户中创建和管理器操作组
description: 了解如何在 Azure 门户中创建和管理操作组。
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a9d0fa9efaa07582212344e617d9a42f264b99ee
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337708"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 门户中创建和管理器操作组
操作组是由 Azure 订阅的所有者定义的通知首选项的集合。 Azure Monitor 和服务运行状况警报使用操作组来通知用户某个警报已触发。 各种警报可以使用相同的操作组或不同的操作组，具体取决于用户的要求。 可以在订阅中最多配置 2,000 个操作组。

本文演示如何在 Azure 门户中创建和管理操作组。

每个操作包含以下属性：

* **类型**：已执行的通知或操作。 示例包括发送语音呼叫、短信、电子邮件，或者触发各种类型的自动化操作。 请参阅本文下文中的“类型”。
* **Name**：操作组中的唯一标识符。
* **详细信息**：按*类型*变化的相应详细信息。

有关如何使用 Azure 资源管理器模板以配置操作组的信息，请参阅[操作组资源管理器模板](./action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 门户创建操作组

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“监视”。 “监视”窗格将所有监视设置和数据合并到一个视图中。

1. 选择 "**警报**"，然后选择 "**管理操作**"。

    ![“管理操作”按钮](./media/action-groups/manage-action-groups.png)
    
1. 选择 "**添加操作组**"，并在向导体验中填写相关字段。

    ![“添加操作组”命令](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>配置基本操作组设置

在 "**项目详细信息**" 下：

选择要在其中保存操作组的**订阅**和**资源组**。

在“实例详细信息”下：

1. 输入**操作组名称**。

1. 输入**显示名称**。 使用此组发送通知时，显示名称用于代替完整的操作组名称。

      ![“添加操作组”对话框](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>配置通知

1. 单击 "**下一步：通知 >** " 按钮转到 "**通知**" 选项卡，或选择屏幕顶部的 "**通知**" 选项卡。

1. 定义要在触发警报时发送的通知的列表。 为每个通知提供以下内容：

    a. **通知类型**：选择要发送的通知的类型。 可用选项是：
      * 电子邮件 Azure 资源管理器角色-将电子邮件发送给分配到某些订阅级别 ARM 角色的用户。
      * 电子邮件/短信/推送/语音-将这些通知类型发送给特定收件人。
    
    b. **名称**：输入通知的唯一名称。

    c. **详细信息**：根据所选的通知类型，输入电子邮件地址、电话号码等。
    
    d. **常见警报架构**：可以选择启用[常见警报架构](https://aka.ms/commonAlertSchemaDocs)，这可获得在 Azure Monitor 中的所有警报服务中具有单个可扩展和统一的警报有效负载的优势。

    !["通知" 选项卡](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>配置操作

1. 单击 "**下一步：操作 >** " 按钮转到 "**操作**" 选项卡，或选择屏幕顶部的 "**操作**" 选项卡。

1. 定义触发警报时要触发的操作的列表。 为每个操作提供以下内容：

    a. **操作类型**：选择自动化 Runbook、Azure 函数、ITSM、逻辑应用、安全 Webhook、webhook。
    
    b. **名称**：输入操作的唯一名称。

    c. **详细信息**：根据操作类型，输入 webhook URI、Azure 应用、ITSM 连接或自动化 runbook。 对于 ITSM 操作，另外指定 ITSM 工具需要的“工作项”和其他字段。
    
    d. **常见警报架构**：可以选择启用[常见警报架构](https://aka.ms/commonAlertSchemaDocs)，这可获得在 Azure Monitor 中的所有警报服务中具有单个可扩展和统一的警报有效负载的优势。
    
    !["操作" 选项卡](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>创建操作组

1. 如果你愿意，可以浏览“选项卡”设置。 这使你可以将键/值对关联到分类的操作组，并且是适用于任何 Azure 资源的功能。

    !["标记" 选项卡](./media/action-groups/action-group-4-tags.png)
    
1. 单击“查看 + 创建”以查看设置。 这会快速验证输入，以确保选择所有必填字段。 如果有问题，将在此处报告。 查看设置后，单击 "**创建**" 预配操作组。
    
    !["查看" 和 "创建" 选项卡](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> 将操作配置为通过电子邮件或短信通知人员时，他们将收到一条确认消息，指示已将其添加到操作组。

## <a name="manage-your-action-groups"></a>管理操作组

创建操作组后，可以通过从“监视”窗格中的“警报”登陆页面中选择“管理操作”来查看“操作组”   。 选择要管理的操作组：

* 添加、编辑或删除操作。
* 删除操作组。

## <a name="action-specific-information"></a>特定于操作的信息

> [!NOTE]
> 请参阅[针对监视的订阅服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits)，了解下面每个项的数值限制。  

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

操作组中的电子邮件操作数可能有限。 请参阅[速率限制信息](./alerts-rate-limiting.md)一文。

### <a name="email-azure-resource-manager-role"></a>通过电子邮件发送 Azure 资源管理器角色
向订阅角色的成员发送电子邮件。 电子邮件将仅发送给该角色的“Azure AD 用户”成员。 不会将电子邮件发送到 Azure AD 组或服务主体。

操作组中的电子邮件操作数可能有限。 请参阅[速率限制信息](./alerts-rate-limiting.md)一文。

### <a name="function"></a>函数
调用 [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app) 中的现有 HTTP 触发器终结点。

操作组中的函数操作数可能有限。

### <a name="itsm"></a>ITSM
ITSM 操作需要 ITSM 连接。 了解如何创建 [ITSM 连接](./itsmc-overview.md)。

操作组中的 ITSM 操作数可能有限。 

### <a name="logic-app"></a>逻辑应用
操作组中的逻辑应用操作数可能有限。

### <a name="secure-webhook"></a>安全 Webhook
操作组 Webhook 操作使你能够利用 Azure Active Directory 来保护操作组和受保护的 Web API（Webhook 终结点）之间的连接。 下面介绍了利用此功能的整个工作流。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](../../active-directory/develop/v2-overview.md)。

1. 为受保护的 Web API 创建 Azure AD 应用程序。 请参阅[受保护的 WEB API：应用注册](../../active-directory/develop/scenario-protected-web-api-app-registration.md)。
    - 配置要[由后台程序应用调用的](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)受保护 API。
    
2. 启用操作组以使用 Azure AD 应用程序。

    > [!NOTE]
    > 你必须是 [Azure AD 应用程序管理员角色](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles)的成员才能执行此脚本。
    
    - 修改 PowerShell 脚本的 Connect-AzureAD 调用以使用 Azure AD 租户 ID。
    - 将 PowerShell 脚本的变量 $myAzureADApplicationObjectId 修改为使用 Azure AD 应用程序的对象 ID。
    - 运行修改的脚本。
    
3. 配置操作组安全 Webhook 操作。
    - 从脚本中复制值 $myApp.ObjectId，并将其输入到 Webhook 操作定义中的“应用程序对象 ID”字段。
    
    ![保护 Webhook 操作](./media/action-groups/action-groups-secure-webhook.png)

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
有关其他重要信息，请参阅[速率限制信息](./alerts-rate-limiting.md)和[短信警报行为](./alerts-sms-behavior.md)。 

操作组中的短信操作数可能有限。

> [!NOTE]
> 如果在 Azure 门户操作组用户界面无法选择你的国家/地区代码，则表示你所在的国家/地区不支持短信。  如果你的国家/地区代码不可用，则可以在[用户之声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)投票以请求添加你的国家/地区。 此时，一个解决办法是使操作组向你所在国家/地区支持的第三方短信提供商调用 Webhook。  

受支持国家/地区的定价在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)中列出。
  

### <a name="voice"></a>语音
有关其他重要行为，请参阅[速率限制信息](./alerts-rate-limiting.md)一文。

操作组中的语音操作数可能有限。

> [!NOTE]
> 如果在 Azure 门户操作组用户界面无法选择你的国家/地区代码，则表示你所在的国家/地区不支持语音呼叫。 如果你的国家/地区代码不可用，则可以在[用户之声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)投票以请求添加你的国家/地区。  此时，一个解决办法是使操作组向你所在国家/地区支持的第三方语音呼叫提供商调用 Webhook。  

受支持国家/地区的定价在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)中列出。

### <a name="webhook"></a>Webhook
使用以下规则处理 webhook
- 最多尝试三次 webhook 调用。
- 如果在超时期限内未收到响应，或者返回以下 HTTP 状态代码之一，将重试此调用：408、429、503或504。
- 第一次调用将为响应等待10秒。
- 第二次和第三次尝试将为响应等待30秒。
- 3次尝试调用 webhook 失败后，任何操作组都不会调用该终结点15分钟。

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
* 详细了解[短信警报行为](./alerts-sms-behavior.md)。  
* 获取[对活动日志警报 webhook 架构的了解](./activity-log-alerts-webhook.md)。  
* 详细了解[ITSM 连接器](./itsmc-overview.md)。
* 详细了解有关警报的[速率限制](./alerts-rate-limiting.md)。
* 获取[活动日志警报概述](./alerts-overview.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](../../service-health/alerts-activity-log-service-notifications-portal.md)。

