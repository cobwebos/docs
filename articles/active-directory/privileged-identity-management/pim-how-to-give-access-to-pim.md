---
title: 授予对其他管理员可以管理 Azure Active Directory 的 PIM 的访问权限 |Microsoft Docs
description: 了解如何授予其他管理员访问权限以管理 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22e3cc93baebac023c0148812c6a4c6c95be60
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489204"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>授予其他管理员访问权限以管理 PIM

全局管理员，可以在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 的组织自动获得角色分配和 PIM 的访问权限。 但是，默认情况下，没有任何其他人会获得写入访问权限，包括其他全局管理员。 其他全局管理员、安全管理员和安全读取者拥有 PIM 的只读访问权限。 要授予对 PIM 的访问权限，第一位用户可以将其他用户分配到“特权角色管理员”角色。

> [!NOTE]
> 管理 PIM 需要 Azure MFA。 由于 Microsoft 帐户无法注册 Azure MFA，因此使用 Microsoft 帐户登录的用户无法访问 PIM。

请确保特权角色管理员角色中始终至少有两位用户，以防其中一位用户被锁定或帐户被删除。

## <a name="grant-access-to-manage-pim"></a>授予用于管理 PIM 的访问权限

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

1. 单击“角色”。

    ![PIM Azure AD 角色的角色](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. 单击“特权角色管理员”角色，打开成员页。

    ![特权角色管理员 - 成员](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. 单击“添加成员”打开“添加受管理成员”窗格。

1. 单击“选择成员”，打开“选择成员”窗格。

    ![特权角色管理员 - 选择成员](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. 选择一个成员，然后单击“选择”。

1. 单击“确定”，使该成员有资格获得“特权角色管理员”角色。

    向 PIM 中的某位用户分配新角色时，系统会自动将其配置为“有资格”激活该角色。

1. 若要使该成员成为永久成员，请单击“特权角色管理员”成员列表中的该用户。

1. 单击“更多”，然后单击“永久保留”，使其成为永久成员。

    ![特权角色管理员 - 成为永久成员](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. 向用户发送指向[开始使用 PIM](pim-getting-started.md) 的链接。

## <a name="remove-access-to-manage-pim"></a>删除用于管理 PIM 的访问权限

从特权角色管理员角色中删除某人之前，请确保至少仍有两位用户分配有该角色。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

1. 单击“角色”。

1. 单击“特权角色管理员”角色，打开成员页。

1. 在要删除的用户旁添加复选标记，然后单击“删除成员”。

    ![特权角色管理员 - 删除成员](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. 在出现的询问是否需要从角色删除成员的消息中，单击“是”。

## <a name="next-steps"></a>后续步骤

- [开始使用 PIM](pim-getting-started.md)
