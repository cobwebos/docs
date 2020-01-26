---
title: B2B 协作中的邀请兑换-Azure AD
description: 介绍最终用户的 Azure AD B2B 协作邀请兑换体验，包括对隐私条款的同意。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0a71da025b8b2bb571dc9b00e23bc691ecdd44c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758270"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 协作邀请兑换

本文介绍来宾用户可以访问你的资源的方式以及他们将遇到的同意过程。 如果向来宾发送邀请电子邮件，邀请会提供一个链接，来宾可以兑换该链接来访问应用或门户。 邀请电子邮件只是来宾可以访问资源的一种方式。 作为替代方法，你可以将来宾添加到目录，并为其分配指向要共享的门户或应用程序的直接链接。 无论他们使用哪种方法，都会通过首次同意过程指导来宾。 此过程可确保客人同意隐私条款，并接受已设置[的任何使用条款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)。

将来宾用户添加到目录时，来宾用户帐户的同意状态（可在 PowerShell 中查看）最初设置为**PendingAcceptance**。 此设置将保留，直到来宾接受邀请，并同意你的隐私策略和使用条款。 之后，同意状态将更改为 "已**接受**"，并且同意页面将不再显示给来宾。

## <a name="redemption-through-the-invitation-email"></a>通过邀请电子邮件兑换

[使用 Azure 门户](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)将来宾用户添加到目录中时，会在该过程中向来宾发送邀请电子邮件。 [使用 PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)将来宾用户添加到目录时，还可以选择发送邀请电子邮件。 下面是客户在兑换电子邮件中的链接时的体验说明。

1. 来宾收到从**Microsoft 邀请**发送的[邀请电子邮件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)。
2. 来宾选择电子邮件中的 "**入门**"。
3. 如果来宾没有 Azure AD 帐户、Microsoft 帐户（MSA）或联合组织中的电子邮件帐户，系统会提示他们创建 MSA （除非启用了[一次性密码](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)功能，这不需要 msa）。
4. 来宾通过下面所述的[许可经验](#consent-experience-for-the-guest)来指导。

## <a name="redemption-through-a-direct-link"></a>通过直接链接兑换

作为邀请电子邮件的替代方法，你可以向来宾授予你的应用或门户的直接链接。 首先需要通过[Azure 门户](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)或[PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)将来宾用户添加到目录。 然后，你可以使用任何[可自定义的方式将应用程序部署到用户](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)，包括直接登录链接。 当来宾使用直接链接而不是邀请电子邮件时，他们仍将通过首次同意体验指导。

> [!IMPORTANT]
> 直接链接必须特定于租户。 换句话说，它必须包含租户 ID 或已验证的域，以便在你的租户中对来宾进行身份验证，共享应用位于该位置。 常见的 URL （如 https://myapps.microsoft.com ）对来宾不起作用，因为它将重定向到其主租户进行身份验证。 下面是包含租户上下文的直接链接的一些示例：
 > - 应用访问面板： https://myapps.microsoft.com/?tenantid=&lt ; 租户 id&gt; 
 > - 已验证域的 "应用访问面板"： https://myapps.microsoft.com/&lt ; 已验证的域&gt;
 > - Azure 门户： https://portal.azure.com/&lt ; 租户 id&gt;
 > - 单个应用：请参阅如何使用[直接登录链接](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情况下，建议通过直接链接使用邀请电子邮件。 如果这些特殊案例对你的组织很重要，我们建议你通过使用仍发送邀请电子邮件的方法邀请用户：
 - 用户没有 Azure AD 帐户、MSA 或联合组织中的电子邮件帐户。 除非你使用的是一次性密码功能，否则来宾需要兑换邀请电子邮件，以指导你完成创建 MSA 的步骤。
 - 有时，由于与联系人对象（例如，Outlook 联系人对象）存在冲突，受邀用户对象可能会没有电子邮件地址。 在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。
 - 用户可使用受邀电子邮件地址的别名登录。 （别名是一个与电子邮件帐户关联的附加电子邮件地址。）在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。

## <a name="consent-experience-for-the-guest"></a>来宾的许可体验

当来宾首次登录以访问合作伙伴组织中的资源时，将通过以下页面进行指导。 

1. 来宾会查看 "**评审权限**" 页，其中描述了邀请组织的隐私声明。 用户必须根据邀请的组织的隐私策略，**接受**其信息，然后才能继续操作。

   ![显示“查看权限”页的屏幕截图](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有关如何以租户管理员身份链接到组织隐私声明的信息，请参阅[操作说明：在 Azure Active Directory 中添加组织的隐私信息](https://aka.ms/adprivacystatement)。

2. 如果配置了使用条款，则来宾将打开并查看使用条款，然后选择 "**接受**"。 

   ![显示新使用条款的屏幕截图](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > 你可以配置在**管理** > **组织关系** > **使用条款**中查看[使用条款](../governance/active-directory-tou.md)。

3. 除非另外指定，否则来宾将重定向到 "应用访问面板"，其中列出了来宾可以访问的应用程序。

   ![显示应用访问面板的屏幕截图](media/redemption-experience/myapps.png) 

在目录中，来宾的 "**邀请已接受**" 值更改为 **"是"** 。 如果创建了 MSA，则来宾的**源**会显示**Microsoft 帐户**。 有关来宾用户帐户属性的详细信息，请参阅[AZURE AD B2B 协作用户的属性](user-properties.md)。 

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 添加 Azure Active Directory B2B 协作用户](customize-invitation-api.md#powershell)
- [以来宾用户身份离开组织](leave-the-organization.md)
