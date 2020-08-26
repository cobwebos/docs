---
title: 使用 Azure AD 中的 Privileged Identity Management 将角色分配给组 |Microsoft Docs
description: 预览用于委托标识管理的自定义 Azure AD 角色。 在 Azure 门户、PowerShell 或图形 API 中管理 Azure 角色。
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
ms.openlocfilehash: 343da87048cf43c04a137376e9a7f24270ce729a
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475983"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>使用 Privileged Identity Management 将角色分配给组

本文介绍如何使用 Azure AD Privileged Identity Management （PIM）将 Azure Active Directory （Azure AD）角色分配给组。

> [!NOTE]
> 必须使用 Privileged Identity Management 的更新版本才能使用 PIM 将组分配给 Azure AD 角色。 如果 Azure AD 组织利用 Privileged Identity Management API，你可能会在较旧版本的 PIM。 如果是这样，请联系别名 pim_preview@microsoft.com 以移动组织并更新 API。 有关详细信息[，请参阅 AZURE AD PIM 中的角色和功能](../privileged-identity-management/azure-ad-roles-features.md)。

## <a name="using-azure-ad-admin-center"></a>使用 Azure AD 管理中心

1. 以特权角色管理员或组织中的全局管理员身份登录到[Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) 。

1. 选择**Privileged Identity Management**  >  **Azure AD 角色**  >  **角色**  >  **添加分配**

1. 选择一个角色，然后选择一个组。 仅显示符合角色分配（角色可分配的组）的组，而不显示所有组。

    ![选择要分配到该角色的用户](./media/roles-groups-pim-eligible/select-member.png)

1. 选择所需的成员资格设置。 对于需要激活的角色，请选择 "**符合条件**"。 默认情况下，该用户是永久有资格的，但你也可以为用户的资格设置开始时间和结束时间。 完成后，请单击 "保存" 并添加来完成角色分配。

    ![选择要分配到该角色的用户](./media/roles-groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>使用 PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>下载 Azure AD 预览 PowerShell 模块

若要安装 Azure AD #PowerShell 模块，请使用以下 cmdlet：

```powershell
install-module azureadpreview
import-module azureadpreview
```

若要验证该模块是否可供使用，请使用以下 cmdlet：

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>将组分配为角色的合格成员

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](roles-groups-concept.md)
- [分配给云组的角色疑难解答](roles-groups-faq-troubleshooting.md)
- [在 Privileged Identity Management 中配置 Azure AD 管理员角色设置](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](../privileged-identity-management/pim-resource-roles-assign-roles.md)
