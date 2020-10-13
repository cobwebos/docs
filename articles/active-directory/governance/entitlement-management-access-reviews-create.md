---
title: 在 Azure AD 权利管理中创建访问包的访问评审
description: 了解如何在 Azure Active Directory 访问评审（预览版）中创建权利管理访问包的访问评审策略。
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
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798505"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中创建访问包的访问评审

为了降低访问权限过时的风险，应在 Azure AD 权利管理中为具有访问包的有效分配的用户启用定期评审。 可以在创建新的访问包或编辑现有访问包时启用评审。 本文介绍如何启用访问包的访问评审。

## <a name="prerequisites"></a>必备条件

若要启用访问包的评审，必须满足以下创建访问包的先决条件：
- Azure AD Premium P2
- 全局管理员、用户管理员、目录所有者或访问包管理员

有关详细信息，请参阅[许可证要求](entitlement-management-overview.md#license-requirements)。


## <a name="create-an-access-review-of-an-access-package"></a>创建访问包的访问评审

可以在[创建新的访问包](entitlement-management-access-package-create.md)或[编辑现有访问包](entitlement-management-access-package-lifecycle-policy.md)策略时启用访问评审。 请按照以下步骤启用访问包的访问评审：

1. 打开访问包的“生命周期”选项卡，向下滚动到“访问评审”。

1. 将“需要访问评审”开关移动到“是”。

    ![添加访问评审](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 在“开始时间”旁边指定开始评审的日期。

1. 接下来，将“评审频率”设置为“每年”、“每半年”、“每季度”或“每月”。
此设置确定访问评审的发生频率。

1. 请设置“持续时间”，以定义定期进行的一系列评审中每次评审开放（接受审阅者输入）的天数。 例如，可以计划一个年度评审，该评审在 1 月 1 日开始并开放 30 天供评审，以便审阅者在该月结束前做出响应。

1. 如果希望用户执行其自己的访问评审，请在“审阅者”旁边选择“自我评审”；如果想要指定审阅者，请选择“特定审阅者”。

    ![选择“添加审阅者”](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. 如果选择了“特定审阅者”，请指定哪些用户将执行访问评审：
    1. 选择“添加审阅者”。
    1. 在“选择审阅者”窗格中，搜索并选择要作为审阅者的用户。
    1. 选择了审阅者后，请单击“选择”按钮。

    ![指定审阅者](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 如果要创建新的访问包，请在页面底部单击“查看 + 创建” ；如果要编辑访问包，请在页面底部单击“更新”。

## <a name="view-the-status-of-the-access-review"></a>查看访问评审的状态

开始日期之后，访问评审将在“访问评审”部分中列出。 请按照以下步骤查看访问评审的状态：

1. 在“Identity Governance”中，单击“访问包”，然后选择带有要查看的访问评审状态的访问包。   

1. 进入访问包的“概述”后，请单击左侧菜单中的“访问评审”。
    
    ![选择访问评审](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 此时将显示一个列表，其中包含所有与访问评审关联的策略。 单击该评审以查看其报表。

    ![访问评审列表](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 查看报表时，报表会显示审阅者已评审的用户数以及审阅者对其执行的操作。

    ![查看评审状态](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>访问评审电子邮件通知
你可以指定审阅者，或者让用户审阅他们自己的访问权限。 默认情况下，在评审开始后不久，Azure AD 会向审阅者或自我审阅者发送一封电子邮件。

该电子邮件将包含有关如何评审对访问包的访问权限的说明。 如果该评审供用户评审他们自己的访问权限，请向他们显示有关如何对其访问包执行自我评审的说明。
  
如果已将来宾用户分配为审阅者，但他们未接受其 Azure AD 来宾邀请，则他们不会收到来自 Azure AD 访问评审的电子邮件。 他们必须首先接受邀请，并使用 Azure AD 创建帐户，然后才能接收电子邮件。 

## <a name="next-steps"></a>后续步骤

- [评审访问包的访问权限](entitlement-management-access-reviews-review-access.md)
- [自我评审访问包](entitlement-management-access-reviews-self-review.md)
