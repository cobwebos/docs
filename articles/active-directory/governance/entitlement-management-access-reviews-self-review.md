---
title: Azure AD 授权管理中访问包的自我审查
description: 了解如何在 Azure 活动目录访问审核（预览）中查看用户对授权管理访问包的访问。
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
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967769"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 授权管理中访问包的自我审查

Azure AD 授权管理简化了企业管理对组、应用程序和 SharePoint 站点的访问的方式。 本文介绍用户如何自行查看其分配的访问包。

## <a name="open-the-access-review"></a>打开访问评审

要执行访问审核，必须首先打开访问审核。 使用以下过程查找和打开访问审核：

1. 您可能会收到来自 Microsoft 的电子邮件，要求您查看访问权限。 找到电子邮件以打开访问审核。 下面是请求查看访问权限的电子邮件示例： 
    
    ![访问审阅自我审阅电子邮件](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. 单击 **"查看访问"** 链接。

1. 如果您没有收到电子邮件，您也可以直接https://myaccess.microsoft.com查找挂起的访问审核。  （对于美国政府，`https://myaccess.microsoft.us`请使用。

1. 单击左侧导航栏上的 **"访问审阅**"以查看分配给您挂起的访问审核的列表。


1.  单击要开始的评论。

## <a name="perform-the-access-review"></a>执行访问评审

打开访问审核后，即可看到访问权限。 使用以下过程执行访问审核：

1.  确定是否仍需要访问访问包。 例如，您正在处理的项目尚未完成，因此您仍然需要访问权限才能继续处理该项目。

1.  单击“是”保留访问权限，**** 或者单击“否”删除访问权限****。
    >[!NOTE]
    >如果您说不再需要访问权限，则不会立即从访问包中删除。 审核结束时或管理员停止审核时，您将从访问包中删除。

1.  如果单击"**是**"，则可能需要在 **"原因"** 框中包含一个对齐语句。

1.  单击 **“提交”**。

如果您改变主意并决定在审核结束前更改您的回复，您可以返回审核。

## <a name="next-steps"></a>后续步骤

- [查看访问包的访问权限](entitlement-management-access-reviews-review-access.md) 
