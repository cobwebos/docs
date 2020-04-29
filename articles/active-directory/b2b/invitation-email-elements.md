---
title: B2B 邀请电子邮件的元素-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 协作邀请电子邮件模板
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81407224"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 协作邀请电子邮件的元素 - Azure Active Directory

邀请电子邮件是将合作伙伴登记为 Azure AD 中的 B2B 协作用户的关键组件。 尽管[不需要你发送电子邮件来邀请使用 B2B 协作的人员](add-user-without-invite.md)，但这样做会为用户提供他们做出决定是否接受邀请所需的所有信息。 它还提供了一个链接，在未来需要返回到你的资源时，他们始终可以引用它。

![显示企业间邀请电子邮件的屏幕截图](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 这一新的电子邮件模板仍被推出给所有租户，因此某些租户仍在使用较旧的设计。 在5月2020日结束时，来自所有租户的邀请将使用此模板。

## <a name="explaining-the-email"></a>电子邮件说明

让我们看看电子邮件的几个元素，以便了解如何充分利用其功能。

### <a name="subject"></a>Subject

电子邮件的主题遵循此模式：

&lt;用户名&gt;邀请你访问其组织中的应用程序。

### <a name="from-address"></a>发件人地址

对“发件人地址”使用类似于 LinkedIn 的模式。 这种模式应该清楚地说，虽然电子邮件来自invites@microsoft.com于其他组织，但邀请也是如此。 格式 <invites@microsoft.com>为：代表&lt;tenantname&gt; <invites@microsoft.com>的 microsoft 邀请或 microsoft 邀请。 

### <a name="reply-to"></a>回复

回复电子邮件设置为邀请方的电子邮件（如果适用），以便在回复该电子邮件后可将电子邮件发回给邀请方。

### <a name="phishing-warning"></a>网络钓鱼警告

电子邮件首先会向用户发送有关仿冒网站的简短警告，通知他们应该只接受他们期待的邀请。 最好的做法是确保邀请的合作伙伴不会因你的邀请而感到吃惊，因为你可以提前提到。

![电子邮件中的网络钓鱼警告的图像](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>邀请方的信息

该电子邮件包含有关邀请者及其发送邀请的组织的信息。 这包括发送者的姓名和电子邮件地址，以及与组织关联的名称和主域。 所有这些信息都应有助于被邀请者做出有关接受邀请的明智决策。

![电子邮件中邀请者信息的图像](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>邀请消息

如果邀请者在邀请[来宾用户加入目录、组或应用](add-users-administrator.md)或[使用邀请 API](customize-invitation-api.md)时，在其邀请中包含一条消息，则该消息将突出显示在电子邮件的主要部分。 如果已设置，还包含邀请者的名称和配置文件映像。 消息本身是一个文本区域，因此出于安全原因，它不会处理 HTML 标记。

![电子邮件中邀请邮件的图像](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>接受按钮和重定向 URL

电子邮件的下一部分包含有关邀请在接受邀请后将在何处拍摄的信息，以及用于执行此操作的按钮。  将来，被邀请者始终可以使用此链接返回到你的资源。

![电子邮件中的 "接受" 按钮和重定向 URL 的图像](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>页脚部分

页脚包含有关所发送邀请的详细信息。 被邀请者始终有一个选项来阻止将来的邀请。 如果组织已[设置了隐私声明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)，则会在此处显示该语句的链接。  否则，说明表明组织尚未设置隐私声明。

![电子邮件中的页脚部分的图像](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>语言是如何确定的

邀请电子邮件中呈现给来宾用户的语言是由以下设置确定的。 这些设置是按优先级顺序列出的。 如果某个设置未配置，则将由列表中的下一设置来确定语言。

- [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) 对象的 **messageLanguage** 属性（如果使用了“创建邀请 API”）
-   在来宾的[用户对象](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)中指定的 **preferredLanguage** 属性
-   在来宾用户的主租户的属性中设置的**通知语言**（仅仅适用于 Azure AD 租户）
-   在资源租户的属性中设置的**通知语言**

如果这些设置全都没有配置，则语言默认为“英语(美国)”。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作](what-is-b2b.md)
- [Azure Active Directory 管理员如何添加 B2B 协作用户？](add-users-administrator.md)
- [信息工作者如何添加 B2B 协作用户？](add-users-information-worker.md)
- [B2B 协作邀请兑换](redemption-experience.md)
- [在没有邀请的情况下添加 B2B 协作用户](add-user-without-invite.md)
