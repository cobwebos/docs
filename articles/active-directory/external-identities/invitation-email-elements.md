---
title: B2B 邀请电子邮件的元素 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 协作邀请电子邮件模板
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03391ba5a82c128197c86ea6ed84389552fadb9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439848"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 协作邀请电子邮件的元素 - Azure Active Directory

邀请电子邮件是将合作伙伴登记为 Azure AD 中的 B2B 协作用户的关键组件。 虽然[不一定要使用 B2B 协作通过电子邮件来邀请某人](add-user-without-invite.md)，但这样做可为用户提供所需的全部信息，让他们决定是否接受你的邀请。 邀请电子邮件中还会包含链接，将来当用户需要返回到你的资源时，始终可以使用该链接。

![显示企业间邀请电子邮件的屏幕截图](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 我们正在将此新电子邮件模板推出到所有租户，因此某些租户仍在使用旧设计。 在 2020 年 5 月底，来自所有租户的邀请将使用此模板。

## <a name="explaining-the-email"></a>电子邮件说明

让我们看看电子邮件的几个元素，以便了解如何充分利用其功能。

### <a name="subject"></a>使用者

电子邮件的主题遵循此模式：

&lt;用户名&gt; 邀请你访问其组织内的应用程序。

### <a name="from-address"></a>发件人地址

对“发件人地址”使用类似于 LinkedIn 的模式。 此模式应该明确指出，尽管电子邮件来自 invites@microsoft.com，但邀请却是另一组织发出的。 格式为：Microsoft Invitations <invites@microsoft.com>，或 Microsoft 代表 &lt;租户名称&gt; <invites@microsoft.com> 发出的邀请。 

### <a name="reply-to"></a>回复

回复电子邮件设置为邀请方的电子邮件（如果适用），以便在回复该电子邮件后可将电子邮件发回给邀请方。

### <a name="phishing-warning"></a>网络钓鱼警告

电子邮件首先会向用户发出有关网络钓鱼的简短警告，提醒他们只应接受意料之中的邀请。 好的做法是提前通知你所邀请的合作伙伴，确保他们不会因你的邀请而感到吃惊。

![电子邮件中网络钓鱼警告的图像](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>邀请方的信息

电子邮件包含有关邀请方以及发送邀请的组织的信息。 这些信息包括发件人的姓名和电子邮件地址，以及与组织关联的名称和主域。 所有这些信息应有助于受邀方在是否接受邀请方面做出明智决策。

![电子邮件中邀请方信息的图像](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>邀请消息

如果邀请方在[邀请来宾用户访问目录、组或应用](add-users-administrator.md)时或者[使用邀请 API](customize-invitation-api.md) 时在其邀请中包含了一条消息，该消息会在电子邮件的主要部分突出显示。 另外，还会包括邀请方的姓名和个人资料图像（如果邀请方已进行此方面的设置）。 消息本身是一个文本区域，因此出于安全原因，它不会处理 HTML 标记。

![电子邮件中邀请消息的图像](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>接受按钮和重定向 URL

电子邮件的下一部分包含有关受邀方在接受邀请后将要定向到的位置的信息，以及用于接受邀请的按钮。  将来，受邀方始终可以使用此链接直接返回到你的资源。

![电子邮件中的接受按钮和重定向 URL 的图像](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>页脚部分

页脚包含有关所发送邀请的详细信息。 受邀方始终可以通过一个选项来阻止将来的邀请。 如果组织已[设置隐私声明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)，则此处会显示该声明的链接。  否则会有一条注释，指出该组织尚未设置隐私声明。

![电子邮件中页脚部分的图像](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>阻止组织 (取消订阅) 

在来自组织的邀请中，页脚包含用于 **阻止未来邀请**的选项。 来宾用户可以选择此链接来阻止来自组织的任何未来邀请。 此操作还会将组织添加到用户的取消订阅列表 [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) 。

### <a name="viewing-organizations-youve-blocked"></a>查看已阻止的组织

来宾用户可以按照以下步骤查看或导出已阻止的组织：

1. 转到 [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage)。
2. 输入电子邮件并按照电子邮件一次性密码身份验证的登录步骤进行操作。
3. 查看已阻止的组织，或使用复制和粘贴导出名称。
   > [!NOTE]
   > 如果希望允许组织再次邀请你，可以选择组织，然后选择 " **下一步**"。

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
