---
title: 更改 Azure AD 授权管理中访问包的请求和审批设置 - Azure 活动目录
description: 了解如何在 Azure 活动目录授权管理中更改访问包的请求和审批设置。
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
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655927"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>更改 Azure AD 授权管理中访问包的请求和审批设置

作为访问包管理器，您可以通过编辑策略或添加新策略来更改可以随时请求访问包的用户。 您还可以更改审批设置。

本文介绍如何更改现有访问包的请求和审批设置。

## <a name="choose-between-one-or-multiple-polices"></a>在一个或多个两个""""""""""""""""""

指定可以请求访问包的人员的方式与策略一起。 创建访问包时，指定创建策略的请求和审批设置。 大多数访问包将具有单个策略，但单个访问包可以有多个策略。 如果要允许使用不同的请求和审批设置为不同的用户集分配，则可以为访问包创建多个策略。 例如，单个策略不能用于将内部和外部用户分配给同一访问包。 但是，可在同一个访问包中创建两个策略 - 一个用于内部用户，一个用于外部用户。 如果对某个用户应用了多个策略，当用户请求选择要分配到的策略时，系统会提示他们。 下图显示了具有两个策略的访问包。

![访问包中的多个策略](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| 方案 | 策略数 |
| --- | --- |
| 我希望目录中的所有用户都具有相同的访问包请求和审批设置 | 一种 |
| 我希望某些已连接组织的所有用户能够请求访问包 | 一种 |
| 我想允许目录中的用户以及目录外部的用户请求访问包 | 多个 |
| 我想为某些用户指定不同的审批设置 | 多个 |
| 我希望某些用户访问包分配过期，而其他用户可以扩展其访问权限 | 多个 |

有关应用多个策略时使用的优先级逻辑的信息，请参阅[多个策略](entitlement-management-troubleshoot.md#multiple-policies
)。

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>打开请求和审批设置的现有策略

要更改访问包的请求和审批设置，需要打开相应的策略。 按照以下步骤打开访问包的请求和审批设置。

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击“策略”，然后单击要编辑的策略。****

    页面底部会打开“策略详细信息”窗格。

    ![访问包 -“策略详细信息”窗格](./media/entitlement-management-shared/policy-details.png)

1. 单击“编辑”以编辑策略。****

    ![访问包 - 编辑策略](./media/entitlement-management-shared/policy-edit.png)

1. 单击"**请求**"选项卡以打开请求和审批设置。

1. 在以下请求部分之一中执行这些步骤。

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>添加新的请求和审批设置策略

如果您有一组用户应具有不同的请求和审批设置，则可能需要创建新策略。 按照以下步骤开始向现有访问包添加新策略。

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 依次单击“策略”、“添加策略”。********

1. 键入策略的名称和说明。

    ![创建策略并输入名称和说明](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. 单击“下一步”，打开“请求”选项卡********。

1. 在以下请求部分之一中执行这些步骤。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

如果要编辑策略，请单击"**更新**"。 如果要添加新策略，请单击"**创建**"。

## <a name="next-steps"></a>后续步骤

- [更改访问包的生命周期设置](entitlement-management-access-package-lifecycle-policy.md)
- [查看访问包的请求](entitlement-management-access-package-requests.md)