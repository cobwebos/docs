---
title: 在访问评审中评审对组或应用程序的访问权限 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure Active Directory 访问评审中评审组成员的访问权限或应用程序访问权限。
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e51ad8faecd935ea999b1287e542e4b14d55290a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948244"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中评审对组或应用程序的访问权限

Azure Active Directory (Azure AD) 借助称为“Azure AD 访问评审”的功能，简化了企业对 Azure AD 及其他 Microsoft Online Services 中的组和应用程序访问权限的管理方式。

本文介绍指定的审阅者如何对组成员或有权访问应用程序的用户执行访问评审。

## <a name="open-the-access-review"></a>打开访问评审

执行访问评审的第一步是找到并打开访问评审。

1. 找到要求你执行访问评审的 Microsoft 电子邮件。 以下示例电子邮件要求评审对某个组的访问权限。

    ![要求评审对某个组的访问权限的 Microsoft 示例电子邮件](./media/perform-access-review/access-review-email.png)

1. 单击“开始评审”链接打开访问评审。

如果未收到该电子邮件，可遵循以下步骤找到待处理的访问评审。

1. 登录到“我的应用”门户 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。

    ![“我的应用”门户，其中列出了你有权访问的应用](./media/perform-access-review/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 单击“访问评审”磁贴查看待处理的访问评审列表。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![应用和组的待处理访问评审列表](./media/perform-access-review/access-reviews-list.png)

1. 单击要执行的访问评审对应的“开始评审”链接。

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审后，你将看到需要评审的用户的名称。

如果请求是评审你自己的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审自己对组或应用程序的访问权限](review-your-access.md)。

![打开访问评审，其中会列出需要评审的用户](./media/perform-access-review/perform-access-review.png)

可通过两种方式批准或拒绝访问权限：

- 可以批准或拒绝一个或多个用户的访问权限，或者
- 可以接受系统建议，这是最简单最快捷的方式。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>批准或拒绝一个或多个用户的访问权限

1. 评审用户列表，以确定是要批准还是拒绝其持续访问权限。

1. 若要批准或拒绝单个用户的访问权限，请单击相应的行打开一个窗口，以指定要执行的操作。 若要批准或拒绝多个用户的访问权限，请勾选这些用户，然后单击“评审 X 个用户”按钮打开一个窗口，以指定要执行的操作。

1. 单击“批准”或“拒绝”。 如果不确定，可以单击“不知道”。 这样，该用户会保留其访问权限，但所做的选择会反映在审核日志中。

    ![包括“批准”、“拒绝”和“不知道”选项的操作窗口](./media/perform-access-review/approve-deny.png)

1. 如有必要，请在“原因”框中输入原因。

    访问评审的管理员可能要求你提供批准持续访问权限或组成员资格的原因。

1. 指定要执行的操作后，单击“保存”。

    若要更改响应，请选择相应的行并更新响应。 例如，可以批准以前已拒绝的用户，或者拒绝以前已批准的用户。 在访问评审结束之前，随时可以更改响应。

    如果有多个评审者，将记录最后提交的响应。 假设有一个管理员指定两个审阅者（Alice 和 Bob）的示例。 Alice 首先打开访问评审并批准了访问权限。 在评审结束之前，Bob 打开了访问评审并拒绝了访问权限。 那么，将会记录最后一个“拒绝”响应。

    > [!NOTE]
    > 如果拒绝了某个用户的访问权限，不会立即删除该用户。 该用户将在评审结束时或者在管理员停止评审时删除。

### <a name="approve-or-deny-access-based-on-recommendations"></a>根据建议批准或拒绝访问权限

为了让你更轻松、更快捷地评审访问权限，我们还会提供建议，单击一下鼠标就能接受这些建议。 建议是根据用户的登录活动生成的。

1. 在页面底部的蓝色栏中，单击“接受建议”。

    ![打开访问评审列表，其中会显示“接受建议”按钮](./media/perform-access-review/accept-recommendations.png)

    你将看到建议操作的摘要。

    ![显示建议操作摘要的窗口](./media/perform-access-review/accept-recommendations-summary.png)

1. 单击“确定”接受建议。

## <a name="next-steps"></a>后续步骤

- [完成组或应用程序的访问评审](complete-access-review.md)
