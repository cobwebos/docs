---
title: 分配和列出具有管理单元范围的角色（预览）-Azure Active Directory |Microsoft Docs
description: 使用管理单元在 Azure Active Directory 中限制角色分配的作用域
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 918675b111b7b1b85669692b63fed683ea2831f8
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475628"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>将作用域内角色分配到管理单元

在 Azure Active Directory （Azure AD）中，你可以将用户分配到一个 Azure AD 角色，该角色的范围限制为一个或多个管理单元（澳大利亚）以实现更精细的管理控制。

有关将 PowerShell 和 Microsoft Graph 用于管理单元的管理的准备步骤，请参阅[入门](roles-admin-units-manage.md#get-started)。

## <a name="roles-available"></a>可用角色

角色  |  描述
----- |  -----------
身份验证管理员  |  有权查看、设置和重置分配的管理单元中的任何非管理员用户的身份验证方法信息。
组管理员  |  可以管理组和组设置的所有方面，例如仅分配的管理单元中的命名和过期策略。
支持管理员  |  只能为分配的管理单元中的非管理员和技术支持管理员重置密码。
许可证管理员  |  只能在管理单元中分配、删除和更新许可证分配。
密码管理员  |  只能为分配的管理单元中的非管理员和密码管理员重置密码。
用户管理员  |  可以管理用户和组的所有方面，包括仅为分配的管理单元中的有限管理员重置密码。

## <a name="assign-a-scoped-role"></a>分配作用域内角色

### <a name="azure-portal"></a>Azure 门户

在门户中，转到“Azure AD”>“管理单元”。 选择要将角色分配给用户的管理单元。 在左侧窗格中，选择 "角色和管理员" 列出所有可用的角色。

![选择管理单元以更改角色作用域](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

选择要分配的角色，然后选择 "**添加分配**"。 此时会在右侧打开一个面板，可以在其中选择一个或多个要分配到该角色的用户。

![选择要作用域的角色，然后选择 "添加分配"](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>列出 AU 上的作用域管理员

### <a name="azure-portal"></a>Azure 门户

可以在 Azure AD 的 "[管理单元" 部分](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中查看使用管理单元作用域完成的所有角色分配。 在门户中，转到“Azure AD”>“管理单元”。 为要列出的角色分配选择管理单元。 选择 "**角色和管理员**" 并打开角色以查看管理单元中的分配。

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](roles-groups-concept.md)
- [分配给云组的角色疑难解答](roles-groups-faq-troubleshooting.md)
