---
title: B2B 协作邀请兑换 - Azure Active Directory | Microsoft Docs
description: 介绍最终用户的 Azure AD B2B 协作邀请兑换体验，包括对隐私条款的同意。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2e354bc4ae06e86afd5d14e87ef796fce942521b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074775"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 协作邀请兑换

若要通过 Azure Active Directory (Azure AD) B2B 协作与来自合作伙伴组织的用户协作，你可邀请来宾用户访问共享应用。 通过用户界面将来宾用户添加到目录后，或者通过 PowerShell 邀请用户后，来宾用户必须完成首次同意流程并同意[隐私条款](#privacy-policy-agreement)。 此流程以下列任一方式发生：

- 来宾邀请者发送指向共享应用的直接链接。 被邀请者单击链接登录，接受隐私条款，然后无缝访问共享资源。 （来宾用户仍会收到带有兑换 URL 的邀请电子邮件，但除部分特殊情况外，不再需要使用邀请电子邮件。）  
- 来宾用户收到邀请电子邮件并单击兑换 URL。 作为首次登录体验的一部分，系统会提示他们接受隐私条款。

## <a name="redemption-through-a-direct-link"></a>通过直接链接兑换

来宾邀请者可通过发送指向共享应用的直接链接来邀请来宾用户。 对于来宾用户，兑换体验就像登录与其共享的应用一样简单。 他们可单击指向该应用的链接，查看并接受隐私条款，然后无缝访问该应用。 在大多数情况下，来宾用户不再需要单击邀请电子邮件中的兑换 URL。

如果通过用户界面邀请来宾用户，或选择将发送邀请电子邮件作为 PowerShell 邀请体验的一部分，则受邀用户仍会收到邀请电子邮件。 此电子邮件可用于以下特殊情况：

- 用户没有 Azure AD 帐户或 Microsoft 帐户 (MSA)。 在这种情况下，用户必须先创建 MSA，然后才能单击该链接，或者用户可使用邀请电子邮件中的兑换 URL。 兑换流程会自动提示用户创建 MSA。
- 有时，由于与联系人对象（例如，Outlook 联系人对象）存在冲突，受邀用户对象可能会没有电子邮件地址。 在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。
- 用户可使用受邀电子邮件地址的别名登录。 （别名指与电子邮件帐户关联的其他电子邮件地址。）在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。

如果这些特殊情况对贵组织而言很重要，我们建议使用仍会发送邀请电子邮件的方法来邀请用户。 此外，如果用户不属于这些特殊情况，他们仍可单击邀请电子邮件中的 URL 来获取访问权限。

## <a name="redemption-through-the-invitation-email"></a>通过邀请电子邮件兑换

如果通过发送邀请电子邮件的方法受到邀请，用户还可通过邀请电子邮件兑换邀请。 受邀用户可单击电子邮件中的兑换 URL，然后查看并接受隐私条款。 下文更详细地介绍了该流程：

1.  受到邀请后，被邀请者将通过发自 **Microsoft Invitations** 的电子邮件收到邀请。
2.  被邀请者选择电子邮件中的“开始使用”。
3.  如果被邀请者没有 Azure AD 帐户或 MSA，则系统会提示创建 MSA。
4.  被邀请者会重定向到“查看权限”屏幕，他们可在该屏幕查看邀请组织的隐私声明并接受相关条款。

## <a name="privacy-policy-agreement"></a>隐私策略协议

任何来宾用户在首次登录以访问合作伙伴组织中的资源后，都会看到“查看权限”屏幕。 在这里，他们可以查看邀请组织的隐私声明。 用户必须接受根据邀请组织的隐私策略使用其信息才能继续。

![显示访问面板中用户设置的屏幕截图](media/active-directory-b2b-redemption-experience/ConsentScreen.png) 

有关如何以租户管理员身份链接到组织隐私声明的信息，请参阅[操作说明：在 Azure Active Directory 中添加组织的隐私信息](https://aka.ms/adprivacystatement)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](active-directory-b2b-admin-add-users.md)
- [信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？](active-directory-b2b-iw-add-users.md)
- [使用 PowerShell 添加 Azure Active Directory B2B 协作用户](active-directory-b2b-api.md#powershell)
- [以来宾用户身份离开组织](active-directory-b2b-leave-the-organization.md)