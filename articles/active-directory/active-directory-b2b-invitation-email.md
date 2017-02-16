---
title: "Azure Active Diretory B2B 协作邀请电子邮件的元素 | Microsoft 文档"
description: "Azure Active Directory B2B 协作邀请电子邮件模板"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/01/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8fdd2993e6b3f0345948f35753ebb7a20ed174e3
ms.openlocfilehash: 5a31469014b7b0c7ea8a6d6a207646694c6c0b50


---


# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>B2B 协作邀请电子邮件的元素

邀请电子邮件是将合作伙伴登记为 Azure AD 中的 B2B 协作用户的关键组件。 它的主要目标是增强收件人的信任，在电子邮件中添加合法性与社交证据，确保收件人乐于选择“开始”按钮来接受邀请。 它也是减少共享冲突的一个关键组件。 当然，我们还希望电子邮件赏心悦目！

## <a name="explaining-the-email"></a>电子邮件说明
让我们看看电子邮件的几个元素，以便知道如何充分利用这些功能。

### <a name="subject"></a>使用者
电子邮件的主题遵循以下模式：诚邀你加入 &lt;tenantname&gt; 组织

### <a name="from-address"></a>发件人地址
对“发件人地址”使用类似于 LinkedIn 的模式。 此元素的目标是明确指出邀请方是谁，来自哪家公司，同时澄清该电子邮件发自某个 Microsoft 电子邮件地址。 格式为：&lt;租户名称&gt; 的 &lt;邀请方显示名称&gt;（通过 Microsoft）<invites@microsoft.com&gt;

### <a name="reply-to"></a>回复
回复电子邮件设置为邀请方的电子邮件（如果适用），以便在回复该电子邮件后可将电子邮件发回给邀请方。

### <a name="branding"></a>品牌
从租户发送的邀请电子邮件使用你为租户设置的公司品牌。 如果想要利用此功能，请参考下面提供的配置详细信息。 横幅徽标显示在电子邮件中。 请遵循此处的图像大小和质量说明以获得最佳效果。 此外，公司名称也会显示在行动号召中。

### <a name="call-to-action"></a>行动号召
行动号召由两个部分组成：解释收件人为什么会收到该邮件，请求收件人采取哪种行动。
- 可以使用以下模式填写“为什么”部分：诚邀你访问 &lt;tenantname&gt; 组织中的应用程序

- “请求你采取哪种行动”部分可通过提供“开始”按钮来指明。 如果无需发出邀请即会添加收件人，则不显示此按钮。

### <a name="inviters-information"></a>邀请方的信息
邀请方的显示名称将包含在电子邮件中。 此外，如果为 Azure AD 帐户设置了个人资料图片，邀请电子邮件也会包含该图片。 这两项信息旨在增强收件人对电子邮件的信任。
如果邀请方尚未设置其个人资料图片，Azure AD 将创建一个包含邀请者姓名首字母缩写的图标来取代该图片，如下所示：

  ![显示邀请方的姓名首字母缩写](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>正文
正文包含邀请方在用户界面中键入的或通过邀请 API 传递的消息。 这只是一个简单的文本框，出于安全原因不会处理 html 标记。

### <a name="footer-section"></a>页脚部分

页脚包含 Microsoft 公司品牌，告知收件人该电子邮件是否从未受监视的别名中发出。 特殊情况：

- 邀请方在邀请租户中没有电子邮件地址

  ![邀请方的图片在邀请租户中没有电子邮件地址](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- 收件人不需要兑换邀请

  ![当收件人不需要兑换邀请时](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


