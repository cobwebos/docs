---
title: 通过 Azure AD 智能锁定功能防止暴力攻击
description: Azure Active Directory 智能锁定功能可帮助保护组织免受试图猜出密码的暴力攻击
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e44b6a8232bcd0bc72c4faa0f7116d42cec79111
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172410"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory 智能锁定

智能锁定功能可帮助锁定那些试图猜测用户密码或使用暴力方法进入的不良参与者。 它可识别来自有效用户的登录，还能将其与攻击者和其他未知来源的登录区别对待。 智能锁定会锁定攻击者，同时让你的用户能够继续访问其帐户并提高效率。

默认情况下，智能锁定会在 10 次尝试失败后锁定帐户，使其在一分钟内无法进行登录尝试。 在每次后续登录尝试失败后，帐户会再次锁定，第一次锁定一分钟，后续尝试失败会锁定更长时间。

智能锁定跟踪最后三个错误的密码哈希，以避免对相同密码增大锁定计数器。 如果有人多次输入同一个错误密码，此行为不会导致帐户被锁定。

 > [!NOTE]
 > 对于启用了直通身份验证的客户，哈希跟踪功能不可用，因为身份验证是在本地而不是在云中进行的。

智能锁定始终对所有 Azure AD 客户启用，其这些默认设置提供了合适的安全性和可用性组合。 要使用组织特定的值自定义智能锁定设置，需要向用户提供 Azure AD Basic 或更高版本的许可证。

使用智能锁定不保证真正的用户永远不会被锁定。当智能锁定锁定用户帐户时，我们会尽最大努力来确保不锁定真正的用户。 锁定服务会尽力确保不良参与者无法访问真正的用户帐户。  

* 每个 Azure Active Directory 数据中心都会独立地跟踪锁定。 如果用户访问每个数据中心，则用户将具有 (threshold_limit * datacenter_count) 次尝试。
* 智能锁定使用熟悉的位置与不熟悉的位置来区分不良参与者与真正的用户。 不熟悉的位置和熟悉的位置都将具有独立的锁定计数器。

智能锁定可与混合部署集成，使用密码哈希同步或直通身份验证来避免攻击者锁定本地 Active Directory 帐户。 通过在 Azure AD 中适当地设置智能锁定策略，可在攻击到达本地 Active Directory 之前将其筛选掉。

使用[直通身份验证](../hybrid/how-to-connect-pta.md)时，需要确保：

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
1. 将“锁定持续时间(以秒计)”设置为每次锁定的时长（以秒计）。 默认值为 60 秒（一分钟）。

> [!NOTE]
> 如果锁定后的首次登录也失败了，则帐户再次锁定。 如果帐户重复锁定，则锁定持续时间增加。

![在 Azure 门户中自定义 Azure AD 智能锁定策略](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smartlockout-feature-is-working-or-not"></a>如何确定 Smartlockout 功能是否正常运行

触发 smartlockout 阈值后，将在帐户锁定时收到以下消息：

**帐户暂时锁定以防止未经授权的使用。请稍后再试！如果仍有问题，请与管理员联系。**


## <a name="next-steps"></a>后续步骤

[了解如何使用 Azure AD 禁止组织中的错误密码。](howto-password-ban-bad.md)

[配置自助服务密码重置，让用户能够解锁自己的帐户。](quickstart-sspr.md)
