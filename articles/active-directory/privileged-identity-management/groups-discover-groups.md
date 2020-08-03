---
title: 发现要在 PIM 中管理的角色可分配的组-Azure AD |Microsoft Docs
description: 了解如何发现角色可分配的组作为 Privileged Identity Management （PIM）中的特权访问组进行管理。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505999"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>发现要在 Privileged Identity Management 中管理的特权访问组（预览）

在 Azure Active Directory （Azure AD）中，你可以将 Azure AD 内置角色分配给云组，以简化角色分配的管理。 现在，可以使用 Privileged Identity Management （PIM）分配这些组的成员资格或所有权的资格，以保护 Azure AD 角色和保护访问。 在 Privileged Identity Management 中将 Azure AD 角色可分配的组作为特权访问组进行管理之前，你必须发现该组并在 PIM 中管理它。

## <a name="discover-resources"></a>发现资源

1. 以特权角色管理员角色权限[登录到 Azure AD](https://aad.portal.azure.com) 。
1. 在 Azure AD 中创建可分配角色的组。 你必须是组的所有者才能使用 Privileged Identity Management 发现和管理它。
1. 打开**Privileged Identity Management**。
1. 选择 "**特权访问（预览）**"。

    ![针对首次体验发现组命令](./media/groups-discover-groups/groups-discover-groups.png)

1. 选择 "**发现组**"。
1. 按组名称搜索。
1. 选择组，并选择 "**管理组**" 以将其置于 PIM 管理之下。

    ![发现未列出任何资源的组以获取首次体验](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > 在管理特权访问组后，不能将其从管理中排除。 这可防止其他资源管理员删除 Privileged Identity Management 设置。

1. 如果看到确认加入要管理的所选资源的消息，请选择“是”****。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置特权访问组分配](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中分配特权访问组](pim-resource-roles-assign-roles.md)
