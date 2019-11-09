---
title: 在 PIM Azure AD 中创建 Azure 资源角色的访问评审Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中创建 Azure 资源角色的访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847073"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中创建 Azure 资源角色的访问评审

员工的特权 Azure 资源角色的访问权限会随时间推移而变化。 若要降低与过时角色分配相关的风险，应定期查看访问权限。 可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 为特权 Azure 资源角色创建访问评审。 还可以配置自动进行的定期访问评审。

本文介绍如何为特权 Azure 资源角色创建一个或多个访问评审。

## <a name="prerequisites"></a>先决条件

[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>打开访问评审

1. 使用“特权角色管理员”角色成员的用户身份登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 在左侧菜单中，选择 " **Azure 资源**"。

1. 选择要管理的资源，例如订阅或管理组。

1. 在 "管理" 下，选择 "**访问评审**"。

    ![Azure 资源 - 访问评审列表，其中显示所有评审的状态](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，单击“启动”。 访问评审将显示在列表中，并带有其状态指示器。

![访问评审列表，其中显示已启动评审的状态](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向他们显示有关如何[评审 Azure 资源角色访问权限](pim-resource-roles-perform-access-review.md)的说明。

## <a name="manage-the-access-review"></a>管理访问审阅

可以在访问评审的“概述”页上跟踪评审者完成评审的进度。 在[评审完成](pim-resource-roles-complete-access-review.md)之前，目录中的任何访问权限都不会更改。

![显示评审详细信息的访问评审概述页](./media/pim-resource-roles-start-access-review/access-review-overview.png)

如果这是一次性评审，则请在访问评审期限结束后或管理员停止了访问评审后，按照[完成 Azure 资源角色的访问评审](pim-resource-roles-complete-access-review.md)中的步骤查看并应用结果。  

若要管理一系列访问评审，请导航到访问评审，此时会在“计划的评审”中找到即将进行的评审，然后即可相应地编辑结束日期或添加/删除评审者。

根据你在“完成后操作”设置中的选择，自动应用会在评审的结束日期之后执行，或在你手动停止评审后执行。 评审状态将从“已完成”变为各种中间状态（例如“正在应用”），并最终变为“已应用”状态。 几分钟后，应当会看到被拒绝的用户（如果有）被从角色中删除。

## <a name="next-steps"></a>后续步骤

- [评审对 Azure 资源角色的访问权限](pim-resource-roles-perform-access-review.md)
- [完成 Azure 资源角色的访问评审](pim-resource-roles-complete-access-review.md)
- [创建 Azure AD 角色的访问评审](pim-how-to-start-security-review.md)
