---
title: 标识要在 Privileged Identity Management 中管理的组 - Azure AD | Microsoft Docs
description: 了解如何在 Privileged Identity Management (PIM) 中加入可分配角色的组以作为特权访问组进行管理。
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
ms.openlocfilehash: 68795033d36ad0bcb36b7cd81cea0d848ecd5113
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049003"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>将特权访问组（预览）引入 Privileged Identity Management

在 Azure Active Directory (Azure AD) 中，可以将 Azure AD 内置角色分配给云组，以简化管理角色分配的方式。 若要保护 Azure AD 角色和确保访问安全，现在可以使用 Privileged Identity Management (PIM) 来管理这些组的成员或所有者的实时访问。 若要将 Azure AD 可分配角色的组作为 Privileged Identity Management 中的特权访问组来管理，必须将该组引入 PIM 中进行管理。

## <a name="identify-groups-to-manage"></a>标识要管理的组

可以在 Azure AD 中创建角色可分配的组，如 [Azure Active Directory 中的创建可分配角色的组](../users-groups-roles/roles-groups-create-eligible.md)中所述。 你已是组的所有者，以使用 Privileged Identity Management 管理它。

1. 使用特权角色管理员角色权限[登录到 Azure AD](https://aad.portal.azure.com)。
1. 选择“组”，然后选择要在 PIM 中管理的可分配角色的组。 可以搜索和筛选列表。

    ![查找要在 PIM 中管理的可分配角色的组](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 打开组，选择“特权访问(预览)”。

    ![打开 Privileged Identity Management 体验](./media/groups-discover-groups/groups-discover-groups.png)

1. 开始在 PIM 中管理分配。

    ![管理 Privileged Identity Management 中的分配](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 一旦某个特权访问组受到管理，就不能将其从管理中删除。 这可防止其他资源管理员删除 Privileged Identity Management 设置。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置特权访问组分配](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中分配特权访问组](pim-resource-roles-assign-roles.md)
