---
title: 在 Azure AD 授权管理中查看访问包
description: 了解如何在 Azure Active Directory 访问评审（预览版）中查看授权管理访问包的用户访问权限。
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
ms.openlocfilehash: 89a346f48856ea33ae5e27e3537f53dfa1ca3ae3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608808"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中查看访问包

Azure AD 的权利管理简化了企业如何管理对组、应用程序和 SharePoint 站点的访问。 本文介绍用户如何对分配的访问包进行自我审查。

## <a name="open-the-access-review"></a>打开访问评审

若要进行访问评审，必须先打开访问评审。 使用以下过程查找并打开访问评审：

1. 你可能会收到 Microsoft 发送的一封电子邮件，要求你评审访问权限。 找到电子邮件以打开访问评审。 下面是请求查看访问权限的电子邮件示例： 
    
    ![访问评审自助审阅者电子邮件](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. 单击 "**查看访问权限**" 链接。

1. 如果你没有收到电子邮件，你还可以直接进入 https://myaccess.microsoft.com 查找待定的访问评审。

1. 单击左侧导航栏上的 "**访问评审**" 可查看分配给你的待定访问评审的列表。


1.  单击要开始的查看。

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审后，可以看到您的访问权限。 使用以下过程进行访问评审：

1.  确定是否仍需要访问访问包。 例如，你正在处理的项目未完成，因此你仍需要访问以继续处理该项目。

1.  单击“是”保留访问权限，或者单击“否”删除访问权限。
    >[!NOTE]
    >如果你指出不再需要访问，则不会立即从访问包中删除。 当评审结束或管理员停止评审时，将从访问包中删除。

1.  如果单击了 **"是**"，则可能需要在 "**原因**" 框中包含对齐语句。

1.  单击“提交”。

如果你改变主意并决定在评审结束之前更改你的响应，你可以返回到评审。

## <a name="next-steps"></a>后续步骤

- [查看访问包访问权限](entitlement-management-access-reviews-review-access.md) 
