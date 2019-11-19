---
title: 通过 Azure 事件网格中的 Azure AD 进行安全 WebHook 传递
description: 介绍如何使用 Azure 事件网格将事件传递给受 Azure Active Directory 保护的 HTTPS 终结点
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 9ee1dd74384725b3fffbfea7144c3a536b50c531
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174546"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>将事件发布到 Azure Active Directory 受保护的终结点

本文介绍如何利用 Azure Active Directory 来保护事件订阅和 webhook 终结点之间的连接。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)。

本文使用 Azure 门户演示，但也可以使用 CLI、PowerShell 或 Sdk 启用此功能。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-and-azure-ad-application"></a>创建和 Azure AD 应用程序

首先为受保护的终结点创建 Azure AD 应用程序。 请参阅 https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview。
    - 将受保护的 API 配置为通过守护程序应用进行调用。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>启用事件网格以使用 Azure AD 应用程序

使用下面的 PowerShell 脚本在 Azure AD 应用程序中创建角色和服务主体。 你将需要 Azure AD 应用程序中的租户 ID 和对象 ID：

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. 修改 PowerShell 脚本的 $myTenantId，以使用 Azure AD 租户 ID。
1. 将 PowerShell 脚本的 $myAzureADApplicationObjectId 修改为使用 Azure AD 应用程序的对象 ID
1. 运行修改的脚本。

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
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
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>配置事件订阅

在事件订阅的创建流中，选择 "终结点类型" "Web 挂钩"。 提供终结点 URI 后，单击 "创建事件订阅" 边栏选项卡顶部的 "其他功能" 选项卡。

![选择终结点类型 webhook](./media/secure-webhook-delivery/select-webhook.png)

在 "其他功能" 选项卡中，选中 "使用 AAD 身份验证" 复选框，并配置租户 ID 和应用程序 ID：

* 从脚本的输出中复制 Azure AD 的租户 ID，并将其输入到 AAD 租户 ID 字段。
* 从脚本的输出中复制 Azure AD 的应用程序 ID，并将其输入到 "AAD 应用程序 ID" 字段中。

    ![保护 Webhook 操作](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。