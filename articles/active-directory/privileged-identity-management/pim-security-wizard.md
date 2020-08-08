---
title: Privileged Identity Management 以前的安全向导中 Azure AD 角色发现和见解 (预览) -Azure Active Directory
description: 发现和 insights (以前的安全向导) 帮助你将永久 Azure AD 角色分配转换为与 Privileged Identity Management 的实时分配。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/07/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1de7e98a9cb57f83b87589ceddedc3cdd80927
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005890"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>发现和见解 (预览) 用于 Azure AD 角色 (以前的安全向导) 

如果你开始使用 Privileged Identity Management (PIM) Azure Active Directory (Azure AD 组织，则可以使用 "**发现和见解) 预览 (** " 页开始使用。 此功能显示已分配到组织中的特权角色的用户，以及如何使用 PIM 快速将永久角色分配更改为实时分配。 你可以在**发现和见解 (预览) **中查看或更改你的永久特权角色分配。 它是一个分析工具和一个操作工具。

## <a name="discovery-and-insights-preview"></a>发现和见解 (预览) 

在你的组织开始使用 Privileged Identity Management 之前，所有角色分配都是永久性的。 即使用户不需要其权限，用户也始终处于其分配的角色中。 发现和见解 (预览) ，它将替换以前的安全向导，其中显示了特权角色的列表以及当前在这些角色中有多少个用户。 你可以列出角色的分配，以了解有关分配的用户的详细信息，前提是其中一个或多个用户不熟悉。

： heavy_check_mark： **Microsoft 建议**你保留2个将永久分配到全局管理员角色的无中断玻璃帐户，登录时不需要多重身份验证。 你可以在任何中断玻璃方案中使用这些方案，或在任何情况下都不能提升为全局管理员角色。

此外，如果用户有一个 Microsoft 帐户 (换言之，请将角色分配永久保留，这是用于登录 Microsoft 服务（如 Skype 或 Outlook.com) ）的帐户。 如果需要在此类角色中激活多重身份验证，则用户将被锁定。

## <a name="open-discovery-and-insights-preview"></a>打开发现和见解 (预览) 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择 " **Azure AD 角色**"，然后选择 "**发现" 和 "见解 (预览") **。 打开页面将开始发现过程，以查找相关的角色分配。

    ![Azure AD 角色-发现和见解页面显示3个选项](./media/pim-security-wizard/new-preview-link.png)

1. 选择 "**降低全局管理员**"。

    ![缩小全局管理员-显示所有成员的角色窗格](./media/pim-security-wizard/new-preview-page.png)

1. 查看全局管理员角色分配的列表。

    ![缩小全局管理员-显示所有成员的角色窗格](./media/pim-security-wizard/new-global-administrator-list.png)

1. 选择 "**下一步**" 以选择要使其符合条件的用户或组，然后选择 "**创建符合条件**" 或 "**删除分配**"。

    ![“将成员转换为符合条件成员”页，其中显示选择要使其符合角色条件的成员的选项](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. 你还可以要求所有全局管理员查看其自己的访问权限。

    ![显示 "访问评审" 部分的 "全局管理员" 页面](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. 选择任何这些更改后，你将看到 Azure 通知。

1. 然后，你可以选择 "**消除持续访问**" 或 "**查看服务主体**"，以在其他特权角色和服务主体角色分配上重复上述步骤。 对于服务主体角色分配，只能删除角色分配。

    ![用于消除持续访问和检查服务主体的其他见解选项 ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [授予其他管理员访问权限以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)
