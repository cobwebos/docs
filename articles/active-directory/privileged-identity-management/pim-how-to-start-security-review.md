---
title: 在 PIM-Azure Active Directory 中创建 Azure AD 角色的访问评审Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中创建 Azure AD 角色的访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72d9f0c7df12bd6746cd5cf0336e7bd58b6cc88d
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809034"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中创建 Azure AD 角色的访问评审

用户获取对特权 Azure Active Directory （Azure AD）角色的访问权限的要求可能会随时间而改变。 若要降低与过时角色分配相关的风险，应定期查看访问权限。 您可以使用 Azure AD Privileged Identity Management （PIM）为特权 Azure AD 角色创建访问评审。 你还可以配置自动发生的定期访问评审。

本文介绍如何为特权 Azure AD 角色创建一个或多个访问评审。

## <a name="prerequisites"></a>必备组件

[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>打开访问评审

1. 使用 "特权角色管理员" 角色的成员用户身份登录到[Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 在左侧菜单中，选择 " **Azure AD 角色**"，然后选择 "**访问评审**"。

1. 在 "管理" 下，选择 "**访问评审**"。

    ![Azure AD 角色-显示所有审阅状态的访问评审列表](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，请选择 "**启动**"。 访问评审将在列表中显示，并显示其状态指示器。

![显示已开始审阅状态的访问评审列表](./media/pim-how-to-start-security-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 您可以向他们展示如何[查看对 Azure AD 角色的访问权限](pim-how-to-perform-security-review.md)的说明。

## <a name="manage-the-access-review"></a>管理访问审阅

当审阅者在访问评审的 "**概述**" 页上完成其审阅时，可以跟踪进度。 在[评审完成](pim-how-to-complete-review.md)之前，不会更改目录中的访问权限。

![显示评审详细信息的访问评审概述页](./media/pim-how-to-start-security-review/access-review-overview.png)

如果是一次性检查，则在访问评审期限结束后，或者管理员停止了访问评审后，请按照[完成 Azure AD 角色的访问评审](pim-how-to-complete-review.md)中的步骤查看并应用结果。  

若要管理一系列访问评审，请导航到 "访问评审"，然后在 "计划的审阅" 中找到即将发生的事件，并相应地编辑结束日期或添加/删除审阅者。

根据你在**完成设置**中所做的选择，自动应用将在评审结束日期之后或在你手动停止审阅时执行。 此评审的状态将从 "**已完成**" 更改为 "正在**应用**的状态"，如 "**应用**" 和 "最终"。 在数分钟内，你应该会看到已拒绝的用户（如果有）被从角色中删除。

## <a name="next-steps"></a>后续步骤

- [查看对 Azure AD 角色的访问权限](pim-how-to-perform-security-review.md)
- [完成 Azure AD 角色的访问评审](pim-how-to-complete-review.md)
- [创建 Azure 资源角色的访问评审](pim-resource-roles-start-access-review.md)
