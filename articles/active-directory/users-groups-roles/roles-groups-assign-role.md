---
title: 在 Azure Active Directory 中将角色分配给云组 |Microsoft Docs
description: 将 Azure AD 角色分配给 Azure 门户、PowerShell 或图形 API 中可分配角色的组。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b5709369dfc2025b55bc3acec69e9328ce403
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513411"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>将角色分配到 Azure Active Directory 中的云组

本部分介绍 IT 管理员如何将 Azure Active Directory （Azure AD）角色分配到 Azure AD 组。

## <a name="using-azure-ad-admin-center"></a>使用 Azure AD 管理中心

向 Azure AD 角色分配组类似于分配用户和服务主体，只不过只能使用可分配角色的组。 在 Azure 门户中，只显示可分配角色的组。

1. 在 Azure AD 组织中，以特权角色管理员或全局管理员权限登录到[Azure AD 管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。

1. 选择 " **Azure Active Directory**  >  **角色和管理员**"，然后选择要分配的角色。

1. 在 "***角色名称***" 页上，选择 >**添加分配**"。

   ![添加新的角色分配](./media/roles-groups-assign-role/add-assignment.png)

1. 选择组。 仅显示可分配给 Azure AD 角色的组。

    [![对于新的角色分配，仅显示可分配的组。](media/roles-groups-assign-role/eligible-groups.png "对于新的角色分配，仅显示可分配的组。")](media/roles-groups-assign-role/eligible-groups.png#lightbox)

1. 选择“添加”  。

有关分配角色权限的详细信息，请参阅[向用户分配管理员和非管理员角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="using-powershell"></a>使用 PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>创建可分配给角色的组

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>获取要分配的角色的角色定义

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>创建角色分配

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>创建可 Azure AD 角色分配的组

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>获取角色定义

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>创建角色分配

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](roles-groups-concept.md)
- [分配给云组的角色疑难解答](roles-groups-faq-troubleshooting.md)
