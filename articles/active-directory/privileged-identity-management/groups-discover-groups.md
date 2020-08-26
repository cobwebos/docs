---
title: 标识要在 Privileged Identity Management Azure AD 中管理的组 |Microsoft Docs
description: 了解如何在 Privileged Identity Management （PIM）中加入可作为特权访问组管理的角色可分配组。
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
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f7b61b6fb065409a2fe4b2ed8f599f321cd1db
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542260"
---
# <a name="bring-a-privileged-access-groups-preview-into-privileged-identity-management"></a>将特权访问组（预览）引入 Privileged Identity Management

在 Azure Active Directory （Azure AD）中，你可以将 Azure AD 内置角色分配给云组，以简化管理角色分配的方式。 若要保护 Azure AD 角色和保护访问，你现在可以使用 Privileged Identity Management （PIM）来管理这些组的成员或所有者的实时访问。 若要将 Azure AD 角色分配的组作为 Privileged Identity Management 中的特权访问组进行管理，必须在 PIM 中管理它。

## <a name="identify-groups-to-manage"></a>确定要管理的组

可以在 Azure AD 中创建角色可分配的组，如[Azure Active Directory 中的创建可分配角色的组](../users-groups-roles/roles-groups-create-eligible.md)中所述。 你已是组的所有者，以使用 Privileged Identity Management 管理它。

1. 以特权角色管理员角色权限[登录到 Azure AD](https://aad.portal.azure.com) 。
1. 选择 "**组**"，然后选择要在 PIM 中管理的角色分配的组。 可以搜索和筛选列表。

    ![查找要在 PIM 中管理的角色分配的组](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 打开 "组"，然后选择 "**特权访问（预览）**"。

    ![打开 Privileged Identity Management 体验](./media/groups-discover-groups/groups-discover-groups.png)

1. 开始在 PIM 中管理分配。

    ![管理 Privileged Identity Management 中的分配](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 在管理特权访问组后，不能将其从管理中排除。 这可防止其他资源管理员删除 Privileged Identity Management 设置。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置特权访问组分配](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中分配特权访问组](pim-resource-roles-assign-roles.md)
