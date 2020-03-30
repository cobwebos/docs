---
title: 在 Azure AD 授权管理中创建访问包的访问审核
description: 了解如何在 Azure 活动目录访问审核（预览）中为授权管理访问包创建访问审核策略。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608834"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中创建访问包的访问审核

为了降低过时访问的风险，应启用对 Azure AD 授权管理中具有活动分配到访问包的用户的定期检查。 您可以在创建新访问包或编辑现有访问包时启用审核。 本文介绍如何启用访问包的访问审查。

## <a name="prerequisites"></a>先决条件

要启用访问包检查，必须满足创建访问包的先决条件：
- Azure AD Premium P2
- 全局管理员、用户管理员、目录所有者或访问包管理员

有关详细信息，请参阅[许可证要求](entitlement-management-overview.md#license-requirements)。


## <a name="create-an-access-review-of-an-access-package"></a>创建访问包的访问审核

您可以在[创建新访问包](entitlement-management-access-package-create.md)或[编辑现有访问包](entitlement-management-access-package-lifecycle-policy.md)策略时启用访问审核。 按照以下步骤启用访问包的访问审查：

1. 打开访问包的**生命周期**选项卡，向下滚动到**访问审核**。

1. 将 **"需要访问审核**"移到"**是**"。

    ![添加访问审核](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 指定评论将在 **"开始"旁边开始**的日期。

1. 接下来，将**审核频率**设置为**每年**、**每半年**、**每季度**或**每月**。
此设置确定访问审核发生的频率。

1. 设置 **"持续时间"** 以定义定期系列的每个审阅将打开多少天供审阅者输入。 例如，您可以安排从 1 月 1 日开始并开放审核 30 天的年度审核，以便审阅者必须到月底才能回复。

1. **如果希望**用户执行自己的访问审阅，请选择 **"自我审阅**"，或者，如果要指定审阅者，请选择 **"特定审阅者"。**

    ![选择"添加审阅者"](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. 如果选择 **"特定审阅者"，** 请指定哪些用户将执行访问审核：
    1. 选择 **"添加审阅者**"。
    1. 在 **"选择审阅者"窗格中**，搜索并选择要成为审阅者的用户。
    1. 选择审阅者后，单击 **"选择"** 按钮。

    ![指定审阅者](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 如果要在页面底部编辑访问包，请单击 **"查看 " 创建**，如果正在创建新的访问包或**更新**。

## <a name="view-the-status-of-the-access-review"></a>查看访问审核的状态

开始日期后，访问审核将在 **"访问审核**"部分中列出。 按照以下步骤查看访问审核的状态：

1. 在 **"身份治理"中**，单击 **"访问包**"，然后选择具有要检查的访问审阅状态的访问包。   

1. 访问包概览后，单击左侧菜单上的 **"访问评论**"。
    
    ![选择访问审核](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 将显示一个列表，其中包含具有与其关联的访问审核的所有策略。 单击审核以查看其报告。

    ![访问审核列表](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 查看报表时，它会显示已审核的用户数以及审阅者对报表执行的操作。

    ![查看审核状态](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>访问评论电子邮件通知
您可以指定审阅者，也可以用户自行查看其访问权限。 默认情况下，Azure AD 将在审阅开始后不久向审阅者或自我审阅者发送电子邮件。

电子邮件将包括有关如何查看访问包访问权限的说明。 如果审核是供用户查看其访问权限，则向他们显示有关如何对其访问包进行自我审阅的说明。
  
如果您已指定来宾用户为审阅者，并且他们尚未接受 Azure AD 来宾邀请，则他们将不会收到来自 Azure AD 访问审核的电子邮件。 必须先接受邀请，并在收到电子邮件之前使用 Azure AD 创建帐户。 

## <a name="next-steps"></a>后续步骤

- [查看访问包的访问](entitlement-management-access-reviews-review-access.md)
- [访问包的自我审查](entitlement-management-access-reviews-self-review.md)
