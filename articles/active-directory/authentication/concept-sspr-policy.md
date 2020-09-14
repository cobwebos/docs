---
title: 自助密码重置策略 - Azure Active Directory
description: 了解各种 Azure Active Directory 自助式密码重置策略选项
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 4b729e975ddc9c184c1b0f39a6d3be548211cdfc
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052709"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Azure Active Directory 中的密码策略和账户限制

在 Azure Active Directory (Azure AD) 中，有一个密码策略用于定义密码复杂性、长度或期限等设置。 还有一个策略可用于定义用户名可接受的字符和长度。

使用自助服务密码重置 (SSPR) 在 Azure AD 中更改或重置密码时，将检查密码策略。 如果密码不满足策略要求，则系统会提示用户重试。 Azure 管理员对使用与普通用户帐户不同的 SSPR 有一些限制。

本文介绍与 Azure AD 租户中的用户帐户关联的密码策略设置和复杂性要求，以及如何使用 PowerShell 来检查或设置密码过期设置。

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>用户名策略

登录到 Azure AD 的每个帐户都必须有唯一的与其帐户关联的用户主体名称 (UPN) 属性值。 在混合环境中，如果其中有一个本地 Active Directory 域服务 (AD DS) 环境使用 Azure AD Connect 同步到 Azure AD，默认情况下，Azure AD UPN 设置为本地 UPN。

下表概括了既应用于同步到 Azure AD 的本地 AD DS 账户又应用于直接在 Azure AD 中创建的仅限云的用户帐户的用户名策略：

| 属性 | UserPrincipalName 要求 |
| --- | --- |
| 允许的字符 |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 不允许的字符 |<ul> <li>任何不分隔用户名和域的“\@\"”字符。</li> <li>不能包含紧靠在“\@\"”符号前面的点字符“.”</li></ul> |
| 长度约束 |<ul> <li>总长度不能超过 113 个字符</li><li>在“\@\"”符号前最多能有 64 个字符</li><li>在“\@\"”符号后最多能有 48 个字符</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD 密码指南

密码策略应用于直接在 Azure AD 中创建和管理的所有用户帐户。 虽然你可以 [为 Azure AD 密码保护配置自定义禁止密码](tutorial-configure-custom-password-protection.md)，但无法修改此密码策略。

除非启用 EnforceCloudPasswordPolicyForPasswordSyncedUsers，否则密码策略不适用于使用 Azure AD Connect 从本地 AD DS 环境同步的用户帐户。

定义了下列密码策略选项：

| 属性 | 要求 |
| --- | --- |
| 允许的字符 |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ;</li> <li>空白</li></ul> |
| 不允许的字符 | Unicode 字符。 |
| 密码限制 |<ul><li>至少 8 个字符，最多包含 256 个字符。</li><li>需满足以下 4 项中的 3 项：<ul><li>小写字符。</li><li>大写字符。</li><li>数字 (0-9)。</li><li>符号（请参阅前面的密码限制）。</li></ul></li></ul> |
| 密码过期期限（最长密码期限） |<ul><li>默认值：“90”天。</li><li>可通过 Windows PowerShell 的 Azure Active Directory 模块中的 `Set-MsolPasswordPolicy` cmdlet 来配置该值。</li></ul> |
| 密码到期通知（何时通知用户密码到期） |<ul><li>默认值：“14”天（密码到期前）。</li><li>可使用 `Set-MsolPasswordPolicy` cmdlet 配置该值。</li></ul> |
| 密码过期（让密码永不过期） |<ul><li>默认值：**false**（指示密码有到期日期）。</li><li>可使用 `Set-MsolUser` cmdlet 配置单个用户帐户的值。</li></ul> |
| 密码更改历史记录 | 用户更改密码时，上一个密码*不能*再次使用。 |
| 密码重置历史记录 | 用户重置忘记的密码时，上一个密码*可以*再次使用。 |
| 帐户锁定 | 使用错误密码 10 次登录尝试失败之后，用户会被锁定一分钟。 后续的错误登录尝试会增加用户被锁定的时间。 [智能锁定](howto-password-smart-lockout.md) 跟踪最后三个错误密码哈希，以避免为同一密码增加锁定计数器的值。 如果有人多次输入同一个错误密码，此行为不会导致帐户被锁定。 |

## <a name="administrator-reset-policy-differences"></a>管理员重置策略差异

Microsoft 为任意 Azure 管理员角色强制实施默认强*双门*密码重置策略。 此策略可能与你为用户定义的策略不同，因此无法更改此策略。 你应始终以未被分配任何 Azure 管理员角色的用户身份测试密码重置功能。

使用双门策略，管理员将无法使用安全问题。

双门策略需要两条身份验证数据（如电子邮件地址、身份验证应用或电话号码）。 双门策略在以下情况下适用：

* 以下所有 Azure 管理员角色将受到影响：
  * 支持管理员
  * 服务支持管理员
  * 计费管理员
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

### <a name="exceptions"></a>异常

单门策略需要一条身份验证数据，如电子邮件地址或电话号码。 单门策略在以下情况下适用：

* 它在试用订阅的前 30 天中；或
* 尚未为 Azure AD 租户配置自定义域，因此使用默认的 **. onmicrosoft.com*。 不建议在生产环境中使用默认的 **. onmicrosoft.com* 域;与
* Azure AD Connect 未同步标识

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>密码过期策略

*全局管理员*或*用户管理员*可以使用[用于 Windows PowerShell 的 Microsoft Azure AD 模块](/powershell/module/Azuread/?view=azureadps-2.0)将用户密码设置为永不过期。

还可以使用 PowerShell cmdlet 删除永不过期配置，或者查看已将哪些用户密码设置为永不过期。

本指南适用于其他提供程序，例如 Intune 和 Microsoft 365，它们也依赖于 Azure AD 用于标识和目录服务。 密码过期是策略中唯一可更改的部分。

> [!NOTE]
> 只能将未通过 Azure AD Connect 进行同步的用户帐户的密码配置为永不过期。 有关目录同步的详细信息，请参阅[将 AD 与 Azure AD 连接](../hybrid/whatis-hybrid-identity.md)。

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>使用 PowerShell 设置或检查密码策略

首先，[下载并安装 Azure AD PowerShell 模块](/powershell/module/Azuread/?view=azureadps-2.0)，然后[将其连接到 Azure AD 租户](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples)。

安装该模块后，请按照以下步骤完成每个所需任务。

### <a name="check-the-expiration-policy-for-a-password"></a>检查密码过期策略

1. 使用“全局管理员”或“用户管理员”帐户打开 PowerShell 提示符，并[连接到 Azure AD 租户](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) 。
1. 为单个用户或所有用户运行以下命令之一：

   * 若要查看单个用户的密码是否设置为永不过期，请运行以下 cmdlet。 `<user ID>`将替换为要检查的用户的用户 ID，如*driley \@ contoso.onmicrosoft.com*：

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * 若要查看所有用户的“密码永不过期”设置，请运行以下 cmdlet：

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>设置密码过期

1. 使用“全局管理员”或“用户管理员”帐户打开 PowerShell 提示符，并[连接到 Azure AD 租户](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) 。
1. 为单个用户或所有用户运行以下命令之一：

   * 若要将某一用户的密码设置为会过期的密码，请运行以下 cmdlet。 替换 `<user ID>` 为要检查的用户的用户 ID，如 *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * 若要将组织中所有用户的密码设置为过期，请使用以下 cmdlet：

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>将密码设置为永不过期

1. 使用“全局管理员”或“用户管理员”帐户打开 PowerShell 提示符，并[连接到 Azure AD 租户](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) 。
1. 为单个用户或所有用户运行以下命令之一：

   * 若要将某一用户的密码设置为永不过期，请运行以下 cmdlet。 替换 `<user ID>` 为要检查的用户的用户 ID，如 *driley \@ contoso.onmicrosoft.com*

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

若要开始进行 SSPR，请参阅[教程：使用户能够使用 Azure Active Directory 自助式密码重置来解锁其帐户或重置密码](tutorial-enable-sspr.md)。

如果你或用户遇到 SSPR 问题，请参阅[排查自助式密码重置问题](active-directory-passwords-troubleshoot.md)
