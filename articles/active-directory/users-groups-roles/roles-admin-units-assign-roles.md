---
title: 使用管理单位作用域（预览）分配和列出角色 - Azure 活动目录 |微软文档
description: 使用管理单位限制 Azure 活动目录中的角色分配范围
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870419"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>将作用域角色分配给管理单位

在 Azure 活动目录 （Azure AD） 中，可以将用户分配给范围仅限于一个或多个管理单位 （A） 的 Azure AD 角色，以便进行更精细的管理控制。

有关准备使用 PowerShell 和 Microsoft 图形进行管理单元管理的步骤，请参阅[入门](roles-admin-units-manage.md#get-started)。

## <a name="roles-available"></a>可用角色

角色  |  说明
----- |  -----------
身份验证管理员  |  只能查看、设置和重置分配管理单元中的任何非管理员用户的任何非管理员用户身份验证方法信息。
组管理员  |  只能管理组和组设置的所有方面，如指定管理单元中的命名和过期策略。
支持管理员  |  只能重置分配管理单元中非管理员和帮助台管理员的密码。
许可证管理员  |  只能在管理单位内分配、删除和更新许可证分配。
密码管理员  |  只能重置分配管理单元中非管理员和密码管理员的密码。
用户管理员  |  可以管理用户和组的所有方面，包括仅为分配的管理单元中的有限管理员重置密码。

## <a name="assign-a-scoped-role"></a>分配作用域角色

### <a name="azure-portal"></a>Azure 门户

转到**Azure AD >门户中的管理单位**。 选择要将角色分配给用户的管理单位。 在左侧窗格中，选择角色和管理员以列出所有可用角色。

![选择一个管理单位来更改角色范围](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

选择要分配的角色，然后选择 **"添加分配**"。 这将在右侧滑动打开面板，您可以在其中选择要分配给角色的一个或多个用户。

![选择要范围的角色，然后选择"添加分配"](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

突出显示的部分可以根据需要更改特定环境。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>在 AU 上列出作用域管理员

### <a name="azure-portal"></a>Azure 门户

使用管理单位作用域完成的所有角色分配都可以在 Azure AD[的管理单位部分](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中查看。 转到**Azure AD >门户中的管理单位**。 选择要列出的角色分配的管理单位。 选择**角色和管理员**并打开角色以查看管理单元中的分配。

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

突出显示的部分可以根据需要更改特定环境。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>后续步骤

- [管理单位故障排除和常见问题解答](roles-admin-units-faq-troubleshoot.md)
