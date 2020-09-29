---
title: 分配和列出具有管理单元范围的角色-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元来限制角色分配的范围
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ae663b2c7a88e116315464c11b8d162135f0aff
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450389"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>向管理单元分配限定范围的角色

在 Azure Active Directory (Azure AD) 中，可将用户分配到 Azure AD 角色，角色的范围限制为一个或多个管理单元 (AU)，以实现更精细的管理控制。

有关将 PowerShell 和 Microsoft Graph 用于管理单元的管理的准备步骤，请参阅[入门](roles-admin-units-manage.md#get-started)。

## <a name="roles-available"></a>可用的角色

角色  |  描述
----- |  -----------
身份验证管理员  |  其访问权限仅限于查看、设置和重置所分配的管理单元中任何非管理员用户的身份验证方法信息。
组管理员  |  只能在所分配的管理单元中管理组和组设置的所有方面，如命名策略和过期策略。
支持管理员  |  只能重置所分配的管理单元中非管理员和支持管理员的密码。
许可证管理员  |  只能在管理单元内分配、删除和更新许可证分配。
密码管理员  |  只能在所分配的管理单元内重置非管理员和密码管理员的密码。
用户管理员  |  只能在所分配的管理单元内管理用户和组的所有方面，包括重置受限管理员的密码。

## <a name="security-principals-that-can-be-assigned-to-an-au-scoped-role"></a>可分配给 AU 范围角色的安全主体
以下安全主体可以分配给 AU 范围的角色：
* 用户
* 角色可分配的云组 (预览) 
* 服务主体名称 (SPN)

## <a name="assign-a-scoped-role"></a>分配限定范围的角色

### <a name="azure-portal"></a>Azure 门户

在门户中，转到“Azure AD”>“管理单元”。 选择要在其中给用户分配角色的管理单元。 在左侧窗格中，选择“角色和管理员”以列出所有可用的角色。

![选择要更改角色范围的管理单元](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

选择要分配的角色，然后选择“添加分配”。 此时右侧会打开一个面板，可在其中选择要分配到该角色的一个或多个用户。

![选择要限定范围的角色，然后选择“添加分配”](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> 若要使用 PIM 在管理单元上分配角色，请按照 [此处](/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md#assign-a-role-with-restricted-scope)的步骤操作。

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>列出 AU 上限定范围的管理员

### <a name="azure-portal"></a>Azure 门户

在 [Azure AD 的“管理单元”部分](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中可查看使用管理单元范围完成的所有角色分配。 在门户中，转到“Azure AD”>“管理单元”。 为需要列出的角色分配选择管理单元。 选择“角色和管理员”并打开一个角色，以查看该管理单元中的分配。

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](roles-groups-concept.md)
- [分配给云组的角色疑难解答](roles-groups-faq-troubleshooting.md)
