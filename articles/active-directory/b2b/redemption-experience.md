---
title: B2B 协作邀请兑换 - Azure Active Directory | Microsoft Docs
description: 介绍最终用户的 Azure AD B2B 协作邀请兑换体验，包括对隐私条款的同意。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052458"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 协作邀请兑换

本文介绍了来宾用户可以访问你的资源和他们会遇到在同意处理方法。 如果发送到来宾邀请电子邮件，邀请包括来宾可以兑换的链接以获取你的应用程序或门户的访问权限。 只是一种来宾有权访问你的资源的邀请电子邮件。 或者，可以将来宾添加到你的目录，并将它们提供给门户或你想要共享的应用的直接链接。 它们使用的方法，无论来宾指导完成首次同意过程中。 此过程可确保您的客人同意隐私条款，接受任何[使用条款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)，已设置好。

来宾用户帐户时向你的目录添加来宾用户，具有一个许可状态 （可在 PowerShell 中查看），最初设置为**PendingAcceptance**。 此设置将保持直到来宾接受邀请并同意你的隐私策略和使用条款。 此后，同意状态将变为**已接受**，并在同意页不再呈现给来宾。

## <a name="redemption-through-the-invitation-email"></a>通过邀请电子邮件兑换

将来宾用户添加到的目录[使用 Azure 门户](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)，邀请电子邮件发送到进程中来宾。 您还可以选择发送邀请电子邮件后[使用 PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)将来宾用户添加到你的目录。 当他们兑换的电子邮件中的链接时，以下是体验的来宾的说明。

1. 来宾会收到[邀请电子邮件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)从发送**Microsoft 邀请**。
2. 来宾选择**开始**电子邮件中。
3. 如果来宾不具有在联合组织中的 Azure AD 帐户、 Microsoft 帐户 (MSA) 或电子邮件帐户，系统会提示他们创建 MSA (除非[一次性密码](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)启用功能后，不需要 MSA).
4. 来宾指导[同意体验](#consent-experience-for-the-guest)如下所述。

## <a name="redemption-through-a-direct-link"></a>通过直接链接兑换

作为邀请电子邮件的替代方法，您可以进行来宾的直接链接到你的应用程序或门户。 首先需要将来宾用户添加到你的目录通过[Azure 门户](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)或[PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)。 然后，可以使用任一[向用户部署应用程序的可自定义方法](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)，包括直接登录链接。 当来宾而不是邀请电子邮件使用直接链接时，它们将仍可指导您完成第一次同意体验。

> [!IMPORTANT]
> 直接链接必须是特定于租户的。 换而言之，它必须包含租户 ID，或已验证域，以便来宾可以在租户中，共享的应用所在的位置进行身份验证。 公共 URL 类似于 https://myapps.microsoft.com 不适用于来宾因为它将重定向到其宿主租户中进行身份验证。 下面是与租户上下文的直接链接的一些示例：
 > - 应用程序访问面板： https://myapps.microsoft.com/?tenantid=&lt ; 租户 id&gt; 
 > - 应用访问面板中的已验证的域： https://myapps.microsoft.com/&lt ; 已验证域&gt;
 > - Azure 门户： https://portal.azure.com/&lt ; 租户 id&gt;
 > - 单个应用： 请参阅如何使用[直接登录链接](../manage-apps/end-user-experiences.md#direct-sign-on-links)

有某些情况下，邀请电子邮件建议通过直接链接。 如果这些特殊的情况下对你的组织至关重要，我们建议您通过使用方法仍将发送邀请电子邮件邀请用户：
 - 用户不具有在联合组织中的 Azure AD 帐户、 MSA 或电子邮件帐户。 除非使用一次性密码功能，来宾将需要兑换邀请电子邮件，指导您完成创建 MSA 的步骤。
 - 有时，由于与联系人对象（例如，Outlook 联系人对象）存在冲突，受邀用户对象可能会没有电子邮件地址。 在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。
 - 用户可使用受邀电子邮件地址的别名登录。 （别名指与电子邮件帐户关联的其他电子邮件地址。）在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。

## <a name="consent-experience-for-the-guest"></a>来宾的的同意体验

来宾身份登录时要访问第一次的合作伙伴组织中的资源，它们被指导您完成以下页面。 

1. 来宾评审**查看权限**页描述邀请方组织的隐私声明。 用户必须**接受**到邀请方组织的隐私策略，以继续根据其信息的用途。

   ![显示“查看权限”页的屏幕截图](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有关如何以租户管理员身份链接到组织隐私声明的信息，请参阅[如何：在 Azure Active Directory 中添加组织的隐私信息](https://aka.ms/adprivacystatement)。

2. 如果配置了使用条款，来宾将打开并查看使用条款，，然后选择**接受**。 

   ![显示新使用条款的屏幕截图](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > 你可以配置，请参阅[使用条款](../governance/active-directory-tou.md)中**管理** > **组织关系** > **使用条款**.

3. 除非另行指定，来宾将重定向到的应用程序访问面板中，其中列出了来宾可以访问的应用程序。

   ![显示应用程序访问面板的屏幕截图](media/redemption-experience/myapps.png) 

在目录中，来宾的**已接受邀请**值更改为**是**。 如果创建 MSA，访客**源**演示**的 Microsoft 帐户**。 有关来宾用户帐户属性的详细信息，请参阅[的 Azure AD B2B 协作用户属性](user-properties.md)。 

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 添加 Azure Active Directory B2B 协作用户](customize-invitation-api.md#powershell)
- [以来宾用户身份离开组织](leave-the-organization.md)
