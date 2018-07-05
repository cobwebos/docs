---
title: Azure 资源的 Privileged Identity Management - 启用订阅管理 | Microsoft Docs
description: 了解全局管理员可以如何管理租户中的订阅。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 6aeb82ff1feb3521f3a09dc1b28186754568bb27
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442965"
---
# <a name="enable-subscription-management-in-your-tenant"></a>在租户中启用订阅管理

作为目录的全局管理员，你可能并非对你的租户中的所有订阅资源都具有默认访问权限。 本文概述如何为自己授予访问租户中所有订阅的权限。 此外，对于收到访问权限后，如何继续遵循组织要求的所有安全控制，本文提供了相关建议做法。

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>谁可以启用对我的目录中的订阅的管理？

分配到全局管理员角色的每个用户必须遵循以下步骤来启用订阅管理。 在为自己启用订阅管理后，可以添加可能也需要访问资源的其他全局管理员。 没有可用于为全局管理员角色的所有成员启用访问的目录设置。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

使用全局管理员角色的合格或活动成员的帐户登录到 Azure 门户。 如果该帐户是合格的全局管理员，则在继续执行下一步骤之前必须先激活角色。

## <a name="access-the-azure-active-directory-admin-center"></a>访问 Azure Active Directory 管理中心

现在，你已作为全局管理员登录到 Azure 门户，可对提供 Azure 订阅访问权限的设置进行编辑。 浏览到 Azure Active Directory (Azure AD) 管理中心，并选择“属性”。

![突出显示“属性”的 Azure AD 管理中心屏幕快照](media/azure-pim-resource-rbac/aad_properties.png)

在属性列表的“全局管理员可以管理 Azure 订阅”下，选择“是”。

![切换按钮设置为“是”的属性页屏幕快照](media/azure-pim-resource-rbac/aad_properties_save.png)

现在，帐户将自动添加到租户中每个订阅资源的用户访问管理员角色。

## <a name="browse-to-azure-ad-pim"></a>浏览到 Azure AD PIM

 从这里，转到 Azure AD Privileged Identity Management (PIM)。 在“管理”下，选择“Azure 资源”。

![突出显示 Azure 资源的 PIM 屏幕快照](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>管理和发现资源

如果组织已在使用 Azure AD PIM 保护 Azure AD 中的管理员，则可在该边栏选项卡加载后会看到一个订阅列表。

![边栏选项卡中显示订阅列表的 PIM 屏幕快照](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> 如果未看到任何资源，请确认：
>- 全局管理员角色是否已过期。 
>- 组织是否有 Azure 订阅。

![资源列表为空的 PIM 屏幕快照](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>配置分配

从列表中选择一个订阅并将自己（或你所在的某个组）分配为该资源的合格所有者。 
[阅读有关分配角色的详细信息](pim-resource-roles-assign-roles.md)。

在继续执行下一步骤之前，请为每个资源重复此过程。

## <a name="clean-up-standing-access"></a>清理现有访问权限

现在，已经针对组织中的重要订阅为你分配了合格角色，即可通过在目录属性中禁用相应选项来清理现有访问权限。

![切换按钮设置为“否”的属性页屏幕快照](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>后续步骤

[发现资源](pim-resource-roles-discover-resources.md)

[配置角色设置](pim-resource-roles-configure-role-settings.md)








