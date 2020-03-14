---
title: PIM 中的 Azure AD 角色安全向导-Azure Active Directory |Microsoft Docs
description: 介绍了可以用来使用 Azure AD Privileged Identity Management (PIM) 将永久特权 Azure AD 角色分配转换为符合条件的分配的安全向导。
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
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266567"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Privileged Identity Management 中的 Azure AD 角色安全向导

如果你是第一位在 Azure Active Directory （Azure AD）组织中使用 Privileged Identity Management （PIM）的用户，则会看到一个向导来开始使用。 此向导可帮助你了解特权标识的安全风险，以及如何使用 Privileged Identity Management 降低这些风险。 如果希望以后再对现有的角色分配进行任何更改，则暂时不需要在向导中进行这些更改。

## <a name="wizard-overview"></a>向导概述

在你的组织开始使用 Privileged Identity Management 之前，所有角色分配都是永久性的：即使用户当前不需要其权限，用户也始终处于这些角色中。 该向导的第一个步骤显示高特权角色的列表，以及当前有多少用户充当这些角色。 如果不熟悉其中的一个或多个用户，可以深入到某个特定角色来详细了解相应的用户。

该向导的第二个步骤用于更改管理员的角色分配。  

> [!WARNING]
> 至少有一个全局管理员，并且有多个具有组织帐户（而不是 Microsoft 帐户）的特权角色管理员，这一点非常重要。 如果只有一个特权角色管理员，则组织将无法管理 Privileged Identity Management （如果该帐户已删除）。
> 此外，如果用户具有 Microsoft 帐户（即，用于登录到 Microsoft 服务（如 Skype 和 Outlook.com）的帐户，请将角色分配保留为永久。 如果你计划要求为该角色激活多因素身份验证，则该用户将被锁定。

## <a name="run-the-wizard"></a>运行向导

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择 " **Azure AD 角色**"，然后选择 "**向导**"。

    ![Azure AD 角色-向导页面，其中显示了运行向导的三个步骤](./media/pim-security-wizard/wizard-start.png)

1. 选择**1 发现特权角色**。

1. 查看特权角色列表以查看哪些用户是永久的或符合条件的。

    ![发现特权角色-显示永久和合格成员的角色窗格](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. 选择 "**下一步**" 以选择要使其符合条件的用户或组。

    ![将成员转换为符合条件的页，其中包含选择要为角色提供资格的成员的选项](./media/pim-security-wizard/convert-members-eligible.png)

1. 选择用户或组后，选择 "**下一步**"。

    !["查看更改" 页面，其中显示了具有将转换的永久角色分配的成员](./media/pim-security-wizard/review-changes.png)

1. 选择 **"确定"** 将永久分配转换为符合条件。

    转换完成后，你将看到一条通知。

    ![显示转换状态的通知](./media/pim-security-wizard/notification-completion.png)

如果需要将其他特权角色分配转换为符合条件的，可以再次运行向导。 如果要使用 Privileged Identity Management 接口而不是向导，请参阅[在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [向其他管理员授予访问权限以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)
