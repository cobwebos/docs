---
title: 在 Azure AD 权利管理中评审访问包的访问权限
description: 了解如何在 Azure Active Directory 访问评审（预览版）中完成权利管理访问包的访问评审。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798591"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中评审访问包的访问权限

Azure AD 权利管理简化了企业管理组、应用程序和 SharePoint 站点访问权限的方式。 本文介绍如何针对作为指定审阅者分配到访问包的其他用户执行访问评审。

## <a name="prerequisites"></a>必备条件

若要查看用户的有效访问包分配，必须满足执行访问评审的先决条件：
- Azure AD Premium P2
- 全局管理员
- 指定的用户管理员、目录所有者或访问包管理者

有关详细信息，请参阅[许可证要求](entitlement-management-overview.md#license-requirements)。


## <a name="open-the-access-review"></a>打开访问评审

请执行以下步骤找到并打开访问评审：

1. 你可能会从 Microsoft 收到一封要求你进行访问评审的电子邮件。 请找到该电子邮件，打开访问评审。 下面是用于评审访问权限的示例电子邮件：
    
    ![“访问评审”审阅者电子邮件](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. 单击“评审用户访问权限”链接，打开访问评审。 

1. 如果未收到该电子邮件，可通过直接导航到 https://myaccess.microsoft.com 查找待处理的访问评审。  （对于 US Government 版，请改用 `https://myaccess.microsoft.us`。）

1. 单击左侧导航栏上的“访问评审”，查看分配给你的待处理访问评审的列表。
    
    ![在“我的访问权限”上选择“访问评审”](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 单击要开始的评审。
    
    ![选择访问评审](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审后，你将看到需要评审的用户的名称。 可通过两种方式批准或拒绝访问权限：
- 可以手动批准或拒绝一个或多个用户的访问权限
- 可以接受系统建议

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手动批准或拒绝一个或多个用户的访问权限
1. 审阅用户的列表，并确定哪些用户需要继续拥有访问权限。

    ![要审阅的用户的列表](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 若要批准或拒绝访问权限，请选择用户名称左侧的单选按钮。

1. 在用户名上方的栏中选择“批准”或“拒绝”。

    ![选择用户](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 如果不确定，可以单击“不知道”按钮。

    如果做出这一选择，则用户将保留访问权限，并且此选择将记录在审核日志中。 该日志会向任何其他审阅者显示你仍完成了评审。

1. 你可能需要提供你做出决策的原因。 请键入一个原因，然后单击“提交”。

    ![批准或拒绝访问权限](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 你可以在评审结束之前随时更改你的决策。 为此，请从列表中选择用户并更改决策。 例如，可以为以前已拒绝的用户批准访问权限。

如果有多个评审者，将记录最后提交的响应。 假设有一个管理员指定两个审阅者（Alice 和 Bob）的示例。 Alice 首先打开该评审并批准了访问权限。 在评审结束之前，Bob 打开该评审并拒绝了访问权限。 在这种情况下，将记录上次的拒绝访问决策。

>[!NOTE]
>如果拒绝了某个用户的访问权限，不会立即从访问包中删除该用户。 当评审结束时，或在管理员结束评审时，系统会将该用户从访问包中删除。

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>使用系统生成的建议批准或拒绝访问权限

若要更快地评审多个用户的访问权限，可以使用系统生成的建议，只需单击一下即可接受建议。 建议是根据用户的登录活动生成的。

1.  在页面底部的栏中，单击“接受建议”。
    
    ![选择“接受建议”](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    你将看到建议操作的摘要。

1.  单击“提交”接受建议。

## <a name="next-steps"></a>后续步骤

- [自我评审访问包](entitlement-management-access-reviews-self-review.md)