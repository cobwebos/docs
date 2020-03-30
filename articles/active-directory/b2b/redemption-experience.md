---
title: B2B 协作中的邀请兑换 - Azure AD
description: 介绍最终用户的 Azure AD B2B 协作邀请兑换体验，包括对隐私条款的同意。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050836"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 协作邀请兑换

本文介绍了来宾用户访问您的资源的方式以及他们遇到的同意过程。 如果您向来宾发送邀请电子邮件，邀请包括该来宾可以兑换的链接，以便访问您的应用或门户。 邀请电子邮件只是来宾访问资源的方式之一。 作为替代方法，您可以将来宾添加到目录，并为他们提供指向要共享的门户或应用的直接链接。 无论他们使用哪种方法，客人都会通过首次同意程序。 此过程可确保您的客人同意隐私条款，并接受您设置的任何[使用条款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)。

当您将来宾用户添加到目录时，来宾用户帐户具有同意状态（在 PowerShell 中查看），最初设置为 **"挂起接受"。** 此设置将保留，直到客人接受您的邀请并同意您的隐私政策和使用条款。 之后，同意状态更改为 **"已接受**"，并且同意页不再呈现给来宾。

   > [!IMPORTANT]
   > **从 2021 年 3 月 31 日起**，Microsoft 将不再支持为 B2B 协作方案创建非托管 Azure AD 帐户和租户来兑换邀请。 在准备中，我们鼓励客户选择[电子邮件一次性密码身份验证](one-time-passcode.md)。 我们欢迎您对此公共预览功能的反馈，并兴奋地创建更多协作方式。

## <a name="redemption-through-the-invitation-email"></a>通过邀请电子邮件兑换

使用[Azure 门户](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)将来宾用户添加到目录时，将在此过程中向来宾发送邀请电子邮件。 当您[使用 PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)将来宾用户添加到目录中时，您还可以选择发送邀请电子邮件。 以下是客人在电子邮件中兑换链接时的体验描述。

1. 来宾收到从**微软邀请**发送[的邀请电子邮件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)。
2. 来宾选择在电子邮件中**开始**。
3. 如果来宾在联合组织中没有 Azure AD 帐户、Microsoft 帐户 （MSA） 或电子邮件帐户，系统将提示他们创建 MSA（除非启用了[一次性密码](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)功能，不需要 MSA）。
4. 客人将体验以下的[同意体验](#consent-experience-for-the-guest)。

## <a name="redemption-through-a-direct-link"></a>通过直接链接兑换

作为邀请电子邮件的替代方法，您可以向来宾提供指向应用或门户的直接链接。 首先需要通过[Azure 门户](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)或[PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)将来宾用户添加到目录中。 然后，您可以使用任何[可自定义的方式将应用程序部署到用户](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)，包括直接登录链接。 当来宾使用直接链接而不是邀请电子邮件时，他们仍将获得首次同意体验的指导。

> [!IMPORTANT]
> 直接链接必须特定于租户。 换句话说，它必须包括租户 ID 或已验证的域，以便可以在共享应用所在的租户中对来宾进行身份验证。 类似https://myapps.microsoft.com这样的通用 URL 对来宾不起作用，因为它将重定向到其主租户进行身份验证。 下面是一些与租户上下文的直接链接的示例：
 > - 应用访问面板： https://myapps.microsoft.com/?tenantid=&lt;租户 ID&gt; 
 > - 已验证域的应用访问面板： https://myapps.microsoft.com/&lt;已验证域&gt;
 > - Azure 门户https://portal.azure.com/&lt： ;租户 ID&gt;
 > - 单个应用：了解如何使用[直接登录链接](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情况下，建议通过直接链接发送邀请电子邮件。 如果这些特殊情况对您的组织很重要，我们建议您使用仍发送邀请电子邮件的方法邀请用户：
 - 用户在联合组织中没有 Azure AD 帐户、MSA 或电子邮件帐户。 除非您使用的是一次性密码功能，否则来宾需要兑换邀请电子邮件，以便指导您完成创建 MSA 的步骤。
 - 有时，由于与联系人对象（例如，Outlook 联系人对象）存在冲突，受邀用户对象可能会没有电子邮件地址。 在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。
 - 用户可使用受邀电子邮件地址的别名登录。 （别名是与电子邮件帐户关联的其他电子邮件地址。在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。

## <a name="consent-experience-for-the-guest"></a>客人的同意体验

当来宾首次登录以访问合作伙伴组织中的资源时，他们会通过以下页面进行引导。 

1. 来宾查看描述邀请组织隐私声明的 **"审阅权限**"页。 用户**必须接受根据**邀请组织的隐私政策继续使用其信息。

   ![显示“查看权限”页的屏幕截图](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有关如何以租户管理员身份链接到组织隐私声明的信息，请参阅[操作说明：在 Azure Active Directory 中添加组织的隐私信息](https://aka.ms/adprivacystatement)。

2. 如果配置了使用条款，来宾将打开并查看使用条款，然后选择"**接受**"。 

   ![显示新使用条款的屏幕截图](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > 您可以在 **"管理** > **组织关系** > 使用**条款**"中配置[使用条款](../governance/active-directory-tou.md)。

3. 除非另有说明，否则来宾将重定向到"应用访问"面板，该面板列出了来宾可以访问的应用程序。

   ![显示应用访问面板的屏幕截图](media/redemption-experience/myapps.png) 

在目录中，来宾接受**的邀请值**更改为 **"是**"。 如果创建了 MSA，则来宾的**源**将显示**Microsoft 帐户**。 有关来宾用户帐户属性的详细信息，请参阅 Azure [AD B2B 协作用户的属性](user-properties.md)。 

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 添加 Azure Active Directory B2B 协作用户](customize-invitation-api.md#powershell)
- [以来宾用户身份离开组织](leave-the-organization.md)
