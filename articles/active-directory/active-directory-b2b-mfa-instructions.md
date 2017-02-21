---
title: "Azure Active Directory B2B 协作用户的多重身份验证 | Microsoft 文档"
description: "Azure Active Directory B2B 协作支持多重身份验证 (MFA)，以便对公司应用程序进行选择性访问"
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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 894f94fdefe081679b1e35183bd4127be35cd33c


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Azure Active Directory B2B 协作用户的多重身份验证

在此 Azure AD B2B 协作公共预览版刷新中，我们为组织引入了了也对 B2B 协作用户强制实施多重身份验证 (MFA) 策略的功能。 在此刷新中，始终对资源租户强制实施 MFA。

这意味着：
1. 公司 A 中的管理员或信息工作者邀请公司 B 中的用户加入公司 A 中的应用程序 Foo。
2. 公司 A 中的应用程序 *Foo* 配置为在访问时需要进行 MFA。
3. 当公司 B 中的用户尝试访问公司 A 租户中的应用 Foo 时，他们会被要求完成公司 A 的 MFA 策略要求的 MFA 质询。
4. 用户可以使用公司 A 设置其 MFA，选择其 MFA 选项
5. 这将适用于任何标识（Azure AD 或 MSA，例如，如果公司 B 中的用户使用社交 ID 进行身份验证）
6. 公司 A 将需要具有足够的高级 Azure AD SKU 以支持 MFA。 公司 B 中的用户将使用公司 A 提供的此许可证。
7. 总之，邀请方租户*始终*负责对合作伙伴组织中的 B2B 协作用户进行 MFA，而不是由合作伙伴组织本身进行 MFA（即使它具有 MFA 功能，也是如此）。 在将来版本中，我们将允许邀请方组织信任特定合作伙伴组织的 MFA，而不是使用邀请方组织的 MFA。

## <a name="setting-up-mfa-for-b2b-collaboration-users"></a>为 B2B 协作用户设置 MFA
若要了解为 B2B 协作用户设置 MFA 有多轻松，请参阅[此视频](https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup)中的操作方法。

## <a name="b2b-collaboration-users-mfa-experience-for-offer-redemption"></a>用于产品/服务兑换的 B2B 协作用户 MFA 体验
请查看下面的动画了解兑换体验，如[此视频](https://channel9.msdn.com/Blogs/Azure/MFA-redemption)中所示。

## <a name="mfa-reset-for-b2b-collaboration-users"></a>为 B2B 协作用户重置 MFA
目前，管理员可以要求 B2B 协作用户只使用以下 PowerShell cmdlet 再次身份验证。 因此，如果要重置 B2B 协作用户的身份验证方法，应使用以下 PowerShell cmdlet。

> [!NOTE]
> 若要使用新的 cmdlet，需要安装 Azure AD PowerShell V2 模块，可以从此处获取该模块：https://www.powershellgallery.com/packages/AzureADPreview

1. 连接到 Azure AD

  ```
  Connect-MsolService and login
  ```
2. 使用身份验证方法获取所有用户

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  下面是一个示例：

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 重置特定用户的 MFA 方法。 然后可以使用该 UserPrincipalName 运行 reset 命令，以要求 B2B 协作用户重新设置身份验证方法。 示例：

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作故障排除](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


