---
title: "Azure Active Directory 中的密码策略和限制 | Microsoft Docs"
description: "介绍适用于 Azure Active Directory 中的密码的策略，包括允许的字符、长度和过期"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e7cae5cf-e0ee-467b-9a90-db6fdf56cd52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: ae3a2236b163e6a6ffdd7f1aeabf93406981ff0b
ms.lasthandoff: 04/13/2017


---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory 中的密码策略和限制
本文介绍与 Azure AD 目录中存储的用户帐户关联的各种密码策略和复杂性要求。

> [!IMPORTANT]
> **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md#reset-my-password)。
>
>

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>适用于所有用户帐户的 UserPrincipalName 策略
需登录到 Azure AD 身份验证系统的每个用户帐户都必须有唯一的与该帐户关联的用户主体名称 (UPN) 属性值。 下表概括了既适用于本地 Active Directory 来源的用户帐户（同步到云）也适用于仅限云的用户帐户的策略。

| 属性 | UserPrincipalName 要求 |
| --- | --- |
| 允许的字符 |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> 。 - \_ ! \# ^ \~</li></ul> |
| 不允许的字符 |<ul> <li>任何不分隔用户名和域的“@”字符。</li> <li>不能包含紧靠在“@”符号前面的点字符“.”</li></ul> |
| 长度约束 |<ul> <li>总长度不能超过 113 个字符</li><li>“@”符号前为 64 个字符</li><li>“@”符号后为 48 个字符</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>仅适用于云用户帐户的密码策略
下表描述了可用于在 Azure AD 中创建和管理的用户帐户的密码策略设置。

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

## <a name="next-steps"></a>后续步骤
* **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md#reset-my-password)。
* [从任意位置管理密码](active-directory-passwords.md)
* [密码管理的工作原理](active-directory-passwords-how-it-works.md)
* [密码管理入门](active-directory-passwords-getting-started.md)
* [自定义密码管理](active-directory-passwords-customize.md)
* [密码管理最佳实践](active-directory-passwords-best-practices.md)
* [如何使用密码管理报告获取操作见解](active-directory-passwords-get-insights.md)
* [密码管理常见问题解答](active-directory-passwords-faq.md)
* [排查密码管理问题](active-directory-passwords-troubleshoot.md)
* [了解详细信息](active-directory-passwords-learn-more.md)

