---
title: PIM 中 Azure AD 角色的 PowerShell-Azure AD |Microsoft Docs
description: 使用 Azure AD Privileged Identity Management （PIM）中的 PowerShell cmdlet 管理 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: efb85b4a54b8f61e44f1f8bc75f893f93a0feb8a
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164953"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management 中的 Azure AD 角色的 PowerShell

本文包含有关使用 Azure Active Directory （Azure AD） PowerShell cmdlet 管理 Privileged Identity Management （PIM） Azure AD 角色的说明。 此外，本文还介绍如何安装 Azure AD PowerShell 模块。

> [!Note]
> 仅当你在 Azure AD Privileged Identity Management 的新版本时，才支持我们的官方 PowerShell。 请在 "快速启动" 边栏选项卡中转到 Privileged Identity Management 并确保具有以下横幅。
> [![检查 Privileged Identity Management 的版本](media/pim-how-to-add-role-to-user/pim-new-version.png "选择 Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)如果你没有此版权标志，请等待，因为我们目前正在推出以下几个星期的更新体验。
> Privileged Identity Management PowerShell cmdlet 通过 Azure AD 预览模块来支持。 如果你使用的模块不同，并且该模块现在返回一条错误消息，请开始使用此新模块。 如果在不同的模块上构建了任何生产系统，请联系 pim_preview@microsoft.com

## <a name="installation-and-setup"></a>安装和设置

1. 安装 Azure AD 预览模块

        Install-module AzureADPreview

1. 在继续操作之前，请确保具有所需的角色权限。 如果尝试执行管理任务（例如提供角色分配或更新角色设置），请确保具有 "全局管理员" 或 "特权角色管理员" 角色。 如果只是尝试激活自己的分配，则除了默认用户权限以外，无需任何权限。

1. 连接到 Azure AD。

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. 转到**Azure Active Directory** > "**属性**" > **目录 id**，查找租户 id。 在 "cmdlet" 部分中，无论何时需要提供 resourceId，都应使用此 ID。

    ![在 Azure AD 组织的属性中查找租户 ID](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 以下部分是可帮助你启动和运行的简单示例。 可以在 https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management找到有关以下 cmdlet 的更详细文档。 但是，需要将 providerID 参数中的 "azureResources" 替换为 "aadRoles"。 还需要记得使用 Azure AD 组织的租户 ID 作为 resourceId 参数。

## <a name="retrieving-role-definitions"></a>检索角色定义

使用以下 cmdlet 获取 Azure AD 组织（租户）中的所有内置和自定义 Azure AD 角色。 此重要步骤提供角色名称和 roleDefinitionId 之间的映射。 在这些 cmdlet 中使用 roleDefinitionId，以便引用特定的角色。

RoleDefinitionId 特定于 Azure AD 组织，不同于角色管理 API 返回的 roleDefinitionId。

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

结果：

![获取 Azure AD 组织的所有角色](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>检索角色分配

使用以下 cmdlet 检索 Azure AD 组织中的所有角色分配。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

使用以下 cmdlet 检索特定用户的所有角色分配。 在 Azure AD 门户中，此列表也称为 "我的角色"。 唯一的区别在于，你已添加了使用者 ID 的筛选器。 此上下文中的使用者 ID 是用户 ID 或组 ID。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

使用以下 cmdlet 检索特定角色的所有角色分配。 此处的 roleDefinitionId 是上一个 cmdlet 返回的 ID。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Cmdlet 将生成如下所示的角色分配对象的列表。 使用者 ID 是角色分配到的用户的用户 ID。 分配状态可以是 "活动" 或 "符合条件"。 如果用户处于活动状态，并且在 LinkedEligibleRoleAssignmentId 字段中有一个 ID，则表示该角色当前已激活。

结果：

![检索 Azure AD 组织的所有角色分配](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>分配角色

使用以下 cmdlet 创建符合条件的分配。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

计划（定义分配的开始和结束时间）是可创建的对象，如下例所示：

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"

## <a name="activate-a-role-assignment"></a>激活角色分配

使用以下 cmdlet 激活符合条件的分配。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

此 cmdlet 与用于创建角色分配的 cmdlet 几乎完全相同。 Cmdlet 之间的主要区别在于，对于– Type 参数，激活为 "userAdd" 而不是 "adminAdd"。 另一个不同之处在于– AssignmentState 参数是 "活动"，而不是 "符合条件"。

> [!Note]
> 通过 PowerShell 实现角色激活有两种限制方案。
> 1. 如果你的角色设置中需要票证系统/票证号，则无法将其作为参数提供。 因此，不能在 Azure 门户之外激活角色。 此功能将在接下来的几个月内推出给 PowerShell。
> 1. 如果你需要多重身份验证来激活角色，则当前无法通过 PowerShell 在用户激活其角色时质询用户。 相反，当用户通过在我们的一位工程师中执行[此博客文章](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)连接到 Azure AD 时，用户将需要触发 MFA 质询。 如果要为 PIM 开发应用程序，一种可能的实现方法是质询用户，并在收到 "MfaRule" 错误后将其重新连接到模块。

## <a name="retrieving-and-updating-role-settings"></a>检索和更新角色设置

使用以下 cmdlet 获取 Azure AD 组织中的所有角色设置。

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

设置中有四个主要对象。 PIM 当前只使用其中的三个对象。 UserMemberSettings 是 "激活" 设置，"AdminEligibleSettings" 是适用于符合条件的分配的 "分配设置"，而 "AdminmemberSettings" 是活动分配的分配设置。

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

若要更新角色设置，需要先定义设置对象，如下所示：

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

然后，可以继续将设置应用于特定角色的对象之一，如下所示。 此处的 ID 是可以从 list role settings cmdlet 的结果中检索的角色设置 ID。

    Set-AzureADMSPrivilegedRoleSetting -ProviderId ‘aadRoles’ -Id ‘ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId ‘3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId ‘2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>后续步骤

- [分配 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [删除或更新 Azure AD 自定义角色分配](azure-ad-custom-roles-update-remove.md)
- [配置 Azure AD 自定义角色分配](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定义](../users-groups-roles/directory-assign-admin-roles.md)
