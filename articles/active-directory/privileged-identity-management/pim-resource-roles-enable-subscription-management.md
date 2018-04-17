---
title: Azure 资源的 Privileged Identity Management - 启用订阅管理 | Microsoft Docs
description: 了解全局管理员可以如何管理租户中的订阅。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>启用订阅管理

作为目录的全局管理员，你可能并非对你的租户中的所有订阅资源都具有默认访问权限。 本文将概述向自己授予访问权限以访问租户中所有订阅的步骤，并提供了在收到访问权限后保持符合组织所需的任何安全控制要求的建议方法。

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>谁可以启用对我的目录中的订阅的管理？

分配到全局管理员角色的每个用户必须遵循以下步骤来启用订阅管理。 在为你自己启用订阅管理后，你可以添加可能也需要访问资源的其他全局管理员。 没有可用于为全局管理员角色的所有成员启用访问的目录设置。

## <a name="log-on-to-the-azure-portal"></a>登录到 Azure 门户。

首先使用是全局管理员角色的合格或活动成员的帐户登录到 Azure 门户。 如果该帐户是合格的全局管理员，则在继续执行下一步骤之前必须先激活角色。

## <a name="access-the-azure-ad-admin-center"></a>访问 Azure AD 管理中心

现在，你已作为全局管理员登录到 Azure 门户，可以对将提供对 Azure 订阅的访问权限的设置进行编辑了。 导航到 Azure AD 管理中心，在左侧导航栏中找到并选择“属性”选项卡。

![](media/azure-pim-resource-rbac/aad_properties.png)

在属性列表中，将标题为“全局管理员可以管理 Azure 订阅”的选项切换到“是”。

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>导航到 Azure AD PIM

启用此选项后，你的帐户将自动添加到租户中每个订阅资源的“用户访问管理员”角色。 从这里，导航到 Azure AD PIM 并在左侧导航栏的“管理”部分下选择 Azure 资源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>管理和发现资源

如果你的组织已在使用 Azure AD PIM 保护 Azure Active Directory 中的管理员，则你在该边栏选项卡加载后会看到一个订阅列表。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> 如果未看到任何资源，请检查：
>- 你的全局管理员角色是否已过期 
>- 你的组织是否有 Azure 订阅

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>配置分配

从列表中选择一个订阅并将你自己（或你是其成员的某个组）分配为该资源的合格所有者。 
[阅读有关分配角色的详细信息](pim-resource-roles-assign-roles.md)。

在继续执行下一步骤之前，请为每个资源重复此过程。

## <a name="clean-up-standing-access"></a>清理现有访问权限

现在，已经针对组织中的重要订阅为你分配了合格角色，你现在可以通过在目录属性中禁用相应选项来清理现有访问权限：

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>后续步骤

[发现资源](pim-resource-roles-discover-resources.md)

[配置角色设置](pim-resource-roles-configure-role-settings.md)








