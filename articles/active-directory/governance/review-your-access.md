---
title: 在访问评审中查看对组 & 应用的访问权限-Azure AD
description: 了解如何在 Azure Active Directory 访问评审中查看自己对组或应用程序的访问权限。
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
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422405"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>查看 Azure AD 访问评审中的组或应用程序的访问权限

Azure Active Directory （Azure AD）简化了企业如何使用一项名为 Azure AD 访问评审的功能来管理对 Azure AD 和其他 Microsoft Online Services 中的组或应用程序的访问。

本文介绍如何查看自己对组或应用程序的访问权限。

## <a name="open-the-access-review"></a>打开访问评审

执行访问评审的第一步是查找并打开访问评审。

1. 查找 Microsoft 提供的一封电子邮件，要求您查看访问权限。 下面是一个示例电子邮件，用于查看对组的访问权限。

    ![Microsoft 的电子邮件示例，用于查看对组的访问权限](./media/review-your-access/access-review-email.png)

1. 单击 "**查看访问权限**" 链接以打开访问评审。

如果没有电子邮件，可以通过执行以下步骤来查找待定的访问评审。

1. 在[https://myapps.microsoft.com](https://myapps.microsoft.com)登录到 MyApps 门户。

    ![MyApps 门户列出你有权访问的应用](./media/review-your-access/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 在页面右侧，单击 "**访问评审**" 磁贴以查看待定访问评审的列表。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![应用和组的挂起访问审阅列表](./media/review-your-access/access-reviews-list.png)

1. 单击要执行的访问评审的 "**开始审阅**" 链接。

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审后，可以看到您的访问权限。

1. 查看访问权限，并决定是否仍需要访问权限。

    如果请求要查看其他人的访问权限，则页面看起来会有所不同。 有关详细信息，请参阅[查看对组或应用程序的访问权限](perform-access-review.md)。

    ![打开访问评审询问你是否仍需要访问组](./media/review-your-access/perform-access-review.png)

1. 单击 **"是"** 以保留访问权限，或者单击 "**否**" 以删除访问权限。

1. 如果单击 **"是**"，则可能需要在 "**原因**" 框中指定理由。

    ![完成访问评审询问你是否仍需要访问组](./media/review-your-access/perform-access-review-submit.png)

1. 单击“提交”。

    你的选择已提交并返回到 MyApps 门户。

    如果要更改您的响应，请重新打开 "访问评审" 页并更新您的响应。 你可以随时更改你的响应，直到访问评审结束。

    > [!NOTE]
    > 如果你指出不再需要访问，则不会立即删除。 当评审结束或管理员停止评审时，将删除。

## <a name="next-steps"></a>后续步骤

- [完成对组或应用程序的访问评审](complete-access-review.md)
