---
title: 在访问评审中评审自己对组或应用程序的访问权限 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure Active Directory 访问评审中评审自己对组或应用程序的访问权限。
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
ms.openlocfilehash: df8b03d513e2d78083031fbc9a3f6dbc1b7d15d9
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958570"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中评审自己对组或应用程序的访问权限

Azure Active Directory (Azure AD) 借助称为“Azure AD 访问评审”的功能，简化了企业对 Azure AD 及其他 Microsoft Online Services 中的组或应用程序访问权限的管理方式。

本文介绍如何评审自己对组或应用程序的访问权限。

## <a name="open-the-access-review"></a>打开访问评审

执行访问评审的第一步是找到并打开访问评审。

1. 找到要求你执行访问评审的 Microsoft 电子邮件。 以下示例电子邮件要求评审你对某个组的访问权限。

    ![要求评审你对某个组的访问权限的 Microsoft 示例电子邮件](./media/review-your-access/access-review-email.png)

1. 单击“评审访问权限”链接，打开访问评审。

如果未收到该电子邮件，可遵循以下步骤找到待处理的访问评审。

1. 登录到“我的应用”门户 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。

    ![“我的应用”门户，其中列出了你有权访问的应用](./media/review-your-access/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 在页面右侧，单击“访问评审”磁贴来查看待处理的访问评审。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![应用和组的待处理访问评审列表](./media/review-your-access/access-reviews-list.png)

1. 单击你要执行的访问评审对应的“开始评审”链接。

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审以后，即可看到自己的访问权限。

1. 评审你的访问权限，决定是否仍需要访问权限。

    如果请求是评审他人的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审对组或应用程序的访问权限](perform-access-review.md)。

    ![打开访问评审，系统会询问你是否仍需访问某个组](./media/review-your-access/perform-access-review.png)

1. 单击“是”保留访问权限，或者单击“否”删除访问权限。

1. 如果单击“是”，你可能需要在“原因”框中指出原因。

    ![完成访问评审，系统会询问你是否仍需访问某个组](./media/review-your-access/perform-access-review-submit.png)

1. 单击“提交”。

    此时会提交你所做的选择，并会将你返回到“我的应用”门户。

    若要更改响应，请重新打开访问评审页面并更新响应。 在访问评审结束之前，随时可以更改响应。

    > [!NOTE]
    > 如果你指出不再需要访问权限，系统不会立即将你删除。 系统会在评审结束时或者在管理员停止评审时将你删除。

## <a name="next-steps"></a>后续步骤

- [完成组或应用程序的访问评审](complete-access-review.md)
