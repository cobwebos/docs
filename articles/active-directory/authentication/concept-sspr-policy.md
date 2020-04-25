---
title: 自助密码重置策略 - Azure Active Directory
description: 了解不同 Azure Active Directory 自助服务密码重置策略选项
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8b6d08dd2073de80ac0f7fd08f510d9cda80545
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143233"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>自助服务密码重置策略和 Azure Active Directory 中的限制

本文介绍与 Azure Active Directory (Azure AD) 租户中的用户帐户关联的密码策略和复杂性要求。

## <a name="administrator-reset-policy-differences"></a>管理员重置策略差异

**Microsoft 对任何 Azure 管理员角色强制实施强默认的*双门*密码重置策略**。 此策略可能不同于为用户定义的策略，因此无法更改此策略。 你应始终以未被分配任何 Azure 管理员角色的用户身份测试密码重置功能。

使用双门策略，**管理员将无法使用安全问题**。

双门策略需要两条身份验证数据，如“电子邮件地址”、“身份验证应用”或“电话号码”******。 双门策略在以下情况下适用：

* 以下所有 Azure 管理员角色将受到影响：
  * 支持管理员
  * 服务支持管理员
  * 帐务管理员
  * 合作伙伴一线支持人员
  * 合作伙伴二线支持人员
  * Exchange 管理员
  * Skype for Business 管理员
  * 用户管理员
  * 目录写入者
  * 全局管理员或公司管理员
  * SharePoint 管理员
  * 法规管理员
  * 应用程序管理员
  * 安全管理员
  * 特权角色管理员
  * Intune 管理员
  * 应用程序代理服务器管理员
  * Dynamics 365 管理员
  * Power BI 服务管理员
  * 身份验证管理员
  * 特权身份验证管理员

* 如果在试用订阅中已过 30 天；或
* 已为 Azure AD 租户配置了自定义域，如 *contoso.com*；或
* Azure AD Connect 正在从本地目录同步标识

### <a name="exceptions"></a>例外

单门策略需要一条身份验证数据，如电子邮件地址或电话号码。 单门策略在以下情况下适用：

* 它在试用订阅的前 30 天中；或
* 尚未为 Azure AD 租户配置自定义域，因此使用默认的 **. onmicrosoft.com*。 不建议在生产环境中使用默认的 **. onmicrosoft.com*域;与
* Azure AD Connect 未同步标识

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>适用于所有用户帐户的 UserPrincipalName 策略

需登录到 Azure AD 的每个用户帐户都必须有唯一的与其帐户关联的用户主体名称 (UPN) 属性值。 下表概述了适用于本地 Active Directory 域服务用户帐户（同步到云）和仅限云的用户帐户的策略：

| 属性 | UserPrincipalName 要求 |
| --- | --- |
| 允许的字符 |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 不允许的字符 |<ul> <li>任何不分隔用户名和域的“\@\"”字符。</li> <li>不能包含紧靠在“\@\"”符号前面的点字符“.”</li></ul> |
| 长度约束 |<ul> <li>总长度不能超过 113 个字符</li><li>在“\@\"”符号前最多能有 64 个字符</li><li>在“\@\"”符号后最多能有 48 个字符</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>仅适用于云用户帐户的密码策略

下表描述了适用于在 Azure AD 中创建和管理的用户帐户的密码策略设置：

| 属性 | 要求 |
| --- | --- |
| 允许的字符 |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [] {} &#124; \： '，。 ? / \` ~ " ( ) ;</li> <li>空白</li></ul> |
| 不允许的字符 | Unicode 字符。 |
| 密码限制 |<ul><li>至少 8 个字符，最多包含 256 个字符。</li><li>需满足以下 4 项中的 3 项：<ul><li>小写字符。</li><li>大写字符。</li><li>数字 (0-9)。</li><li>符号（请参阅前面的密码限制）。</li></ul></li></ul> |
| 密码过期期限（最长密码期限） |<ul><li>默认值：**90** 天。</li><li>可通过 Windows PowerShell 的 Azure Active Directory 模块中的 `Set-MsolPasswordPolicy` cmdlet 来配置该值。</li></ul> |
| 密码到期通知（何时通知用户密码到期） |<ul><li>默认值：**14** 天（密码到期前）。</li><li>可使用 `Set-MsolPasswordPolicy` cmdlet 配置该值。</li></ul> |
| 密码过期（允许密码永不过期） |<ul><li>默认值：**false**（指示密码有到期日期）。</li><li>可使用 `Set-MsolUser` cmdlet 配置单个用户帐户的值。</li></ul> |
| 密码更改历史记录 | 用户更改密码时，上一个密码*不能*再次使用。 |
| 密码重置历史记录 | 用户重置忘记的密码时，上一个密码*可以*再次使用。 |
| 帐户锁定 | 使用错误密码 10 次登录尝试失败之后，用户会被锁定一分钟。 后续的错误登录尝试会增加用户被锁定的时间。 [智能锁定](howto-password-smart-lockout.md)跟踪最后三个错误的密码哈希，以避免对相同密码增大锁定计数器。 如果有人多次输入同一错误密码，则此行为不会导致帐户锁定。 |

## <a name="set-password-expiration-policies-in-azure-ad"></a>在 Azure AD 中设置密码过期策略

Microsoft 云服务的*全局管理员*或*用户管理员*可以使用*用于 Windows PowerShell 的 Microsoft Azure AD 模块*将用户密码设置为永不过期。 还可以使用 Windows PowerShell cmdlet 删除永不过期配置，或者查看已将哪些用户密码设置为永不过期。

本指南适用于其他提供程序（如 Intune 和 Office 365），这些提供程序也依赖于 Azure AD 提供标识和目录服务。 密码过期是策略中唯一可更改的部分。

> [!NOTE]
> 只能将未通过目录同步进行同步的用户帐户的密码配置为永不过期。 有关目录同步的详细信息，请参阅[将 AD 与 Azure AD 连接](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>使用 PowerShell 设置或检查密码策略

若要开始，请[下载并安装 Azure AD PowerShell 模块](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)并[将其连接到 Azure AD 租户](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples)。 安装该模块后，请使用以下步骤配置每个字段。

### <a name="check-the-expiration-policy-for-a-password"></a>检查密码过期策略

1. 使用用户管理员或公司管理员凭据连接到 Windows PowerShell。
1. 运行下列命令之一：

   * 若要查看单个用户的密码是否设置为永不过期，请使用 UPN （例如， *aprilr\@contoso.onmicrosoft.com*）或要查看的用户的用户 ID 运行以下 cmdlet：

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * 若要查看所有用户的 "**密码永不过期**" 设置，请运行以下 cmdlet：

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>设置密码过期

1. 使用用户管理员或公司管理员凭据连接到 Windows PowerShell。
1. 执行以下命令之一：

   * 若要将某一用户的密码设置为过期，请使用该用户的 UPN 或用户 ID 运行以下 cmdlet：

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * 若要将组织中所有用户的密码设置为过期，请使用以下 cmdlet：

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>将密码设置为永不过期

1. 使用用户管理员或公司管理员凭据连接到 Windows PowerShell。
1. 执行以下命令之一：

   * 若要将某一用户的密码设置为永不过期，请使用该用户的 UPN 或用户 ID 运行以下 cmdlet：

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * 若要将组织中所有用户的密码设置为永不过期，请运行以下 cmdlet：

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > 设置为 `-PasswordPolicies DisablePasswordExpiration` 的密码仍会基于 `pwdLastSet` 属性过时。 基于 `pwdLastSet` 属性，如果将过期更改为 `-PasswordPolicies None`，则所有 `pwdLastSet` 早于 90 天的密码将需要用户在下一次登录时更改它们。 此更改可能会影响很多用户。

## <a name="next-steps"></a>后续步骤

若要开始使用 SSPR，请参阅[教程：使用户能够使用 Azure Active Directory 自助服务密码重置解锁其帐户或重置密码](tutorial-enable-sspr.md)。

如果你或用户遇到 SSPR 问题，请参阅[排查自助密码重置](active-directory-passwords-troubleshoot.md)问题
