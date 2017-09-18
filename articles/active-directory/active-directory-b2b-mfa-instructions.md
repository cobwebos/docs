---
title: "Azure Active Directory B2B 协作用户的条件性访问 | Microsoft Docs"
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
ms.date: 09/11/2017
ms.author: sasubram
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: d3917754069ad961ffd9233d852aab6fa172eaef
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---

# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 协作用户的条件性访问

## <a name="multi-factor-authentication-for-b2b-users"></a>针对 B2B 用户的多重身份验证
通过 Azure AD B2B 协作，组织可以针对 B2B 用户强制实施多重身份验证 (MFA) 策略。 可以在租户、应用或个人用户级别强制实施这些策略，与针对全职员工和组织成员启用这些策略的方式相同。 资源组织中强制实施 MFA 策略。

示例：
1. 公司 A 中的管理员或信息工作者邀请公司 B 中的用户加入公司 A 中的应用程序 Foo。
2. 公司 A 中的应用程序 *Foo* 配置为在访问时需要进行 MFA。
3. 公司 B 中的用户尝试访问公司 A 租户中的应用 Foo 时，会要求其完成 MFA 质询。
4. 用户可设置公司 A 对其的 MFA，并选择 MFA 选项。
5. 此方案适用于任何标识（Azure AD 或 MSA，例如，如果公司 B 中的用户使用社交 ID 进行身份验证）
6. 公司 A 必须具有足够的支持 MFA 的高级 Azure AD 许可证。 公司 B 中的用户使用公司 A 提供的此许可证。

邀请方租户始终负责对合作伙伴组织中的用户进行 MFA（即使合作伙伴组织具有 MFA 功能）。

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>为 B2B 协作用户设置 MFA
若要了解为 B2B 协作用户设置 MFA 有多轻松，请参阅以下视频：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>用于产品/服务兑换的 B2B 用户 MFA 体验
请查看下面的动画了解兑换体验：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>为 B2B 协作用户重置 MFA
目前，管理员可以要求 B2B 协作用户只使用以下 PowerShell cmdlet 再次进行身份验证：

1. 连接到 Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. 使用身份验证方法获取所有用户

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  下面是一个示例：

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 重置特定用户的 MFA 方法，以要求该 B2B 协作用户再次设置身份验证方法。 示例：

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>为什么在资源租户中执行 MFA？

在当前版本中，为确保可预测性，资源租户始终需要执行 MFA。 例如，假设 Contoso 用户 (Sally) 被邀请到 Fabrikam，并且 Fabrikam 针对 B2B 用户启用了 MFA。

如果 Contoso 对 App1 启用了 MFA 策略，但未对 App2 启用，那么查看令牌中的 Contoso MFA 声明时，可能会发现以下问题：

* 第 1 天：用户在 Contoso 中进行了 MFA 并访问 App1，因此 Fabrikam 中没有显示其他 MFA 提示。

* 第 2 天：用户在 Contoso 中访问了 App2，那么如果现在访问 Fabrikam，就必须在其中注册 MFA。

这个过程可能会令人困惑，并可能会导致成功登录的次数降低。

此外，即使 Contoso 启用了 MFA 功能，Fabrikam 也不可能一直信任 Contoso MFA 策略。

最后，资源租户 MFA 还适用于 MSA 和社交 ID，以及尚未设置 MFA 的合作伙伴组织。

因此，在针对 B2B 用户进行 MFA 方面，建议始终需要在邀请方租户中进行 MFA。 此要求可能会导致有些时候需要进行两次 MFA，但是无论在什么时候访问邀请方租户，最终用户体验都是可以预测的：Sally 必须注册邀请方租户的 MFA。

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>B2B 用户基于设备、位置和风险的条件性访问

Contoso 为其公司数据启用基于设备的条件访问策略时，会阻止从不受 Contoso 管理并且不符合 Contoso 设备策略的设备进行访问。

如果 B2B 用户的设备不受 Contoso 管理，则在强制实施这些策略的任何上下文中，合作伙伴组织中的 B2B 用户进行的访问都将被阻止。 但是，Contoso 可以创建包含特定合作伙伴用户的排除列表，将其排除在基于设备的条件访问策略之外。

#### <a name="location-based-conditional-access-for-b2b"></a>针对 B2B 的基于位置的条件性访问

如果发出邀请的组织能够创建定义其合作伙伴组织的受信任 IP 地址范围，则可以针对 B2B 用户强制实施基于位置的条件访问策略。

#### <a name="risk-based-conditional-access-for-b2b"></a>针对 B2B 的基于风险的条件性访问

目前是在 B2B 用户的本组织中进行风险评估，因此不能对 B2B 用户应用基于风险的登录策略。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作疑难解答](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)

