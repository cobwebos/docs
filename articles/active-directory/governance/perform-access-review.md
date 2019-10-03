---
title: 查看对访问评审中的组或应用程序的访问-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure Active Directory 访问评审中查看组成员或应用程序访问权限的访问权限。
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
ms.openlocfilehash: 3be5d6eb443d90d3413510576a9609fe43df7caa
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499630"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中查看对组或应用程序的访问权限

Azure Active Directory (Azure AD) 通过一项名为 Azure AD 访问评审的功能, 简化了企业如何管理对 Azure AD 和其他 Microsoft Online Services 中的组和应用程序的访问。

本文介绍指定的审阅者如何对组成员或有权访问应用程序的用户执行访问评审。

## <a name="prerequisites"></a>先决条件

- Azure AD Premium P2

有关详细信息, 请参阅[哪些用户必须拥有许可证？](access-reviews-overview.md#which-users-must-have-licenses)。

## <a name="open-the-access-review"></a>打开访问评审

执行访问评审的第一步是查找并打开访问评审。

1. 查找 Microsoft 提供的一封电子邮件, 要求您查看访问权限。 下面是一个示例电子邮件, 用于查看对组的访问权限。

    ![Microsoft 的电子邮件示例, 用于查看对组的访问权限](./media/perform-access-review/access-review-email.png)

1. 单击 "**启动审阅**" 链接以打开访问评审。

如果没有电子邮件, 可以通过执行以下步骤来查找待定的访问评审。

1. 登录到 MyApps 门户[https://myapps.microsoft.com](https://myapps.microsoft.com)。

    ![MyApps 门户列出你有权访问的应用](./media/perform-access-review/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 单击 "**访问评审**" 磁贴以查看待定访问评审的列表。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![应用和组的挂起访问审阅列表](./media/perform-access-review/access-reviews-list.png)

1. 单击要执行的访问评审的 "**开始审阅**" 链接。

## <a name="perform-the-access-review"></a>执行访问评审

在您打开访问评审后, 您将看到需要评审的用户的名称。

如果请求要查看自己的访问权限, 则页面看起来会有所不同。 有关详细信息, 请参阅[查看对组或应用程序的访问权限](review-your-access.md)。

![打开访问评审列出了需要查看的用户](./media/perform-access-review/perform-access-review.png)

可以通过两种方式批准或拒绝访问:

- 你可以批准或拒绝一个或多个用户的访问权限, 或者
- 您可以接受系统建议, 这是最简单、最快捷的方法。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>批准或拒绝一个或多个用户的访问权限

1. 查看用户列表以决定是批准还是拒绝其持续访问。

1. 若要批准或拒绝单个用户的访问权限, 请单击该行以打开一个窗口, 以指定要执行的操作。 若要批准或拒绝多个用户的访问权限, 请在用户旁边添加复选标记, 然后单击 "**查看 X 用户**" 按钮打开窗口以指定要执行的操作。

1. 单击 "**批准**" 或 "**拒绝**"。 如果不确定, 可以单击 "**不知道**"。 这样做将导致用户保持其访问权限, 但会在审核日志中反映所做的选择。

    ![包括批准、拒绝和不知道选项的操作窗口](./media/perform-access-review/approve-deny.png)

1. 如有必要, 请在 "**原因**" 框中输入原因。

    访问评审的管理员可能要求你提供批准继续访问权限或组成员身份的原因。

1. 指定要执行的操作后, 单击 "**保存**"。

    如果要更改您的响应, 请选择该行并更新响应。 例如, 你可以批准之前拒绝的用户, 或拒绝之前批准的用户。 你可以随时更改你的响应, 直到访问评审结束。

    如果有多个审阅者, 则记录最后提交的响应。 假设有一个管理员指定两个审阅者 (Alice 和 Bob) 的示例。 Alice 首先打开访问评审并批准访问。 在评审结束之前, 小明会打开访问评审并拒绝访问。 最后一个 "拒绝" 响应是记录的内容。

    > [!NOTE]
    > 如果用户被拒绝访问, 则不会立即删除。 当评审结束或管理员停止评审时, 它们会被删除。

### <a name="approve-or-deny-access-based-on-recommendations"></a>根据建议批准或拒绝访问

为了更轻松、更快速地查看访问权限, 我们还提供了只需单击即可接受的建议。 建议基于用户的登录活动生成。

1. 在页面底部的蓝色栏中, 单击 "**接受建议**"。

    ![打开 "访问评审" 列表, 其中显示 "接受建议" 按钮](./media/perform-access-review/accept-recommendations.png)

    你将看到建议的操作的摘要。

    ![显示建议操作摘要的窗口](./media/perform-access-review/accept-recommendations-summary.png)

1. 单击 **"确定"** 以接受建议。

## <a name="next-steps"></a>后续步骤

- [完成对组或应用程序的访问评审](complete-access-review.md)
