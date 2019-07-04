---
title: 在 PIM 的 Azure Active Directory 中的 azure AD 角色安全向导 |Microsoft Docs
description: 介绍了可以用来使用 Azure AD Privileged Identity Management (PIM) 将永久特权 Azure AD 角色分配转换为符合条件的分配的安全向导。
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
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4fd850ac2116dc7f353eea87845501fff020bb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476227"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>PIM 中的 Azure AD 角色安全向导

如果您是运行 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 为你的组织的第一个人，则将显示一个向导。 该向导可帮助你了解特权标识的安全风险，以及如何使用 PIM 来降低这些风险。 如果希望以后再对现有的角色分配进行任何更改，则暂时不需要在向导中进行这些更改。

## <a name="wizard-overview"></a>向导概述

在组织开始使用 PIM 之前，所有角色分配都是永久性的：即使用户当前无需其权限，但他们一直保留这些角色。 该向导的第一个步骤显示高特权角色的列表，以及当前有多少用户充当这些角色。 如果不熟悉其中的一个或多个用户，可以深入到某个特定角色来详细了解相应的用户。

该向导的第二个步骤用于更改管理员的角色分配。  

> [!WARNING]
> 必须至少有一个全局管理员，并且要有多个具有组织帐户（而不是 Microsoft 帐户）的特权角色管理员。 如果只有一个特权角色管理员，一旦该帐户遭到删除，组织就无法管理 PIM。
> 此外，如果用户具有 Microsoft 帐户（用于登录 Skype 和 Outlook.com 等 Microsoft 服务），请将角色分配设置为永久。 如果打算要求激活该角色之前执行 MFA，该用户会被锁定。

## <a name="run-the-wizard"></a>运行向导

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure AD 角色”  ，然后单击“向导”  。

    ![Azure AD 角色-显示 3 个步骤，运行该向导的向导页](./media/pim-security-wizard/wizard-start.png)

1. 单击“1 发现特权角色”  。

1. 查看特权角色列表以查看哪些用户是永久的或符合条件的。

    ![发现特权的角色的角色窗格，其中显示永久符合条件的成员](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. 单击“下一步”  以选择要使其符合条件的成员。

    ![成员转换为使用选项来选择你想要设为符合条件的角色的成员资格页](./media/pim-security-wizard/convert-members-eligible.png)

1. 选择成员后，单击“下一步”。 

    ![查看更改页面，其中显示具有永久角色分配将转换的成员](./media/pim-security-wizard/review-changes.png)

1. 单击“确定”  以将永久分配转换为符合条件的。

    转换完成后，你将看到一条通知。

    ![通知显示转换的状态](./media/pim-security-wizard/notification-completion.png)

如果需要将其他特权角色分配转换为符合条件的，可以再次运行向导。 如果你想要使用 PIM 接口而不是该向导，请参阅[分配 Azure AD PIM 中的角色](pim-how-to-add-role-to-user.md)。

## <a name="next-steps"></a>后续步骤

- [将 Azure AD PIM 中的角色分配](pim-how-to-add-role-to-user.md)
- [授予其他管理员访问权限以管理 PIM](pim-how-to-give-access-to-pim.md)
