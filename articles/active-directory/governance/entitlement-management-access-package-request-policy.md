---
title: Azure AD 权限管理中更改访问包的请求和审批设置-Azure Active Directory
description: 了解如何在 Azure Active Directory 权限管理中更改访问包的请求和审批设置。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 649a4a50766d26f73584dae4481652c8007b2e9e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174676"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 权限管理中更改访问包的请求和批准设置

作为访问包管理器，你可以通过编辑策略或添加新策略，随时更改可以请求访问包的用户。 你还可以更改审批设置。

本文介绍如何更改现有访问包的请求和审批设置。

## <a name="choose-between-one-or-multiple-polices"></a>在一个或多个策略之间进行选择

指定可以请求访问包的用户的方法是使用策略。 创建访问包时，可以指定创建策略的请求和审批设置。 大多数访问包都有一个策略，但单个访问包可以有多个策略。 如果希望允许不同的用户组通过不同的请求和审批设置授予分配，则可以为访问包创建多个策略。 例如，无法使用单个策略将内部和外部用户分配到相同的访问包。 但是，你可以在同一个访问包中创建两个策略，一个用于内部用户，另一个用于外部用户。 如果有多个策略应用于用户，则在请求时，系统将提示他们选择要分配到的策略。 下图显示了一个具有两个策略的访问包。

![访问包中的多个策略](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| 场景 | 策略数 |
| --- | --- |
| 我想让我的目录中的所有用户都具有相同的访问包请求和批准设置 | 一种 |
| 我想让某些连接的组织中的所有用户都能够请求访问包 | 一种 |
| 我想允许目录中的用户以及我的目录以外的用户请求访问包 | 多个 |
| 我想为某些用户指定不同的审批设置 | 多个 |
| 我想让某些用户访问包分配使其过期，而其他用户可以扩展其访问权限 | 多个 |

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>打开请求和审批设置的现有策略

若要更改访问包的请求和审批设置，需要打开相应的策略。 按照以下步骤打开访问包的请求和批准设置。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 单击 "**策略**"，然后单击要编辑的策略。

    "策略详细信息" 窗格将在页面底部打开。

    ![访问包-"策略详细信息" 窗格](./media/entitlement-management-shared/policy-details.png)

1. 单击 "**编辑**" 以编辑策略。

    ![访问包-编辑策略](./media/entitlement-management-shared/policy-edit.png)

1. 单击 "**请求**" 选项卡以打开请求和审批设置。

1. 执行以下请求部分之一中的步骤。

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>添加请求和审批设置的新策略

如果有一组用户需要不同的请求和审批设置，则可能需要创建新策略。 按照以下步骤开始向现有访问包添加新策略。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 单击 "**策略**"，然后单击 "**添加策略**"。

1. 键入策略的名称和描述。

    ![创建具有名称和说明的策略](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. 单击 "**下一步**" 以打开 "**请求**" 选项卡。

1. 执行以下请求部分之一中的步骤。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

如果正在编辑策略，请单击 "**更新**"。 如果要添加新策略，请单击 "**创建**"。

## <a name="next-steps"></a>后续步骤

- [更改访问包的生命周期设置](entitlement-management-access-package-lifecycle-policy.md)
- [查看访问包的请求](entitlement-management-access-package-requests.md)