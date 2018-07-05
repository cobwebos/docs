---
title: 通过 Azure AD 智能锁定功能防止暴力攻击
description: Azure Active Directory 智能锁定功能可帮助保护组织免受试图猜出密码的暴力攻击
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: d5beb5ce6e167cd100bec2ed54dc6ea0e78ba37b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034303"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory 智能锁定

智能锁定功能使用云智能锁定试图猜测用户密码或使用暴力访问方式的不良参与者。 该智能可识别来自有效用户的登录，还能将其与攻击者和其他未知来源的登录区别对待。 智能锁定可锁定攻击者，同时让你的用户能够继续访问其帐户并提高效率。

智能锁定始终对所有 Azure AD 客户启用，其默认设置提供了合适的安全性和可用性组合。 要使用组织特定的值自定义智能锁定设置，需要向用户提供 Azure AD Basic 或更高版本的许可证。

智能锁定可与混合部署集成，使用密码哈希同步或直通身份验证来避免攻击者锁定本地 Active Directory 帐户。 通过在 Azure AD 中适当地设置智能锁定策略，可在攻击到达本地 Active Directory 之前将其筛选掉。

使用[直通身份验证](../connect/active-directory-aadconnect-pass-through-authentication.md)时，需要确保：

   * Azure AD 的锁定阈值小于 Active Directory 的帐户锁定阈值。 请设置此值，以便使 Active Directory 的帐户锁定阈值至少长于 Azure AD 锁定阈值的二倍或三倍。 
   * Azure AD 的锁定持续时间（以秒计）长于 Active Directory 的“在...后重置帐户锁定计数器”的持续时间（以分钟计）。

> [!IMPORTANT]
> 目前，如果用户的云帐户已被智能锁定功能锁定，管理员将无法为其解锁。 管理员必须等到锁定持续时间到期。

## <a name="verify-on-premises-account-lockout-policy"></a>验证本地帐户锁定策略

按以下说明验证本地 Active Directory 帐户锁定策略：

1. 打开“组策略管理”工具。
2. 编辑包含组织帐户锁定策略的组策略，例如默认域策略。
3. 浏览到“计算机配置” > “策略” > “Windows 设置” > “安全设置” > “帐户策略” > “帐户锁定策略”。
4. 验证“帐户锁定阈值”和“在此后重置帐户锁定计数器”值。

![使用组策略对象修改本地 Active Directory 帐户锁定策略](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>管理 Azure AD 智能锁定值

根据组织的要求，可能需要自定义智能锁定值。 要使用组织特定的值自定义智能锁定设置，需要向用户提供 Azure AD Basic 或更高版本的许可证。

要检查或修改组织的智能锁定值，请按以下步骤操作：

1. 登录 [Azure 门户](https://portal.azure.com)，再依次单击“Azure Active Directory”和“身份验证方法”。
1. 根据帐户在第一次锁定之前允许的登录失败次数，设置“锁定阈值”。 默认值为 10。
1. 将“锁定持续时间(以秒计)”设置为每次锁定的时长（以秒计）。

> [!NOTE]
> 如果锁定后的首次登录也失败了，则帐户再次锁定。 如果帐户重复锁定，则锁定持续时间增加。

![在 Azure 门户中自定义 Azure AD 智能锁定策略](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>后续步骤

[了解如何使用 Azure AD 禁止组织中的错误密码。](howto-password-ban-bad.md)

[配置自助服务密码重置，让用户能够解锁自己的帐户。](quickstart-sspr.md)