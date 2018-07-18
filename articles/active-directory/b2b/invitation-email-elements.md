---
title: B2B 协作邀请电子邮件的元素 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 协作邀请电子邮件模板
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: e8285779154914bd09513c057d8e5ae0b6388831
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259483"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 协作邀请电子邮件的元素 - Azure Active Directory

邀请电子邮件是将合作伙伴登记为 Azure AD 中的 B2B 协作用户的关键组件。 可使用它们增强收件人的信任。 可在电子邮件中添加合法性与社交证据，确保收件人愿意选择“开始”按钮来接受邀请。 这种信任是减少共享冲突的关键。 同时还要让电子邮件赏心悦目！

![Azure AD B2B 邀请电子邮件](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>电子邮件说明
让我们看看电子邮件的几个元素，以便了解如何充分利用其功能。

### <a name="subject"></a>主题
电子邮件的主题遵循以下模式：诚邀你加入 &lt;tenantname&gt; 组织

### <a name="from-address"></a>发件人地址
对“发件人地址”使用类似于 LinkedIn 的模式。  应明确邀请方是谁，来自哪家公司，同时澄清该电子邮件发自某个 Microsoft 电子邮件地址。 格式为：&lt;tenantname&gt; 的&lt;邀请者显示名称&gt;（通过 Microsoft）<invites@microsoft.com>

### <a name="reply-to"></a>回复
回复电子邮件设置为邀请方的电子邮件（如果适用），以便在回复该电子邮件后可将电子邮件发回给邀请方。

### <a name="branding"></a>品牌
从租户发送的邀请电子邮件使用为租户设置的公司品牌。 如果要利用此功能，请参考[此处](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)提供的配置详细信息。 横幅徽标显示在电子邮件中。 请遵循[此处](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)的图像大小和质量说明以获得最佳效果。 此外，公司名称也会显示在行动号召中。

### <a name="call-to-action"></a>行动号召
行动号召由两个部分组成：解释收件人为什么会收到该邮件，请求收件人采取哪种行动。
- 可以使用以下模式填写“为什么”部分：诚邀你访问 &lt;tenantname&gt; 组织中的应用程序

- “请求你采取哪种行动”部分可通过提供“开始”按钮来指明。 如果无需发出邀请即会添加收件人，则不显示此按钮。

### <a name="inviters-information"></a>邀请方的信息
邀请方的显示名称包含在电子邮件中。 此外，如果为 Azure AD 帐户设置了个人资料图片，邀请电子邮件也会包含该图片。 这两项信息旨在增强收件人对电子邮件的信任。

如果尚未设置个人资料图片，将使用含有邀请者姓名首字母缩写的图标来取代该图片，如下所示：

  ![显示邀请方的姓名首字母缩写](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Body
正文包含邀请方撰写的或通过邀请 API 传递的消息。 这是文本区域，因此出于安全原因不会处理 HTML 标记。

### <a name="footer-section"></a>页脚部分
页脚包含 Microsoft 公司品牌徽标，告知收件人该电子邮件是否从未受监视的别名中发出。 特殊情况：

- 邀请方在邀请租户中没有电子邮件地址

  ![邀请方的图片在邀请租户中没有电子邮件地址](media/invitation-email-elements/inviter-no-email.png)


- 收件人不需要兑换邀请

  ![当收件人不需要兑换邀请时](media/invitation-email-elements/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作](what-is-b2b.md)
- [Azure Active Directory 管理员如何添加 B2B 协作用户？](add-users-administrator.md)
- [信息工作者如何添加 B2B 协作用户？](add-users-information-worker.md)
- [B2B 协作邀请兑换](redemption-experience.md)
- [在没有邀请的情况下添加 B2B 协作用户](add-user-without-invite.md)
