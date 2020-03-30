---
title: 在 PIM 中创建 Azure AD 角色的访问审核 - Azure AD |微软文档
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中创建 Azure AD 角色的访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f7e9ef503a9a3469ecbc835be8d9229fbd0167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847110"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>在特权标识管理中创建 Azure AD 角色的访问审查

若要降低与过时角色分配相关的风险，应定期查看访问权限。 您可以使用 Azure AD 特权标识管理 （PIM） 为特权 Azure AD 角色创建访问审核。 还可以配置自动进行的定期访问评审。

本文介绍如何为特权 Azure AD 角色创建一个或多个访问评审。

## <a name="prerequisites"></a>先决条件

[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>打开访问评审

1. 使用作为特权角色管理员角色成员的用户登录到[Azure 门户](https://portal.azure.com/)。

1. 打开**Azure AD 特权标识管理**。

1. 在左侧菜单中，选择**Azure AD 角色**，然后选择 **"访问评论**"。

1. 在"管理"下，选择**访问审核**。

    ![Azure AD 角色 - 访问评审列表，其中显示所有评审的状态](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>启动访问评审

指定访问审核的设置后，选择 **"开始**"。 访问评审将显示在列表中，并带有其状态指示器。

![访问评审列表，其中显示已启动评审的状态](./media/pim-how-to-start-security-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向他们显示有关如何[评审 Azure AD 角色访问权限](pim-how-to-perform-security-review.md)的说明。

## <a name="manage-the-access-review"></a>管理访问审阅

可以在访问评审的“概述”**** 页上跟踪评审者完成评审的进度。 [在完成审核](pim-how-to-complete-review.md)之前，目录中不会更改任何访问权限。

![显示评审详细信息的访问评审概述页](./media/pim-how-to-start-security-review/access-review-overview.png)

如果这是一次性评审，则请在访问评审期限结束后或管理员停止了访问评审后，按照[完成 Azure AD 角色的访问评审](pim-how-to-complete-review.md)中的步骤查看并应用结果。  

若要管理一系列访问评审，请导航到访问评审，此时会在“计划的评审”中找到即将进行的评审，然后即可相应地编辑结束日期或添加/删除评审者。

根据您在**完成设置**中的选择，自动应用将在审核结束日期后或手动停止审核时执行。 审核的状态将从 **"已完成"** 更改为中间状态，如 **"应用**"，最后更改为"**已应用"。** 几分钟后，应当会看到被拒绝的用户（如果有）被从角色中删除。

## <a name="next-steps"></a>后续步骤

- [评审 Azure AD 角色的访问权限](pim-how-to-perform-security-review.md)
- [完成 Azure AD 角色的访问评审](pim-how-to-complete-review.md)
- [创建 Azure 资源角色的访问评审](pim-resource-roles-start-access-review.md)
