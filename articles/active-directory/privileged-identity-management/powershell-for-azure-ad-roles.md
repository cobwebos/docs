---
title: PIM 中 Azure AD 角色的电源外壳 - Azure AD |微软文档
description: 在 Azure AD 特权标识管理 （PIM） 中使用 PowerShell cmdlet 管理 Azure AD 角色。
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
ms.openlocfilehash: 364b0268be99685b18db175ffbabd9350531d500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048871"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>特权标识管理中 Azure AD 角色的 PowerShell

本文包含有关使用 Azure 活动目录 （Azure AD） PowerShell cmdlet 在特权标识管理 （PIM） 中管理 Azure AD 角色的说明。 此外，本文还介绍如何安装 Azure AD PowerShell 模块。

> [!Note]
> 仅当您使用新版本的 Azure AD 特权标识管理时，我们的官方 PowerShell 才受支持。 请转到特权标识管理，并确保快速入门边栏选项卡上具有以下横幅。
> [![检查您拥有的特权身份管理版本](media/pim-how-to-add-role-to-user/pim-new-version.png "选择 Azure AD >特权标识管理")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)如果您没有此横幅，请稍候，因为我们目前正在在未来几周内推出此更新的体验。
> 通过 Azure AD 预览模块支持特权标识管理 PowerShell cmdlet。 如果您一直在使用其他模块，并且该模块现在返回一条错误消息，请开始使用此新模块。 如果您有其他模块之上构建的任何生产系统，请联系pim_preview@microsoft.com

## <a name="installation-and-setup"></a>安装和设置

1. 安装 Azure AD 预览模块

        Install-module AzureADPreview

1. 在继续操作之前，请确保您具有所需的角色权限。 如果您尝试执行管理任务，如授予角色分配或更新角色设置，请确保具有全局管理员或特权角色管理员角色管理员角色。 如果您只是尝试激活自己的分配，则不需要超出默认用户权限的权限。

1. 连接到 Azure AD。

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. 通过访问**Azure 活动目录** > **属性** > 目录 ID 查找租户**ID。** 在 cmdlet 部分中，每当需要提供资源 Id 时，都会使用此 ID。

    ![在 Azure AD 组织的属性中查找租户 ID](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 以下各节是可帮助您启动和运行的简单示例。 您可以在 中找到有关 以下 cmdlet 的更多详细信息https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management文档。 但是，您需要将提供程序 ID 参数中的"azureResources"替换为"aadRole"。 您还需要记住将 Azure AD 组织的租户 ID 用作 resourceId 参数。

## <a name="retrieving-role-definitions"></a>检索角色定义

使用以下 cmdlet 获取 Azure AD 组织（租户）中的所有内置和自定义 Azure AD 角色。 这一重要步骤为您提供了角色名称和角色定义 Id 之间的映射。 角色定义Id用于这些 cmdlet，以便引用特定角色。

角色定义Id特定于 Azure AD 组织，与角色管理 API 返回的角色定义Id 不同。

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

结果：

![获取 Azure AD 组织的所有角色](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>检索角色分配

使用以下 cmdlet 检索 Azure AD 组织中的所有角色分配。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

使用以下 cmdlet 检索特定用户的所有角色分配。 此列表在 Azure AD 门户中也称为"我的角色"。 这里唯一的区别是您为主题 ID 添加了筛选器。 此上下文中的主题 ID 是用户 ID 或组 ID。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

使用以下 cmdlet 检索特定角色的所有角色分配。 此处的角色定义 Id 是上一个 cmdlet 返回的 ID。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

cmdlet 会导致下面显示的角色分配对象列表。 主题 ID 是角色分配给的用户的用户 ID。 分配状态可以是活动状态，也可以符合条件。 如果用户处于活动状态，并且"链接合格角色分配 Id"字段中存在 ID，则表示角色当前已激活。

结果：

![检索 Azure AD 组织的所有角色分配](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>分配角色

使用以下 cmdlet 创建符合条件的分配。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

定义分配开始和结束时间的计划是一个可以创建的对象，如下所示：

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"

## <a name="activate-a-role-assignment"></a>激活角色分配

使用以下 cmdlet 激活符合条件的分配。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

此 cmdlet 与创建角色分配的 cmdlet 几乎相同。 cmdlet 之间的主要区别是，对于 _Type 参数，激活是"userAdd"而不是"adminAdd"。 另一个区别是 _AssignmentState 参数为"活动"而不是"合格"。

> [!Note]
> 通过 PowerShell 进行角色激活有两种限制方案。
> 1. 如果您在角色设置中需要票证系统/票证编号，则无法将这些号码作为参数提供。 因此，无法在 Azure 门户之外激活角色。 此功能将在未来几个月内推广到 PowerShell。
> 1. 如果需要多重身份验证才能激活角色，PowerShell 目前无法在用户激活其角色时对其提出质疑。 相反，用户在连接到 Azure AD 时需要通过关注我们的工程师的[博客文章](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)来触发 MFA 挑战。 如果您正在开发 PIM 应用，一个可能的实现是向用户发起挑战，并在用户收到"MfaRule"错误后将其重新连接到模块。

## <a name="retrieving-and-updating-role-settings"></a>检索和更新角色设置

使用以下 cmdlet 获取 Azure AD 组织中的所有角色设置。

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

设置中有四个主要对象。 PIM 目前仅使用其中三个对象。 "用户成员设置"是激活设置，"管理员资格设置"是符合条件的分配分配设置，管理员成员设置是活动分配的分配设置。

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

要更新角色设置，您需要首先定义设置对象，如下所示：

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

然后，您可以继续将设置应用于特定角色的对象之一，如下所示。 此处的 ID 是从列表角色设置 cmdlet 的结果中检索的角色设置 ID。

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>后续步骤

- [分配 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [删除或更新 Azure AD 自定义角色分配](azure-ad-custom-roles-update-remove.md)
- [配置 Azure AD 自定义角色分配](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定义](../users-groups-roles/directory-assign-admin-roles.md)
