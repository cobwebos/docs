---
title: 将可分配角色的组作为特权访问组进行管理-Azure AD |Microsoft Docs
description: 同意将角色分配的组作为 Privileged Identity Management (PIM) 中的特权访问组进行管理。
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
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869519"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>管理特权访问组 (预览) 在 Privileged Identity Management

在 Azure Active Directory (Azure AD) 中，你可以将 Azure AD 内置角色分配给云组，以简化管理角色分配的方式。 若要保护 Azure AD 角色和保护访问，你现在可以使用 Privileged Identity Management (PIM) 来管理这些组的成员或所有者的实时访问权限。 若要将 Azure AD 角色分配的组作为 Privileged Identity Management 中的特权访问组进行管理，必须在 PIM 中管理它。

## <a name="identify-groups-to-manage"></a>确定要管理的组

可以在 Azure AD 中创建角色可分配的组，如 [Azure Active Directory 中的创建可分配角色的组](../users-groups-roles/roles-groups-create-eligible.md)中所述。 你已是组的所有者，以便在 Privileged Identity Management 中管理它。

1. 以特权角色管理员权限[登录到 Azure AD](https://aad.portal.azure.com) 。
1. 选择 " **组** "，然后选择要管理的角色分配的组。 您可以搜索或筛选列表。

    ![查找要在 PIM 中管理的角色分配的组](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 打开 "组"，然后选择 " **特权访问 (预览") **。

    ![打开 Privileged Identity Management 体验](./media/groups-discover-groups/groups-discover-groups.png)

1. 如果尚未在 PIM 中管理组，请选择 " **启用特许访问** 以同意管理"。 只有组的全局管理员或所有者才能获得此许可。

    ![如果需要，同意在 Privileged Identity Management 中管理组](./media/groups-discover-groups/consent-page.png)

1. 开始在 PIM 中管理分配。

    ![管理 Privileged Identity Management 中的分配](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 在管理特权访问组后，不能将其从管理中排除。 这会阻止其他管理员删除 Privileged Identity Management 设置。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置特权访问组分配](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中分配特权访问组](pim-resource-roles-assign-roles.md)
