---
title: Privileged Identity Management 中以前称为安全向导的 Azure AD 角色发现和见解（预览版）- Azure Active Directory
description: 发现和见解（以前称为安全向导）可帮助你将永久 Azure AD 角色分配转换为 Privileged Identity Management 的实时分配。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b826c6a1fd5a60247f8c69cb10cd24e7e001fe71
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335878"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Azure AD 角色的发现和见解（以前称为安全向导）（预览版）

如果你在 Azure Active Directory (Azure AD) 组织中开始使用 Privileged Identity Management (PIM)，可以使用**发现和见解（预览版）** 页来入门。 此功能显示已分配到组织中的特权角色的用户，以及如何使用 PIM 快速将永久角色分配更改为实时分配。 可以在**发现和见解（预览版）** 中查看或更改永久特权角色分配。 它是一个分析工具，也是一个操作工具。

## <a name="discovery-and-insights-preview"></a>发现和见解（预览版）

在你的组织开始使用 Privileged Identity Management 之前，所有角色分配都是永久性的。 即使用户不需要其权限，用户也始终处于已分配的角色中。 发现和见解（预览版）取代了以前的安全向导，它会显示特权角色的列表以及当前有多少个用户处于这些角色中。 你可以列出角色的分配，以便详细了解分配的用户（如果你不熟悉其中一个或多个用户）。

： heavy_check_mark： **Microsoft 建议** 将两个中断玻璃帐户永久分配给全局管理员角色。 请确保这些帐户不需要与正常管理帐户相同的多重身份验证机制登录，如 [Azure AD 管理紧急访问帐户中](../users-groups-roles/directory-emergency-access.md)所述。

此外，如果用户具有 Microsoft 帐户（即，用于登录 Skype 或 Outlook.com 等 Microsoft 服务的帐户），则永久保留角色分配。 如果要求具有 Microsoft 帐户的用户执行多重身份验证以激活角色分配，则该用户将被锁定。

## <a name="open-discovery-and-insights-preview"></a>打开发现和见解（预览版）

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure AD 角色”，然后选择“发现和见解(预览)”。 打开该页面将开始发现过程，以查找相关的角色分配。

    ![Azure AD 角色 - 发现和见解页面，其中显示 3 个选项](./media/pim-security-wizard/new-preview-link.png)

1. 选择“减少全局管理员”。

    ![减少全局管理员 -“角色”窗格，其中显示所有成员](./media/pim-security-wizard/new-preview-page.png)

1. 查看全局管理员角色分配的列表。

    ![降低全局管理员-显示所有全局管理员的角色窗格](./media/pim-security-wizard/new-global-administrator-list.png)

1. 选择“下一步”以选择要设为符合条件的管理员的用户或组，然后选择“设为符合条件的管理员”或“删除分配”。

    ![“将成员转换为符合条件成员”页，其中显示选择要使其符合角色条件的成员的选项](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. 还可以要求所有全局管理员评审其自己的访问权限。

    ![“全局管理员”页面，其中显示“访问评审”部分](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. 选择其中任何一个更改后，你将看到 Azure 通知。

1. 然后，可以选择“消除现有访问权限”或“评审服务主体”，在其他特权角色和服务主体分配上重复上述步骤。 对于服务主体角色分配，只能删除角色分配。

    ![用于消除现有访问权限和评审服务主体的其他见解选项 ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [授予其他管理员访问权限以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)
