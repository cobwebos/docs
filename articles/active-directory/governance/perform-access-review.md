---
title: 评审访问权限给组或访问评审的 Azure Active Directory 中的应用程序 |Microsoft Docs
description: 了解如何查看组成员的访问或应用程序访问 Azure Active Directory 访问评审中。
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42fe972b79ecd9bcee65d0664c5d13da02d2238
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470604"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>查看对组的访问或 Azure AD 中的应用程序访问评审

Azure Active Directory (Azure AD) 简化了企业在 Azure AD 中管理访问组和应用程序和名为 Azure AD 访问的功能与其他 Microsoft Online Services 评审的方式。

本文介绍如何指定审阅者成员的组或用户有权访问应用程序的执行访问评审。

## <a name="open-the-access-review"></a>打开访问评审

若要执行访问评审的第一步是查找和打开访问评审。

1. 查找从会要求你评审访问权限的 Microsoft 的电子邮件。 下面是示例电子邮件以查看组的访问权限。

    ![评审访问权限电子邮件](./media/perform-access-review/access-review-email.png)

1. 单击**启动评审**链接以打开访问评审。

如果没有电子邮件，可以查找未完成的访问评审通过执行以下步骤。

1. 登录到 MyApps 门户[ https://myapps.microsoft.com ](https://myapps.microsoft.com)。

    ![MyApps 门户](./media/perform-access-review/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 单击**访问评审**磁贴以查看未完成的访问评审的列表。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![访问评审列表](./media/perform-access-review/access-reviews-list.png)

1. 单击**开始评审**你想要执行访问评审的链接。

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审之后, 您将看到需要评审的用户的名称。

如果请求是要评审你自己的访问权限，页上将看起来不同。 有关详细信息，请参阅[评审自己的访问权限，对组或应用程序](review-your-access.md)。

![执行访问评审](./media/perform-access-review/perform-access-review.png)

有两种方法可以批准或拒绝访问：

- 您可以批准或拒绝每个请求，或
- 你可以接受的系统建议，这是最简单且最快速的方法。

### <a name="approve-or-deny-access-for-each-request"></a>批准或拒绝访问的每个请求

1. 查看要决定是否批准或拒绝其持续访问权限的用户列表。

1. 若要批准或拒绝每个请求，请单击要打开一个窗口来指定要执行的操作的行。

1. 单击**批准**或**拒绝**。 如果您不确定，则可以单击**不知道**。 执行此操作将导致用户维护其访问权限，但所选内容将反映在审核日志。

    ![执行访问评审](./media/perform-access-review/approve-deny.png)

1. 如有必要，输入在原因**原因**框。

    访问评审的管理员可能要求你提供批准持续访问权限或组成员身份的原因。

1. 指定要执行的操作后，单击**保存**。

    如果你想要更改您的响应，选择的行和更新的响应。 例如，可以批准之前拒绝的用户，或拒绝之前批准的用户。 可以在任何时间访问评审已结束之前更改您的响应。

    如果有多个审阅者，记录上一次提交的响应。 请考虑在管理员指定两个审阅者 – Alice 和 Bob 的示例。 Alice 先打开访问评审和批准访问权限。 审阅结束之前，Bob 将打开访问评审，并拒绝访问。 最后一个拒绝响应是什么记录。

    > [!NOTE]
    > 如果用户被拒绝访问，它们不会立即删除。 评审已结束或管理员停止评审时，会删除它们。

### <a name="approve-or-deny-access-based-on-recommendations"></a>批准或拒绝访问根据建议

若要为你更轻松、 更快地使访问评审，我们还提供可接受的建议单击一次。 基于用户的登录活动生成建议。

1. 在页面底部的蓝色条形，单击**接受建议**。

    ![接受建议](./media/perform-access-review/accept-recommendations.png)

    请参阅建议的操作的摘要。

    ![接受建议摘要](./media/perform-access-review/accept-recommendations-summary.png)

1. 单击**确定**以接受这些建议。

## <a name="next-steps"></a>后续步骤

- [完成访问评审的组或应用程序](complete-access-review.md)
