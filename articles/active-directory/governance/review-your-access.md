---
title: 对组或应用程序在访问评审的 Azure Active Directory 中为自己评审访问权限 |Microsoft Docs
description: 了解如何查看自己的访问权限给组或 Azure Active Directory 访问评审中的应用程序。
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
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22f12255bf14ca424a8a79107e7ca8e403552b62
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471742"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>评审自己的访问权限，向组或 Azure AD 中的应用程序访问评审

Azure Active Directory (Azure AD) 简化了企业在 Azure AD 中管理对组或应用程序的访问和名为 Azure AD 访问的功能与其他 Microsoft Online Services 评审的方式。

本文介绍如何查看自己的组或应用程序的访问权限。

## <a name="prerequisites"></a>必备组件

- Azure AD Premium P2

有关详细信息，请参阅[哪些用户必须具有许可证？](access-reviews-overview.md#which-users-must-have-licenses)。

## <a name="open-the-access-review"></a>打开访问评审

若要执行访问评审的第一步是查找和打开访问评审。

1. 查找从会要求你评审访问权限的 Microsoft 的电子邮件。 下面是示例电子邮件来评审访问权限给组。

    ![Microsoft 为评审访问权限给组的示例电子邮件](./media/review-your-access/access-review-email.png)

1. 单击**评审访问权限**链接以打开访问评审。

如果没有电子邮件，可以查找未完成的访问评审通过执行以下步骤。

1. 登录到 MyApps 门户[ https://myapps.microsoft.com ](https://myapps.microsoft.com)。

    ![列出应用程序有权 MyApps 门户](./media/review-your-access/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 在页面的右侧，单击**访问评审**磁贴以查看未完成的访问评审的列表。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![挂起的访问权限审查应用和组的列表](./media/review-your-access/access-reviews-list.png)

1. 单击**开始评审**你想要执行访问评审的链接。

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审之后, 您可以看到你的访问权限。

1. 查看你的访问权限并确定是否仍需要访问权限。

    如果请求是要为其他人评审访问权限，页上将看起来不同。 有关详细信息，请参阅[评审为组或应用程序的访问权限](perform-access-review.md)。

    ![打开访问评审询问是否仍需要访问组](./media/review-your-access/perform-access-review.png)

1. 单击**是**以保留你的访问权限，或单击**否**删除你的访问权限。

1. 如果单击**是**，可能需要指定在理由**原因**框。

    ![完成访问评审询问是否仍需要访问组](./media/review-your-access/perform-access-review-submit.png)

1. 单击“提交”  。

    提交你的选择并返回到 MyApps 门户。

    如果你想要更改您的响应，重新打开访问评审页并更新您的响应。 可以在任何时间访问评审已结束之前更改您的响应。

    > [!NOTE]
    > 如果指示不再需要访问，则不会立即删除。 删除或管理员停止评审时评审已结束。

## <a name="next-steps"></a>后续步骤

- [完成访问评审的组或应用程序](complete-access-review.md)
