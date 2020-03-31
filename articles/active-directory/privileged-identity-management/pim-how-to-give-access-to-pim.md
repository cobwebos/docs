---
title: 授予管理 PIM 的访问权限 - Azure 活动目录 |微软文档
description: 了解如何授予其他管理员访问权限以管理 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022114"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>授予其他管理员访问权限以管理 Privileged Identity Management

为组织启用 Privileged Identity Management (PIM) 的全局管理员会自动获得角色分配和 Privileged Identity Management 的访问权限。 但是，默认情况下，Azure Active Directory (Azure AD) 组织中的其他任何人（包括其他全局管理员）都不会获得写入访问权限。 其他全局管理员、安全管理员和安全信息读取者拥有 Privileged Identity Management 的只读访问权限。 要授予对 Privileged Identity Management 的访问权限，第一位用户可以将其他用户分配到“特权角色管理员”角色****。

> [!NOTE]
> 管理 Privileged Identity Management 需要 Azure 多重身份验证。 由于 Microsoft 帐户无法注册 Azure 多重身份验证，因此使用 Microsoft 帐户登录的用户无法访问 Privileged Identity Management。

请确保特权角色管理员角色中始终至少有两位用户，以防其中一位用户被锁定或帐户被删除。

## <a name="grant-access-to-manage-pim"></a>授予用于管理 PIM 的访问权限

1. 登录到 Azure[门户](https://portal.azure.com/)。

1. 在 Azure AD 中，打开 **Privileged Identity Management**。

1. 选择“Azure AD 角色”****。

1. 选择“角色”****。

    ![Privileged Identity Management Azure AD 角色 - 角色](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. 选择“特权角色管理员”角色，打开成员页****。

    ![特权角色管理员 - 成员](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. 选择“添加成员”打开“添加受管理成员”窗格****。

1. 选择“选择成员”，打开“选择成员”窗格****。

    ![特权角色管理员 - 选择成员](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. 选择一个成员，然后单击“选择”****。

1. 选择“确定”，使该成员有资格获得“特权角色管理员”角色********。

    向 Privileged Identity Management 中的某位用户分配新角色时，系统会自动将其配置为“有资格”激活该角色****。

1. 要使该成员成为永久成员，请在“特权角色管理员”成员列表中选择该用户。

1. 选择“更多”，然后选择“永久保留”，使其成为永久成员********。

    ![特权角色管理员 - 成为永久成员](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. 向用户发送[开始使用 Privileged Identity Management](pim-getting-started.md) 的链接。

## <a name="remove-access-to-manage-pim"></a>删除用于管理 PIM 的访问权限

从特权角色管理员角色中删除某人之前，请确保至少仍有两位用户分配有该角色。

1. 登录到 Azure[门户](https://portal.azure.com/)。

1. 打开**Azure AD 特权标识管理**。

1. 选择“Azure AD 角色”****。

1. 选择“角色”****。

1. 选择“特权角色管理员”角色，打开成员页****。

1. 选中要删除的用户旁边的复选框，然后选择“删除成员”****。

    ![特权角色管理员 - 删除成员](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. 如果要求确认是否要从角色中删除成员，请选择“是”****。

## <a name="next-steps"></a>后续步骤

- [开始使用 Privileged Identity Management](pim-getting-started.md)
