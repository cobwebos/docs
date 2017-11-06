---
title: "策略：Azure AD SSPR | Microsoft Docs"
description: "Azure AD 自助密码重置策略选项"
services: active-directory
keywords: "Active Directory 密码管理, 密码管理, Azure AD 自助密码重置"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5c33f08e54d522e0eea13a3e267f14f407fc59b6
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory 中的密码策略和限制

本文介绍与 Azure AD 租户中存储的用户帐户关联的密码策略和复杂性要求。

## <a name="administrator-password-policy-differences"></a>管理员密码策略差异

Microsoft 对任何 Azure 管理员角色（例如，全局管理员、支持管理员、密码管理员等）强制实施强默认的“双重关口”密码重置策略

这会禁止管理员使用安全问题，并强制实施以下项。

双入口策略适用于以下环境，该策略需要两条身份验证数据（电子邮件地址和电话号码）

* 所有 Azure 管理员角色
  * 支持管理员
  * 服务支持管理员
  * 计费管理员
  * 合作伙伴一线支持人员
  * 合作伙伴二线支持人员
  * Exchange 服务管理员
  * Lync 服务管理员
  * 用户帐户管理员
  * 目录编写人员
  * 全局管理员/企业管理员
  * SharePoint 服务管理员
  * 符合性管理员
  * 应用程序管理员
  * 安全管理员
  * 特权角色管理员
  * Intune 服务管理员
  * 应用程序代理服务器管理员
  * CRM 服务管理员
  * Power BI 服务管理员
  
* 试用中 30 天已过**或**
* 虚域存在 (contoso.com) **或**
* Azure AD Connect 正在从本地目录同步标识

### <a name="exceptions"></a>异常
单一入口策略适用于以下环境，该策略需要一条身份验证数据（电子邮件地址或电话号码）

* 试用的前 30 天**或**
* 虚域不存在 (*.onmicrosoft.com) **且** Azure AD Connect 未在同步标识


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>适用于所有用户帐户的 UserPrincipalName 策略

需登录到 Azure AD 的每个用户帐户都必须有唯一的与其帐户关联的用户主体名称 (UPN) 属性值。 下表概括了既适用于同步到云的本地 Active Directory 用户帐户，又适用于仅限云的用户帐户的策略。

| 属性 | UserPrincipalName 要求 |
| --- | --- |
| 允许的字符 |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> 。 - \_ ! \# ^ \~</li></ul> |
| 不允许的字符 |<ul> <li>任何不分隔用户名和域的“@”字符。</li> <li>不能包含紧靠在“@”符号前面的点字符“.”</li></ul> |
| 长度约束 |<ul> <li>总长度不能超过 113 个字符</li><li>“@”符号前为 64 个字符</li><li>“@”符号后为 48 个字符</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>仅适用于云用户帐户的密码策略

下表介绍了可应用于在 Azure AD 中创建和管理的用户帐户的可用密码策略设置。

| 属性 | 要求 |
| --- | --- |
| 允许的字符 |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 不允许的字符 |<ul><li>Unicode 字符</li><li>空格</li><li> **仅限强密码**：不能包含紧靠在“@”符号前面的点字符“.”</li></ul> |
| 密码限制 |<ul><li>最少 8 个字符，最多 16 个字符</li><li>**仅限强密码**：需满足以下 4 条中的 3 条：<ul><li>小写字符</li><li>大写字符</li><li>数字 (0-9)</li><li>符号（请参阅上面的密码限制）</li></ul></li></ul> |
| 密码过期期限 |<ul><li>默认值：**90** 天 </li><li>可通过 Windows PowerShell 的 Azure Active Directory 模块中的 Set-MsolPasswordPolicy cmdlet 来配置该值。</li></ul> |
| 密码过期通知 |<ul><li>默认值：**14** 天（密码到期前）</li><li>可使用 Set-MsolPasswordPolicy cmdlet 配置值。</li></ul> |
| 密码过期 |<ul><li>默认值：**false** 天（表示已启用密码到期） </li><li>可使用 Set-msoluser cmdlet 配置单个用户帐户的值。 </li></ul> |
| 密码**更改**历史记录 |**更改**密码后将**无法**再次使用上一个密码。 |
| 密码**重置**历史记录 | 由于忘记密码而**重置**后，**可能**还可再次使用上一个密码。 |
| 帐户锁定 |10 次登录尝试失败（错误密码）之后，用户会被锁定一分钟。 后续的错误登录尝试会增加用户被锁定的时间。 |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>在 Azure Active Directory 中设置密码过期策略

Microsoft 云服务的全局管理员可使用用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块将用户密码设置为永不过期。 还可以使用 Windows PowerShell cmdlet 删除永不过期配置，或者查看已将哪些用户密码设置为永不过期。 本指南适用于其他提供程序（如 Microsoft Intune 和 Office 365），这些提供程序也依赖于 Microsoft Azure Active Directory 提供标识和目录服务。

> [!NOTE]
> 只能将未通过目录同步进行同步的用户帐户的密码配置为永不过期。 有关目录同步的详细信息，请参阅[将 AD 与 Azure AD 连接](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>使用 PowerShell 设置或检查密码策略

若要开始，需要[下载并安装 Azure AD PowerShell 模块](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)。 安装后，可以遵照以下步骤配置每个字段。

### <a name="how-to-check-expiration-policy-for-a-password"></a>如何检查密码过期策略
1. 使用公司管理员凭据连接到 Windows PowerShell。
2. 执行以下命令之一：

   * 若要查看单个用户的密码是否已设置为永不过期，请使用用户主体名称 (UPN)（例如 aprilr@contoso.onmicrosoft.com）或要查看的用户的用户 ID 运行以下 cmdlet：`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * 若要查看所有用户的“密码永不过期”设置，请运行以下 cmdlet：`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>设置密码过期

1. 使用公司管理员凭据连接到 Windows PowerShell。
2. 执行以下命令之一：

   * 要将某一个用户的密码设置为过期，请使用用户主体名称 (UPN) 或该用户的用户 ID 运行以下 cmdlet：`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * 要将组织中所有用户的密码设置为过期，请使用以下 cmdlet：`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>将密码设置为永不过期

1. 使用公司管理员凭据连接到 Windows PowerShell。
2. 执行以下命令之一：

   * 要将某一个用户的密码设置为永不过期，请使用用户主体名称 (UPN) 或该用户的用户 ID 运行以下 cmdlet：`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * 要将组织中所有用户的密码设置为永不过期，请运行以下 cmdlet：`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [如何完成 SSPR 成功推出？](active-directory-passwords-best-practices.md)
* [重置或更改密码](active-directory-passwords-update-your-own-password.md)。
* [注册自助服务密码重置](active-directory-passwords-reset-register.md)。
* [是否有许可问题？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些数据？你应为用户填充哪些数据？](active-directory-passwords-data.md)
* [哪些身份验证方法可供用户使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [什么是密码写回？我为什么关心它？](active-directory-passwords-writeback.md)
* [如何报告 SSPR 中的活动？](active-directory-passwords-reporting.md)
* [SSPR 中的所有选项是什么？它们有哪些含义？](active-directory-passwords-how-it-works.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)
