---
title: 更改用户和组的许可计划-Azure AD |Microsoft Docs
description: 如何使用中的组许可将组内的用户迁移到不同的服务计划 Azure Active Directory
services: active-directory
keywords: Azure AD 许可
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025907"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>在 Azure Active Directory 中更改用户或组的许可证分配

本文介绍如何在 Azure Active Directory （Azure AD）中的服务许可证计划之间移动用户和组。 目标 Azure AD 的方法是确保在许可证更改期间不会丢失任何服务或数据。 用户应在服务之间无缝切换。 本文中的许可计划分配步骤介绍了如何将 Office 365 E1 上的用户或组更改为 Office 365 E3，但这些步骤适用于所有许可计划。 当你更新用户或组的许可证分配时，将同时创建许可证分配删除和新分配，以便在许可证更改期间用户不会失去对其服务的访问权限，或者查看计划之间的许可证冲突。

## <a name="before-you-begin"></a>开始之前

在更新许可证分配之前，请务必验证是否对要更新的所有用户或组执行了某些假设。 如果组中的所有用户都不满足上述条件，则迁移可能会失败。 因此，某些用户无法访问服务或数据。 请确保：

- 用户的当前许可计划（在本例中为 Office 365 E1）分配给组，并由用户继承，而不是直接分配。

- 你有足够的可用许可证用于你要分配的许可证计划。 如果没有足够的许可证，可能不会为某些用户分配新的许可计划。 你可以检查可用的许可证数量。

- 用户没有其他已分配的服务许可证可能会与所需的许可证冲突，或者不允许删除当前许可证。 例如，来自工作区分析或 Project Online 等服务的许可证依赖于其他服务。

- 如果在本地管理组，并通过 Azure AD Connect 将它们同步到 Azure AD，则可以使用本地系统添加或删除用户。 可能需要一段时间才能使所做的更改与 Azure AD 进行同步，以由组授权选取。

- 如果使用 Azure AD 动态组成员身份，则可以通过更改用户的属性添加或删除用户，但是许可证分配的更新过程保持不变。

## <a name="change-user-license-assignments"></a>更改用户许可证分配

在 "**更新许可证分配**" 页上，如果你看到某些复选框不可用，则表示无法更改的服务，因为它们继承自组许可证。

1. 使用 Azure AD 组织中的许可证管理员帐户登录到[Azure 门户](https://portal.azure.com/)。
1. 选择 " **Azure Active Directory** > **用户**"，然后打开用户的**配置文件**页面。
1. 选择“许可证”。
1. 选择 "**分配**"，编辑用户或组的许可证分配。 可以在 "**分配**" 页上解决许可证分配冲突。
1. 选中 Office 366 E3 的复选框，并确保至少选择了分配给该用户的所有 E1 服务。
1. 清除 Office 365 E1 的复选框。

    ![显示 Office 365 E1 已清除并选中 Office 365 E3 的用户的 "许可证分配" 页](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. 选择“保存”。

Azure AD 会应用新的许可证，并同时删除旧许可证以提供服务连续性。

## <a name="change-group-license-assignments"></a>更改组许可证分配

1. 使用 Azure AD 组织中的许可证管理员帐户登录到[Azure 门户](https://portal.azure.com/)。
1. 选择**Azure Active Directory** > **组**，然后打开组的 "**概述**" 页。
1. 选择“许可证”。
1. 选择 "**分配**" 命令，编辑用户或组的许可证分配。
1. 选中 Office 366 E3 的复选框。 若要保持服务的连续性，请确保选择已分配给用户的所有 E1 服务。
1. 清除 Office 365 E1 的复选框。

    ![选择 "用户或组许可证" 页上的 "分配" 命令](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. 选择“保存”。

若要提供服务连续性，Azure AD 会应用新的许可证，并同时删除组中所有用户的旧许可证。

## <a name="next-steps"></a>后续步骤

参阅以下文章，了解通过组进行许可证管理的其他方案：

- [将许可证分配到 Azure Active Directory 中的组](../users-groups-roles/licensing-groups-assign.md)
- [识别和解决 Azure Active Directory 中组的许可问题](../users-groups-roles/licensing-groups-resolve-problems.md)
- [如何将单个许可用户迁移到 Azure Active Directory 中的组许可](../users-groups-roles/licensing-groups-migrate-users.md)
- [Azure Active Directory 组许可附加方案](../users-groups-roles/licensing-group-advanced.md)
- [Azure Active Directory 中的组许可的 PowerShell 示例](../users-groups-roles/licensing-ps-examples.md)
