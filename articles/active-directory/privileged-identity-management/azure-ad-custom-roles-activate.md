---
title: 在 Privileged Identity Management (PIM) 中激活 Azure AD 自定义角色 | Microsoft Docs
description: 如何为 Privileged Identity Management (PIM) 激活 Azure AD 自定义角色
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ac8acf2193442de5c85e0a1134d46330821c77b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756450"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>在 Privileged Identity Management 中激活 Azure AD 自定义角色

Azure Active Directory (Azure AD) 中的 Privileged Identity Management 现在支持对自定义角色进行恰时分配和有时限的分配，这些自定义角色是在“标识和访问管理”管理体验中为了进行应用程序管理而创建的。 若要详细了解如何在 Azure AD 中创建自定义角色来委托应用程序管理，请参阅 [Azure Active Directory 中的自定义管理员角色（预览）](../users-groups-roles/roles-custom-overview.md)。

> [!NOTE]
> 在预览版中，Azure AD 自定义角色未集成内置的目录角色。 此功能的正式版发布后，可在内置的角色体验中进行角色管理。

## <a name="activate-a-role"></a>激活角色

需要激活 Azure AD 自定义角色时，请在 Privileged Identity Management 中选择 "我的角色" 导航选项来请求激活。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 打开 Azure AD [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)。

1. 选择“Azure AD 自定义角色”查看符合条件的 Azure AD 自定义角色分配列表。

   ![查看符合条件的 Azure AD 自定义角色分配列表](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

1. 在“Azure AD 自定义角色(预览版)”页上，找到所需的分配。
1. 选择“激活角色”打开“激活”页。
1. 如果角色需要多重身份验证，请选择“验证你的身份，然后继续”。 在每个会话中只需执行身份验证一次。
1. 选择“验证我的身份”，并遵照说明提供任何其他安全验证。
1. 若要指定自定义应用程序范围，请选择“范围”打开筛选器窗格。 应在所需的最小范围内请求对角色的访问权限。 如果你的分配处于应用程序范围，则只能在该范围激活。

   ![分配角色分配的 Azure AD 资源范围](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. 根据需要指定自定义的激活开始时间。 如果使用该选项，角色成员将在指定的时间激活。
1. 在“原因”框中，输入该激活请求的原因。 这些设置有时是必需的，有可能不是在角色设置中指定。
1. 选择“激活”。

如果该角色不需要审批，则它会根据设置激活，并添加到活动角色列表中。 若要使用激活的角色，请从[在 Privileged Identity Management 中分配 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)中的步骤开始。

如果激活角色需要审批，你将收到 Azure 通知，告知请求正在等待审批。

## <a name="next-steps"></a>后续步骤

- [发票 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [删除或更新 Azure AD 自定义角色分配](azure-ad-custom-roles-update-remove.md)
- [配置 Azure AD 自定义角色分配](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定义](../users-groups-roles/directory-assign-admin-roles.md)
