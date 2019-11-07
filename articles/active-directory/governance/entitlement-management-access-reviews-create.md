---
title: 在 Azure AD 权限管理中创建访问包的访问评审
description: 了解如何在 Azure Active Directory 访问评审（预览版）中创建授权管理访问包的访问评审策略。
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
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608834"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权限管理中创建访问包的访问评审

为了降低过时访问的风险，您应该在 Azure AD 授权管理中，对访问包具有活动分配的用户进行定期检查。 你可以在创建新的访问包或编辑现有访问包时启用评论。 本文介绍如何启用访问权限包的访问评审。

## <a name="prerequisites"></a>先决条件

若要启用访问包的检查，必须满足创建访问包的先决条件：
- Azure AD Premium P2
- 全局管理员、用户管理员、目录所有者或访问包管理者

有关详细信息，请参阅[许可要求](entitlement-management-overview.md#license-requirements)。


## <a name="create-an-access-review-of-an-access-package"></a>创建访问包的访问评审

可以在[创建新的访问包](entitlement-management-access-package-create.md)或[编辑现有的访问包](entitlement-management-access-package-lifecycle-policy.md)策略时启用访问评审。 请按照以下步骤启用访问包的访问评审：

1. 打开访问包的 "**生命周期**" 选项卡，向下滚动以**访问审阅**。

1. 将 "**需要访问评审**" 切换切换为 **"是"** 。

    ![添加访问评审](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 指定开始评审的开始**日期。**

1. 接下来，将**审核频率**设置为**每**年、每**两年** **、每**年、每**月或每月**。
此设置确定访问评审的频率。

1. 设置 "**持续时间**"，以定义为审阅者的输入打开的重复序列的每个查看天数。 例如，您可以计划在1月1日开始的年度评审，并在30天内开启查看，以便审阅者在该月结束之前做出响应。

1. 如果你希望用户执行其自己的访问评审，**则选择 "** **审阅者**" 旁边的 "查看"，如果要指定审阅者，请选择 "查看**特定审阅者**"。

    ![选择 "添加审阅者"](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. 如果选择了 "**特定审阅者**"，请指定将执行访问评审的用户：
    1. 选择 "**添加审阅者**"。
    1. 在 "**选择审阅者**" 窗格中，搜索并选择要作为审阅者的用户。
    1. 选择了审阅者后，请单击 "**选择**" 按钮。

    ![指定审阅者](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 如果正在创建新的访问包，则单击 "**查看 + 创建**" **; 如果您**正在编辑访问包，则单击页面底部的 "查看"。

## <a name="view-the-status-of-the-access-review"></a>查看访问评审的状态

开始日期之后，访问评审将在 "**访问评审**" 部分列出。 按照以下步骤查看访问评审的状态：

1. 在 "**身份管理**" 中，单击 "**访问包**"，然后选择具有要检查的访问评审状态的访问包。   

1. 在访问包概述后，单击左侧菜单中的 "**访问评审**"。
    
    ![选择访问评审](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 将显示一个列表，其中包含具有与之关联的访问评审的所有策略。 单击 "查看" 查看其报表。

    ![访问评审列表](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 查看报表时，它会显示已评审的用户数以及审阅者对其执行的操作。

    ![查看评审状态](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>访问评审电子邮件通知
您可以指定审阅者，或者用户可以查看他们自己的访问权限。 默认情况下，在评审开始后，Azure AD 会立即向审阅者或自助审阅者发送一封电子邮件。

电子邮件将提供有关如何查看访问包访问权限的说明。 如果评审的用户要查看其访问权限，请向他们显示有关如何执行其访问包的自我检查的说明。
  
如果已将来宾用户分配为审阅者，但他们未接受 Azure AD 的来宾邀请，则不会收到 Azure AD 访问评审的电子邮件。 他们必须首先接受邀请，并使用 Azure AD 创建帐户，然后才能接收电子邮件。 

## <a name="next-steps"></a>后续步骤

- [查看访问包的访问权限](entitlement-management-access-reviews-review-access.md)
- [查看访问包](entitlement-management-access-reviews-self-review.md)
