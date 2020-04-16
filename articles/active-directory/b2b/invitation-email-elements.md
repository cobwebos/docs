---
title: B2B 邀请电子邮件的元素 - Azure 活动目录 |微软文档
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407224"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 协作邀请电子邮件的元素 - Azure Active Directory

邀请电子邮件是将合作伙伴登记为 Azure AD 中的 B2B 协作用户的关键组件。 虽然[不需要您发送电子邮件来邀请使用 B2B 协作的用户](add-user-without-invite.md)，但这样做为用户提供了决定是否接受您的邀请所需的所有信息。 它还为他们提供了一个链接，当他们需要返回到您的资源时，他们将来可以随时参考。

![显示企业间邀请电子邮件的屏幕截图](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 此新电子邮件模板仍在向所有租户推出，因此某些租户仍在使用较旧的设计。 到 2020 年 5 月底，来自所有租户的邀请都将使用此模板。

## <a name="explaining-the-email"></a>电子邮件说明

让我们看看电子邮件的几个元素，以便了解如何充分利用其功能。

### <a name="subject"></a>主题

电子邮件的主题遵循以下模式：

&lt;用户名&gt;邀请您访问其组织内的应用程序。

### <a name="from-address"></a>发件人地址

对“发件人地址”使用类似于 LinkedIn 的模式。 此模式应明确说明，虽然电子邮件来自invites@microsoft.com，但邀请来自另一个组织。 格式是：微软 <invites@microsoft.com>邀请或代表租户名称&lt;&gt; <invites@microsoft.com>的微软邀请。 

### <a name="reply-to"></a>回复

回复电子邮件设置为邀请方的电子邮件（如果适用），以便在回复该电子邮件后可将电子邮件发回给邀请方。

### <a name="phishing-warning"></a>网络钓鱼警告

电子邮件首先向用户发出有关网络钓鱼的简短警告，提醒用户他们只接受他们期望的邀请。 最好能确保您邀请的合作伙伴不会因为您的邀请而感到惊讶，提前向他们提及邀请。

![电子邮件中网络钓鱼警告的图像](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>邀请方的信息

电子邮件包括有关邀请者及其发送邀请的组织的信息。 这包括发件人的姓名和电子邮件地址，以及与组织关联的名称和主要域。 所有这些信息都应帮助被邀请者就接受邀请做出明智的决定。

![邀请者信息在电子邮件中的图像](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>邀请消息

如果邀请者在[邀请来宾用户访问目录、组或应用](add-users-administrator.md)或使用[邀请 API](customize-invitation-api.md)时，将邮件作为邀请的一部分，则该邮件将突出显示到电子邮件的主部分。 还包括被邀请者的姓名和个人资料图像（如果他们设置了一个）。 邮件本身是一个文本区域，因此出于安全原因，它不处理 HTML 标记。

![电子邮件中邀请邮件的图像](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>接受按钮和重定向 URL

电子邮件的下一部分包含有关被邀请者接受邀请后将采取哪些内容的信息，以及执行此操作的按钮。  将来，被邀请者始终可以使用此链接直接返回到您的资源。

![接受按钮的图像和电子邮件中的重定向 URL](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>页脚部分

页脚包含有关要发送的邀请的详细信息。 邀请者始终可以选择阻止将来的邀请。 如果组织[设置了隐私声明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)，则指向该语句的链接将在此处显示。  否则，注释指示组织未设置隐私声明。

![电子邮件中页脚部分的图像](media/invitation-email-elements/footer-section.png)
 
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
